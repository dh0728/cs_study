# 시간복잡도와 공간복잡도

## 시간복잡도란?

알고리즘이 **입력 크기(n)에 따라 얼마나 많은 연산을 수행하는지**를 나타내는 척도다.

실제 실행 시간(초)이 아닌 **연산 횟수**를 기준으로 삼는다. 실행 시간은 컴퓨터 사양에 따라 달라지기 때문에, 환경에 독립적인 기준이 필요하다.

---

## Big-O 표기법

시간복잡도는 **Big-O 표기법**으로 나타낸다. Big-O는 **최악의 경우**를 기준으로 표기한다.

**표기 규칙**
- 상수는 무시한다. 2n, 5n 모두 O(n)
- 가장 큰 항만 남긴다. n² + n + 1 → O(n²)

---

## 자주 쓰이는 Big-O

| 표기법 | 이름 | 설명 |
|--------|------|------|
| O(1) | 상수 | 입력 크기와 무관하게 연산 횟수 고정 |
| O(log n) | 로그 | 입력이 2배 증가해도 연산은 1번 증가 |
| O(n) | 선형 | 입력 크기에 비례하여 연산 증가 |
| O(n log n) | 선형로그 | 정렬 알고리즘에서 자주 등장 |
| O(n²) | 이차 | 이중 반복문에서 자주 등장 |
| O(2ⁿ) | 지수 | 입력 증가 시 연산이 폭발적으로 증가 |

속도 순서: **O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(2ⁿ)**

---

## 코드로 보는 시간복잡도

### O(1) — 상수
```python
def get_first(arr):
    return arr[0]  # 배열 크기와 무관하게 항상 1번 연산
```

### O(n) — 선형
```python
def print_all(arr):
    for x in arr:   # 배열 크기만큼 반복
        print(x)
```

### O(n²) — 이차
```python
def print_pairs(arr):
    for x in arr:
        for y in arr:   # 이중 반복문
            print(x, y)
```

### O(log n) — 로그
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1  # 절반을 버림
        else:
            right = mid - 1
    return -1
```

이진 탐색은 매 단계마다 탐색 범위를 절반으로 줄인다. n=1000이어도 약 10번의 연산으로 탐색이 가능하다.

---

## 정리

- 시간복잡도는 입력 크기에 따른 연산 횟수를 나타낸다
- Big-O로 표기하며 최악의 경우를 기준으로 한다
- 상수와 낮은 차수의 항은 무시한다
- O(1) → O(log n) → O(n) → O(n²) 순으로 느려진다