---
title: "[C++] STL queue"
date: 2025-09-03 19:10:00 +0900
last_mod: 2025-09-05
categories: [C++]
tags: [C++, STL, queue, priority_queue, 백준, 1158번, 2075번]
---

C++ STL의 queue는 **먼저 들어온 원소가 가장 먼저 나가는 FIFO(First In, First Out) 구조를 구현한 컨테이너 어댑터(container adapter)** 이다.<br>

기본 컨테이너는 `deque`이며, queue 자체가 컨테이너는 아니다.

## 주요 멤버 함수

| 함수명    | 설명                 |
| --------- | -------------------- |
| push(val) | 맨 뒤에 원소 삽입    |
| pop()     | 맨 앞 원소 제거      |
| front()   | 맨 앞 원소 반환      |
| back()    | 맨 뒤 원소 반환      |
| empty()   | 큐가 비어있는지 확인 |
| size()    | 원소 개수 반환       |

## 사용 예시

```cpp
#include <iostream>
#include <queue>
using namespace std;

int main() {
    queue<int> q;

    q.push(10);
    q.push(30);
    q.push(20);

    cout << "size: " << q.size() << "\n";
    cout << "front: " << q.front() << "\n";
    cout << "back: " << q.back() << "\n";

    q.pop();

    cout << "pop 이후 front: " << q.front() << "\n";

    while (!q.empty()) {
        cout << q.front() << " ";
        q.pop();
    }

    return 0;
}
```

🔽 출력 :

```plaintext
size: 3
front: 10
back: 20
pop 이후 front: 30
30 20
```

## 실전

![Image](/assets/images/2025-09-03/2025-09-03-baekjoon-1158.png)

[https://www.acmicpc.net/problem/1158](https://www.acmicpc.net/problem/1158)

queue 개념을 이용하면 쉽게 풀 수 있는 문제인 요세푸스 문제를 풀어보자.

원을 이루면서 앉아 있는 형태를 circular queue라고 생각하고 문제를 풀면 될 것 같다.

```cpp
#include <iostream>
#include <queue>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(NULL);

    int N, K;
    cin >> N >> K;

    queue<int> josephus;
    for (int i = 1; i <= N; i++) {
        josephus.push(i);
    }

    cout << "<";

    while (josephus.size() != 1) {
        for (int i = 1; i < K; i++) {
            josephus.push(josephus.front());
            josephus.pop();
        }
        cout << josephus.front() << ", ";
        josephus.pop();
    }

    cout << josephus.front() << ">";

    return 0;
}
```

<br>

# priority_queue

한편, `<queue>` 헤더 파일에는 queue 이외에 `priority_queue`도 포함되어 있다. C++ STL의 `priority_queue`는 우선순위 큐를 구현한 컨테이너 어댑터로, 내부적으로 힙(heap) 자료구조를 사용하여 원소들을 정렬한다.

기본적으로는 최대 힙(max-heap) 구조이며, 가장 큰 값이 top에 위치한다.

## 주요 멤버 함수

| 함수명    | 설명                                       |
| --------- | ------------------------------------------ |
| push(val) | 원소 삽입                                  |
| pop()     | top() 원소 제거                            |
| top()     | 현재 큐에서 가장 우선순위가 높은 원소 반환 |
| empty()   | 큐가 비어있는지 확인                       |
| size()    | 원소 개수 반환                             |

## 최소 힙(min-heap)으로 만들기

```cpp
#include <queue>

priority_queue<int, vector<int>, greater<int>> min_heap;
```

### 주의

`sort`에서는 greater를 인자로 전달하면 내림차순 정렬이 됐었는데, `priority_queue`에서는 **비교 연산자가 true일 때 우선순위가 낮다** 는 의미이기 때문에 greater를 쓰면 작은 값이 먼저 나오는 최소 힙이 된다.

## 실전

![Image](/assets/images/2025-09-05/2025-09-05-baekjoon-2075.png)

[https://www.acmicpc.net/problem/2075](https://www.acmicpc.net/problem/2075)

- N개의 최댓값만 유지하는 최소 힙(min-heap)을 사용한다.

- N개 이하일 때는 그냥 삽입, 이후에는 현재 숫자가 heap의 top(N개 중 가장 작은 값)보다 크면 top을 제거하고 새 값을 삽입한다.

- 그 결과 가장 큰 수들 중 상위 N개만 남게 되고, 이중 가장 작은 값이 N번째 큰 수이다.

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(NULL);

    int N;
    cin >> N;

    priority_queue<int, vector<int>, greater<int>> min_heap;

    for (int i = 0; i < N * N; i++) {
        int number;
        cin >> number;

        if (min_heap.size() < N) {
            min_heap.push(number);
        }
        else {
            if (number > min_heap.top()) {
                min_heap.pop();
                min_heap.push(number);
            }
        }
    }

    cout << min_heap.top();

    return 0;
}
```
