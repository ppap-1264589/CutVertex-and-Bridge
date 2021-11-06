# [Trang chủ](https://ppap-1264589.github.io/interesting-solution)

## Các bài toán
1) [Đường trọng yếu (tìm cầu)](https://cses.fi/problemset/task/2076)

2) [Thành phố trọng yếu (tìm khớp)](https://cses.fi/problemset/task/2077)

3) [Bắt trộm (tìm khớp và cầu + kĩ thuật nâng cao)](https://oj.vnoi.info/problem/nkpolice)

## Cấu trúc dữ liệu và giải thuật: 

1) [VNOI](https://vnoi.info/wiki/algo/graph-theory/Depth-First-Search-Tree.md#b%C3%A0i-to%C3%A1n-2)

2) [Cp-Algorithms - Tìm cầu](https://cp-algorithms.com/graph/bridge-searching.html)

3) [Cp-Algorithm - Tìm khớp](https://cp-algorithms.com/graph/cutpoints.html)

Trong các code dưới đây, chủ yếu dùng cách làm đã được hướng dẫn ở VNOI. Cp-Algorithm chỉ là nguồn tham khảo để dễ hình dung tính đúng đắn của thuật toán

## Code:

1) [Đường trọng yếu](https://ideone.com/fBuGqH)

2) [Thành phố trọng yếu](https://ideone.com/JDpfht)

3) [Bắt trộm](https://ideone.com/F5FsLp)

Preview cách giải bài "Bắt Trộm". Cũng có thể vận dụng phương pháp quy hoạch các đỉnh bằng Sparse Table như VNOI đã hướng dẫn

```c++
#include <bits/stdc++.h>
#pragma GCC optimize("Ofast")
#pragma GCC optimize ("unroll-loops")
#pragma GCC target("sse,sse2,sse3,ssse3,sse4,popcnt,abm,mmx,avx,tune=native")
 
#define Task "A"
#define up(i,a,b) for (int i = (a); i <= (b); i++)
using namespace std;
 
const int maxn = 100001;
vector<int> a[maxn];
vector<int> under[maxn];
int Tin[maxn], Tout[maxn], low[maxn], idx[maxn], par[maxn];
bool isJoint[maxn];
int tdfs, step;
 
void DFS(int u){
    low[u] = idx[u] = ++tdfs;
    Tin[u] = ++step;
    int child = bool(u != 1);
    for (int v : a[u]){
        if (par[u] == v) continue;
        if (!idx[v]){
            par[v] = u;
            DFS(v);
 
            low[u] = min(low[u], low[v]);
            under[u].emplace_back(v);
            if (low[v] >= idx[u]) child++;
        }
        else low[u] = min(low[u], idx[v]);
    }
    isJoint[u] = bool(child >= 2);
    Tout[u] = ++step;
}
//make the DFS tree
//Tin[i] is the time begin exploring i'th node
//Tout[i] is the time terminate exploring i'th node
 
bool inDfs(int u, int v){
    return (Tin[u] <= Tin[v] && Tout[v] <= Tout[u]);
}  
// check if v is 'under' u (is child of u in DFS tree)
 
bool toBridge(){
    int A,B,C,D;
    cin >> A >> B >> C >> D;
    if (Tin[C] > Tin[D]) swap(C, D);
    if (low[D] < idx[D]) return 1;          // not a bridge ?
    return (inDfs(D, A) == inDfs(D, B));
}
 
bool comp(int x, int y){
    return (Tin[x] < Tin[y]);
}
 
int get_parent(int u, int v){
    if (!inDfs(u, v)) return -1;
    // check if (v not 'under' u) ?
    
    int x = under[u][upper_bound(under[u].begin(), under[u].end(), v, comp) - under[u].begin() - 1];
    // combine comp() with upper_bound is more effecient than sort-and-find
    
    if (low[x] < idx[u]) return -1; 
    // if x still can comeback to parent of u ?
    return x;
}
 
bool toJoint(){
    int A, B, C;
    cin >> A >> B >> C;
    if (!isJoint[C]) return 1;
    return (get_parent(C, A) == get_parent(C, B));
}
 
signed main (){
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    if (fopen(Task".inp", "r")){
        freopen (Task".inp", "r", stdin);
        freopen (Task".out", "w", stdout);
    }
 
    int n,m;
    cin >> n >> m;
    up(i, 1, m){
        int u,v;
        cin >> u >> v;
        a[u].emplace_back(v);
        a[v].emplace_back(u);
    }
    DFS(1);
 
    int tt;
    cin >> tt;
    int query;
    bool flag;
    while (tt--){
        cin >> query;
        if (query == 1) flag = toBridge();
        else flag = toJoint();
        cout << (flag ? "yes" : "no") << "\n";
    }
}
```
