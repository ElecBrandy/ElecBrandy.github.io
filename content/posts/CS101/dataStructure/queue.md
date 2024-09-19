+++
title = '[자료구조] Queue'
date = 2024-09-19
featured_image = "https://www.educative.io/v2api/editorpage/5393602882568192/image/6038582739138560"
tags = ['dataStructure', 'cs101']
+++

{{<series title="📚 /dataStructure" series="dataStructure">}}

<br>

## 1. 소개
____
**Queue**(큐)는 선형 자료구조로, 데이터가 삽입된 순서대로 처리되는 **FIFO(First In First Out)** 방식이다. 즉, 먼저 들어온 데이터가 먼저 나가는 구조이다. 큐는 컴퓨터 시스템에서 작업 예약, 데이터 버퍼링 등 다양한 분야에서 활용된다.

<br>
<br>

## 2. 큐의 개념과 종류
____
큐는 한쪽 끝에서는 삽입이, 반대쪽 끝에서는 삭제가 이루어지는 구조이다. 기본적인 큐 외에도 다양한 변형이 존재한다.

- **원형 큐(Circular Queue)**: 마지막 위치가 첫 위치와 연결되어 원형으로 구성된 큐이다.
- **우선순위 큐(Priority Queue)**: 각 요소에 우선순위가 부여되어, 높은 우선순위의 요소가 먼저 처리되는 큐이다.
- **덱(Deque)**: 양쪽 끝에서 삽입과 삭제가 모두 가능한 큐이다.

<br>
<br>

## 3. 큐의 구현
____
큐는 배열이나 연결 리스트를 이용하여 구현할 수 있다. 여기서는 `C` 언어를 사용하여 배열 기반의 큐를 구현해보고, `C++`의 STL에서 제공하는 `queue`의 사용법을 알아보겠다.

<br>

### 3-1. 배열 기반 큐 구현 (with C)

#### 큐 구조체 정의
```c
#define MAX_SIZE 100

typedef struct {
    int data[MAX_SIZE];
    int front;
    int rear;
} Queue;
```
`Queue` 구조체는 `data` 배열과 `front`, `rear` 인덱스를 포함한다. `front`는 삭제 위치를, `rear`는 삽입 위치를 가리킨다.

<br>

#### 초기화 함수
```c
void initQueue(Queue *q) {
    q->front = 0;
    q->rear = 0;
}
```
큐를 초기화하여 `front`와 `rear`를 0으로 설정한다.

<br>

#### 공백 및 포화 상태 확인 함수
```c
int isEmpty(Queue *q) {
    return q->front == q->rear;
}

int isFull(Queue *q) {
    return (q->rear + 1) % MAX_SIZE == q->front;
}
```
큐가 비어있는지 또는 가득 찼는지 확인하는 함수이다.

<br>

#### 삽입 연산
```c
void enqueue(Queue *q, int item) {
    if (isFull(q)) {
        printf("Queue is full.\n");
        return;
    }
    q->rear = (q->rear + 1) % MAX_SIZE;
    q->data[q->rear] = item;
}
```
`enqueue` 함수는 큐의 `rear` 위치에 새로운 요소를 추가한다.

<br>

#### 삭제 연산
```c
int dequeue(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue is empty.\n");
        return -1;
    }
    q->front = (q->front + 1) % MAX_SIZE;
    return q->data[q->front];
}
```
`dequeue` 함수는 `front` 위치의 요소를 제거하고 반환한다.

<br>

#### 사용 예제
```c
int main() {
    Queue q;
    initQueue(&q);

    enqueue(&q, 10);
    enqueue(&q, 20);
    enqueue(&q, 30);

    printf("%d\n", dequeue(&q));  // 출력: 10
    printf("%d\n", dequeue(&q));  // 출력: 20

    return 0;
}
```
위의 예제는 큐에 값을 삽입하고 삭제하는 기본적인 동작을 보여준다.

<br>
<br>

### 3-2. STL `queue` 사용법 (with C++)

`C++`의 STL에서는 `queue` 라이브러리를 제공하여 쉽게 큐를 사용할 수 있다.

```cpp
#include <iostream>
#include <queue>

int main() {
    // std::queue 선언
    std::queue<int> myQueue;

    // 요소 추가 (push)
    myQueue.push(10);
    myQueue.push(20);
    myQueue.push(30);

    // front와 back 확인
    std::cout << "Front: " << myQueue.front() << std::endl;  // 출력: 10
    std::cout << "Back: " << myQueue.back() << std::endl;    // 출력: 30

    // 요소 삭제 (pop)
    myQueue.pop();  // 10 제거

    // 다시 front 확인
    std::cout << "Front after pop: " << myQueue.front() << std::endl;  // 출력: 20

    // 큐가 빌 때까지 모든 요소 제거
    while (!myQueue.empty()) {
        std::cout << "Removing: " << myQueue.front() << std::endl;
        myQueue.pop();
    }

    return 0;
}
```
`std::queue`는 FIFO 구조를 가지며, `push`, `pop`, `front`, `back`, `empty` 등의 멤버 함수를 제공한다.

<br>

#### 우선순위 큐 `priority_queue`

우선순위 큐는 `priority_queue`를 사용하여 구현할 수 있다.

```cpp
#include <iostream>
#include <queue>

int main() {
    // std::priority_queue 선언
    std::priority_queue<int> pq;

    // 요소 추가
    pq.push(30);
    pq.push(10);
    pq.push(20);

    // 가장 큰 요소부터 출력
    while (!pq.empty()) {
        std::cout << pq.top() << " ";  // 출력: 30 20 10
        pq.pop();
    }

    return 0;
}
```
`std::priority_queue`는 기본적으로 큰 숫자가 높은 우선순위를 가진다.

<br>
<br>

## 4. 큐의 활용 예시
____
큐는 다음과 같은 다양한 분야에서 활용된다.

- **데이터 버퍼링**: 데이터 스트림에서 일시적으로 데이터를 저장하는 데 사용된다.
- **작업 스케줄링**: 운영체제에서 프로세스나 스레드의 작업을 예약하고 관리한다.
- **너비 우선 탐색(BFS)**: 그래프 탐색 알고리즘에서 방문해야 할 노드의 목록을 관리한다.

<br>
<br>

## 5. Reference
____
- [GeeksforGeeks - Queue Data Structure](https://www.geeksforgeeks.org/queue-data-structure/)
- [C++ Reference - std::queue](https://cplusplus.com/reference/queue/queue/)
- [위키백과 - 큐 (자료 구조)](https://ko.wikipedia.org/wiki/큐_(자료_구조))

<br>
<br>