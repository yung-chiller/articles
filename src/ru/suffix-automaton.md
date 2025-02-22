# Суффиксный автомат

Если вы не знаете, что такое префиксное дерево или автомат, рекомендуется сначала почитать про Ахо-Корасик.

Рассмотрим задачу:

> Дан текст $T$ и $n$ строк $s_i$, для каждой из которых нужно узнать, встречается ли она в тексте.

Есть два основных подхода к решению этой задачи. Первый: алгоритм Ахо-Корасик, который строит по набору строк автомат, распознающий эти строки в потоке текста. Второй: использование суффиксных структур, под которыми обычно подразумневают суффиксный массив, суффиксный автомат или суффикское дерево.

Данная статья посвящена последним двум их них.

## Наивное решение

Возьмем все суффиксы $T$ и объединим их в бор, в котором каждой подстроке $T$ будет соответствовать ровно одна вершина. С его помощью можно за $O(|T|^2)$ времени на построение и $O(|s_i|)$ времени на запрос узнавать, входит ли $s_i$ в $T$.

Идея дальнейшей оптимизации заключается в убирании «лишних» состояний в этом боре.

От квадратиченой сложности можно избавиться двумя разными способами — ведущими, соответственно, либо к суффиксному автомату, либо к суфффиксному дереву.

**Сжатое суффиксное дерево.** Любой путь от корня в этом боре будет подстрокой $T$, а значит, если из вершины $v$ нет исходящий рёбер, то можно заменить путь на ребро, храня рядом с ним всю строку. Непосредственно хранить строку при этом не обязательно — она есть где-то как подстрока $T$, а значит можно просто хранить пару чисел $[l, r]$, указывающую на её местоположение.

Выясняется, что такая структура данных занимает $O(|T|)$ памяти. Чтобы это понять, нужно оценить количество «развилок» — мест, где путь разъеденяется на два.

Пусть мы бор строли путем добавления суффиксов. Тогда, каждая новая строка совпадала каким-то префиксом с уже имеющейся, а потом отпочковалась и породила новый путь. Получается, что вершин тут не больше, чем строк.

Сжатое суффиксное дерево тоже годится для нашей задачи: только теперь по рёбрам нужно шагать виртуально, просматривая один символ за другим.

Существуют алгоритмы, которые строят сжатое суффиксное дерево эффективно — например, алгортим Укконена. Мы их рассматривать в этой статье не будем и сразу перейдём ко второму методу.

**Суффиксным автоматом** строки $s$ называется *минимальный* (с наименьшим количеством вершин) автомат, принимающий все суффиксы строки $s$ и только их.

Выясняется, что он тоже маленький и его можно построить за линейное время.

Поразмышляем немного над его природой:

* Суффиксный автомат ацикличен — иначе можно было бы принимать бесконечно большие строки, а мы его строили только для суффиксов.

* Пусть $a$ и $b$ — строки, которые принимает состояние $v$ суффиксного автомата. Тогда для любой строки $c$ строки $ac$ и $bc$ принимаются
  
  или не принимаются одновременно. Иными словами, независимо от того, как мы «пришли» в состояние $v$, если мы пройдём из него по пути, соответствующему строке $c$, мы всегда сможем точно сказать, в какое состояние мы попадём.

* В каком-то смысле все строки разделяются на *классы эквивалентности* в зависимости от того, в какое состояние они приводят.

* Назовём *правым контекстом* строки (или состояния) множество всех строк-продолжений, которые принимает автомат.

Если правый контекст какой-то вершины пуст, то её можно удалить.

Если два правых контекста какой-то вершины совпали, то эти вершины можно слить в одну, ничего не сломав — значит автомат, в котором такое возможно, не является минимальным.

Рассмотрим множество $R$ непустых правых контекстов всех возможных слов
над $\Sigma$. В любом автомате, задающем данный язык будет хотя бы $|R|$
состояние.

Рассмотрим любой правый контекст в $R$. Пусть он порождён строкой $a$,
тогда в автомате должно быть некоторое состояние $q$, принимающее строку
$a$ и имеющее такой правый контекст, иначе автомат бы не смог принять
строку $ax$. При этом все эти состояния будут различными, так как у них
будут различный правые контексты.

