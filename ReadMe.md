# Проверка ряда на ортонормированность
Так как ряд бесконечен мы не можем проверить все элементы в нем.  
Но, мы знаем что все элементы кроме первого можно представить через 2 и 3 элементы  
Каждый нечетный элемент, начиная с 5, представляется как F3(k*x), где k - целое число  
Каждый четный элемент, начиная с 4, представляется как F2(k*x), где k - целое число  
Ряд будет ортонормальным, если  
* $$\int_{a}^{b} F_1(x)F_2(x) dx = 0$$
* $$\int_{a}^{b} F_1(x)F_3(x) dx = 0$$
* $$\int_{a}^{b} F_2(x)F_3(x) dx = 0$$
* $$\int_{a}^{b} F_1(x)F_1(x) dx = 1$$
* $$\int_{a}^{b} F_2(x)F_2(x) dx = 1$$
* $$\int_{a}^{b} F_3(x)F_3(x) dx = 1$$  
Для автоматизации решения данной задачи было написано 4 программы. Одна программа на c++, две на python и одна на javascript. 
Программа на c++ не использует никаких дополнительных библиотек. Только math и iostream.  
Листинг программы на c++

```c++
// main.cpp
#include <iostream>
#include <math.h>

/*
    Метод симпсона. Программа принимает 4 параметра.
    double a - нижняя граница интегрирования
    double b - верхняя граница интегрирования
    int n - точность интегрирования
    T&& f - шаблонный параметр для передачи функций или лямда функций
    с математическими функциями ряда
*/
template <class T>
double CalulateIntegral(double a, double b, int n, T&& f)
{
    // С помощью побитового сдвига зануляем самый правый бит числа, благодаря чему мы делаем число четным
    n >>= 1;
    n <<= 1;

    // Шаг интегрирования по точкам
    double h = (b - a) / n;
    // Сумма значений функции в начале и в конце отрезков интегрирования. Необходима в формуле Симпсона
    double summ = f(a) + f(b);
    
    // Добавляем шаг интегрирования к началу
    a += h;

    // Считаем интеграл по формуле Симпсона
    for (int i = 1; i < n - 1; i += 2)
    {
        summ += 4 * f(a);
        a += h;
        summ += 2 * f(a);
        a += h;
    }
    
    // Считаем интеграл 
    summ += 4 * f(a);
    summ *= h / 3;

    return summ;
}

// Функция для задания первой функции ряда
double F1(double x)
{
    return 1 / sqrt(2*M_PI);
}

// Функция для задания второй функции ряда
double F2(double x)
{
    return cos(x) / sqrt(M_PI);
}

// Функция для задания третий функции ряда
double F3(double x)
{
    return sin(x) / sqrt(M_PI);
}

int main()
{
    // Количество знаков после запятой
    const int N = 1000000;

    std::cout << "Нули" << std::endl;

    double summ = CalulateIntegral(0, 2*M_PI, 100, [](double x){ return F1(x)*F2(x); });
    std::cout << round(summ * N) / N << std::endl;

    summ = CalulateIntegral(0, 2*M_PI, 100, [](double x){ return F1(x)*F3(x); });
    std::cout << round(summ * N) / N << std::endl;

    summ = CalulateIntegral(0, 2*M_PI, 100, [](double x){ return F2(x)*F3(x); });
    std::cout << round(summ * N) / N << std::endl;

    std::cout << "Единицы" << std::endl;

    summ = CalulateIntegral(0, 2*M_PI, 100, [](double x){ return F1(x)*F1(x); });
    std::cout << round(summ * N) / N << std::endl;

    summ = CalulateIntegral(0, 2*M_PI, 100, [](double x){ return F2(x)*F2(x); });
    std::cout << round(summ * N) / N << std::endl;

    summ = CalulateIntegral(0, 2*M_PI, 100, [](double x){ return F3(x)*F3(x); });
    std::cout << round(summ * N) / N << std::endl;
}
```

Для сборки кода с помощью MinGW или GCC нужно выполнить команду 
> g++ main.cpp  

Для проверки ряда на ортонормальность можно заменить математические функии в F1, F2, F3 на функции ряда. 
Вывод программы для ортонормального ряда:
> Нули  
> 0  
> 0  
> 0  
> Единицы  
> 1  
> 1  
> 1  

Если ряд не ортонормальный, то вывод будет оличаться.  
Две программы на python отличаются только методом интегрирования. Если первая программа для интегрирования использует самописный метод симпсона, то вторая программа для интегрирования использует встроенный метод scipy.

