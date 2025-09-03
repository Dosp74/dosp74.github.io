---
title: "[C++] STL queue"
date: 2025-09-03 19:10:00 +0900
last_mod: 2025-09-03
categories: [C++]
tags: [C++, STL, queue, 백준, 1158번]
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
