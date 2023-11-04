## Лабораторная Работа 3. AsyncIO

**Задание выполнил:** Буряков Иван Олегович ИВТ(1)

### Задание: 

Комплект 1: Библиотека Asyncio.

1.1: Создайте простую асинхронную функцию, которая в бесконечном цикле отображает текущее время с интервалом 1 секунда, и запустите ее с помощью asyncio. Завершать программу можно через комбинацию клавиш типа Ctrl + C или Ctrl + Break .

1.2: Улучшите предыдущую программу. Распечатывайте текущие дату и время в одной и той же строке (используйте символ возврата каретки '\r') разными цветами с помощью библиотеки termcolor. Добавьте обработку события нажатия клавиши Esc с помощью библиотеки pynput, чтобы можно было выйти из программы.

1.3: Разработайте программу, которая использует asyncio.gather для выполнения двух асинхронных задач параллельно и затем обрабатывает результаты в отдельной функции в цикле.

1.4: Улучшите предыдущую программу, сделав параллельно запрос к публичному веб­серверу и к любой публичной базе данных с распечаткой результатов этих вызовов. Например сделайте запросы к публичному ресурсу последовательностей РНК ­ RNACentral: https://rnacentral.org/about-us.
~~~
# https :// rnacentral . org/api
# URL вебсервера−
WEB_SERVER_URL = " https :// rnacentral . org/api/v1/rna/"
# https :// rnacentral . org/help/public−database
# Строкаподключениякбазеданных
DB_CONNECTION_STRING = \
" postgres ://reader :NWDMCE5xdipIjRrp@hh−pgsql−public.ebi.ac.uk:5432/pfmegrnargs "
~~~

В этом случае вам потребуются библиотеки aiohttp ­ для любых HTTP запросов и asyncpg ­ для базы данных Postgres ресурса RNACentral.
Отформатируйте красиво JSON сообщение с помощбю модуля json, если это URL типа https://rnacentral.org/api/v1/rna/.

1.5: Создайте асинхронный веб­скрапер, используя aiohttp и asyncio, который собирает информацию из нескольких веб­страниц одновременно. Вынесите код скрапера в отдельный класс. Загружайте список URLs из отдельного файла.

1.6: Улучшите асинхронный веб­скрапер из предыдущей задачи. Преобразуйте его в класс асинхронного менеджера контекста и используйте вместе с async with изнутри асинхронной функции main, которая вызывается через asyncio.run(main()).

1.7: Реализуйте сервер и клиент с использованием библиотек asyncio и HTTPS протокола для отправки текстовых JSON сообщений, позволяющий передавать текстовые сообщения в обе стороны в цикле с клавиатуры в режиме echo.


___________________________________________
### Решение:
___________________________________________

## Комплект 1: Библиотека Asyncio.

## 1.1

Для создания асинхронной функции, которая будет отображать текущее время с интервалом 1 секунда в бесконечном цикле и остановит выполнение программы при нажатии Ctrl + C, вы можете воспользоваться библиотекой asyncio. Вот пример такой программы:

```python
import asyncio
import signal
import datetime

async def display_time():
    while True:
        current_time = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        print(current_time)
        await asyncio.sleep(1)

async def main():
    try:
        await display_time()
    except asyncio.CancelledError:
        pass

def handle_exit():
    loop.stop()

loop = asyncio.get_event_loop()
loop.add_signal_handler(signal.SIGINT, handle_exit)
loop.add_signal_handler(signal.SIGBREAK, handle_exit)

try:
    loop.run_until_complete(main())
except KeyboardInterrupt:
    pass
finally:
    loop.close()
```

Этот код создает асинхронную функцию `display_time`, которая выводит текущее время каждую секунду, и основную асинхронную функцию `main`, которая обрабатывает Ctrl + C. Мы используем `asyncio.sleep(1)` для создания задержки в 1 секунду между отображением времени.

Также мы добавляем обработчики сигналов SIGINT (Ctrl + C) и SIGBREAK (Ctrl + Break) для корректного завершения программы при нажатии соответствующих клавиш.

Запустите эту программу, и она будет выводить текущее время с интервалом 1 секунда, останавливаясь только при нажатии Ctrl + C или Ctrl + Break.

## 1.2

Для улучшения программы, чтобы она выводила текущую дату и время в одной и той же строке с разными цветами с использованием библиотеки `termcolor` и обрабатывала событие нажатия клавиши Esc с использованием библиотеки `pynput`, вы можете внести следующие изменения:

1. Установите библиотеки `termcolor` и `pynput`, если у вас их еще нет:
   ```
   pip install termcolor pynput
   ```

