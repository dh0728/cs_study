# 트리 (Tree)

## 트리란?

**노드(Node)** 와 **간선(Edge)** 으로 구성된 비선형 자료구조다. 그래프의 특수한 형태로, 사이클이 없고 계층 구조를 가진다.

```
        A          ← 루트 노드
       / \
      B   C        ← 내부 노드
     / \   \
    D   E   F      ← 리프 노드
```

---

## 트리 용어

| 용어 | 설명 |
|------|------|
| 루트(Root) | 최상위 노드. 부모가 없다 |
| 부모(Parent) | 자신과 연결된 상위 노드 |
| 자식(Child) | 자신과 연결된 하위 노드 |
| 형제(Sibling) | 같은 부모를 가진 노드 |
| 리프(Leaf) | 자식이 없는 노드 |
| 깊이(Depth) | 루트에서 해당 노드까지의 간선 수 |
| 높이(Height) | 트리의 최대 깊이 |

---

## 이진 트리 (Binary Tree)

각 노드가 최대 2개의 자식을 갖는 트리다. 왼쪽 자식(left)과 오른쪽 자식(right)으로 구분한다.

```
        A
       / \
      B   C
     / \
    D   E
```

```python
class Node:
    def __init__(self, data):
        self.data  = data
        self.left  = None
        self.right = None
```

---

## 이진 탐색 트리 (BST, Binary Search Tree)

이진 트리에 다음 규칙을 추가한 형태다.

> 왼쪽 자식 < 부모 < 오른쪽 자식

이 규칙 덕분에 탐색을 O(log n)에 수행할 수 있다.

```
        5
       / \
      3   7
     / \ / \
    2  4 6  8
```

```python
# 탐색 슈도코드
def search(node, target):
    if node is None or node.data == target:
        return node
    if target < node.data:
        return search(node.left, target)   # 왼쪽 탐색
    else:
        return search(node.right, target)  # 오른쪽 탐색
```

단, 트리가 한쪽으로 치우친 경우(편향 트리) 탐색이 O(n)까지 느려질 수 있다.

```
1
 \
  2
   \
    3       ← 편향 트리: 사실상 연결리스트와 같다
     \
      4
```

---

## 트리 순회 (Tree Traversal)

트리의 모든 노드를 방문하는 방법이다. 방문 순서에 따라 세 가지로 나뉜다.

```
        A
       / \
      B   C
     / \
    D   E
```

| 순회 방식 | 순서 | 결과 |
|-----------|------|------|
| 전위(Pre-order) | 루트 → 왼 → 오 | A B D E C |
| 중위(In-order) | 왼 → 루트 → 오 | D B E A C |
| 후위(Post-order) | 왼 → 오 → 루트 | D E B C A |

```python
def preorder(node):
    if node is None: return
    print(node.data)       # 루트 먼저
    preorder(node.left)
    preorder(node.right)

def inorder(node):
    if node is None: return
    inorder(node.left)
    print(node.data)       # 왼쪽 다음 루트
    inorder(node.right)

def postorder(node):
    if node is None: return
    postorder(node.left)
    postorder(node.right)
    print(node.data)       # 마지막에 루트
```

> BST를 중위 순회하면 오름차순으로 정렬된 값을 얻을 수 있다.

---

## 트리 표현법

이진 트리는 left/right 두 포인터로 자식을 표현하면 되지만, 자식 수가 제한 없는 **일반 트리**는 별도의 표현법이 필요하다.

### N링크 표현법

각 노드가 자식 수만큼 포인터를 가지는 방식이다.

```
노드 A의 자식이 3개라면:
[ data | child1 | child2 | child3 ]
```

```python
class Node:
    def __init__(self, data, num_children):
        self.data     = data
        self.children = [None] * num_children  # 자식 수만큼 배열
```

자식 수가 노드마다 다르면 포인터 배열 크기도 달라져 메모리 낭비가 발생할 수 있다.

### LCRS 표현법 (Left Child Right Sibling)

각 노드가 **첫 번째 자식(left)** 과 **다음 형제(right)** 만 가리키는 방식이다. 자식이 몇 명이든 포인터 2개로 고정된다.

```
        A
      / | \
     B  C  D     →    A
                      |
                      B → C → D
```

```python
class Node:
    def __init__(self, data):
        self.data    = data
        self.left    = None   # 첫 번째 자식
        self.right   = None   # 다음 형제
```

N링크에 비해 메모리 효율이 좋고, 이진 트리와 동일한 구조를 사용하므로 구현이 간단하다.

---

## 시간복잡도

| 연산 | 이진 탐색 트리(BST) 평균 | BST 최악 (편향 트리) |
|------|--------------------------|----------------------|
| 탐색 | O(log n) | O(n) |
| 삽입 | O(log n) | O(n) |
| 삭제 | O(log n) | O(n) |
| 순회 | O(n) | O(n) |

편향 트리가 되면 연결리스트와 동일한 성능으로 저하된다.

---

## 그래프와의 비교

| | 트리 | 그래프 |
|--|------|--------|
| 사이클 | 없음 | 있을 수 있음 |
| 방향 | 부모→자식 (단방향) | 방향/무방향 모두 가능 |
| 루트 | 있음 | 없음 |
| 연결 | 모든 노드가 연결됨 | 연결되지 않을 수 있음 |

---

## 정리

- 트리는 사이클 없는 계층 구조의 자료구조다
- 이진 트리는 자식이 최대 2개, BST는 여기에 정렬 규칙을 추가한 형태다
- BST는 탐색이 O(log n)이지만 편향 트리가 되면 O(n)으로 저하된다
- 순회는 전위/중위/후위 세 가지 방식이 있다
- 일반 트리 표현법으로 N링크(자식 수만큼 포인터)와 LCRS(첫째 자식 + 다음 형제)가 있다