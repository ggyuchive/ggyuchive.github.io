---
layout: post
title: "성균관대학교 프로그래밍 경진대회 후기"
date: 2022-02-26
categories:
- Record
tags:
- 대회
---
### 대회 결과  
성균관대 프로그래밍 대회에 참가했다.  
결과는 **3솔브/10등**이다.(휴학생 포함, 나도 (군)휴학생이다.)
![image](https://i.imgur.com/0c3hYlg.jpeg)

문제 세트는 [여기](https://www.acmicpc.net/category/detail/3031)서 확인할 수 있다.  

### A. 내 뒤에 나와 다른 수 [(백준 24523)](https://www.acmicpc.net/problem/24523) (AC/+4min)  

투포인터 느낌으로 풀었다.  
pnt를 계속 증가시키면서 찾기 때문에 $ O(N) $이다.

<코드>  

``` c++
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
    // ans[i] 입력이 없을 때 -2로 초기화해 -1을 출력하도록 설정  
    for (int i = 0; i < n; i++) {
        if (n <= ans[i]) ans[i] = -2; 
    }
    for (int i = 0; i < n; i++) cout << ans[i]+1 << ' ';
}
```

### B. 아름다운 문자열 [(백준 24524)](https://www.acmicpc.net/problem/24524) (AC/+11min)  

전형적인 그리디 문제이다.  
문자열 T의 모든 문자가 다르다는 것이 포인트이다.  
아래 풀이는 $ O(\lvert S \rvert \lvert T \rvert) $ 에 풀린다.  
여기서 배열 a[i]는 T의 i-1번째 문자까지 같은 문자열의 개수이다.  

<코드>  

``` c++
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);cin.tie(NULL);cout.tie(NULL);
    string s; string t;
    cin >> s >> t;
    int n = s.size(); int m = t.size();
    int a[m+1];
    for (int i = 0; i < m+1; i++) a[i]=0;
    a[0] = 2147483647;
    // O(ST)  
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (s[i] == t[j]) {
                if (a[j] > 0) {
                    a[j+1]+=1;
                    a[j]-=1;
                }
            }
        }
    }
    cout << dp[m];
}
```

### C. SKK 문자열 [(백준 24525)](https://www.acmicpc.net/problem/24525) (WA)  

투포인터로 계속 접근하면서 정당성 증명을 못한 채 제출해 틀렸다.  
그 뒤 누적합을 써야 한다는 사실을 깨달았지만 $ O(N^2) $ 미만으로 줄이기는 어려웠다.
K의 개수가 S의 개수의 2배여야 하므로 K를 1, S를 -2, 나머지를 0으로 치환해 누적합이 0이면 된다. 
누적합이 0인 구간을 모두 파악하는 것도 naive하게 구현하면 $ O(N^2) $이 되는데,
map을 이용해 sum[i] = sum[j]가 되는지 (i~j까지 SKK 문자열인지) $ O(\log N) $에 구현이 가능하다.  
즉, 전체 시간복잡도는 $ O(N \log N) $이다.

<코드>  

``` c++
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);cin.tie(NULL);cout.tie(NULL);
    string s; cin >> s;
    int n = s.size();
    int arr[n+1];
    arr[0]=0;
    for (int i = 1; i < n+1; i++) {
        if (s[i-1] == 'K') arr[i]=1;
        else if (s[i-1] == 'S') arr[i]=-2;
        else arr[i]=0;
    }
    for (int i = 1; i < n+1; i++) arr[i] = arr[i-1]+arr[i];
    int skcount[n+1];
    skcount[0]=0;
    for (int i = 1; i < n+1; i++) {
        skcount[i] = skcount[i-1];
        if (s[i-1]=='K'||s[i-1]=='S') skcount[i]++;
    }
    map<int,int> m;
    int ans = -1;
    for (int i = 0; i < n+1; i++) {
        if (m.find(arr[i]) != m.end()) {
            int len = i-m[arr[i]];
            if (skcount[i]-skcount[m[arr[i]]] > 0 && len > ans) {
                ans = len;
            }
        }
        else m.insert({arr[i],i});
    }
    cout << ans;
} 
```

### D. 전화 돌리기 [(백준 24526)](https://www.acmicpc.net/problem/24526) (AC/+242min)  

유향 그래프에서 사이클로 빠지지 않는 출발점 노드의 개수를 출력하는 문제이다. 
그래프에 사이클이 있는지 판단하는 문제는 한 번의 DFS로 풀 수 있다.  
다만 DFS 함수 안에서 사이클을 이루는 노드를 찾아버리면 그래프의 형태에 따라 **시간 초과**가 발생한다.
DFS 역방향 간선이 있을 경우 역방향 간선을 포함하는 노드들은 사이클을 구성하므로 큐에 넣는다.  
그 후 큐에 넣은 노드들을 출발점으로 해 backedge를 써서 BFS로 탐색하면 $ O(V+E) $ 로 풀 수 있다.

<코드>  

``` c++
#include <bits/stdc++.h>
using namespace std;

bool visited[100001]; bool answer[100001];
bool finish[100001];
bool iscycle[100001];

void dfs(int s, vector<vector<int>>& edge) {
    visited[s] = true;
    for (int i = 0; i < edge[s].size(); i++) {
        int e = edge[s][i];
        if (!visited[e]) dfs(e, edge);
        else if (!finish[e]) iscycle[s] = true;
    }
    finish[s] = true;
}

int main() {
    ios_base::sync_with_stdio(false);cin.tie(NULL);cout.tie(NULL);
    
    int n, m; cin >> n >> m;
    vector<vector<int>> edge;
    vector<vector<int>> backedge;
    edge.resize(n, vector<int>());
    backedge.resize(n, vector<int>());
    
    for (int i = 0; i < m; i++) {
        int a,b; cin >> a >> b;
        edge[a-1].push_back(b-1);
        backedge[b-1].push_back(a-1);
    }
    for (int i = 0; i < n; i++) answer[i] = true;
    for (int i = 0; i < n; i++) {
        if (!visited[i]) dfs(i, edge);
    }
    
    bool vis2[n];
    for (int i = 0; i < n; i++) vis2[i]=false;
    queue<int> q;
    for (int i = 0; i < n; i++) {
        if (iscycle[i]) {
            q.push(i); vis2[i]=true; answer[i]=false;
        }
    }
    while (!q.empty()) {
        int s = q.front();
        q.pop();
        for (int i = 0; i < backedge[s].size(); i++) {
            int e = backedge[s][i];
            if (!vis2[e]) {
                vis2[e]=true; answer[e]=false;
                q.push(e);
            }
        }
    }
    int ans = 0;
    for (int i = 0; i < n; i++) {
        if (answer[i]) ans++;
    } cout << ans;
} 
```

### E. 이상한 나라의 갈톤보드 [(백준 24527)](https://www.acmicpc.net/problem/24527) (WA)  

Lazy Segtree를 써야겠다는 생각으로 처음에 접근했다.  
WA를 받은 이유는 구슬의 시작점과 끝점을 판단하는 코드가 잘못됐기 때문이다.  
20분을 남기고 정답 코드를 짤 수 있었지만 자유로운 환경이 아니기에 대회를 마쳤다.  
이 문제는 Lazy Segtree를 써도 되지만 쓸 필요는 없는 문제이다.  
업데이트 쿼리가 모두 주어지고 기댓값을 묻는 쿼리가 주어지기 때문에 누적합으로 풀린다.  
만약 두 쿼리가 섞여서 나왔다면 정해는 Lazy Segtree일 것이다.  

<코드>  

``` c++
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);cin.tie(NULL);cout.tie(NULL);
    cout << fixed;
    cout.precision(10);
    long long h; cin >> h;
    int q,r; cin >> q >> r;
    double presum[h+1];
    for (int i = 0; i < h+1; i++) presum[i]=0;

    vector<long long> comb;
    for (long long i = 0; i < h+1; i++) comb.push_back((i*(i+1))/2);
    for (int i = 0; i < q; i++) {
        long long a; double b;
        cin >> a >> b;
        int l, r;
        a--;
        if (a == comb[lower_bound(comb.begin(), comb.end(), a)-comb.begin()]) l = 0;
        else l = a-comb[lower_bound(comb.begin(), comb.end(), a)-comb.begin()-1];
        a++;
        if (a == comb[lower_bound(comb.begin(), comb.end(), a)-comb.begin()]) r = h;
        else r = h-(comb[lower_bound(comb.begin(), comb.end(), a)-comb.begin()]-a);
        double bunmo = r-l+1;
        double tosum = b/bunmo;
        presum[r]+=tosum;
        if (l!=0) presum[l-1]-=tosum;
    }
    for (int i = h-1; i >= 0; i--) {
        presum[i] = presum[i]+presum[i+1];
    }
    for (int i = 0; i < h; i++) {
        presum[i+1] = presum[i+1]+presum[i];
    }
    for (int i = 0; i < r; i++) {
        int a, b; cin >> a >> b;
        a--; b--;
        double ans = presum[b];
        if (a != 0) ans = ans - presum[a-1];
        cout << ans << '\n';
    }
} 
```
