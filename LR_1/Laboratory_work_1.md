## Лабораторная Работа 1. Численное интегрирование

**Задание выполнил:** Буряков Иван Олегович ИВТ(1)

### Задание: 
Реализуйте интегрирование, используя последовательные вычисления в основном потоке.
Формулировка задания указана по ссылке: [https://replit.com/@DmitryVlasov3/programming-07-theme01-lr01#main.py](https://replit.com/@DmitryVlasov3/programming-07-theme01-lr01#main.py)

Сделайте форк данного борда, напишите код, позволяющий решить задачу и представьте ссылку в качестве ответа на данное задание.
~~~
# Написание программы для численного интегрирования площади под кривой.
def integrate(f, a, b, *, n_iter=1000):
    print(n_iter)

# или можно так:
def integrate2(f, a, b, n_iter=1000):
    print(n_iter)
~~~

Для вызова функций в первом и во втором случаях доступны следующие способы:

~~~
integrate(math.sin, 0, 1, n_iter=100) 
# иначе аргумент n_iter не передать, неявно (как ниже) - не получится
~~~
~~~
integrate2(math.cos, 0, 1, 100) # или integrate2(math.cos, 0, 1, n_iter=100)
~~~
Оценить скорость выполнения интегрирования с помощью модуля timeit при количестве итерацией n_iter = 10**4, 10**5, 10**6 для какой-либо функции (в качестве функции предлагается sin, cos, или tg).

Для борда выше:

Существует два способа вызвать timeit для оценки времени для n_iter=1000 собственно внутри кода:
~~~
timeit.timeit("integrate(lambda x: x+1, 0, 1, n_iter=1000)",
 setup="from integrate import integrate")
~~~
или из командной строки:
~~~
>>>python -m timeit -s "from integrate import integrate" 
                      "integrate(lambda x: x+1, 0, 1, n_iter=1000)"
~~~
Для импорта нескольких библиотек после ключа -s указываем импорты через ";" т.е. например, для импорта функции sin -s "from integrate import integrate; from math import sin"

Если будете использовать Jupyter Notebook:
~~~
%%timeit -n100

integrate(math.atan, 0, math.pi / 2, n_iter=10**5)

integrate(math.atan, 0, math.pi / 2, n_iter=10**6)
~~~
Если будете делать это в обычной среде, то см [документацию](https://docs.python.org/3/library/timeit.html#python-interface). 

Например, можно в режиме REPL или из командной строки (см. [примеры](https://docs.python.org/3/library/timeit.html#examples)):
~~~
>>> import timeit
>>> timeit.timeit('"-".join(str(n) for n in range(100))', number=10000)
~~~
Реализуйте интегрирование, используя Thread, Lock на списке потоков, который сформирован вручную.

Реальзуйте численной интегрирование, создав список потоков, каждый из которых интегрирует (производит суммирование) функции на заданном числе подинтервалов. Используйте созданный вручную список потоков и объект типа Lock для доступа на запись к переменной, содержащей интегральную сумму. Замерьте время этих вычислений с помощью модуля timeit.
Опишите результаты в отчете. Сравните время последовательных вычислений в одном главном потоке с могопоточным решением.


### Решение:
Интегрирование с использованием последовательных вычислений в основном потоке можно реализовать следующим образом:

```python
def integrate(f, a, b, *, n_iter=1000):
    dx = (b - a) / n_iter  # вычисляем шаг интегрирования
    total_area = 0

    for i in range(n_iter):
        x = a + i * dx
        area = f(x) * dx  # вычисляем элементарную площадь
        total_area += area

    return total_area

# Пример использования:
import math

result = integrate(math.sin, 0, 1, n_iter=1000)
print(result)
```

Для многопоточного решения с использованием объекта Lock и списка потоков можно воспользоваться следующим кодом:

```python
import threading

def integrate_thread(f, a, b, n_iter=1000):
    dx = (b - a) / n_iter  # вычисляем шаг интегрирования
    total_area = 0
    lock = threading.Lock()

    def integrate_range(start, end):
        nonlocal total_area
        
        area = 0
        for i in range(start, end):
            x = a + i * dx
            area += f(x) * dx
        
        with lock:
            total_area += area

    num_threads = 4  # количество потоков (можно изменить)

    threads = []
    for i in range(num_threads):
        start = int((n_iter / num_threads) * i)
        end = int((n_iter / num_threads) * (i + 1))
        thread = threading.Thread(target=integrate_range, args=(start, end))
        threads.append(thread)
        thread.start()

    for thread in threads:
        thread.join()

    return total_area

# Пример использования:
import math

result = integrate_thread(math.sin, 0, 1, n_iter=1000)
print(result)
```

Для сравнения времени выполнения можно воспользоваться модулем `timeit`. Например, для оценки времени выполнения функции `integrate` с параметром `n_iter=10**4`, `n_iter=10**5`, `n_iter=10**6` для функции `math.sin` можно выполнить следующий код:

```python
import math
import timeit

time_10_4 = timeit.timeit("integrate(math.sin, 0, 1, n_iter=10**4)",
                          setup="from __main__ import integrate, math", number=10)
time_10_5 = timeit.timeit("integrate(math.sin, 0, 1, n_iter=10**5)",
                          setup="from __main__ import integrate, math", number=10)
time_10_6 = timeit.timeit("integrate(math.sin, 0, 1, n_iter=10**6)",
                          setup="from __main__ import integrate, math", number=10)

print(f"Time for n_iter=10^4: {time_10_4}")
print(f"Time for n_iter=10^5: {time_10_5}")
print(f"Time for n_iter=10^6: {time_10_6}")
```

Аналогично, можно оценить время выполнения функции `integrate_thread`:

```python
import math
import timeit

time_10_4 = timeit.timeit("integrate_thread(math.sin, 0, 1, n_iter=10**4)",
                          setup="from __main__ import integrate_thread, math", number=10)
time_10_5 = timeit.timeit("integrate_thread(math.sin, 0, 1, n_iter=10**5)",
                          setup="from __main__ import integrate_thread, math", number=10)
time_10_6 = timeit.timeit("integrate_thread(math.sin, 0, 1, n_iter=10**6)",
                          setup="from __main__ import integrate_thread, math", number=10)

print(f"Time for n_iter=10^4: {time_10_4}")
print(f"Time for n_iter=10^5: {time_10_5}")
print(f"Time for n_iter=10^6: {time_10_6}")
```

Замеры времени позволят сравнить скорость выполнения интегрирования с последовательными вычислениями и многопоточным решением.

Код также продублирован в онлайн-среде разработки, Replit:

URL: [Лабораторная работа 1](https://replit.com/@Buryackov-Ivan/)

