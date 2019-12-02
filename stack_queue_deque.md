# Стэк и очереди

В этой статье я расскажу о трех структурах данных **стэк**(stack), **очередь**(queue) и **двойная очередь**(deque). Эти структуры могут помочь при решении практических задач связанных с очередностью выполнения действий, а так же их часто спрашивают на техническом интервью.

## Стэк

<p style="text-align: center">
<img src="https://raw.githubusercontent.com/zhas/articles/master/images/stack.png">
</p>

Стэк это структура данных позволяющая хранить список элементов, добавлять в конец списка и удалять последний из списка. Главный принцип - последний вошел, первым вышел(Last In First Out, **LIFO**).
Пример, из жизни - стопка тарелок. Мы кладем тарелки друг на друга, и если хотим взять вторую сверху нам необходимо убрать и верхнюю.

### Реализация:

Существуют разные реализации стэка, в этой статье элементы стэка будут хранится в питоновском списке и будет задано ограничение на количество элементов max_size.

Заведем список **st** с длиной **max_size**, и переменную **st_top**, которая будет указывать на индекс, куда мы положим следующий элемент. 
Если мы хотим добавить новый элемент, то присваеваем его **st[st_top]** и увеличиваем **st_top** на единицу. Для удаления еще проще, достаточно уменьшить **st_top** на единицу.
При этом, при каждой модификации необходимо проверять количество элементов, чтобы недопустить переполнения(overflow) или попытку удалить элемент из пустого списка(underflow).

Код(python3):
```python
# Basic stack implementation

class MyStack:
    def __init__(self, max_size=1000):
        self.max_size = max_size
        self.st = [0] * max_size
        self.st_top = 0

    def is_empty(self):
        return self.st_top == 0

    def push(self, x):
        assert self.st_top < self.max_size - 1, 'Overflow'
        self.st[self.st_top] = x
        self.st_top += 1

    def pop(self):
        assert self.st_top > 0, 'Underflow'
        self.st_top -= 1
        return self.st[self.st_top]

```

## Очередь

<p style="text-align: center">
<img src="https://raw.githubusercontent.com/zhas/articles/master/images/queue.png">
</p>

Таже как и стэк, очередь является структурой хранящей список элементов, но следуя немного другому принципу - первым зашел, первым вышел(First In First Out, **FIFO**). Примером может являться любая очередь из людей, например в поликлинику, где каждый новый человек занимает место в конце, и заходит только после остальных.  

### Реализация:
В отличие от реализации стэка, где первый элемент всегда находился в начале списка, в реализации очереди позиция первого и последнего элемента будет цикличной, т.е. возможно такое, что позиция хвоста будет меньше позиции начала. Заведем список длинной **max_size + 1** и две переменные **head** и **tail**. Первая переменная будет указывать на голову, а вторая на хвост нашей очереди. При добавлени элемента(**enqueue**) будем увеличивать переменную **tail** на 1 и устанавливать элемент на эту позицию, при этом, если достигнут конец зарезервированного списка, то прыгаем на начало списка(для элегантности сделаем это взяв полученное выражение по модулю **max_size + 1**). Для вывода из очереди(**deque**), совершим аналогичную манипуляцию с переменной **head**, только вместо записи переменной, сделаем её вывод. Так же, следует не забыть про граничные случаи, предупредить переполнение списка и опустошение пустого списка.

Код(python3):
```python
# Basic queue implementation

class MyQueue:
    def __init__(self, max_size=1000):
        self.max_size = max_size
        self.q = [0] * (max_size + 1)
        self.tail = 0
        self.head = 0

    def __len__(self):
        if self.tail >= self.head:
            return self.tail - self.head
        else:
            return self.max_size - (self.head - self.tail) + 1

    def is_empty(self):
        return len(self) == 0

    def is_full(self):
        return len(self) == self.max_size

    def enqueue(self, x):
        assert not self.is_full(), 'Overflow'
        self.tail = (self.tail + 1) % (self.max_size + 1)
        self.q[self.tail] = x

    def dequeue(self):
        assert not self.is_empty(), 'Underflow'
        self.head = (self.head + 1) % (self.max_size + 1)
        return self.q[self.head]
```

## Двойная очередь
Двойная очередь является модификацией обычной очереди, где добавляется функционал добавления в начало очереди и удаление из хвоста.

### Реализация:
Как и в случае с добавлением в конец, и удалением с начала, манипулируем переменными **head** и **tail**, только идем не вперед списка, а назад. Для простоты отнаследуемся от обычной очереди, реализованной ранее, и добавим новые названия для enqueue и dequeue - push_back и pop_front соответственно. 

Код(python3):
```python
# Basic deque implementation
from elementary.queue import MyQueue

class MyDeque(MyQueue):
    def push_back(self, x):
        self.enqueue(x)

    def pop_front(self):
        return self.dequeue()

    def push_front(self, x):
        assert not self.is_full(), 'Overflow'
        self.q[self.head] = x
        self.head = (self.head - 1) % (self.max_size + 1)

    def pop_back(self):
        assert not self.is_empty(), 'Underflow'
        x = self.q[self.tail]
        self.tail = (self.tail - 1) % (self.max_size + 1)
        return x
```

## Использование collections.deque

collections.deque - двойная очередь реализованная в стандартной библиотеке python. Более подробно о ней можно почитать в [официальной документации](https://docs.python.org/3.8/library/collections.html#collections.deque). Так же, её можно использовать в качестве стэка или очереди. 


Пример оборачивания deque в стэк и очередь, для наглядности(python3):
```python
# Implementing stack and queue with deque from collections
from collections import deque


class Stack(deque):
    def push(self, x):
        super().append(x)

    def pop(self):
        return super().pop()


class Queue(deque):
    def enqueue(self, x):
        self.append(x)

    def dequeue(self):
        return self.popleft()
```

## Заключение
В данной статье были описаны три классических структуры данных с реализацией на языке python. Стоит отметить, что все описанные операции над этими структурами имеют сложность O(1), т.е. они являются эффективными. При решении практических задач, если потребуется, можно изменять и дополнять данные структуры, как например, двойная очередь была реализована на основе обычной очереди.


### Используемая литература
* Т.Кормен, Ч.Лейзерсон, Р.Ривест, К.Штайн - Алгоритмы. Пострение и анализ.
