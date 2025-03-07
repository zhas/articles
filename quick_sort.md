Быстрая сортировка — это широко распространенный и эффективный алгоритм сортировки изобретенный Чарльзом Хоаром. В данной статье вы узнаете, что из себя представляет данный алгоритм, какие бывают вариации алгоритма и как их реализовать на языке python.

## 1. Описание алгоритма

Быстрая сортировка, как и сортировка слиянием, использует подход "разделяй и властвуй", который в контексте этого алгоритма состоит из следующих этапов:

1. выбираем в последовательности опорный элемент

2. сдвигаем все что меньше опорного элемента влево от этого элемента, а все что больше или равно — вправо

3. рекурсивно запускаем сортировку для левой и правой части

Иллюстрация на примере последовательности [2,5,4,1,5,3] на картинке ниже:

<p style="text-align: center">
<img src="https://raw.githubusercontent.com/zhas/articles/master/images/quick_sort.png" alt="быстрая сортировка"><br>
рис1. Быстрая сортировка
</p>

<!--more-->

Существуют разные способы разбиения на части, в примере выше использовалось **разбиение Ломуто**, которое является самым простым в реализации.

###  1.1 Сортировка с разбиением Ломуто

В данном способе, в качестве опорного элемента выбирается последний элемент. Затем все элементы меньше опорного перемещаются в левую часть последовательности. Является самой простой в реализации версией быстрой сортировки.

#### Реализация

Реализация данного алгоритма, состоит из двух функций: **lomuto_quick_sort** и **lomuto_partition**. 

Функция **lomuto_quick_sort** является основной функцией, которая разбивает входящую последовательность на две части при помощи функции **lomuto_partition**, а затем запускает себя рекурсивно для левой и правой части. 

В функции **lomuto_partition** происходит разбиение на левую(все что меньше опорного) и правую(все что больше или равно опорному) части. В качестве опорного элемента выбирается последний элемент. Затем, происходит последовательная итерация по списку, и элементы которые меньше опорного переносятся в левую часть списка, при этом запоминается позиция в переменной **i**. В конце, сам опорный элемент переставляется за левой частью, таким образом оказываясь посередине между левой и правой частью.

Код на python3:
```python
def lomuto_partition(nums, l, r):
    last = r - 1
    pivot = nums[last]

    i = l

    for j in range(l, last):
        if nums[j] < pivot:
            nums[j], nums[i] = nums[i], nums[j]
            i += 1

    nums[i], nums[last] = nums[last], nums[i]

    return i


def lomuto_quick_sort(nums, l, r):
    if (r - l) > 1:
        q = lomuto_partition(nums, l, r)
        lomuto_quick_sort(nums, l, q)
        lomuto_quick_sort(nums, q + 1, r)

```

### 1.2 Сортировка с разбиением Хоара

Классическое разбиение представленное самим создателем быстрой сортировки. Эффективнее разбиения Ломуто, так как в среднем делает в три раза меньше перестановок. Состоит из следующих шагов(на примере последовательности [4,6,1,5,3,9,7]):