**tl;dr:** Автомат является минимальным тогда и только тогда, когда правые контексты всех его состояний попарно различны и не пусты.

**Утверждение.** Любое состояние суффиксного автомата принимает какую-то строку и какое-то множество её самых длинных суффиксов.



Суффиксный автомат
==================

Детерминированный конечный автомат (далее автомат) --- это пятёрка
$A = (Q, \Sigma, \delta, q_0, F)$, где $Q$ --- множество состояний,
$\Sigma$ --- алфавит, $\delta \subset Q \times \Sigma \times Q$ ---
множество переходов, $q_0 \in Q$ --- начальное состояние, $F \subset Q$
--- множество финальных состояний.

Всякому автомату можно сопоставить ориентированный граф такой что
состояния автомата --- это вершины графа, а переходы --- это дуги
помеченные символами из алфавита. Соответственно, в таком графе должна
быть выделена некоторая вершина $q_0$ (начальное состояние) и набор
вершин $F$ (финальные состояния). Кроме того, из любого состояния не
может быть двух переходов по одному и тому же символу
(детерминированность).

Состояние $q$ принимает строку $s$, если есть путь из $q_0$ в $q$ такой
что если выписать все символы, которые мы встретили на этом пути, мы
получим строку $s$. Автомат принимает строку $s$, если её принимает хотя
бы одно из финальных состояний. Множество строк принимаемых автоматом
будем называть его языком.

Суффиксным автоматом строки $s$ будем называть *минимальный* автомат,
который принимает все суффиксы строки и только их. Под минимальностью
подразумевается, что число состояний в нём должно быть наименьшим
возможным.

Суффиксный автомат не только ориентированный, но и ациклический граф,
ведь множество принимаемых им строк конечно, а цикл позволил бы принять
сколь угодно длинную строку. Рассмотрим факты, позволяющие нам уточнить
природу суффиксного автомата.

Пусть $a$ и $b$ --- строки, которые принимает состояние $q$ некоторого
автомата $A$. Тогда для любой строки $x$ строки $ax$ и $bx$ принимаются
или не принимаются $A$ одновременно.

Независимо от того, как мы "пришли" в состояние $q$, если мы пройдём из
него по пути, соответствующему строке $x$, мы сможем точно сказать, в
какое состояние мы попадём и, в частности, будет ли оно финальным.

Правым контекстом $X(q)$ состояния $q$ назовём множество строк $x$,
переводящих $q$ в одно из финальных состояний. Соответственно, назовём
правым контекстом строки $a$ множество $X(a)$ строк $x$ таких что $ax$
принимается автоматом.

Правый контекст строки совпадает с правым контекстом состояния, которое
её принимает, поэтому правые контексты языка можно рассматривать
независимо от конкретного автомата:

Рассмотрим множество $R$ непустых правых контекстов всех возможных слов
над $\Sigma$. В любом автомате, задающем данный язык, будет хотя бы $|R|$
состояний.

Рассмотрим любой правый контекст в $R$. Пусть он порождён строкой $a$,
тогда в автомате должно быть некоторое состояние $q$, принимающее строку
$a$ и имеющее такой правый контекст, иначе автомат бы не смог принять
строку $ax$. При этом все эти состояния будут различными, так как у них
будут различный правые контексты.

Существует автомат, на котором эта оценка достигается.

Если в автомате есть состояние с пустым правым контекстом, мы можем
удалить его, не изменим принимаемый язык. Иначе пусть в автомате есть
два состояния $q_1$, $q_2$ такие что $X(q_1) = X(q_2)$. Мы можем удалить
состояние $q_2$ и перевести переходы, ведущие в него в состояние $q_1$.
Множество принимаемых строк от этого не изменится, следовательно, мы
можем продолжать эту процедуру, пока число состояний не будет равно
числу различных непустных правых контекстов.

Таким образом, мы доказали:

Автомат является минимальным тогда и только тогда, когда правые
контексты всех его состояний попарно различны и не пусты.

