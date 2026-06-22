# 그래프 (Graph)

## 그래프란?

**노드(Node)** 와 **간선(Edge)** 으로 구성된 비선형 자료구조다.

노드는 데이터를 저장하고, 간선은 노드 간의 관계를 나타낸다. 지하철 노선도, 소셜 네트워크, 도로망 등이 그래프 구조다.

```
노드: A, B, C, D
간선: A-B, A-C, B-D

    A
   / \
  B   C
  |
  D
```

---

## 그래프의 종류

**방향 그래프 vs 무방향 그래프**

- **무방향 그래프**: 간선에 방향이 없다. A-B 연결은 A→B, B→A 모두 가능
- **방향 그래프**: 간선에 방향이 있다. A→B는 가능하지만 B→A는 불가능할 수 있음

```
무방향: A — B
방향:   A → B
```

**가중치 그래프**: 간선에 비용(거리, 시간 등)이 부여된 그래프

```
A —(5)— B —(3)— C
```

---

## 그래프 표현 방식

### 인접 행렬 (Adjacency Matrix)

노드 수 × 노드 수 크기의 2차원 배열로 표현한다. 연결되어 있으면 1, 없으면 0.

```
     A  B  C
A  [ 0, 1, 1 ]
B  [ 1, 0, 0 ]
C  [ 1, 0, 0 ]
```

```python
graph = [
    [0, 1, 1],
    [1, 0, 0],
    [1, 0, 0]
]
```

가중치 그래프는 연결 여부 대신 가중치 값을 넣는다.

```python
graph = [
    [0, 5, 3],
    [5, 0, 2],
    [3, 2, 0],
]
```

연결 없음을 0과 구분해야 할 때는 INF(무한대)를 사용한다.

```python
INF = 65535
graph = [
    [INF, 5, 3],
    [5, INF, 2],
    [3, 2, INF],
]
```

- 두 노드의 연결 여부 확인: O(1)
- 공간: O(N²) → 노드가 많고 간선이 적으면 낭비가 크다

### 인접 리스트 (Adjacency List)

각 노드에 연결된 노드 목록을 리스트로 저장한다.

```python
graph = {
    'A': ['B', 'C'],
    'B': ['A'],
    'C': ['A']
}
```

- 공간: O(N + E) → 실제 간선 수에 비례하므로 효율적
- 두 노드의 연결 여부 확인: O(N)

---

## 그래프 탐색

### DFS (Depth First Search, 깊이 우선 탐색)

한 방향으로 끝까지 탐색하고 막히면 되돌아오는 방식이다. **스택** 또는 **재귀**로 구현한다.

```
탐색 순서 예시:
    A
   / \
  B   C
  |
  D

A → B → D → (막힘, 되돌아옴) → C
```

```python
def dfs(graph, start, visited=set()):
    visited.add(start)
    print(start)
    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
```

### BFS (Breadth First Search, 너비 우선 탐색)

시작 노드에서 가까운 노드부터 순서대로 탐색하는 방식이다. **큐**로 구현한다.

```
탐색 순서 예시:
    A
   / \
  B   C
  |
  D

A → B → C → D
```

```python
from collections import deque

def bfs(graph, start):
    visited = set([start])
    queue = deque([start])
    while queue:
        node = queue.popleft()
        print(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

### DFS vs BFS 비교

| | DFS | BFS |
|--|-----|-----|
| 방식 | 깊이 우선 | 너비 우선 |
| 자료구조 | 스택 (재귀) | 큐 |
| 적합한 경우 | 경로 존재 여부, 사이클 탐지 | 최단 경로 탐색 |

---

## 시간복잡도

N: 노드 수, E: 간선 수

| 연산 | 인접 행렬 | 인접 리스트 |
|------|-----------|-------------|
| 두 노드 연결 여부 확인 | O(1) | O(N) |
| 한 노드의 인접 노드 조회 | O(N) | O(E) |
| 노드 추가 | O(N²) | O(1) |
| 간선 추가 | O(1) | O(1) |
| DFS/BFS 탐색 | O(N²) | O(N + E) |
| 공간 | O(N²) | O(N + E) |

간선이 적은 희소 그래프에서는 인접 리스트가 유리하고, 간선이 많은 밀집 그래프에서는 인접 행렬이 유리하다.

---

## 정리

- 그래프는 노드와 간선으로 구성된 자료구조다
- 방향/무방향, 가중치 유무에 따라 종류가 나뉜다
- 인접 행렬은 연결 확인이 빠르고, 인접 리스트는 공간 효율이 좋다
- DFS는 깊이 우선 탐색, BFS는 너비 우선 탐색이며 목적에 따라 선택한다