1. В качестве опорного элемента выбираем элемент посередине
![](https://raw.githubusercontent.com/zhas/articles/master/images/hoare1.png)

2. Итерируясь слева направо, находим элемент больший или равный опорному 
![](https://raw.githubusercontent.com/zhas/articles/master/images/hoare2.png) 

3. Итерируясь справа налево, находим элемент меньший или равный опорному
![](https://raw.githubusercontent.com/zhas/articles/master/images/hoare3.png) 

4. Обмениваем местами выбранные элементы
![](https://raw.githubusercontent.com/zhas/articles/master/images/hoare4.png) 

5. Шаги 1,2,3,4 повторяем до тех пор, пока позиция элемента найденного справа, будет больше позиции элемента найденного слева.

#### Реализация

Функции **hoare_quick_sort** вызывает разбиения, и запускает два рекурсивных вызова для левой и правой части. 

Функция **hoare_partition**, выполняет шаги следуя алгоритму описанному выше. Сначала выбирается опорный элемент — средний элемент в последовательности. В бесконечном цикле, используя переменную **i**, итерируемся слева направо находя ближайший элемент больше опорного. Аналогично находим элемент меньше опорного итерируясь слева направо используя переменную **j**. Если **j <= i** возвращаем **j** в качесвте индекса разбиения.
 
**Примечание:** в отличие от реализации разбиением Ломуто, где индекс разбиения не включался ни в левую, ни в правую части, в данном случае он включается в левую часть, так как не гарантируется, что он равен опорному элементу(он может быть меньше).  Так же, в отличие от разбиения Ломуто, опорный элемент выбирается выражением **nums[(r + l) // 2 - 1]** : здесь отнимается минус один, для того, чтобы опорный гарантировано не был последним элементом, иначе это может привести к бесконечной рекурсии.

Код на python3:
```python
def hoare_partition(nums, l, r):
    pivot = nums[(r + l) // 2 - 1]
    i = l - 1
    j = r
    while True:
        i, j = i + 1, j - 1

        while nums[j] > pivot:
            j -= 1
        while nums[i] < pivot:
            i += 1

        if i >= j:
            return j
        nums[i], nums[j] = nums[j], nums[i]


def hoare_quick_sort(nums, l, r):
    if (r - l) > 1:
        q = hoare_partition(nums, l, r)
        hoare_quick_sort(nums, l, q + 1)
        hoare_quick_sort(nums, q + 1, r)

```

### 1.3 Сортировка с разбиением на три части

В данном способе список разделяется на три части: левая часть — элементы меньше опорного, средняя часть — равные опорному, правая часть — элементы больше опорного. Затем рекурсивно сортируются левая и правая части, а середина игнорируется. Данное разбиение является более эффективным, когда в последовательности большое количество одинаковых элементов.

#### Реализация

```python
def three_way_partition(nums, l, r):
    i, j = l, r - 1
    pivot = nums[(l + r) // 2]

    k = i
    while k <= j:
        if nums[k] < pivot:
            nums[i], nums[k] = nums[k], nums[i]
            i += 1
            k += 1
        elif nums[k] > pivot:
            nums[j], nums[k] = nums[k], nums[j]
            j -= 1
        else:
            k += 1

    return i, j


def three_way_quick_sort(nums, l, r):
    if (r - l) > 1:
        i, j = three_way_partition(nums, l, r)
        three_way_quick_sort(nums, l, i)
        three_way_quick_sort(nums, j + 1, r)
```

### 2. Асимптотическая  сложность алгоритма

1) **В худшем случае**:
если на каждом шаге разбиения, будет возвращаться последовательность **n - 1**, глубина рекурсии будет **n**. Само же разбиение представляет собой линейный проход, и имеет сложность **O(n)**. Таким образом в наихудшем случае сложность будет **O(n^2)**. Этот наихудший случай, будет происходить когда опорный элемент всегда равен наибольшему. Например, для разбиения Ломуто, наихудший кейс — отсортированная последовательность.

2) **В наилучшем случае**:
 в наилучшем случае, разбиение будет делить последовательность, на равные части, таким образом глубина рекурсии будет **log(n)**, и сложность алгоритма в целом **O(n * log(n))**
 
3) **В среднем случае**:
в среднем, если учесть равновероятность входных данных, поведение алгоритма будет ближе к наилучшему случаю, чем к наихудшему и будет равна **O(n * log(n)).**

### 3. Улучшенный выбор опорного элемента

В приведенных реализациях выше опорный элемент всегда последний или средний по позиции. Можно значительно уменьшить вероятность возникновения наихудшего случая, взяв в качестве опорного медиану из трех или более элементов. Еще один способ — это брать опорный элемент случайным образом.

Пример функции **lomuto_partition** со случайным выбором опорного элемента:
```python
def lomuto_partition(nums, l, r):
    last = r - 1

    pos = random.randint(l, last)
    nums[last], nums[pos] = nums[pos], nums[last]

    pivot = nums[last]
    i = l

    for j in range(l, last):
        if nums[j] < pivot:
            nums[j], nums[i] = nums[i], nums[j]
            i += 1

    nums[i], nums[last] = nums[last], nums[i]

    return i

```

### Заключение

В данной статье был разобран алгоритм быстрой сортировки и его вариации. Стоит отметить, что хоть он имеет сложность в худшем случае **O(n^2)**, в среднем он будет лучше алгоритма [сортировки слиянием](https://nurushev.dev/merge-sort/), который имеет сложность в худшем случае **O(n*log(n))**, так как последний использует дополнительные расходы для выделения памяти.