В случае суффиксного автомата правый контекст $X(a)$ строки $a$ взаимно
однозначно соответствует множеству правых позиций вхождений строки $a$ в
строку $s$. Действительно, если $ax$ принимается автоматом, то есть,
является суффиксом, то $s = yax$, а строке $x$ мы можем сопоставить
позицию $|s|-|x|-1$. Таким образом, каждое состояние автомата принимает
строки с одинаковым множеством *правых* позиций их вхождений и обратно,
все строки с таким множеством позиций принимаются этим состоянием.

Связь между суффиксным автоматом и суффиксным деревом
-----------------------------------------------------

Рассмотрим ребро в суффиксном дереве строки $T$, а точнее все подстроки
$T$, которым соответствует или "внутренняя" вершина ребра, или вершина,
в которой ребро заканчивается. Для любой строки $x$ и любой пары строк
$a$, $b$ из рассматриваемого множества строк, строки $xa$ и $xb$
являются или не являются префиксами строки $s$ одновременно.

Пусть $|a| < |b|$. Тогда $a$ является предком $b$ в боре, то есть, её
префиксом, значит, её множество вхождений точно содержит множество
вхождений строки $b$. Допустим, существует позиция $|x|$, в которой есть
вхождение строки $a$, но не строки $b$.

Рассмотрим строку $a'$, которая является максимальным префиксом строки
$b$, который можно встретить в той позиции. Если $a'$ не упирается в
конец строки, то её можно продолжить, как минимум, двумя различными
символами чтобы она осталась подстрокой $T$. Значит, соответствующая ей
вершина в дереве имеет степень больше двух и должна разбивать ребро, на
котором находится, что конфликтует с предположением о том, что $a$ и $b$
взяты с одного ребра.

Если же $a'$ нельзя продолжить, то она всё ещё должна разбивать ребро,
т.к. является суффиксом строки и её вершина не будет удалена при сжатии
рёбер.

Аналогичным образом можно показать, что для любой строки $a$ все строки
$b$ с таким же множеством строк $x$ находятся на соответствующем строке
$a$ ребре, то есть, есть биекция между рёбрами суффиксного дерева и
множествами левых позиций вхождений строк в $T$. Отсюда:

Для любого состояния $q$ суффиксного автомата строки $T$ найдётся
вершина $q'$ суффиксного дерева развернутой строки $T$ такая, что
множество строк, принимаемых состоянием $q$, совпадает с развёрнутым
множеством строк, таких что соответствующая им вершина в дереве лежит на
ребре, ведущем в $q'$ (включая строку, соответствующую $q'$).

Это целиком описывает состояния автомата и позволяет разработать
алгоритм его построения.

## Построение суффиксного автомата

Пусть длина самой короткой строки, которая принимается состоянием $q$
равна $k$. Тогда суффиксная ссылка $link(q)$ будет вести из этого
состояния в состояние, которое принимает эту же строку без первого
символа.

Обратившись к суффиксному дереву мы поймём, что в нём она будет вести в
предка вершины $q'$. Таким образом, суффиксные ссылки образуют дерево,
которое соответствует суффиксному дереву развернутой строки. Также будем
обозначать длину самой длинной строки, которая принимается состоянием
$q$ как $len(q)$. Длина самой короткой строки из $q$ будет равна
$len(link(q)) + 1$.

Будем дописывать символы в конец строки $T$ по одному и при этом
поддерживать для неё корректный автомат с деревом суффиксных ссылок.
Пусть у нас есть автомат для строки $T$ и она принимается его состоянием
$last$. Мы хотим получить автомат для строки $Tc$. Нам нужно, чтобы для
каждого суффикса новой строки существовало состояние, которое его
примет. При этом нам нужно сохранить минимальность автомата.

Добавим состояние, которое принимает всю строку $Tc$ и назовём его
$new$. Правый контекст $Tc$ состоит из единственной строки --- пустой,
значит, в $new$ будут входить те и только те суффиксы, которые мы
встретили в строке впервые. Все такие строки можно получить дописыванием
символа $c$ к суффиксам $T$, которые принимаются состоянием, из которого
ещё нет перехода по данному символу. Таким образом, чтобы новые суффиксы
принимались, нам необходимо будет "попрыгать" по суффиксным ссылкам и
добавить переходы по символу $c$ в состояние $new$, пока не придём в
корень или не обнаружим, что переход по символу $c$ из состояния уже
есть.

