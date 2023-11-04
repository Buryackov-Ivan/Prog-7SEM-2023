## Лабораторная Работа 2. Потоки в Python

**Задание выполнил:** Буряков Иван Олегович ИВТ(1)

### Задание: 
Комплект 1: Потоки в Python.

1.1: Напишите программу, которая создаёт несколько потоков, а затем выводит их имена изнутри каждого потока.

1.2: Напишите программу для одновременной загрузки нескольких файлов (например картинок) с использованием потоков. Используйте для скачивания одну из библиотек urllib, requests или wget.

1.3: Напишите программу на Python, которая выполняет одновременные HTTP­запросы с использованием потоков. Используйте библиотеку requests.

1.4: Напишите программу для вычисления факториала числа с использованием нескольких потоков.

1.5: Напишите программу для реализации многопоточного алгоритма быстрой сортировки.

Комплект 2: Concurrency и Futures в Python.

2.1: Дополните решение из лабораторной работы № 1 с кодом функции integrate следующим кодом, расположенном по этой ссылке (https://replit.com/@zhukov/sem7-task3#main.py) или по этой ссылке (https://replit.com/@zhukov/prog7-t1-lr2#main.py) и также проведите замеры времени вычисления для аналогичных параметров модуля timeit для кратного числа потоков и процессов (2, 4, 6). Замеры вычислений — только для количества итераций n_iter = 10∗∗6:
integrate(math.atan, 0, math.pi / 2, n_iter=10∗∗6) 
Дополните этой информацией отчёт. Количество повторений / repeat — 100, единицы измерения — msec. Ссылку на борд с кодом с комментариями на Python в repl.it приведите как ответ на это задание.

2.2: Напишите программу, которая будет симулировать банк с использованием потоков и объектов типа Lock. Реализуйте методы deposit и withdraw, которые будут добавлять и снимать деньги со счета клиента соответственно. Гарантируйте, что доступ к счету будет синхронизирован с помощью объекта типа Lock.

2.3: Напишите программу, используя объекты типа Future, чтобы асинхронно скачать несколько изображений с Интернета. Каждое изображение должно быть загружено в отдельный поток и сохранено на диск. Используйте семафор, чтобы ограничить количество одновременно выполняющихся потоков загрузки, чтобы избежать блокировки по IP от сервера или серверов.

2.4: Создайте два потока, один из которых будет записывать данные в файл, а другой ­ считывать данные с файла. Используйте объекты типа Future, чтобы синхронизировать потоки и избежать гонки потоков.

2.5: Напишите программу, которая создает 3 потока. Первый поток должен устанавливать состояние объекта типа Event каждую секунду. Второй поток должен ждать наступления события и выводить сообщение "Event occurred". Третий поток должен выводить сообщение "Event did not occur" каждую секунду, до тех пор, пока не наступит событие. Как только событие происходит, третий поток должен остановиться.
2.6: Создайте программу, которая имеет свой класс "очередь" (Queue), у которого есть методы для добавления элементов в очередь и удаления элементов из очереди. Используйте рекурсивный блокировщик RLock для обеспечения безопасности доступа к очереди из нескольких потоков.

2.7: Сделайте два потока, представляющие сервер и клиент, где клиентский поток будет ждать готовности сервера, прежде чем отправлять ему какой­либо запрос. Используйте threading.Barrier

2.8: Написать программу для параллельного поиска файла в директории.
Каждый поток должен обрабатывать свой фрагмент файлов директории и синхронизироваться с другими потоками, чтобы убедиться, что файл найден только один раз. Как только первый файл по его шаблону найден ­ все потоки поиска завершаются.


___________________________________________
# Решение:
___________________________________________

## Комплект 1: Потоки в Python.

## 1.1
Конечная реализация программы может варьироваться в зависимости от используемого языка программирования. Ниже приведен пример реализации на языке Python с использованием модуля threading:

```python
import threading

def print_thread_name():
    thread_name = threading.current_thread().name
    print("Thread name:", thread_name)

# Создаем несколько потоков
threads = []
for i in range(5):
    thread = threading.Thread(target=print_thread_name)
    threads.append(thread)
    thread.start()

# Ожидаем завершения всех потоков
for thread in threads:
    thread.join()
```

В этом примере мы определяем функцию `print_thread_name`, которая выводит имя текущего потока, используя `threading.current_thread().name`. Затем мы создаем несколько потоков, каждый вызывая функцию `print_thread_name`. После этого мы запускаем каждый поток с помощью метода `start()` и ожидаем их завершения с помощью метода `join()`. В результате на экране будут выведены имена всех потоков.

## 1.2

Ниже приведен пример программы на языке Python для одновременной загрузки нескольких файлов с использованием потоков и библиотеки requests:

```python
import threading
import requests

def download_file(url, filename):
    response = requests.get(url)
    with open(filename, 'wb') as file:
        file.write(response.content)
    print(f"Downloaded {filename}")

# URL-адреса файлов для загрузки
urls = [
    "https://example.com/image1.jpg",
    "https://example.com/image2.jpg",
    "https://example.com/image3.jpg"
]

# Создаем потоки для загрузки каждого файла
threads = []
for i, url in enumerate(urls):
    filename = f"image{i+1}.jpg"
    thread = threading.Thread(target=download_file, args=(url, filename))
    threads.append(thread)
    thread.start()

# Ожидаем завершения всех потоков
for thread in threads:
    thread.join()
```

В этом примере мы определяем функцию `download_file`, которая использует библиотеку requests для загрузки файла по заданному URL-адресу и сохраняет его под указанным именем. Затем мы создаем отдельный поток для загрузки каждого файла, и каждый поток вызывает функцию `download_file` с соответствующим URL-адресом и именем файла. После запуска всех потоков мы ожидаем их завершения с помощью метода `join()`, чтобы программа не завершилась до того, как все файлы будут загружены.

Примечание: В приведенном примере используется библиотека `requests`, однако вы также можете использовать модуль `urllib` или другие подходящие библиотеки для загрузки файлов в своем коде.

## 1.3

Ниже приведен пример программы на языке Python, который выполняет одновременные HTTP-запросы с использованием потоков и библиотеки requests:

```python
import threading
import requests

def make_request(url):
    response = requests.get(url)
    print(f"Response from {url}: {response.status_code}")

# Список URL-адресов для запросов
urls = [
    "https://example.com",
    "https://google.com",
    "https://openai.com"
]

# Создаем потоки для каждого запроса
threads = []
for url in urls:
    thread = threading.Thread(target=make_request, args=(url,))
    threads.append(thread)
    thread.start()

# Ожидаем завершения всех потоков
for thread in threads:
    thread.join()
```

В этом примере мы определяем функцию `make_request`, которая выполняет GET-запрос к заданному URL-адресу с использованием библиотеки requests и выводит статус-код ответа. Затем мы создаем потоки для каждого URL-адреса, и каждый поток вызывает функцию `make_request` с соответствующим URL-адресом. После запуска всех потоков мы ожидаем их завершения с помощью метода `join()`, чтобы программа не завершилась до того, как все запросы будут выполнены.

Примечание: В приведенном примере используется библиотека `requests`, однако вы также можете использовать другие подходящие библиотеки для выполнения HTTP-запросов в своем коде.

## 1.4

Ниже приведен пример программы на языке Python, которая вычисляет факториал числа с использованием нескольких потоков:

```python
import threading

def factorial(n):
    if n <= 1:
        return 1
    else:
        return n * factorial(n - 1)

def calculate_factorial(n):
    result = factorial(n)
    print(f"Factorial of {n} is {result}")

# Ввод числа для вычисления факториала
num = int(input("Enter a number: "))

# Создаем поток для вычисления факториала
thread = threading.Thread(target=calculate_factorial, args=(num,))
thread.start()

# Ожидаем завершения потока
thread.join()
```

В этом примере мы определяем функцию `factorial`, которая рекурсивно вычисляет факториал числа. Затем у нас есть функция `calculate_factorial`, которая вызывает функцию `factorial` с заданным числом и выводит результат на экран.

Мы создаем поток, используя функцию `threading.Thread`. При создании потока мы указываем целевую функцию `calculate_factorial` и передаем аргумент с числом для вычисления факториала. Затем мы запускаем поток с помощью метода `start()`. После этого основной поток ожидает завершения созданного потока с помощью метода `join()`, чтобы программа не завершилась до того, как будет вычислен факториал.

## 1.5

Ниже представлен пример реализации многопоточного алгоритма быстрой сортировки на языке Python с использованием модуля threading:

```python
import threading

def quicksort(arr):
    if len(arr) <= 1:
        return arr
    
    pivot = arr[len(arr) // 2]
    smaller, equal, larger = [], [], []
    
    for num in arr:
        if num < pivot:
            smaller.append(num)
        elif num == pivot:
            equal.append(num)
        else:
            larger.append(num)
    
    # Создаем потоки для рекурсивной сортировки меньших и больших элементов
    smaller_thread = threading.Thread(target=quicksort, args=(smaller,))
    larger_thread = threading.Thread(target=quicksort, args=(larger,))
    
    # Запускаем потоки
    smaller_thread.start()
    larger_thread.start()
    
    # Ожидаем завершения потоков
    smaller_thread.join()
    larger_thread.join()
    
    return smaller + equal + larger

# Пример использования
array = [9, 4, 7, 2, 1, 5, 3, 6, 8]
sorted_array = quicksort(array)
print(sorted_array)
```

В этом примере функция `quicksort` рекурсивно разделяет массив на три части: элементы меньше опорного элемента `smaller`, элементы равные опорному элементу `equal` и элементы больше опорного элемента `larger`. Затем создаются два отдельных потока для сортировки меньших и больших частей массива, соответственно. Потоки запускаются и ждут завершения с помощью методов `start()` и `join()`. В конце функция объединяет отсортированную меньшую часть, равные элементы и отсортированную большую часть, и возвращает отсортированный массив.

Важно отметить, что в данном примере на практике многопоточная реализация быстрой сортировки не всегда будет эффективнее однопоточной реализации из-за переключения контекста между потоками и возможной потери производительности. Тем не менее, этот пример демонстрирует принцип работы многопоточного алгоритма быстрой сортировки.

## Комплект 2: Concurrency и Futures в Python.

## 2.2

Ниже приведен пример программы, которая симулирует банк с использованием потоков и объектов типа Lock для синхронизации доступа к счету клиента:

```python
import threading

class BankAccount:
    def __init__(self):
        self.balance = 0
        self.lock = threading.Lock()

    def deposit(self, amount):
        with self.lock:
            self.balance += amount

    def withdraw(self, amount):
        with self.lock:
            if self.balance >= amount:
                self.balance -= amount
                return True
            else:
                return False


def make_deposit(account, amount):
    account.deposit(amount)
    print(f"Deposited {amount} dollars")


def make_withdrawal(account, amount):
    result = account.withdraw(amount)
    if result:
        print(f"Withdrew {amount} dollars")
    else:
        print("Insufficient funds")


# Пример использования:
account = BankAccount()

deposit_thread = threading.Thread(target=make_deposit, args=(account, 100))
withdraw_thread = threading.Thread(target=make_withdrawal, args=(account, 50))

deposit_thread.start()
withdraw_thread.start()

deposit_thread.join()
withdraw_thread.join()

print(f"Current balance: {account.balance} dollars")
```

В данном примере создается класс BankAccount с методами `deposit` и `withdraw`, которые манипулируют балансом счета клиента. Метод `deposit` увеличивает баланс на указанную сумму, а метод `withdraw` уменьшает баланс на указанную сумму только в том случае, если на счету достаточно средств.

Затем создаются два потока: один для внесения депозита на счет, а другой для снятия денег. Оба потока вызывают соответствующие методы на объекте BankAccount, передавая ему сумму операции.

Для обеспечения синхронизации доступа к счету используется объект Lock. Каждый метод `deposit` и `withdraw` выполняется в блоке с оператором with, который обращается к Lock для захвата блокировки перед выполнением операции, а затем освобождает блокировку после завершения операции.

После выполнения потоков выводится текущий баланс счета. Обратите внимание, что вывод баланса на экран происходит после завершения обоих потоков, чтобы гарантировать корректный результат интерфейсом print().

## 2.3

Ниже приведен пример программы, которая использует объекты типа `Future` и семафор для асинхронной загрузки и сохранения нескольких изображений с Интернета:

```python
import concurrent.futures
import requests

MAX_CONCURRENT_DOWNLOADS = 2  # Максимальное количество одновременно выполняющихся потоков загрузки
IMAGE_URLS = [
    "https://example.com/image1.jpg",
    "https://example.com/image2.jpg",
    "https://example.com/image3.jpg",
    "https://example.com/image4.jpg"
]

def download_image(url, semaphore):
    with semaphore:
        response = requests.get(url)
        if response.status_code == 200:
            filename = url.split("/")[-1]
            with open(filename, "wb") as file:
                file.write(response.content)
            return f"Downloaded {filename}"
        else:
            return f"Failed to download {url}"

# Создание семафора с указанным количеством разрешений
semaphore = concurrent.futures.Semaphore(MAX_CONCURRENT_DOWNLOADS)

# Создание пула потоков
with concurrent.futures.ThreadPoolExecutor() as executor:
    # Создание списка объектов Future для каждого изображения
    futures = [executor.submit(download_image, url, semaphore) for url in IMAGE_URLS]

    # Получение результатов из объектов Future по мере завершения потоков
    for future in concurrent.futures.as_completed(futures):
        result = future.result()
        print(result)
```

В данном примере используется модуль `concurrent.futures`, который предоставляет класс `ThreadPoolExecutor` для создания пула потоков и объект `Semaphore` для создания семафора.

`MAX_CONCURRENT_DOWNLOADS` - это максимальное количество одновременно выполняющихся потоков загрузки, которое мы устанавливаем в 2 в данном примере.

`IMAGE_URLS` - это список URL-адресов изображений, которые мы хотим загрузить.

Функция `download_image` выполняет загрузку изображения по указанному URL-адресу. Она захватывает семафор перед выполнением запроса, чтобы ограничить количество одновременно выполняющихся потоков загрузки. Если загрузка происходит успешно, изображение сохраняется на диск.

В основной части программы создается пул потоков с помощью `ThreadPoolExecutor`. Затем создается список объектов Future, где каждый объект Future создается путем вызова `executor.submit(download_image, url, semaphore)` для каждого URL-адреса изображения. Это запускает выполнение функции `download_image` в отдельном потоке и возвращает объект Future.

Далее, используя `concurrent.futures.as_completed`, мы ожидаем завершения каждого потока и получаем результаты из объектов Future. Результат в этом примере просто выводится на экран, но вы можете изменить код, чтобы обрабатывать результаты по вашему усмотрению.

## 2.4

Ниже приведен пример программы, которая создает два потока: один поток записывает данные в файл, а другой поток считывает данные из файла. Объекты типа `Future` используются для синхронизации потоков и избегания гонки потоков.

```python
import concurrent.futures

# Функция для записи данных в файл
def write_data(filename, data):
    with open(filename, "w") as file:
        file.write(data)
        return f"Data written to {filename}"

# Функция для чтения данных из файла
def read_data(filename):
    with open(filename, "r") as file:
        data = file.read()
        return f"Data read from {filename}: {data}"

# Создание пула потоков
with concurrent.futures.ThreadPoolExecutor() as executor:
    # Запись данных в файл
    write_future = executor.submit(write_data, "data.txt", "Hello, World!")

    # Чтение данных из файла
    read_future = executor.submit(read_data, "data.txt")

    # Ожидание завершения обоих потоков и получение результатов
    write_result = write_future.result()
    read_result = read_future.result()

    print(write_result)
    print(read_result)
```

В данном примере используется модуль `concurrent.futures`, который предоставляет класс `ThreadPoolExecutor` для создания пула потоков.

Функция `write_data` открывает файл в режиме записи и записывает данные в файл.

Функция `read_data` открывает файл в режиме чтения и считывает данные из файла.

В основной части программы создается пул потоков с помощью `ThreadPoolExecutor`. Затем используя `executor.submit()`, мы запускаем выполнение функций `write_data` и `read_data` в отдельных потоках и получаем объекты `Future` для каждого потока.

Далее, используя метод `result()` для каждого объекта `Future`, мы ожидаем завершения каждого потока и получаем результаты. Результаты выводятся на экран.

## 2.5

Ниже приведен пример программы, которая создает 3 потока с использованием объекта типа `Event` для синхронизации. Первый поток устанавливает состояние события каждую секунду, второй поток ожидает наступления события и выводит сообщение, а третий поток проверяет, произошло ли событие и выводит соответствующее сообщение:

```python
import threading
import time

# Создание объекта типа Event
event = threading.Event()

# Функция для установки состояния события каждую секунду
def set_event():
    while True:
        time.sleep(1)
        event.set()

# Функция для ожидания события и вывода сообщения
def wait_event():
    event.wait()
    print("Event occurred")

# Функция для вывода сообщения "Event did not occur"
def check_event():
    while not event.is_set():
        print("Event did not occur")
        time.sleep(1)

# Создание потоков
set_event_thread = threading.Thread(target=set_event)
wait_event_thread = threading.Thread(target=wait_event)
check_event_thread = threading.Thread(target=check_event)

# Запуск потоков
set_event_thread.start()
wait_event_thread.start()
check_event_thread.start()
```

В данном примере используются функции `threading.Event()`, `event.set()`, `event.wait()` и `event.is_set()` для контроля состояния события.

Функция `set_event()` устанавливает состояние события каждую секунду, вызывая `event.set()`.

Функция `wait_event()` ожидает наступления события, вызывая `event.wait()`, и выводит сообщение "Event occurred" после наступления события.

Функция `check_event()` проверяет состояние события с помощью `event.is_set()`. Если событие не произошло, выводится сообщение "Event did not occur" каждую секунду.

Затем создаются и запускаются три потока: первый поток вызывает функцию `set_event()`, второй поток вызывает функцию `wait_event()`, а третий поток вызывает функцию `check_event()`.

## 2.6

Приведен ниже пример класса "очередь" (Queue), который использует рекурсивный блокировщик RLock для обеспечения безопасности доступа к очереди из нескольких потоков:

```python
import threading

class Queue:
    def __init__(self):
        self.queue = []
        self.lock = threading.RLock() # Создание рекурсивного блокировщика

    def enqueue(self, item):
        with self.lock:  # Захватываем блокировку перед добавлением элемента
            self.queue.append(item)

    def dequeue(self):
        with self.lock:  # Захватываем блокировку перед удалением элемента
            if not self.is_empty():
                return self.queue.pop(0)

    def is_empty(self):
        with self.lock:  # Захватываем блокировку для проверки пустоты очереди
            return len(self.queue) == 0
```

В этом примере класс `Queue` содержит методы `enqueue()` для добавления элемента в очередь, `dequeue()` для удаления элемента из очереди и `is_empty()` для проверки, является ли очередь пустой.

Для обеспечения безопасности доступа из нескольких потоков используется рекурсивный блокировщик `threading.RLock()`. Он позволяет одному потоку захватить блокировку несколько раз, без блокировки самого себя.

В каждом методе класса, где требуется доступ к очереди, используется конструкция `with self.lock:` для захвата блокировки перед выполнением операции. Это гарантирует, что только один поток может работать с очередью в определенный момент времени.

Обратите внимание, что в примере реализована простейшая очередь на основе списка, где элементы извлекаются в порядке добавления (FIFO). Вы можете доработать класс, добавив другие функции или изменяя способ хранения элементов в очереди в соответствии со своими потребностями.

## 2.7

Для создания двух потоков, представляющих сервер и клиент, и использования `threading.Barrier` для синхронизации между ними, вы можете воспользоваться следующим примером на языке Python:

```python
import threading

# Создаем барьер, чтобы оба потока ожидали друг друга
barrier = threading.Barrier(2)

# Функция для клиентского потока
def client_thread():
    print("Клиент: Ожидаем сервера...")
    barrier.wait()
    print("Клиент: Отправляем запрос серверу")

# Функция для серверного потока
def server_thread():
    print("Сервер: Готов к работе")
    barrier.wait()
    print("Сервер: Получен запрос от клиента и обрабатывает его")

# Создаем потоки
client = threading.Thread(target=client_thread)
server = threading.Thread(target=server_thread)

# Запускаем потоки
client.start()
server.start()

# Ждем, пока оба потока завершат выполнение
client.join()
server.join()
```

Этот код создает два потока: один представляет клиента, а другой сервер. После создания барьера с `threading.Barrier(2)`, клиентский поток вызывает `barrier.wait()` и ожидает, пока серверный поток тоже вызовет `barrier.wait()`. Только после этого оба потока продолжат выполнение.

## 2.8

Для параллельного поиска файла в директории с использованием нескольких потоков, мы можем воспользоваться модулем `concurrent.futures` для управления потоками и выполнения поиска. Вот пример программы на Python, которая выполняет это задание:

```python
import os
import concurrent.futures

# Функция для поиска файла в директории
def search_file(directory, file_name):
    for root, _, files in os.walk(directory):
        if file_name in files:
            return os.path.join(root, file_name)
    return None

# Функция для каждого потока
def thread_search(thread_id, directory, file_name):
    result = search_file(directory, file_name)
    if result:
        print(f"Поток {thread_id}: Файл найден: {result}")
        return result
    return None

if __name__ == "__main__":
    directory = "/путь/к/директории"  # Замените на путь к директории, в которой ищем файл
    file_name = "название_файла"  # Замените на имя файла, который вы ищете

    # Создаем пул потоков
    num_threads = 4  # Здесь можно установить желаемое количество потоков
    with concurrent.futures.ThreadPoolExecutor(max_workers=num_threads) as executor:
        futures = [executor.submit(thread_search, i, directory, file_name) for i in range(num_threads)]

        for future in concurrent.futures.as_completed(futures):
            result = future.result()
            if result:
                print(f"Первый найденный файл: {result}")
                executor.shutdown()
                break
```

Этот код создает несколько потоков для поиска файла в заданной директории. Первый поток, который находит файл, завершает выполнение, и остальные потоки завершаются сразу после этого. Это обеспечивает, что файл будет найден только один раз, и поиск завершится, как только файл будет обнаружен.

Код также продублирован в онлайн-среде разработки, Replit:

URL: [Лабораторная работа 2](https://replit.com/@Buryackov-Ivan/)


