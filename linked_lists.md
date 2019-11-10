## Связный список

Продолжаем тему структур данных. В прошлой статье были описаны [стэки и очереди](https://nurushev.dev/stack-and-queue/). На этот раз речь пойдет о **связном списке**.

<p style="text-align: center">
<img src="https://raw.githubusercontent.com/zhas/articles/master/images/linkedlist.png">
</p>

**Связный список** - структура данных, которая хранит множество объектов в линейном порядке, где доступ к элементам осуществляется через соседние элементы.
В отличие от массивов:
- более гибкая структура, т.к. порядок обхода задан самими элементами
- может хранить объекты разного типа
- объекты могут храниться на разных участках памяти
- менее эффективный доступ к порядковому элементу (в списке O(n) vs в массиве O(1))

#### Определения
**Узел** - элемент списка, хранящий значение(ключ) и ссылки на соседние элементы

**Односвязный список** - список, где в каждом узле хранится ссылка только на следующий или только на предыдущий элемент, соответственно итерироваться по такому списку можно только в одном направлении.

**Двусвязный список** - список, в котором в каждом узле хранятся ссылки на следующий и предыдущий элементы, т.е. можно итерироваться в обоих направлениях.

**Кольцевой(циклический) список** - список, в котором хвост списка связан с головой.

#### Реализация двусвязного списка

Создадим класс узла - **ListNode**, с аттрибутами **key**(ключ), **next**(ссылка на следующий узел), **prev**(ссылка на предыдущий узел). А так же класс самого списка -  **LinkedList**, в котором будем хранить ссылку на первый элемент списка, т.е. на голову(**head**).

Реализуем три основных операции insert(вставка ключа), search(поиск по ключу), delete(удаление узла):

- Операция **insert**:
Создаем новый узел. Указываем следующим(**next**) узлом текущую голову(**head**). У текущей головы, предыдущим(**prev**) узлом указываем новый узел. И в заключении, делаем наш новый узел головой списка.

- Операция **search**:
Последовательно, от начала списка, при помощи ссылки **next** итерируем по всему списку, пока не наткнемся на нужный ключ.

- Операция **delete**:
Связываем предыдущий и следующий узлы между собой, таким образом узел между ними будет удален из списка. Если у удаляемого узла нет предыдущего элемента, т.е. он является головой, необходимо, чтобы теперь голова указывала на следующий элемент.

```python
# Linked List

class ListNode:
    def __init__(self, key, prev=None, _next=None):
        self.key = key
        self.next = _next
        self.prev = prev


class LinkedList:
    def __init__(self):
        self.head = None

    def insert(self, key):
        new = ListNode(key, _next=self.head)
        if self.head:
            self.head.prev = new
        self.head = new

    def search(self, key):
        cur = self.head
        while cur and cur.key != key:
            cur = cur.next
        return cur

    def delete(self, node):
        if node.prev:
            node.prev.next = node.next
        else:
            self.head = node.next
        if node.next:
            node.next.prev = node.prev
```

##### Разворот двусвязного списка
Для того, чтобы сделать разворот двусвязного списка, достаточно один раз пробежаться от начала до конца списка, меняя местами ссылки на следующий и предыдущий элемент. Когда будет достигнут последний элемент списка, необходимо назначить его новой головой.

```python
# Reverse of linked list

class LinkedListWithReverse(LinkedList):
    def reverse(self):
        cur = self.head
        while cur:
            _next = cur.next
            cur.next, cur.prev = cur.prev, cur.next
            if _next is None:
                self.head = cur
            cur = _next
```

##### Ограничитель(Sentinel)
Можно вместо хранения ссылки на начальный элемент(**head**), использовать узел ограничитель(sentinel). Узел ограничитель это узел-болванка соединяющая конец и начало списка, делая его кольцевым. Это займет чуть больше памяти, но делает код чуть проще, за счет того, что не нужно проверять граничные случаи.

```python
# Linked List with Sentinel

class ListNode:
    def __init__(self, key, prev=None, _next=None):
        self.key = key
        self.next = _next
        self.prev = prev


class LinkedListWithSentinel:
    def __init__(self):
        self.nil = ListNode(key=None)  # Sentinel

    def insert(self, key):
        new = ListNode(key, _next=self.nil.next)
        new.prev = self.nil
        self.nil.next = new

    def search(self, key):
        cur = self.nil.next
        while cur and cur.key != key:
            cur = cur.next
        return cur

    def delete(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev
```

#### Односвязный список

Односвязный список отличается от двусвязного наличием только одной ссылки на соседний элемент. Такой список займет меньше памяти, ведь указателей у него вдвое меньше, но итерироваться можно будет только в одном направлении. Обратите внимание, что операция delete в данном случае будет иметь сложность O(n), в то время как двусвязном списке это можно было сделать за  О(1). Причиной этому является то, что не имея ссылки на предыдущий элемент, необходимо итерироваться с начала списка, чтобы его найти.

```python
# Simple implementation of singly linked list

class ListNode:
    def __init__(self, key, _next=None):
        self.key = key
        self.next = _next

class SinglyLinkedList:
    def __init__(self):
        self.head = None

    def insert(self, key):
        self.head = ListNode(key, _next=self.head)

    def search(self, key):
        cur = self.head
        while cur and cur.key != key:
            cur = cur.next
        return cur

    def delete(self, node):
        prev = self.head
        while prev and prev.next != node:
            prev = prev.next
        if prev:
            prev.next = node.next
        else:
            # if prev not found, it means that its head
            self.head = node.next
```


### Разворот односвязного списка
Итерируемся по списку и каждому элементу указываем следующим элементом предыдущий элемент. Так как ссылка на предыдущий элемент отсутствует, используем дополнительную переменную prev, где и будем хранить предыдущий элемент.

```python
# Reverse of singly linked list

class SinglyLinkedListWithReverse(SinglyLinkedList):
    def reverse(self):
        cur = self.head
        prev = None
        while cur:
            _next = cur.next
            cur.next = prev
            prev = cur
            if _next is None:
                self.head = cur
            cur = _next
```


#### Заключение

В данной статье был рассмотрен связный список, его виды и основные операции над ним. Данная структура данных имеет свои плюсы и минусы в сравнении с массивами. Как и любую структуру данных, связный список можно использовать в симбиозе с другими структурами. Например, в [стэках и очередях](https://nurushev.dev/stack-and-queue/), можно в качестве хранилища элементов использовать связные списки.

##### Используемая литература и ресурсы
* Т.Кормен, Ч.Лейзерсон, Р.Ривест, К.Штайн - Алгоритмы. Пострение и анализ.
* [ru.wikipedia.org](https://ru.wikipedia.org/wiki/%D0%A1%D0%B2%D1%8F%D0%B7%D0%BD%D1%8B%D0%B9_%D1%81%D0%BF%D0%B8%D1%81%D0%BE%D0%BA)