2. Вот обновленный код:

```python
import asyncio
import datetime
from termcolor import colored
from pynput import keyboard

async def display_time():
    while True:
        current_time = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        colored_time = colored(current_time, 'green', 'on_blue')
        print(f"\r{colored_time}", end='', flush=True)
        await asyncio.sleep(1)

def on_key_release(key):
    if key == keyboard.Key.esc:
        loop.stop()

async def main():
    try:
        with keyboard.Listener(on_release=on_key_release) as listener:
            await display_time()
            listener.join()
    except asyncio.CancelledError:
        pass

loop = asyncio.get_event_loop()

try:
    loop.run_until_complete(main())
except KeyboardInterrupt:
    pass
finally:
    loop.close()
```

В этом коде мы используем библиотеку `termcolor` для вывода текущей даты и времени с разными цветами, а также библиотеку `pynput` для обработки события нажатия клавиши Esc. Результат будет отображаться в одной и той же строке с зеленым текстом на синем фоне, и программа завершится при нажатии клавиши Esc.

## 1.3

Для выполнения двух асинхронных задач параллельно с использованием `asyncio.gather` и последующей обработки результатов в отдельной функции в цикле, вы можете воспользоваться следующим примером:

```python
import asyncio

# Асинхронная функция, которая выполняет задачу 1
async def task1():
    await asyncio.sleep(2)  # Подождем 2 секунды
    return "Задача 1 выполнена"

# Асинхронная функция, которая выполняет задачу 2
async def task2():
    await asyncio.sleep(1)  # Подождем 1 секунду
    return "Задача 2 выполнена"

# Функция для обработки результатов
def process_results(results):
    for result in results:
        print(result)

async def main():
    # Запускаем две задачи параллельно с помощью asyncio.gather
    results = await asyncio.gather(task1(), task2())

    # Обработка результатов
    process_results(results)

# Запуск основной асинхронной функции
if __name__ == "__main__":
    asyncio.run(main())
```

Этот код запускает две асинхронные задачи `task1` и `task2` параллельно с использованием `asyncio.gather`. Затем результаты этих задач передаются функции `process_results`, которая их обрабатывает.

## 1.4

Для выполнения параллельных запросов к веб-серверу и базе данных Postgres с использованием библиотек aiohttp и asyncpg и отображения результатов, вы можете использовать следующий код. В этом примере выполняются запросы к веб-серверу RNACentral и к базе данных Postgres.

Перед запуском кода убедитесь, что у вас установлены библиотеки `aiohttp`, `asyncpg` и `json`:

```bash
pip install aiohttp asyncpg
```

Вот улучшенный код:

```python
import asyncio
import aiohttp
import asyncpg
import json

WEB_SERVER_URL = "https://rnacentral.org/api/v1/rna/"
DB_CONNECTION_STRING = "postgres://reader:NWDMCE5xdipIjRrp@hh-pgsql-public.ebi.ac.uk:5432/pfmegrnargs"

async def fetch_data_from_web_server():
    async with aiohttp.ClientSession() as session:
        async with session.get(WEB_SERVER_URL) as response:
            data = await response.json()
            print("Результат запроса к веб-серверу:")
            print(json.dumps(data, indent=2))

async def fetch_data_from_database():
    async with asyncpg.connect(DB_CONNECTION_STRING) as connection:
        result = await connection.fetch("SELECT * FROM your_table_name")
        print("Результат запроса к базе данных:")
        for row in result:
            print(row)

async def main():
    # Запускаем оба асинхронных запроса параллельно
    web_server_task = asyncio.create_task(fetch_data_from_web_server())
    database_task = asyncio.create_task(fetch_data_from_database())

    # Ожидаем выполнения обоих задач
    await web_server_task
    await database_task

if __name__ == "__main__":
    asyncio.run(main())
```

В этом коде две асинхронные функции `fetch_data_from_web_server` и `fetch_data_from_database` выполняют запросы к веб-серверу и базе данных Postgres соответственно. Затем они выводят результаты запросов в формате JSON и строковом виде.

Требуется заменить `"SELECT * FROM your_table_name"` на ваш запрос к базе данных в соответствии с потребностями.

## 1.5

Для создания асинхронного веб-скрапера, который собирает информацию из нескольких веб-страниц одновременно с использованием aiohttp и asyncio, и загружает список URL-адресов из отдельного файла, вы можете создать следующий класс. Предполагается, что список URL-адресов находится в файле `urls.txt`, каждый URL на отдельной строке.

Сначала убедитесь, что у вас установлены библиотеки `aiohttp` и `asyncio`:

