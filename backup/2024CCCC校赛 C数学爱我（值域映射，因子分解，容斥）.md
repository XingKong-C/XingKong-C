# 题目重述
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;给定一个长度为n的数组a, 对于任意i, j(1 <= j < i <= n), 求出所有gcd(ai, aj)的和.
# 题目约束
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;第一行一个正整数n (2 <= n <= 80000), 第二行n个正整数,a1 a2 a3 ... (1 <= ai <= 100000).
# 分析
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;直接暴力求解的复杂度是O(n^2)的肯定会爆掉，所以从其他角度入手，我们观察到ai的值域在十的五次方内，考虑在值域方面做文章。 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;考虑gcd的特性是俩个数所有因子里重合且最大的数，所以我们可以把每个数字的每个因子出现的次数存储下来。 然后使用C(n, 2)计算出现的次数再乘以对应的因子大小。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;但是我们发现每俩个数之间除了最大公因子以外，重合的因子有很多，会导致多余的计算，这时候我们注意到重合的因子其实都是最大公因子的因子，所以对最大公因子进行因子分解给每一个因子对应的组合数减去最大公因子对应的组合数即可；
# 完整思路
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用一个数组存储每个因子出现的次数，然后计算对应的组合数，然后用在值域上从大到小枚举，当前值要是存在组合数，那么就是这些数字的最大公因子，答案加上这些数，然后对当前值进行因子分解，在分解出来的因子对应数组的位置减去当前值对应的组合数，这样就不会导致多余计算了
# 代码如下
```cpp
#pragma GCC optimize(2)
#include<bits/stdc++.h>
using namespace std;

#define ll long long

//映射，排列组合，从值域考虑, 1和a[i]会不会重复掉，减的是什么数
ll ji[100005];
void solve(){
    ll n;
    cin >> n;
    vector<ll>a (n + 1);
    for(int i = 1; i <= n; i ++){
        cin >> a[i];
        for(int j = 1; j * j < a[i]; j ++ ){
            if(a[i] % j == 0){
                ji[j] ++;
            }
        }
        for(int j = sqrt(a[i]); j >= 1; j -- ){
            if(a[i] % j == 0){
                ji[a[i] / j] ++;
            }
        }
    }
    for(int i = 1; i <= 100000; i ++){
        ji[i] = ji[i] * (ji[i] - 1) / 2;
    }
    ll ans = 0;
    for(ll i = 100000; i >= 1; i --){
        if(ji[i] == 0) continue;
        ans += (ji[i]) * i;
        for(int j = 1; j * j < i; j ++ ){
            if(i % j == 0){
                ji[j] -= ji[i];
            }
        }

        for(int j = sqrt(i); j >= 1; j -- ){
            if(i % j == 0){
                ji[i / j] -= ji[i];
            }
        }
    }
    cout << ans <<endl;
}

int main()
{
    ios :: sync_with_stdio(false);
    cin.tie(nullptr);

    int t = 1;
    //    cin >> t;

    while(t--){
        solve();
    }
    return 0;
}
/*   /\_/\
*   (= ._.)
*   / >  \>
*/
```