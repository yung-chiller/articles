# Ликбез по линейной алгебре

**Определение**. Функция $f: \mathbb{R}^n \to \mathbb{R}^m$ называется *линейной*, если для неё выполнено

1. $f(x+y) = f(x) + f(y)$
2. $f(ax) = a f(x), \; a \in \mathbb{R}$

Примеры:

* Функция, которая «растягивает» вектор в $k$ раз: $f(x) = k x$.
* Функция, которая поворачиват вектор на плоскости на угол $\theta$.
* Функция, которая проецирует трёхмерный вектор на какую-нибудь плоскость.
* Скалярное произведение $f(x, y) = x \cdot y = \sum x_ky_k$ также линейно по обоим параметрам.

Линейная алгебра занимается изучением линейных функций. Из одних лишь двух пунктов в определении можно вывести много полезных свойств:

* Сумма линейных функций является линейной функцией.
* Композиция линейных функций $f(g(x)) = (f \circ g)(x)$ является линейной функцией.
* Сумма линейных функций коммутативна: $f+g = g+f$.
* Сумма линейных функций ассоциативна: $(f+g)+h = f+(g+h)$.
* Композция линейных функций ассоциативна: $(f \circ g) \circ h = f \circ (g \circ h) = f \circ g \circ h$.
* Композиция в общем случае не коммутативна. Пример: $f(x) = (-x_2, x_1)$ — поворот точки на плоскости на прямой угол, $g(x) = (x_1, 0)$ — проекция на $Ox$. Почти для всех точек плоскости важен порядок этих двух операций.

## Матрицы

Можно показать, что любую линейную функцию $f: \mathbb{R}^n \to \mathbb{R}^m$ можно представить в следующем виде:

$$
f(x) =
\begin{pmatrix}
a_{11} x_1 + a_{12} x_2 + \ldots + a_{1n} x_n \\
a_{21} x_1 + a_{22} x_2 + \ldots + a_{2n} x_n \\
\ldots \\
a_{m1} x_1 + a_{m2} x_2 + \ldots + a_{mn} x_n \\
\end{pmatrix}
$$

*Матрицы* ввели просто как очень компактную запись этих коэффициентов $a_{ij}$.

$$
A =
\begin{pmatrix}
a_{11} & a_{12} & \ldots & a_{1n} \\
a_{21} & a_{22} & \ldots & a_{2n} \\
\vdots & \vdots & \ddots & \vdots \\
a_{m1} & a_{m2} & \ldots & a_{mn} \\
\end{pmatrix}
$$

Каждой линейной функции $f$ из $\mathbb{R}^n$ в $\mathbb{R}^m$ соответствует какая-то матрица $A$ размера $n \times m$ (первое число это количество строк, второе — столбцов) и наоборот. Элемент на пересечении $i$-ой строки и $j$-го столбца будем обозначать $A_{ij}$. Не перепутайте.

Если вектор — это упорядоченный набор скаляров, то матрицу можно рассматривать как вектор векторов. Вектор, в частности, можно представить как матрицу, у которой одна из размерностей равна единице — тогда его называют *вектор-столбец* либо *вектор-строка*.

Напишем класс для работы с матрицами, который пока что будет просто оборачивать двумерные массивы:

```c++
struct matrix {
    int n, m;
    array<array<int>> t;

    matrix (int _n, int _m) {
        n = _n, m = _m;
        t.assign(n, array<int>(m, 0));
    }

    // сделаем себе удобное индексирование
    int[] operator[] (int k) {
        return t[k*m];
    }
}
```

Ещё есть *тензоры* — ими называют все объекты ещё более высокого порядка: векторы матриц (трёхмерный тензор), матрицы матриц (четырёхмерный тензор) и векторы матриц матриц и так далее.