Если мы пришли в корень, значит, *все* непустые суффиксы строки $Tc$
принимаются состоянием $new$ и мы можем положить $link(new) = q_0$ и
завершить работу.

Иначе мы нашли состояние $q'$, из которого переход по символу $c$ уже
есть. Значит, суффиксы длины $\leq len(q') + 1$ уже встречались в
строке, и новых переходов в состояние $new$ мы проводить не будем.
Однако, для состояния $new$ ещё нужно посчитать суффиксную ссылку.
Наибольшей строкой в ней будет суффикс строки $Tc$ длины $len(q') + 1$.
В данный момент он находится в состоянии $t$, в которое ведёт переход по
символу $c$ из состояния $q'$, но в нём могуть быть также строки большей
длины. Таким образом, если $len(t) = len(q') + 1$, то $t$ и есть искомая
суффиксная ссылка. Проведя её, мы завершим обновление автомата.

Иначе $t$ --- состояние, которое принимает как строки, являющиеся
суффиксами строки, так и строки, которые ими не являются, из-за этого мы
не можем корректно определить его финальность. Чтобы решить данный
конфликт мы должны будем отщепить от $t$ состояние $t'$, которое примет
все строки, которые принимаются $t$, но имеют длину $\leq len(q') + 1$,
то есть, тот самый кусок с суффиксами. Для этого скопируем в $t'$
переходы и суффиксную ссылку из $t$, а длину установим равной
$len(q') + 1$. Затем установим $link(new) = link(t) = t'$. Наконец,
чтобы "перебросить" в него нужные строки из $t$, пройдёмся по суффиксным
ссылкам состояния $q'$ пока переходы по $c$ ведут в $t$ и переправим эти
переходы в $t'$. Таким образом, мы перенаправим все пути интересующей
нас длины, которые ранее проходили через вершину $t$ в вершину $t'$.

В некотором смысле эта процедура соответствует построению суффиксного
дерева добавлением суффиксов в возрастающем (по длине) порядке, приведём
код на языке C++, выполняющий её:

```{.c++
const int maxn = 2e5 + 42; // Максимальное число состояний
map<char, int> to[maxn]; // Переходы
int link[maxn]; // Суффиксные ссылки
int len[maxn]; // Длины максимальных строк в состояниях
int last = 0; // Состояние, соответствующее всей строке
int sz = 1; // Общее число состояний

void add_letter(char c) // Дописываем символ в конец
{
    int p = last; // Записываем в p состояние строки s
    last = sz++; // Создаём для строки sc новое состояние
    len[last] = len[p] + 1;
    for(; to[p][c] == 0; p = link[p]) // (1)
        to[p][c] = last; // Прыгаем по ссылкам, создавая переходы
    if(to[p][c] == last)
    { // Если мы оказались здесь, то символ c встречен впервые
        link[last] = 0;
        return; 
    }
    int q = to[p][c]; 
    if(len[q] == len[p] + 1) 
    { // Если переход сплошной, то q - суфф ссылка
        link[last] = q; 
        return;
    }
    // Расщепляем q на два состояния, одно из которых cl,
    // А второе получит тот же номер, что и q имело ранее
    int cl = sz++; 
    to[cl] = to[q]; // (2)
    link[cl] = link[q]; 
    len[cl] = len[p] + 1; 
    link[last] = link[q] = cl;
    for(; to[p][c] == q; p = link[p]) // (3)
        to[p][c] = cl; // Перенаправляем переходы там, где нужно
}
```

Время работы
============

Покажем линейность работы данного алгоритма. На каждом шаге есть три
места, которые выполняются не за $O(1)$:

1. Прыжки по ссылкам $last$ для создания переходов в $new$.

2. Копирование переходов из $t$ в $t'$.

3. Прыжки по ссылкам $q'$ для перенаправления переходов из $t$ в $t'$.

В первых двух пунктах происходит создание очередного перехода в
автомате. Покажем, что всего переходов будет $O(n)$. Разделим все
переходы $\delta(v, c) = u$ из $v$ в $u$ по символу $c$ на два класса
--- "сплошные", для которых $len(v) + 1 = len(u)$ и все остальные.

Т.к. в каждое состояние, кроме начального, ведёт ровно один сплошной
переход, сплошных переходов будет не больше, чем состояний, а их $O(n)$.

Рассмотрим теперь несплошные переходы. Каждому такому переходу можно
поставить в соответствие строку $acb$, где $a$ --- длиннейшая строка,
которую принимает состояние $v$, а $b$ --- длиннейшая строка, которую
можно вывести из состояния $u$. Данная строка является суффиксом $s$
(иначе мы могли бы продлить $b$ вправо). Кроме того $|a| = len(v)$,
отсюда можно сделать вывод, что $|a|$ составлена исключительно из
сплошных переходов. Значит, по произвольному суффиксу мы можем
определить рассмотренный несплошной переход, как первый, который
встретим, "скармливая" его строке. Значит, такое отображение
взаимооднозначное и сплошных переходов не больше, чем суффиксов в
строке. Отсюда следует, что суммарно переходов в автомате $O(n)$.

Наконец, докажем линейность третьего пункта. Для удобства назовём
$link(link(q))$ второй суффиксной ссылкой состояния $q$. Также будем
использовать такие обозначения: $last$ --- состояние, соответствующее
строке $s$, $new$ --- состояние строки $sc$, $p$ --- состояние, которое
"прыгает" в циклах (можно видеть в коде). Т.к. $sc$ не могла встречаться
в суффиксном автомате $s$, из состояния $last$ изначально нет перехода
по $c$, поэтому в цикле (1) мы сделаем хотя бы один шаг. Отсюда
$len(link(p)) \leq len(link(link(last)))$ (действительно, изначально
$p = last$, после первой итерации
$p = link(last) \rightarrow len(link(p)) = len(link(link(last)))$, на
всех последующих итерациях длина $len(link(p))$ не увеличивается,
значит, упомянутое неравенство верно.

Когда мы вышли из цикла (1), мы имеем $\delta(p, c) = q$. Очевидно, если
есть переход из состояния $p$ в состояние $q$, то если мы допишем символ
$c$ к самой короткой строке из $p$, длина которой равна
$len(link(p)) + 1$, то получим строку, которая будет не короче, чем
самая короткая строка, принимаемая состоянием $q$, длина которой равна
$len(link(q)) + 1$. То есть,
$len(link(p)) + 2 \geq len(link(q)) + 1 \rightarrow len(link(p)) + 1 \geq len(link(q))$.

После выхода из цикла (1) куда бы мы ни поставили суффиксную ссылку из
$new$, вторая суффиксная ссылка точно будет $link(q)$. Отсюда
$link(q) = link(link(new)) \rightarrow len(link(q)) = len(link(link(new)))$.

Теперь посмотрим на цикл (3). Он будет выполняться пока
$\delta(p, c) = q$, то есть, как было упомянуто выше
$len(link(p)) + 1 \geq len(link(q))$. При этом изначально
$len(link(link(last))) \geq len(link(p))$. Так как
$len(link(q)) = len(link(link(new)))$, а также на каждом шаге
$len(link(p))$ уменьшается, получаем, что весь цикл отработает не
дольше, чем за $len(link(link(last))) - len(link(link(new)))$, то есть,
за разность максимальных длин, принимаемых вторыми суффиксными ссылками
состояний, соответствующих всей строке.

Наконец, собирая всё полученное воедино, получим такое неравенство:
$len(link(link(last))) + 1 \geq len(link(p)) + 1 \geq len(link(q)) = len(link(link(new)),$
отсюда $len(link(link(last))) + 1 \geq len(link(link(new)))$, которое
означает, что на каждом шаге длина второй суффиксной ссылки либо
уменьшилась, либо увеличилась не больше, чем на единицу (а значит, её
суммарное уменьшение не превосходит $O(n)$). Линейность алгоритма
доказана!

Применение в решении задач
==========================

1. **Число различных подстрок.** Дана строка $s$, необходимо посчитать
   
   количество её различных подстрок. В каждом состоянии встречаются
   
   строки длины от $len(link(q)) + 1$ до $len(q)$. Всего
   
   $len(q) - len(link(q))$ строк. Просуммировав эту величину по всем
   
   состояниям, получим ответ.
   
   *Упражнение:* решите эту же задачу за $O(n)$, учитывая, что к строке
   
   $s$ символы дописываются по одному и после каждого нового символа
   
   необходимо сказать текущее число различных подстрок строки $s$.
   
   *Упражнение\*:* Возьмём задачу из предыдущего упражнения и скажем,
   
   что теперь мы можем не только дописывать символы *в конец*, но и
   
   удалять их *с начала* строки. Вам требуется отвечать на те же
   
   запросы. Время работы решения всё ещё должно линейно зависеть от
   
   размера входа. *Подсказка:* иногда алгоритм Укконена также бывает
   
   полезен.

2. **Поиск подстрок в тексте.** Пропустив строку через автомат мы
   
   сможем сказать, входит ли она в текст. Допустим, мы хотим узнать
   
   какую-то информацию о её вхождениях. Например, нам нужно выдать
   
   любое конкретное вхождение. Как мы уже знаем, каждому вхождению
   
   соответствует строка $x$ такая что $ax$ --- суффикс $s$. Или, проще
   
   говоря, путь из состояния $q$ в какое-то финальное состояние.
   
   Динамикой по автомату как ациклическому ориентированному графу мы
   
   можем найти длину какого-нибудь такого пути (например, для
   
   определённости минимального или максимального). Отметим, что
   
   аналогичной динамикой считаются многие другие полезные значения,
   
   например, количество строк в правом контексте состояния (или, что то
   
   же самое, количество вхождений строк из состояния в $s$).
   
   Альтернативным решением будет обратиться к дереву суффиксных ссылок,
   
   которое, как мы помним, является суффиксным деревом для $s^T$. Как
   
   мы упоминали в самом начале, любая подстрока строки $s$ является
   
   префиксом одного из суффиксов исходной строки. Таким образом, если
   
   мы запишем в каждую "суффиксную" вершину индекс соответствующего ей
   
   суффикса, то все позиции вхождений строки $t$ в $s$ можно будет
   
   обнаружить в поддереве вершины, которая соответствует строке $t$.
   
   Значит, в частности, динамикой можно будет найти самое первое или
   
   самое последнее вхождение.
   
   Более того, учитывая, что в последнем случае мы работали с деревом,
   
   мы можем обойти его таким образом, чтобы на каждом шаге иметь в
   
   вершине множество возможных позиций, в которых встречаются строки из
   
   соответствующего состояния. Для этого нужно применить идею быстрого
   
   слияния множеств, когда мы всегда добавляем элементы из меньшего
   
   множества в большее, а не наоборот. Тогда такой обход потребует
   
   $O(n \log n)$ операций добавления в множество, т.к. каждый раз когда
   
   мы переносим между множествами элемент $k$, размер нового множества
   
   будет как минимум, в два раза больше старого, в котором он хранился.
   
   *Упражнение\*:* дана строка $s$. Найти число строк $t$ таких, что
   
   они имеют хотя бы $3$ *непересекающихся* вхождения в строку $s$.

3. **Наибольшая общая подстрока.** Нам дано $k$ строк
   
   $s_1, s_2, \dots, s_k$. Нужно найти наибольшую строку $t$, которая
   
   встречается в каждой из строк $s_i$. Одно из возможных решений ---
   
   построить автомат для строки $s_1 t_1 s_2 t_2 \dots s_n t_n$, где
   
   $t_i$ --- уникальный для каждой строки символ-разделитель. Теперь мы
   
   можем завести динамику $dp[q][i]$, в которой хранить $1$, если из
   
   состояния $q$ можно добраться до состояния, из которого есть переход
   
   по $t_i$, не проходя при этом через другие символы-разделители. Это
   
   будет равносильно тому, что строки из $q$ входят в $s_i$. Как и в
   
   прошлый раз, динамику можно пересчитывать по топологической
   
   сортировке автомата как ориентированного ациклического графа. Итого
   
   решение будет работать за $O(k \cdot \sum |s_i|)$.
   
   *Упражнение\*:* решить указанную задачу за $O(\sum |s_i|)$.
