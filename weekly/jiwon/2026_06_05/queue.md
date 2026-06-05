# 큐 (Queue)

## 큐란?

**선입선출(FIFO, First In First Out)** 방식의 선형 자료구조다.

먼저 들어온 데이터가 먼저 나간다. 줄 서기와 동일한 구조다.

```
enqueue →  [ 1 | 2 | 3 ]  → dequeue
           front       rear
           
가장 먼저 들어온 1이 먼저 나감
```

---

## 주요 연산

- **enqueue**: 큐 맨 뒤에 데이터 추가
- **dequeue**: 큐 맨 앞의 데이터 제거 및 반환
- **peek**: 큐 맨 앞의 데이터 확인 (제거하지 않음)

모든 연산은 O(1)이다.

---

## 파이썬에서의 큐

파이썬에서는 `collections.deque`를 사용하는 것이 일반적이다.

```python
from collections import deque

queue = deque()

queue.append(1)     # enqueue [1]
queue.append(2)     # enqueue [1, 2]
queue.append(3)     # enqueue [1, 2, 3]

queue.popleft()     # dequeue → 1 반환, [2, 3]
print(queue[0])     # peek → 2
```

리스트로도 구현할 수 있지만, 리스트의 `pop(0)`은 이후 요소를 전부 앞으로 당겨야 해서 O(n)이 된다. `deque`의 `popleft()`는 O(1)이므로 큐 구현에 적합하다.

---

## 활용 사례

- **프로세스 스케줄링**: 운영체제에서 작업을 순서대로 처리
- **BFS(너비 우선 탐색)**: 그래프 탐색 시 방문할 노드를 순서대로 관리
- **프린터 대기열**: 먼저 요청한 문서부터 순서대로 출력

---

## 스택과의 비교

| | 스택 | 큐 |
|--|------|----|
| 방식 | LIFO (후입선출) | FIFO (선입선출) |
| 삽입 | push (top) | enqueue (rear) |
| 제거 | pop (top) | dequeue (front) |
| 활용 | 함수 호출, undo | 스케줄링, BFS |

---

## 정리

- 큐는 FIFO 구조로 먼저 들어온 데이터가 먼저 나간다
- enqueue, dequeue, peek 모두 O(1)이다
- 파이썬에서는 리스트보다 `collections.deque`를 사용하는 것이 적합하다