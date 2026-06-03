# 연결리스트 (Linked List)

## 연결리스트란?

**노드(Node)** 들이 포인터로 연결된 선형 자료구조다.

각 노드는 두 가지 요소로 구성된다.
- **data**: 실제 저장하는 값
- **next**: 다음 노드를 가리키는 포인터

배열과 달리 메모리상에 연속적으로 저장되지 않는다. 각 노드가 다음 노드의 주소를 가지고 있어서 흩어진 메모리를 연결하는 방식으로 동작한다.

```
[data|next] → [data|next] → [data|next] → None
```

---

## 단순 연결리스트 (Singly Linked List)

각 노드가 다음 노드만 가리키는 가장 기본적인 형태다.

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class SinglyLinkedList:
    def __init__(self):
        self.head = None

    def append(self, data):         # 맨 뒤에 노드 추가
        new_node = Node(data)
        if self.head is None:
            self.head = new_node
            return
        current = self.head
        while current.next:
            current = current.next
        current.next = new_node

    def delete(self, data):         # 특정 값의 노드 삭제
        if self.head is None:
            return
        if self.head.data == data:
            self.head = self.head.next
            return
        current = self.head
        while current.next:
            if current.next.data == data:
                current.next = current.next.next
                return
            current = current.next

    def print_list(self):           # 전체 출력
        current = self.head
        while current:
            print(current.data, end=" → ")
            current = current.next
        print("None")
```

---

## 이중 연결리스트 (Doubly Linked List)

각 노드가 이전 노드와 다음 노드를 모두 가리키는 형태다. 양방향 탐색이 가능하다.

```
None ← [prev|data|next] ↔ [prev|data|next] ↔ [prev|data|next] → None
```

```python
class DoublyNode:
    def __init__(self, data):
        self.data = data
        self.prev = None
        self.next = None

class DoublyLinkedList:
    def __init__(self):
        self.head = None

    def append(self, data):         # 맨 뒤에 노드 추가
        new_node = DoublyNode(data)
        if self.head is None:
            self.head = new_node
            return
        current = self.head
        while current.next:
            current = current.next
        current.next = new_node
        new_node.prev = current

    def delete(self, data):         # 특정 값의 노드 삭제
        current = self.head
        while current:
            if current.data == data:
                if current.prev:
                    current.prev.next = current.next
                else:
                    self.head = current.next
                if current.next:
                    current.next.prev = current.prev
                return
            current = current.next

    def print_list(self):           # 전체 출력
        current = self.head
        while current:
            print(current.data, end=" ↔ ")
            current = current.next
        print("None")
```

---

## 원형 연결리스트 (Circular Linked List)

마지막 노드가 None이 아닌 첫 번째 노드(head)를 가리키는 형태다. 끝이 없는 순환 구조다.

```
[data|next] → [data|next] → [data|next] → (head로 돌아감)
```

```python
class CircularLinkedList:
    def __init__(self):
        self.head = None

    def append(self, data):         # 맨 뒤에 노드 추가
        new_node = Node(data)
        if self.head is None:
            self.head = new_node
            new_node.next = self.head
            return
        current = self.head
        while current.next != self.head:
            current = current.next
        current.next = new_node
        new_node.next = self.head

    def delete(self, data):         # 특정 값의 노드 삭제
        if self.head is None:
            return
        if self.head.data == data:
            current = self.head
            while current.next != self.head:
                current = current.next
            current.next = self.head.next
            self.head = self.head.next
            return
        current = self.head
        while current.next != self.head:
            if current.next.data == data:
                current.next = current.next.next
                return
            current = current.next

    def print_list(self):           # 전체 출력
        if self.head is None:
            return
        current = self.head
        while True:
            print(current.data, end=" → ")
            current = current.next
            if current == self.head:
                break
        print("(head로 돌아감)")
```

---

## 배열과의 비교

| | 연결리스트 | 배열 |
|--|-----------|------|
| 메모리 구조 | 불연속 | 연속 |
| 접근 방식 | 순차 접근 | 인덱스로 직접 접근 |
| 삽입/삭제 | 빠름 O(1) | 느림 O(n) |
| 탐색 | 느림 O(n) | 빠름 O(1) |
| 크기 | 동적 | 고정 |

---

## 정리

- 연결리스트는 노드들이 포인터로 연결된 자료구조다
- 단순: 단방향 / 이중: 양방향 / 원형: 마지막 노드가 head를 가리킴
- 삽입·삭제는 빠르지만 탐색은 느리다
- 크기가 동적으로 변하는 데이터에 적합하다