# Stack and queues

In this article I will talk about three data structures **stack**(stack), **queue**(queue) and **double queue**(deque). These structures can help with practical tasks related to the sequence of actions and are often asked in technical interviews.

## Stack

<p style="text-align: center">
<img src="https://raw.githubusercontent.com/zhas/articles/master/images/stack.png">
</p>

Stack is a data structure that allows you to store the sequence of items, add them to the end of the sequnce and delete the last one from the sequence. The main principle - Last In First Out( **LIFO**).
An example from life is a stack of plates. We put the plates on top of each other, and if we want to take the second one from above, we need to remove the top one.

### Implementation:

There are different variants of stack implementations, in this article stack elements will be stored in Python's list and the number of elements will be limited to max_size.

Let's declare a list **st** with the length **max_size**, and the variable **st_top**, which will indicate the index where we will put the next item. 
If we want to add a new element, we assign it to **st[st_top]** and increment **st_top**. Removing is even easier, just reduce **st_top** by one.

At the same time, you should check the number of items at each modification to prevent overflow or attempting to remove an item from an empty list(underflow).

Code(python3):
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

## Queue

<p style="text-align: center">
<img src="https://raw.githubusercontent.com/zhas/articles/master/images/queue.png">
</p>

Like the stack, queue is the data structure which contains sequence of elements, but following different principle - First In First Out (**FIFO**). An example is any queue of people, where each new person takes a place at the end, and comes only after the rest.

### Implementation:
Unlike the stack implementation, where the first element has always been at the beginning of the list, the position of the first and the last element in the queue implementation will be cyclical, i.e. it is possible that head position will be after tail position. Let's make a list with length **max_size + 1** and two variables **head** and **tail**. The first variable will point to the head, while the second one will point to the tail of our queue. For adding an element (**enqueue**), we will increase the **tail** variable by 1 and set the element to this position. If the end of the reserved list is reached, we will jump to the beginning of the list (for elegance, we will do it by taking the resulting expression by modulo **max_size + 1**). To take element from the queue (**deque**), let's make a similar manipulation with the **head** variable, but instead of storing the variable, let's take it out. Also, we should not forget about boundary cases and prevent overflow and underflow of the list.

Code(python3):
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

## Double queue (deque)
Deque is modification of standard queue, which has extra functionality - adding to the beginning of sequence and popping from tail.

### Implementation:
As in the case with adding to the end and deleting from the beginning, manipulate variables **head** and **tail**, but in backward direction. For simplicity, we will inherit from the queue implemented earlier and add new names for enqueue and dequeue - push_back and pop_front, respectively. 

Как и в случае с добавлением в конец, и удалением с начала, манипулируем переменными **head** и **tail**, только идем не вперед списка, а назад. Для простоты отнаследуемся от обычной очереди, реализованной ранее, и добавим новые названия для enqueue и dequeue - push_back и pop_front соответственно. 

Code(python3):
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

## Using collections.deque

collections.deque - deque implemented in the standard python library. More details about it can be found in [official documentation] (https://docs.python.org/3.8/library/collections.html#collections.deque). Also, it can be used as a stack or a queue.

An example of deque wrapped in a stack and a queue, just for demonstration (python3):
```python
# Implementing stack and queue with deque from collections
from collections import deque

stack = deque()


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

## Conclusion
In this article three classical data structures described and implemented in python language. It should be noted that all the described operations on these structures have complexity O(1), i.e. they are efficient as possible. To solve some practical problems, these data structures can be extended or modified, like deque implementation was based on queue implementation.

## Resources
* Thomas H. Cormen, Charles E. Leiserson, Ronald L. Rivest, Clifford Stein - Introduction to Algorithms.
