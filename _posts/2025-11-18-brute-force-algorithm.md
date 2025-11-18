---
title: "[알고리즘] 브루트포스 알고리즘(Brute Force Algorithm)"
date: 2025-11-18 16:55:00 +0900
last_mod: 2025-11-18
categories: [알고리즘]
tags: [브루트포스 알고리즘, 백준, 2798번, 2309번]
---

브루트포스 알고리즘(Brute Force Algorithm)은 가능한 모든 경우의 수를 전부 탐색하여 답을 찾는 방식이다.

brute(신체적인 힘[폭력]에만 의존하는) + force(힘)의 이름 그대로 **무식하게 밀어붙인다**는 의미이다.

4자리의 비밀번호가 있을 때 0000부터 9999까지 모든 경우를 시도해본다거나, 배열에서 특정 합을 만드는 두 수를 찾는 문제 등에서 사용하는 예를 들 수 있다.

구현이 매우 간단하고 모든 경우를 검사하므로 정확성을 보장한다.

하지만, 입력 크기가 커지면 계산량이 폭발적으로 증가하기 때문에 효율성은 낮다.

## 실전

![Image](/assets/images/2025-11-18/2025-11-18-baekjoon-2798.png)

[https://www.acmicpc.net/problem/2798](https://www.acmicpc.net/problem/2798)

모든 경우의 수를 보며 M을 넘지 않으면서 M에 최대한 가까운 카드 3장의 합을 구하면 된다.

3장의 카드로 이루어지는 조합을 찾아야 하므로 3중 for 문으로 구현할 생각을 했다.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int N, M;
    cin >> N >> M;
    
    vector<int> card(N);
    for (int i = 0; i < N; i++) {
        cin >> card[i];
    }
    
    int optimalSum = 0;
    for (int i = 0; i < card.size(); i++) {
        for (int j = i + 1; j < card.size(); j++) {
            for (int k = j + 1; k < card.size(); k++) {
                if (card[i] + card[j] + card[k] <= M && optimalSum < card[i] + card[j] + card[k]) {
                    optimalSum = card[i] + card[j] + card[k];
                }
            }
        }
    }
    
    cout << optimalSum;
    
    return 0;
}
```

다른 문제를 하나 더 풀어보자.

![Image](/assets/images/2025-11-18/2025-11-18-baekjoon-2309.png)

[https://www.acmicpc.net/problem/2309](https://www.acmicpc.net/problem/2309)

가능한 정답이 여러 가지인 경우에는 아무거나 출력해도 되기 때문에, 일곱 난쟁이의 키의 합이 100이므로 모든 난쟁이의 키의 합에서 100을 뺀 후 해당 값을 만족시키는 조합을 찾았다.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    vector<int> input(9);
    int sum = 0;
    
    for (int i = 0; i < 9; i++) {
        cin >> input[i];
        sum += input[i];
    }
    
    int tolerance = sum - 100;
    
    for (int i = 0; i < 9; i++) {
        for (int j = i + 1; j < 9; j++) {
            if (input[i] + input[j] == tolerance) {
                input.erase(input.begin() + i);
                input.erase(input.begin() + j - 1);
                
                sort(input.begin(), input.end());
                
                for (int i : input) {
                    cout << i << "\n";
                }
                
                return 0;
            }
        }
    }
    
    return -1;
}
```