![](https://pp.userapi.com/c854216/v854216517/2a73a/ARmNQyF_XIg.jpg)

У тензоров есть своя интересная алгебра, но в контекстах, с которыми с ними может столкнуться программист, никакая алгебра обычно не подразумневается, и термин используется лишь потому, что в словосочетании «многомерный массив» слишком много букв.

### Матричное умножение

Пусть линейной функции $f$ соответствует матрица $A$, а функции $g$ соответствует матрица $B$. Тогда композиции этих функций $h = f \circ g$ будет соответствовать *произведение* $C$ матриц $A$ и $B$, определяемое следующим образом:

$$
C = AB: \; C_{ij} = \sum_{i=1}^{k} A_{ik} B_{kj}
$$

Читатель может убедиться в этом, расписав, какие коэффициенты получаются, если формулы из $g$ подставить в $f$.

При перемножении матриц на белковых процессорах удобно думать так: элемент на пересечении $i$-го столбца и $j$-той строки — это скалярное произведение $i$-той строки $A$ и $j$-того столбца $B$. Заметим, что это накладывает ограничение на размерности перемножаемых матриц: если первая матрица имеет размер $n \times k$, то вторая должна иметь размер $k \times m$, то есть «средние» размерности обязательно должны совпадать.

![](https://cdn.kastatic.org/googleusercontent/rk4fR1jNJsGUfdHOc87UzuQh2zokwYDoVo3Hk1m3s6ToGDgW6KxgrsUeIj8-CJeV6cNf6WB8B6sRHt3BoGBdVY7h)

Исходное выражение для $f(x)$ теперь можно компактно записать как $f(x) = Ax$ вместо $m$ уравнений с $n$ слагаемыми в каждом.

Напишем внешнюю функцию, реализующую матричное умножение:

```c++
matrix operator* (matrix a, matrix b) {
    matrix c(a.n, b.m);
    for (int i = 0; i < a.n; i++)
        for (int j = 0; j < b.m; j++)
            for (int k = 0; k < a.m; k++)
                c[i][j] += a[i][k] * b[k][j];
    return c;
}
```

Эта реализация очень проста, но неоптимальна.

BLAS. Кэш-линии.

Транспонирование.

Кстати, наука знает и [более быстрые](https://en.wikipedia.org/wiki/Strassen_algorithm) способы перемножить матрицы, но на контестах они не нужны.

### Свойства матриц

К матрицам не нужно относиться как к табличкам, в которых стоят какие-то числа. Каждой матрице соответствует какая-то линейная функция, как-то преобразующая вектора. Центральными объектами линейной алгебры являются именно линейные функции, а не матрицы.

Благодаря этому взаимно однозначному соотношению все ранее упомянутые свойства линейных функций переносятся и на матрицы:

* Сумма матриц $A$ и $B$ тоже является матрицей: $C = A+B: \; C_{ij} = A_{ij} + B_{ij}$.
* Сумма матриц коммутативна: $A+B = B+A$.
* Сумма матриц ассоциативна: $(A+B)+C = A+(B+C)$.
* Умножение матриц ассоциативно: $(AB)C = A(BC) = ABC$.
* Умножение матриц в общем случае не коммутативно.

Есть специальная матрица $I$, которая называется *единичной* и относительно умножения ничего не делает, то есть ведёт себя как единица. У неё единицы на *главной диагонали* и нули вне неё:

$$
I_3 = \begin{pmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1 \\
\end{pmatrix}
$$

Некоторые линейные функции обратимы (например, поворот на угол), некоторые — необратимы (например, проекция). Понятие обратимости можно продолжить и на матрицы.

**Определение.** Матрица $A$ является *обратимой*, если существует матрица $A^{-1}$ такая, что $A \cdot A^{-1} = I$.

Из формулы следует, что матрица $A$ должна быть хотя бы квадратной. Обратную матрицу можно находить за $O(n^3)$ методом Гаусса, который мы рассмотрим дальше. TODO

### Примеры матриц

Матрица «увеличь всё в два раза»:

$$
\begin{pmatrix}
2 & 0 & 0 \\
0 & 2 & 0 \\
0 & 0 & 2 \\
\end{pmatrix}
$$

Матрица «поменяй $x$ и $y$ местами»:

$$
\begin{pmatrix}
0 & 1 \\
1 & 0 \\
\end{pmatrix}
$$

Матрица поворота на угол $\alpha$ на плоскости:

$$
\begin{pmatrix}
\cos \alpha & -\sin \alpha \\
\sin \alpha & \cos \alpha \\
\end{pmatrix}
$$

Матрица проецирования на плоскость $xy$ в трёхмерном пространстве:

$$
\begin{pmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 0 \\
\end{pmatrix}
$$

## Применения вне геометрии

Пока что все примеры были про геометрию. Так просто проще думать: представлять в уме повороты и растяжения векторов на плоскости легче, чем рассуждать о свойствах абстрактных *n*-мерных пространств.

Однако мы программисты, и нас в основном интересуют задачи вне геометрии.

### Динамическое программирование

Переходы в некоторых динамиках иногда можно выразить в терминах матричного умножения.

Рассмотирм конкретный пример. Пусть у нас есть ориентированный граф $G$, заданный своей матрицей смежности, и нам нужно найти количество путей из $s$ в $t$ через ровно $k$ переходов. Размер графа маленький по сравнению с $k$.

Обычно, мы бы ввели динамику $f_{i,v}$, равную количеству способов дойти до $v$-той вершины через ровно $i$ переходов. Пересчёт — перебор предпоследней вершины в пути:

$$
f_{i,v} = \sum_u g_{uv} \cdot f_{i-1,u}
$$

Выясняется, что $f_{i}$ зависит от $f_{i-1}$ линейно: $v$-тый элемент получается скалярным умножением $f_{i-1}$ и $v$-того столбца матрицы смежности $G$.

Значит, имеет место следующее равенство:

$$
f_i = f_{i-1} G
$$

Поэтому $G$ и называют матрицей смежности.

Получается, $f_k$ можно раскрыть как $f_k = f_0 \cdot  G \cdot G \cdot \ldots \cdot G$. Вспоминая, что умножение матриц коммутативно, по аналогии со скалярами для вычисления $G^k$ мы можем применить бинарное возведение в степень:

$$
A^8 = A^{2^{2^{2}}} = ((AA)(AA))((AA)(AA))
$$

По этой формуле нам нужно $\log k$ раз перемножить две матрицы размера $n$, что суммарно будет работать за $O(n^3 \log k)$.

```c++
matrix identity(int n) {
    // создаёт единичную матрицу
    matrix a(n, n);
    for (int i = 0; i < n; i++)
        a[i][i] = 1;
    return a;
}

matrix binpow(matrix a, int p) {
    matrix b = identity(a.n);
    
    while (p > 0) {
        if (p & 1)
            b = b*a;
         a = a*a;
         p >>= 1;
    }
    
    return b;
}
```

В получившейся матрице в ячейке $g_{ij}$ будет находиться количество способов дойти из $i$-той вершины в $j$-тую, используя ровно $k$ переходов. Ответом нужно просто вывести $g_{st}$.

Практически не меняя алгоритм, можно решить задачу «с какой вероятностью я попаду из вершины $s$ в вершину $t$», если вместо матрицы смежности даны вероятности, с которыми мы переходим из вершины в вершину в марковской цепи.

Если нам не нужно количество способов, а только сам факт, можно ли дойти за ровно $k$ переходов, то можно обернуть матрицу в битсеты и сильно ускорить решение.

Если нам нужно «за не более $k$ переходов», то можно вместо $k$-той степени считать сумму геометрической прогрессии.

Подобную технику можно применить и к другим динамикам, где нужно посчитать количество способов что-то сделать — иногда очень неочевидными способами. Например, можно найти количество строк длины большого $k$, не содержащих данные маленькие подстроки, построив граф легальных переходов в Ахо-Корасике.

Внутри матричного умножения не обязательно использовать сложение и умножение — это могут быть и какие-нибудь другие операции. Например, для нахождения минимального пути. TODO: более адекватный пример

### Линейные рекурренты

Расмотрим другой пример динамики — последовательность Фибоначчи. Ну а чем не динамика?

$$
f_{n} = f_{n-1} + f_{n-2}
$$

Как мы видим, $n$-ный элемент линейно выражается через два предыдущих.

Вообще, последовательностей Фибоначчи много — им не обязательно начинаться с 0 и 1. Для однозначной идентификации достаточно знать позиции и значения любых двух различных элементов. Поэтому в качестве состояния динамики введём.

$$
\begin{pmatrix}
f_{n+1} \\
f_{n+2} \\
\end{pmatrix}
=
\begin{pmatrix}
0+f_{n+1} \\
f_{n}+f_{n+1} \\
\end{pmatrix}
=
\begin{pmatrix}
0 & 1 \\
1 & 1 \\
\end{pmatrix}
\begin{pmatrix}
f_{n} \\
f_{n+1} \\
\end{pmatrix}
$$

Также можно «шагать» в обратном направлении, если обратить эту матрицу.

Обозначим за $A$ эту матрицу перехода. Чтобы посчитать $n$-е число Фибоначчи, нужно применить $n$ раз эту матрицу к вектору $(f_0, f_1) = (0, 1)$.

В общем случае, линейная рекуррента $f_n = a_1 f_{n-1} + a_2 f_{n-2} + \ldots + a_k f_{n-k}$ имеет такую матрицу перехода:

$$
\begin{pmatrix}
0 & 1 & 0 & \ldots & 0 \\
0 & 0 & 1 & \ldots & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & 0 & \ldots & 1 \\
a_k & a_{k-1} & a_{k-2} & \ldots & a_1 \\
\end{pmatrix}
$$

TODO

## Собственные векторы

Очень часто у матриц есть *собственные векторы* — те, которые не меняют направление при применении этой матрицы к ним.

$ Av = k v $, где $k \neq 0$.

$ Av - kv = (A-kI)v = 0 $. Это означает

Вообще, собственные вектора в линейной алгебре имеют почти такое же значение, как простые числа в арифметике.

## Линейная независимость

Базисом множества называется набор векторов, через который можно выразить все вектора этого множества и только их. 

Базисы есть не только в линейной алгебре. Например, $\{1, x, x^2\}$ является базисом всех квадратных трёхчленов. Или $\{\neg, \land, \lor\}$ является базисом всех логических выражений (то есть всё можно выразить через И, ИЛИ и НЕ). В произвольном языке программирования можно выделить какой-то набор команд, через который можно будет написать всё что угодно, и он тоже в этом смысле будет базисом.

## Системы уравнений и метод Гаусса

Иногда встречаются задачи, требующие решения системы линейных уравнений. Очень большая часть из них на самом деле над полем $\mathbb{Z}_2$ — то есть все числа по модулю 2. К примеру: есть $n$ переключателей лампочек, каждый активированный переключатель меняет состояние (включает или выключает) какого-то подмножества из $n$ лампочек. Известно состояние всех лампочек, нужно восстановить состояние переключателей.

Нас по сути просят решить следующую систему:

$$
\begin{cases}
a_{11} x_1 + a_{12} x_2 + \ldots + a_{1n} x_n \equiv b_1 \pmod 2\\
a_{21} x_1 + a_{22} x_2 + \ldots + a_{2n} x_n \equiv b_2 \pmod 2\\
\ldots \\
a_{n1} x_1 + a_{n2} x_2 + \ldots + a_{nn} x_n \equiv b_n \pmod 2
\end{cases}
$$

Здесь $x$ — состояния переключателей, $b$ — состояния лампочек, $A$ — информация о том, влияет ли переключатель на лампочку.

Метод Крамера неоптимален — там $O(n^4)$ операций.

В таком случае можно значительно ускорить и упростить обычный метод Гаусса:

```c++
t gauss (matrix a) {
    for (int i = 0; i < n; i++) {
        int nonzero = i;
        for (int j = i+1; j < n; j++)
            if (a[j][i])
                nonzero = j;
        swap(a[nonzero], a[i]);
        for (int j = 0; j < n; j++)
            if (j != i && a[j][i])
                a[j] ^= a[i];
    }
    t x;
    for (int i = 0; i < n; i++)
        x[i] = a[i][n] ^ a[i][i];
    return x;
}
```

Код находит вектор $x$ из уравнения $Ax = b$ при условии, что решение существует и единственно. Для простоты кода, предполагается, что вектор $b$ приписан справа к матрице $A$.

Часто эту систему нужно решить по модулю 2. Тогда код значительно упрощается и ускоряется (опять же, см. [Битсет](https://algorithmica.org/ru/bitset#%D0%93%D0%B0%D1%83%D1%81%D1%81)).
