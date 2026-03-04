# 🧑🏻‍💻 Heap  
<hr>

- [✅ 힙의 정의와 특징](#-힙의-정의와-특징)  
- [✅ 힙의 종류](#-힙의-종류)  
- [✅ 힙의 핵심 연산](#-힙의-핵심-연산)  
- [✅ 힙의 시간 복잡도](#-힙의-시간-복잡도)  
- [✅ 힙의 활용 예시](#-힙의-활용-예시)  

> [!NOTE]  
> 힙(Heap)은 **완전 이진 트리(Complete Binary Tree)** 형태로 데이터를 저장하며, 항상 부모 노드가 자식 노드보다 크거나(최대 힙) 작도록(최소 힙) 하는 **힙 속성(heap property)** 을 만족하는 자료구조다.

<br>

## ✅ 힙의 정의와 특징  
<hr>

> [!NOTE]  
> - 힙은 완전 이진 트리(complete binary tree) 구조를 가진다.
> - 즉, 마지막 레벨을 제외한 모든 레벨이 꽉 차 있으며, 마지막 레벨도 왼쪽부터 채워진다.
> - 힙은 ~~정렬된 구조~~가 아니라 **“최댓값/최솟값을 빠르게 찾을 수 있도록”** 부분적으로 정렬된 구조다. 
> - 같은 값을 여러 번 저장하는 **중복 원소 허용**이 가능하다.

> [!TIP]
> - 각 노드는 배열로 표현했을 때 인덱스 (i)에 대해 왼쪽 자식은 (2i), 오른쪽 자식은 (2i + 1)  위치에 저장할 수 있다. (root index는 1로 설정)
> - root의 index를 0부터 시작할 경우, (2i + 1), (2i + 2) 위치에 저장할 수 있다.

<br>

## ✅ 힙의 종류  
<hr>

### 💡 최대 힙(Max Heap)  
> [!IMPORTANT]  
> - 모든 노드에 대해, 부모 노드의 키 값이 자식 노드의 키 값보다 크거나 같은 완전 이진 트리다.
> - 항상 **루트 노드가 전체 원소 중 최댓값**이 되며, 최댓값을 빠르게 꺼내야 하는 상황에서 사용된다.

### 💡 최소 힙(Min Heap)  
> [!IMPORTANT]  
> - 모든 노드에 대해, 부모 노드의 키 값이 자식 노드의 키 값보다 작거나 같은 완전 이진 트리다.
> - 항상 **루트 노드가 전체 원소 중 최솟값**이 되며, 최솟값을 빠르게 꺼내야 하는 상황에서 사용된다.

### 💡 기타 변형 힙  
> [!NOTE]  
> - 이진 힙(Binary Heap): 가장 기본적인 힙 구현으로, 각 노드가 최대 2개의 자식을 가지는 힙이다.
> - Min-Max Heap: 루트에서 내려가며 최소 레벨과 최대 레벨이 번갈아 나타나, 최솟값과 최댓값을 모두 효율적으로 접근할 수 있는 힙이다. ()

<br>

## ✅ 힙의 핵심 연산  
<hr>

힙은 보통 **배열 기반 이진 힙**으로 구현하며, 대표적인 연산은 다음과 같다.

### 💡 삽입(Insert)  
> [!NOTE]  
> - 새 원소를 **완전 이진 트리를 유지**하기 위해 트리의 가장 마지막 위치(배열의 맨 뒤)에 삽입한다. 
> - 이후 부모와 값을 비교하며 힙 속성이 만족될 때까지 위로 올려보내는 **heapify up (percolate up)** 과정을 반복한다. 
```java
public void insert(int value) {
    int current = size;
    // 현재 배열의 가장 마지막에 새로운 값 추가
    heap[current] = value;
    // 다음 원소 들어올 인덱스로 증가
    size++;

    // 부모보다 작은 동안 위로 올림 (current == 0이면(root인 경우) 진행 안 함)
    while (current > 0 && heap[current] < heap[(current - 1) / 2]) {
        //  SWAP
        int temp = heap[current];
        heap[current] = heap[(current - 1) /2];
        heap[(current - 1) /2] = heap[current];
        // 교체한 위치로 인덱스 이동
        current = (current - 1) / 2;
    }
}
```


### 💡 삭제(Delete, 보통 루트 삭제)  
> [!NOTE]  
> - 보통 루트(최대 힙: 최댓값, 최소 힙: 최솟값)를 제거하는 연산이 핵심이다. 
> - 루트 자리에 마지막 원소를 가져다 놓은 뒤, 자식들과 비교하며 힙 속성이 만족될 때까지 아래로 내려보내는 **heapify down (percolate down)** 을 수행한다. 
```java
public int delete() {
    if (size == 0) {
        throw new IllegalStateException("Heap is empty");
    }

    // 루트 노드 값 추출
    int min = heap[0];
    // 가장 마지막 값을 루트로 올림
    heap[0] = heap[size - 1];
    // 마지막 인덱스 감소
    size--;

    // 루트 노드부터 비교
    int i = 0;
    while (true) {
        // 현재 값과 자식들을 비교
        int smallest = i;
        int left = 2 * i + 1;
        int right = 2 * i + 2;

        // 자식이 배열의 범위 안인지 체크한 뒤, 현재값보다 작은지 체크
        if (left < size && heap[left] < heap[smallest]) {
            smallest = left;
        }
        if (right < size && heap[right] < heap[smallest]) {
            smallest = right;
        }

        // 최소값이 현재값이 아니라 자식인 경우 SWAP 후 재수행
        if (smallest != i) {
            int temp = heap[i];
            heap[i] = heap[smallest];
            heap[smallest] = heap[i];
            i = smallest;
        } else {
            // 현재값이 자식 값들 보다 작다면 종료
            break;
        }
    }

    // 루트 노드에서 추출한 값 반환
    return min;
}
```

### 💡 조회(Peek)  
> [!NOTE]  
> - 루트 노드를 확인하는 연산으로, 최대 힙에서는 최댓값, 최소 힙에서는 최솟값을 반환한다. 
> - 힙 구조를 변경하지 않으므로 **시간 복잡도는 O(1)** 이다. 
```java
public int peek() {
    if (size == 0) {
        throw new IllegalStateException("Heap is empty");
    }

    // 루트 노드에서 추출한 값 반환
    return heap[0];
}
```

### 💡 힙 구성(Build Heap)  
> [!NOTE]  
> - 임의의 배열을 힙 구조로 만드는 연산이다. 
> - 배열의 중간 인덱스부터 역순으로 heapify down을 수행하면 전체 배열을 **O(n)** 시간에 힙으로 만들 수 있다(Floyd의 알고리즘). 
```java
private void buildHeap() {
    // 마지막 내부 노드부터 역순으로 heapifyDown해야하지만, 마지막 level의 노드들은 leaf노드로 child가 없기 때문에 마지막 전 level의 가장 오른쪽 원소부터 진행
    // --> 배열의 중간인덱스가 자식을 가지고 있는 가장 마지막 원소가 됨.
    for (int i = ((size - 1) - 1) / 2; i >= 0; i--) {
        while (true) {
            // 현재 값과 자식들을 비교
            int smallest = i;
            int left = 2 * i + 1;
            int right = 2 * i + 2;

            // 자식이 배열의 범위 안인지 체크한 뒤, 현재값보다 작은지 체크
            if (left < size && heap[left] < heap[smallest]) {
                smallest = left;
            }
            if (right < size && heap[right] < heap[smallest]) {
                smallest = right;
            }

            // 최소값이 현재값이 아니라 자식인 경우 SWAP 후 재수행
            if (smallest != i) {
                int temp = heap[i];
                heap[i] = heap[smallest];
                heap[smallest] = heap[i];
                i = smallest;
            } else {
                // 현재값이 자식 값들 보다 작다면 종료
                break;
            }
        }
    }
}
```

<br>

## ✅ 힙의 시간 복잡도  
<hr>

| 연산           | 시간 복잡도 | 설명 |
|--------------|-----------|------|
| 삽입(Insert)   | O(log n) | 완전 이진 트리의 높이가 log n 이므로, 최악의 경우 루트까지 올라가며 swap을 수행한다. |
| 삭제(Delete)   | O(log n) | 루트에 마지막 원소를 올린 뒤, 자식 방향으로 내려가며 heapify down을 수행한다. |
| 최댓값/최솟값 조회(Peek) | O(1) | 루트 노드를 바로 확인 가능하다. |
| 힙 구성(Build Heap) | O(n) | 배열 전체에 대해 bottom-up heapify를 수행하는 Floyd 알고리즘을 사용한다. |

> [!TIP]  
> 힙은 “검색” 자체에 최적화된 자료구조는 아니며, **최댓값/최솟값 하나를 빠르게 꺼내는 것**에 최적화되어 있다. 정렬된 순서 전체가 필요하면 힙 정렬 또는 다른 정렬 알고리즘을 사용하는 것이 일반적이다.
<br>

## ✅ 힙의 활용 예시  
<hr>

> [!TIP]  
> - 우선순위 큐(Priority Queue): 우선순위를 기준으로 가장 높은(또는 낮은) 우선순위의 작업을 빠르게 꺼내야 할 때, 내부 구현으로 힙을 사용하는 경우가 많다. 
> - 힙 정렬(Heap Sort): 배열을 힙으로 구성한 뒤, 루트(최댓값/최솟값)를 반복적으로 꺼내면서 정렬을 수행하는 알고리즘이다.
> - K번째 최댓값/최솟값 찾기, 여러 정렬된 리스트 병합, 실시간 랭킹 시스템 등에서도 힙이 자주 사용된다.

<br>

**출처**  
- [Heap Data Structure - GeeksforGeeks](https://www.geeksforgeeks.org/dsa/heap-data-structure/)
- [Heap (data structure) - Wikipedia](https://en.wikipedia.org/wiki/Heap_(data_structure))
- [Binary heap - Wikipedia](https://en.wikipedia.org/wiki/Binary_heap)
- [힙(Heap)이란? Max Heap, Min Heap](https://code-lab1.tistory.com/432)
- [Delete Element from Heap - Programiz](https://www.programiz.com/dsa/heap-data-structure)