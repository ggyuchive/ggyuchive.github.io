---
layout: post
title: "2022 1회차 쇼미더코드 후기 (금손뱃지 획득)"
date: 2022-04-02
categories:
- Record
tags:
- 대회
---
### 대회 결과  
4월 2일에 원티드에서 주관하는 쇼미더코드 대회에 참여했다$.$  
결과는 **3솔브/58분** 으로 금손뱃지를 획득했다.  

![image](https://i.imgur.com/JPlt9Zf.jpeg)
![image](https://i.imgur.com/ThqpYwR.jpg)  
상위 몇%가 금손뱃지를 얻는지는 모르겠지만 문제가 쉬웠어서 제한시간 3시간 안에 모두 풀어야 가능성이 있었을 것 같다.  

문제는 [여기](https://www.acmicpc.net/category/detail/3097) 에서 확인할 수 있다. 

### A. 물약 구매 (AC/+11min)  
c++에 있는 next_permutation()을 활용하면 쉽게 풀 수 있는 문제이다.  
시간복잡도는 $ O(N \times N!) $ 이고, $ N \leq 10 $ 이므로 연산 횟수는 약 3600만회이다.  
next_permutation은 코딩테스트의 순열문제에서 쓸 일이 많으니 익혀두도록 하자 (직접 구현하려면 꽤 시간이 많이 든다.)

<details>
<summary> 소스코드 보기 </summary>
<div markdown="1">
    
~~~ c++
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);cin.tie(NULL);cout.tie(NULL);
    
    int n; cin >> n;
    int cost[n];
    vector<pair<int,int>> dis[n];
    for (int i = 0; i < n; i++) cin >> cost[i];
    for (int i = 0; i < n; i++) {
        int p; cin >> p;
        for (int j = 0; j < p; j++) {
            int a, b; cin >> a >> b; a--;
            dis[i].push_back({a,b});
        }
    }
    vector<int> perm;
    int ans = 2147483647;
    for (int i = 0; i < n; i++) perm.push_back(i);
    do {
        int c = 0;
        int discount[n];
        for (int i = 0; i < n; i++) {
            discount[i] = 0;
        } 
        for (int i = 0; i < n; i++) {
            int p = perm[i];
            if (cost[p]-discount[p] <= 0) c+=1;
            else c += (cost[p]-discount[p]);
            for (int j = 0; j < dis[p].size(); j++) {
                discount[dis[p][j].first] += dis[p][j].second;
            }
        }
        if (ans > c) ans = c;
    } while (next_permutation(perm.begin(), perm.end()));
    cout << ans;
}
~~~

</div>
</details>

* * *

### B. 숫자 이어 붙이기 (AC/+42min)  
트리에서 탐색을 하는 문제다. $ N \leq 1000 $, $ Q \leq 1000 $이므로 쉽게 풀 수 있다.  
각 쿼리 당 BFS를 실행해줘도 $ O(NQ) $이므로 시간 내에 통과한다. 만약 N, Q가 1만 이상일 경우에는 다른 테크닉을 써야할 것이다.  
한 가지 함정은 $ A_{i} \leq 10^9 $ 이고, 이어붙인 숫자의 최댓값은 10,000,000,001,000,000,000이다.  
long long int의 최댓값은 9,223,372,036,854,775,807으로, 범위를 넘어선다. 그러므로 unsigned long long int를 써야 커버가 가능하다.

    
<details>
<summary> 소스코드 보기 </summary>
<div markdown="1">
    
~~~ c++
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);cin.tie(NULL);cout.tie(NULL);
    unsigned long long mod = 1000000007;
    
    int n, q; cin >> n >> q;
    unsigned long long arr[n];
    unsigned long long ten[n];
    vector<int> edge[n];
    for (int i = 0; i < n; i++) cin >> arr[i];
    for (int i = 0; i < n; i++) {
        ten[i] = 1;
        unsigned long long p = arr[i];
        while (p != 0) {
            ten[i]*=10; p/=10;
        }
    }
    for (int i = 0; i < n-1; i++) {
        int a,b; cin >> a >> b;
        a--; b--;
        edge[a].push_back(b);
        edge[b].push_back(a);
    }
    for (int i = 0; i < q; i++) {
        int s, end; cin >> s >> end;
        s--; end--;
        bool visited[n];
        for (int i = 0; i < n; i++) visited[i]=false;
        queue<pair<int,unsigned long long>> q;
        q.push({s,arr[s]});
        visited[s]=true; int tag = 0; unsigned long long ans;
        while (!q.empty()) {
            int start = q.front().first;
            unsigned long long num = q.front().second;
            q.pop();
            for (int i = 0; i < edge[start].size(); i++) {
                int e = edge[start][i];
                if (visited[e] == false) {
                    unsigned long long newnum = (num*ten[e]+arr[e])%mod;
                    q.push({e, newnum});
                    visited[e] = true;
                    if (e == end) {ans = newnum; tag = 1; break;}
                }
            }
            if (tag == 1) break;
        }
        if (s == end) cout << arr[s]%mod << '\n';
        else cout << ans << '\n';
    }
}
~~~

</div>
</details>

* * *

### C. 나는 정말 휘파람을 못 불어 (AC/+58min)
보자마자 누적합을 써야 한다는 것을 알았다. H를 기준으로 왼쪽의 W 개수, 오른쪽의 E 개수를 계산해줘야 한다.  
하나의 H를 기준으로 왼쪽의 W 개수가 $ w $개, 오른쪽의 E 개수가 $ e $개일 경우, 
이 H를 포함하는 '유사휘파람 문자열'의 개수는 $ w \times (2^e-e-1) $개이다.  
$ P_{i} = 2^i-i-1 $ , $ P_{i+1} = 2 \times P_{i} + i $ 이다.  
즉 누적합으로 (1)구간의 W 개수, (2)구간의 E 개수를 전처리하고, DP로 (3)E 개수가 $ e $개일 때 $ 2^e-e-1 $ 값을 전처리한다.
시간복잡도는 $ O(N) $이다.


<details>
<summary> 소스코드 보기 </summary>
<div markdown="1">
    
~~~c++
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);cin.tie(NULL);cout.tie(NULL);
    
    long long mod = 1000000007;
    int n; cin >> n;
    string s; cin >> s;
    long long wsum[n];
    long long esum[n];
    long long P[n+1];
    for (int i = 0; i < n; i++) {
        wsum[i]=0; esum[i]=0;
        if (s[i]=='W') wsum[i]=1;
        if (s[i]=='E') esum[i]=1;
    }
    for (int i = 1; i < n; i++) {
        wsum[i] += wsum[i-1];
        esum[i] += esum[i-1];
    }
    long long ans = 0;
    P[0] = 0;
    for (int i = 1; i < n+1; i++) {
        P[i] = (P[i-1]*2+ i-1)%mod;
    }
    for (int i = 0; i < n; i++) {
        if (s[i] == 'H') {
            long long wcount; long long ecount;
            if (i!=0) wcount = wsum[i-1];
            else wcount = 0;
            ecount = esum[n-1]-esum[i];
            ans = (ans + (wcount * P[ecount])%mod)%mod;
        }
    }
    cout << ans;
}
~~~

</div>
</details>
