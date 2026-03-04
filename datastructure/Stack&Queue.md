# 🕵️ Stack & Queue
<hr>

- [1️⃣ Stack](#1-stack)
- [2️⃣ Queue](#2-queue)

## 1️⃣ Stack
<hr>

### ▶️ 정의
 
- LIFO (Last In First Out, 후입선출) 형태의 자료구조
- 마지막에 들어간 자료가 가장 먼저 나오는 구조

![stack.png](res/stack.png)

### ▶️ 연산 및 특징

- Push : 새로운 데이터를 Top 위치에 삽입
- Pop : Top 위치에 있는 데이터 제거 및 반환
- Peek : Top 위치에 있는 데이터 반환(삭제 X)
- 중간 데이터 직접 수정 및 삭제 불가


### ▶️ 실제 사용사례

- 웹 브라우저 방문 기록 (뒤로가기)
- 실행 취소
- 후위 표기법 계산 (y=abc-dc/+)
  * 피연산자 -> push / 연산자 -> pop
- 깊이 우선 탐색 (DFS)
- 함수 호출 및 재귀

### ▶️ 시간복잡도

|       | 삽입   | 삭제   | 조회   |
|-------|------|------|------|
| Stack | O(1) | O(1) | O(N) |


### ▶️ 구현
```java
package Class;

import java.util.Stack;

public class StackAPITest {
  public static void main(String[] args) {
    Stack<String> stack = new Stack<String>();
    stack.push("test1");
    stack.push("test2");
    stack.push("test3");

    System.out.println("스택 사이즈 : " + stack.size());

    while(!stack.isEmpty()) {
      System.out.println(stack.pop());
    }
  }
}
```

## 2️⃣ Queue
<hr>

### ▶️ 정의

- FIFO (First In First Out, 선입선출) 형태의 자료 구조
- 처음 들어간 자료가 가장 먼저 나오는 구조

![queue.png](res/queue.png)

### ▶️ 연산 및 특징

- enqueue : rear 에서 데이터 삽입
- dequeue : front 에서 데이터 삭제 및 반환
- 중간 데이터 직접 수정 및 삭제 불가
- 오버 플로우(Overflow) : 큐 크기만큼 데이터가 꽉 차서 들어가지 않음
- 언더 플로우(Underflow) : 큐가 비어있는데 데이터를 꺼내려고 하는 경우

### ▶️ 실제 사용사례

- 작업 예약
- 서비스 센터 대기
- 프로세스 관리
- 너비 우선 탐색 (BFS)

### ▶️ 시간복잡도

|       | 삽입   | 삭제   | 조회   |
|-------|------|------|------|
| Queue | O(1) | O(1) | O(N) |


### ▶️ 구현
```java

package Class;

import java.util.ArrayDeque;
import java.util.Queue;

public class QueueAPITest {
  public static void main(String[] args) {
    Queue<String> queue = new ArrayDeque<String>();
    queue.offer("test1");
    queue.offer("test2");
    queue.offer("test3");


    System.out.println("큐 사이즈 : " + queue.size());

    while(!queue.isEmpty()) {
      System.out.println(queue.poll());
    }
  }
}
```
> 실제 구현 시에는 "ArrayDeque" 가 빠름 

### ▶️ 종류 

> 선형 큐 (Linear Queue)
> - 데이터를 FIFO 구조로 처리하는 기본 큐
> 
> ![linear_queue.png](res/linear_queue.png)
>
> 원형 큐 (Circular Queue)
> - 큐의 마지막 요소가 첫 요소와 연결된 큐
> - 선형 큐의 데이터 삽입/삭제 시 데이터들을 앞뒤로 당겨주는 불필요한 낭비 극복 위함
> - isEmpty() : front == rear
> - isFull() : (rear + 1) % size == front
>
> ![circular_queue.png](res/circular_queue.png)
> 
> 우선순위 큐 (Priority Queue)
> - 각 데이터 요소에 우선순위 할당
> - 우선순위애 따라 데이터 처리
> - 힙(Heap)을 이용하여 구현
>
> 데큐 (Deque)
> - 양쪽에서 삽입, 삭제 가능
>
> ![deque.png](res/deque.png)

<hr>

#### 출처
- https://jud00.tistory.com/entry/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-%EC%8A%A4%ED%83%9DStack%EA%B3%BC-%ED%81%90Queue%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90
- https://dream-and-develop.tistory.com/93
- https://yoongrammer.tistory.com/m/45?t_src=GNBlayer_kakaostory
- https://velog.io/@mic050r/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-Tree-Graph%EC%9D%B4%EB%9E%80
- https://nukoori.tistory.com/30