```py
# main1.py
import math

# Метод Симпсона для вычисления интеграла
def calculate_integral(a, b, n, f):
    # Уменьшаем n на 1 и умножаем на 2, чтобы обеспечить четное значение n
    n >>= 1
    n <<= 1

    # Вычисляем шаг h
    h = (b - a) / n
    summ = f(a) + f(b)  # Инициализируем сумму начальными значениями в точках a и b

    a += h

    # Используем метод Симпсона для вычисления интеграла
    for i in range(1, n - 1, 2):
        summ += 4 * f(a)
        a += h
        summ += 2 * f(a)
        a += h
    else:
        summ += 4 * f(a)
        summ *= h / 3

    return summ

# Определение функций, которые будут интегрироваться
def f1(x):
    return 1 / math.sqrt(2 * math.pi)

def f2(x):
    return math.cos(x) / math.sqrt(math.pi)

def f3(x):
    return math.sin(x) / math.sqrt(math.pi)

print("zeroz")

summ = calculate_integral(0, 2 * math.pi, 100, lambda x: f1(x) * f2(x))
print(summ)

summ = calculate_integral(0, 2 * math.pi, 100, lambda x: f1(x) * f3(x))
print(summ)

summ = calculate_integral(0, 2 * math.pi, 100, lambda x: f2(x) * f3(x))
print(summ)

print("ones")

summ = calculate_integral(0, 2 * math.pi, 100, lambda x: f1(x) * f1(x))
print(summ)

summ = calculate_integral(0, 2 * math.pi, 100, lambda x: f2(x) * f2(x))
print(summ)

summ = calculate_integral(0, 2 * math.pi, 100, lambda x: f3(x) * f3(x))
print(summ)
```

```py
# main2.py
from scipy import integrate
import math

def F1(x):
    return 1 / math.sqrt(2 * math.pi)

def F2(x):
    return math.cos(x) / math.sqrt(math.pi)

def F3(x):
    return math.sin(x) / math.sqrt(math.pi)

print("zeroes")

print(round(integrate.quad(lambda x: F1(x) * F2(x), 0, 2 * math.pi)[0]))
print(round(integrate.quad(lambda x: F1(x) * F3(x), 0, 2 * math.pi)[0]))
print(round(integrate.quad(lambda x: F2(x) * F3(x), 0, 2 * math.pi)[0]))
print("ones")
print(round(integrate.quad(lambda x: F1(x) * F1(x), 0, 2 * math.pi)[0]))
print(round(integrate.quad(lambda x: F2(x) * F2(x), 0, 2 * math.pi)[0]))
print(round(integrate.quad(lambda x: F3(x) * F3(x), 0, 2 * math.pi)[0]))
```

В программе на js используется самописный метод интегрирования, так как исследование 
готовых методов не представляет интереса.

```js
// index.js
// Simpson method
function calculateIntegral(a, b, n, f) {
    n >>= 1;
    n <<= 1;

    const h = (b - a) / n;
    let summ = f(a) + f(b);

    a += h;

    for (let i = 1; i < n - 1; i += 2) {
        summ += 4 * f(a);
        a += h;
        summ += 2 * f(a);
        a += h;
    }

    summ += 4 * f(a);
    summ *= h / 3;

    return summ;
}

function F1(x) {
    return 1 / Math.sqrt(2 * Math.PI);
}

function F2(x) {
    return Math.cos(x) / Math.sqrt(Math.PI);
}

function F3(x) {
    return Math.sin(x) / Math.sqrt(Math.PI);
}

console.log("zeroz");

let summ = calculateIntegral(0, 2 * Math.PI, 100, (x) => F1(x) * F2(x));
console.log(summ);

summ = calculateIntegral(0, 2 * Math.PI, 100, (x) => F1(x) * F3(x));
console.log(summ);

summ = calculateIntegral(0, 2 * Math.PI, 100, (x) => F2(x) * F3(x));
console.log(summ);

console.log("ones");

summ = calculateIntegral(0, 2 * Math.PI, 100, (x) => F1(x) * F1(x));
console.log(summ);

summ = calculateIntegral(0, 2 * Math.PI, 100, (x) => F2(x) * F2(x));
console.log(summ);

summ = calculateIntegral(0, 2 * Math.PI, 100, (x) => F3(x) * F3(x));
console.log(summ);
```

Код на javascript тестировал с помощью браузера и node.js сервера. 

Сводная таблица измерений скорости работы. Время в миллисекундах
К сожалению, нам не известна точность интегрирования в библиотеке scipy, для тестов мы использовали 1000 точек на функцию. 

|        |main.cpp|main.cpp O2|main.cpp O3 1000 точек на функцию|main.cpp O3 500 точек на функцию|main1.py|main2.py|index.js browser|index.js node.js|
|--------|--------|-----------|---------------------------------|--------------------------------|--------|--------|-----|-----|
|1000    |33      |20         |17                               |8                               |323     |9       |32     |30  |
|100000  |1848    |1311       |1291                             |636                             |32846   |824     |3340    |3100 |
|10000000|181773  |126903     |122978                           |65751                           |3227412 |85205   |354403 |320561| 

Для корректного сравнения производительности программ нужно точно понимать как работает встроенная в scipy функция интегрирования
и какую точность интегрирования обеспечивает эта функция. Очевидно что, писать свои реализации на python не целесообразно, в тоже
время реализация программ на c++ имеют место быть, так как скорость будет сопоставима с готовыми решениями, но при этом у нас будет полный
контроль над соотношением между точностью вычеслений и скоростью работы программы.  
В то же время использование самописной функции на сайте вполне допустимо, хотя сервер будет считать тот же самый интеграл быстрее. Однако, может быть так, что посчитать интеграл на севрере и отправить клиенту будет быстрее чем считать непосредственно на клиентском устройстве.
