# Dijkstra典题

## [原题链接](https://codeforces.com/contest/2014/problem/E)

## 题目大意
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;有 n 个城市，m 条路，h 个城市中有马，骑马可以路程减半，A在第 1 个城市，B在第 n 个城市，双方同时出发，求最早碰面的时间。
eg：必须在城市中碰面，碰不到输出 -1 .

## 思路
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;由于是双方都出发，考虑使用 Dijkstra 求得 A 和 B 到每个城市的最短路径，遍历每个城市对 A 和 B 的最短路径取max求得最优答案。由于存在马可以使路程减半，故 Dijkstra 的实现需要考虑到马的存在，Dijkstra 的本质是在最优路的情况下推出其他路的最优，所以考虑先求出到有马城市的最优路，在将有马城市标记，推入优先队列。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;简而言之这道题需要当成 2n 个城市进行 Dijkstra 的遍历即可，jiangly使用 2 * i 和2 * i +1 来表示是否是有马情况的最优路，我是则是给打上 jud 标记 记录是否是有马情况的最短路。

## 代码如下
```c++
#include "bits/stdc++.h"
using namespace std;

#define ll long long
#define f(x)for(ll i = 1; i <= x; i++)
constexpr ll inf = 1e18;

ll n, m, h;
void solve(){
    cin >> n >> m >> h;

    vector<bool> horse(n + 1, false);
    f(h){
        ll x;
        cin>>x;
        horse[x] = true;
    }

    vector<vector<pair<ll, ll> > > head(n + 1);
    for(int i = 1; i <= m; i++){
       int u, v, w;
       cin >> u >> v >> w;
       head[u].emplace_back(v, w);
       head[v].emplace_back(u, w);
    }

    auto dijkstra = [&](ll s){
       priority_queue<array<ll, 3>, vector<array<ll, 3>>, greater<> >pq;
       vector<ll>dis1(n + 1, inf);
       vector<ll>dis2(n + 1, inf);
       pq.push({0, s, 0});

       while(!pq.empty()){
           auto [d, u, jud] = pq.top();
            pq.pop();
            if(jud == 0){
                if(dis1[u] != inf) { continue; }
                dis1[u] = d;

                if(horse[u] ){
                    pq.push({d, u, 1});
                }

                for(auto [y, w] : head[u]){
                    pq.push({d + w, y, 0});
                }
            }
            else {
                if(dis2[u] != inf)continue;
                else dis2[u] = d;

                for(auto [y, w] : head[u]){
                    pq.push({d + w / 2, y, 1});
                }
            }
       }
        auto dis = dis1;
        f(n) dis[i] = min(dis1[i], dis2[i]);
      return dis;
    };

    auto d1 = dijkstra(1);
    auto dn = dijkstra(n);

    auto ans = inf;
    f(n)ans = min (ans, max(d1[i], dn[i]) );
    if(ans == inf)
        ans = -1;
    cout << ans << '\n';
}
int main (){
    ios ::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--){
        solve();
    }
    return 0;
}
//优先队列注意一定要出队
//赋值是等于号，不是判断相等
//对ans求最小，对路径取较大，清楚每一步在干什么
```