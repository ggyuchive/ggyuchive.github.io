---
layout: post
title: "[AL] 비트연산을 이용한 Radix Sort"
date: 2021-04-15
categories:
- univ_assessment
tags:
- Algorithm
---
### 문제  $ $
$10^3 \leq N \leq 10^8$, $-10^8 \leq arr[i] \leq 10^8$ 일 때 정렬 라이브러리를 사용하지 않으면서 빠르게 정렬하는 함수 코드를 작성하는 과제이다.

### 문제 탐색
이론상 (시간 복잡도 상) 가장 빠른 정렬은 Radix Sort, Count Sort이다.  
배열의 크기가 $N$일 때, Quick Sort, Merge Sort, Heap Sort 등등은 $O(NlogN)$이지만
Radix Sort, Count Sort는 무려 $O(N)$이다.  
물론 상황에 따라서 (숫자의 범위, 배열의 크기, 배열의 상태(Almost Sorted, Randomly, ...))에 따라 $O(NlogN)$ 정렬 방식이 $O(N)$ 방식보다 더 빠르게 작동할 수 있다.  
왜냐하면 Radix Sort가 $O(N)$이라고 하지만 실제로는 $O(aN)$ ($a$는 숫자의 범위에 따라 커지는 상수)이고, 무엇보다 메모리를 많이 사용한다.  

만약 문제 상황이 $10^3 \leq N \leq 10^8$, $-10^8 \leq arr[i] \leq 10^8$ 라고만 주어졌을 때, 비트 연산을 이용한 LSD Radix Sort 으로 정렬을 하면 빠를 것이라고 생각하였다.  
Radix Sort에도 MSD(가장 높은 자릿수부터 계산), LSD(가장 낮은 자릿수부터 계산)이 있는데, LSD가 구현이 훨씬 쉽기에 선택하였다.  
만약 10진수 Radix Sort로 구현하면, $O(16N)$의 시간복잡도가 나온다.
이 때 $N = 10^6$ 이라면, $logN = 20$ 이므로 Quick Sort, Merge Sort와 Radix Sort가 별반 차이가 없다고 생각이 든다. 오히려 Radix Sort는 10번의 동적할당을 해야하므로 더 느릴수도 있다.



그래서 문제상황의 모든 $N$에 대하여 Radix Sort가 Quick Sort, Merge Sort보다 더 빠르게 작동할 수는 없을까 하는 생각을 하다가 비트 연산을 생각하게 되었다.  
$10^8 = (101111101011110000100000000)_2$ 이다. 즉, 뒤의 27비트만 고려해도 된다!  
음수일 경우에는 $-x = $ ~ $x+1$ 이므로, 앞의 5개 비트가 11111이다. 또한 뒤의 27비트가 클 수록 큰 수이다.
9비트 단위로 끊어서 $0$ ~ $2^9-1$일 때로 나눠서 넣어주면, $2^9$번의 동적할당을 해야하는 단점이 있긴 하지만, $O(6N)$까지 시간복잡도가 줄어든다.

만약 문제상황의 메모리 제한이 적지 않다면 Bitwise Radix Sort는 굉장히 빠른 정렬 방법이다.  


### 소스 코드
```c++
#include <cstdio>
#include <queue>
using namespace std;

void Sort(int *arr, int n) {
    queue<int> que[512]; // que count
    int i, j, count;

    for (i = 0; i < 3; i++) {  // loop count
        for (j = 0; j < n; j++) que[(arr[j] >> (i * 9)) & 511].push(arr[j]);
        count = 0;
        for (j = 0; j < 512; j++) {
            while (!que[j].empty()) {
                arr[count] = que[j].front(); 
                que[j].pop(); 
                count++;
            }
        }
    }
    
    for (j = 0; j < n; j++) {
        if ((arr[j] >> 27) == 0) que[0].push(arr[j]); // 양수일 때
        else que[1].push(arr[j]); // 음수일 때
    }
    j = 0;
    
    while (!que[1].empty()) {
        arr[j] = que[1].front(); que[1].pop(); j++;
    }
    while (!que[0].empty()) {
        arr[j] = que[0].front(); que[0].pop(); j++;
    }
}
```