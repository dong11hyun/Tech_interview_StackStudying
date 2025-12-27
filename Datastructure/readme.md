# 목차 (Table of Contents)

1. [📁Array vs Linked List](#array-vs-linked-list)
2. [📁Stack, Queue, Deque](#stack-queue-deque)

---

## 📁Array vs Linked List

> **데이터를 저장하는 두 가지 기본적인 형태** (메모리 구조의 차이)

1. **Array (배열)**:
    * **연속된 메모리 공간**에 데이터를 순차적으로 저장.
    * **임의 접근(Random Access)**이 가능하다 (인덱스로 O(1) 접근).
    * 데이터 삽입/삭제 시 요소를 이동시켜야 하므로 비효율적이다 (O(N)).
    * 크기가 고정되어 있다 (Static Array 기준, Dynamic Array는 Resizing 비용 발생).

2. **Linked List (연결 리스트)**:
    * **떨어진 메모리 공간**에 데이터(Node)가 존재하며, 링크(Pointer)로 연결됨.
    * **순차 접근(Sequential Access)**만 가능하다 (특정 요소를 찾으려면 O(N) 탐색).
    * 데이터 삽입/삭제가 용이하다 (링크만 변경하면 됨, O(1)).
    * 크기가 동적이다.

| 비교 항목 | Array | Linked List |
| --- | --- | --- |
| **메모리 구조** | 연속됨 (Continuous) | 비연속됨 (Dispersed) |
| **접근 속도(Read)** | **빠름** (O(1)) | 느림 (O(N)) |
| **삽입/삭제(Write)** | 느림 (O(N)) | **빠름** (O(1)) |
| **메모리 효율** | 고정 크기 할당 (낭비 가능성) | 포인터 저장을 위한 추가 메모리 필요 |

[목차🔝](#목차-table-of-contents)

---

## 📁Stack, Queue, Deque

> **데이터의 삽입과 삭제 규칙이 정해진 자료구조**

1. **Stack (스택)**:
    * **LIFO (Last In First Out)**: 나중에 들어온 것이 먼저 나간다.
    * **용도**: 함수 호출 스택(재귀), 웹 브라우저 뒤로 가기, 괄호 검사.
    * **주요 연산**: `push()`, `pop()`, `top()` / `peek()`

2. **Queue (큐)**:
    * **FIFO (First In First Out)**: 먼저 들어온 것이 먼저 나간다.
    * **용도**: 작업 스케줄링(프린터 대기열), 너비 우선 탐색(BFS), 캐시 구현.
    * **주요 연산**: `enqueue()`, `dequeue()`, `front()`, `rear()`

3. **Deque (덱 / Double-Ended Queue)**:
    * **양쪽 끝**에서 삽입과 삭제가 모두 가능한 자료구조.
    * 스택과 큐의 기능을 모두 합친 형태.
    * **용도**: 덱을 이용하면 스택과 큐를 모두 구현 가능, 슬라이딩 윈도우(Sliding Window) 알고리즘.
    * **주요 연산**: `push_front()`, `push_back()`, `pop_front()`, `pop_back()`

[목차🔝](#목차-table-of-contents)
