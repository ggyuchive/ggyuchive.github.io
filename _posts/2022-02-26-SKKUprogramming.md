---
layout: post
title: "성균관대학교 프로그래밍 경진대회 후기"
date: 2022-02-26
categories:
- Record
tags:
- 백준
- 알고리즘
- 대회
---
### 대회 결과  
성균관대 프로그래밍 대회에 참가했다.  
결과는 **3솔브/10등**이다.(휴학생 포함,나도 (군)휴학생이다.)  
![image](https://github.com/ggyuchive/ggyuchive.github.io/blob/main/image/skkuprogramming.jpg)  

문제 세트는 아래에서 확인할 수 있다.  
[성균관대 프로그래밍 대회 문제](https://www.acmicpc.net/category/detail/3031)

### A. 내 뒤에 나와 다른 수 [(백준 24523)](https://www.acmicpc.net/problem/24523) (AC/+4min) 
투포인터 느낌으로 풀었다. 
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);cin.tie(NULL);cout.tie(NULL);
    int n; cin >> n;
    int arr[n];
    for (int i = 0; i < n; i++) cin >> arr[i];
    int ans[n]; int pnt = 1;
    for (int i = 0; i < n; i++) {
        while (arr[i] == arr[pnt]) pnt++;
        ans[i] = pnt;
    }
    for (int i = 0; i < n; i++) {
        if (n <= ans[i]) ans[i] = -2; 
    }
    for (int i = 0; i < n; i++) cout << ans[i]+1 << ' ';
}
```

### B. 아름다운 문자열 [(백준 24524)](https://www.acmicpc.net/problem/24524) (AC/+11min)  

### C. SKK 문자열 [(백준 24525)](https://www.acmicpc.net/problem/24525) (WA)  

### D. 전화 돌리기 [(백준 24526)](https://www.acmicpc.net/problem/24526) (AC/+242min)  

### E. 이상한 나라의 갈톤보드 [(백준 24527)](https://www.acmicpc.net/problem/24527) (WA)