```bash
pip install aiohttp
```

Затем вот код для асинхронного веб-скрапера:

```python
import aiohttp
import asyncio

class WebScraper:
    def __init__(self):
        self.urls = self.load_urls_from_file("urls.txt")

    def load_urls_from_file(self, file_path):
        with open(file_path, 'r') as file:
            return [line.strip() for line in file]

    async def fetch_data(self, session, url):
        async with session.get(url) as response:
            return await response.text()

    async def scrape(self, url):
        async with aiohttp.ClientSession() as session:
            html = await self.fetch_data(session, url)
            # Здесь можно добавить код для обработки HTML-страницы

    async def run(self):
        tasks = []
        for url in self.urls:
            tasks.append(self.scrape(url))
        await asyncio.gather(*tasks)

if __name__ == "__main__":
    scraper = WebScraper()
    asyncio.run(scraper.run())
```

Этот код создает класс `WebScraper`, который загружает список URL-адресов из файла `urls.txt` и выполняет асинхронный скрапинг каждой страницы. В функции `scrape`, вы можете добавить код для обработки HTML-страницы.

Файл `urls.txt` содержит URL-адреса страниц, которые нужно скрапить.

## 1.6

Для улучшения асинхронного веб-скрапера из предыдущей задачи, вы можете преобразовать его в класс асинхронного менеджера контекста и использовать `async with` внутри асинхронной функции `main`. Это позволит более гибко и элегантно управлять ресурсами и выполнением веб-скрапинга. Вот улучшенный код:

```python
import aiohttp
import asyncio

class WebScraper:
    def __init__(self, file_path):
        self.file_path = file_path

    async def __aenter__(self):
        self.session = aiohttp.ClientSession()
        return self

    async def __aexit__(self, exc_type, exc, tb):
        await self.session.close()

    async def fetch_data(self, url):
        async with self.session.get(url) as response:
            return await response.text()

    async def scrape(self, url):
        html = await self.fetch_data(url)
        # Здесь можно добавить код для обработки HTML-страницы

    async def run(self):
        urls = self.load_urls_from_file(self.file_path)
        tasks = []
        for url in urls:
            tasks.append(self.scrape(url))
        await asyncio.gather(*tasks)

    def load_urls_from_file(self, file_path):
        with open(file_path, 'r') as file:
            return [line.strip() for line in file]

async def main():
    async with WebScraper("urls.txt") as scraper:
        await scraper.run()

if __name__ == "__main__":
    asyncio.run(main())
```

В этом коде `WebScraper` становится асинхронным менеджером контекста с методами `__aenter__` и `__aexit__`. Теперь `async with` используется внутри асинхронной функции `main`, что делает код более читаемым и управляемым. Когда `async with` завершает выполнение, менеджер контекста автоматически закрывает сеанс aiohttp.

## 1.7

Для создания сервера и клиента, использующих библиотеку asyncio и HTTPS протокол для отправки текстовых JSON сообщений, вы можете воспользоваться следующими примерами кода для сервера и клиента.

Сервер:

```python
import asyncio
import websockets
import json

async def echo_server(websocket, path):
    async for message in websocket:
        try:
            data = json.loads(message)
            print(f"Сообщение от клиента: {data}")
            await websocket.send(json.dumps(data))  # Отправляем обратно клиенту
        except json.JSONDecodeError:
            print(f"Ошибка в формате JSON: {message}")

if __name__ == "__main__":
    start_server = websockets.serve(echo_server, "localhost", 8765)

    asyncio.get_event_loop().run_until_complete(start_server)
    asyncio.get_event_loop().run_forever()
```

Клиент:

```python
import asyncio
import websockets
import json

async def echo_client():
    async with websockets.connect("wss://localhost:8765") as websocket:
        while True:
            message = input("Введите сообщение: ")
            if message.lower() == 'exit':
                break

            await websocket.send(json.dumps({"message": message}))
            response = await websocket.recv()
            data = json.loads(response)
            print(f"Ответ от сервера: {data}")

if __name__ == "__main__":
    asyncio.get_event_loop().run_until_complete(echo_client())
```

В этом коде сервер и клиент обмениваются JSON сообщениями, и сервер отправляет обратно клиенту то же сообщение, что было получено. Клиент может отправлять сообщения с клавиатуры, и сервер будет отвечать на них.

Запустите сервер и клиент в разных консольных окнах, и они смогут обмениваться текстовыми сообщениями в режиме "echo".

Код также продублирован в онлайн-среде разработки, Replit:

URL: [Лабораторная работа 3](https://replit.com/@Buryackov-Ivan/)



