# 动态规划

### 0-1背包

有n种物品与承重为m的背包。每种物品只有一件，每个物品都有对应的重量weight[i]与价值value[i]，求解如何装包使得价值最大。

状态：$dp[i][j]$ 表示前i个物品，容量为j的背包的最大价值

状态转移方程：

$$
dp[i][j] = max( dp[i-1][j] , dp[i-1][ j - weight[i] ] + value[i])
$$

```cpp
int main()  
{  
    int n,m;  
    cin>>m>>n;  
    memset(dp,0,sizeof(dp));//数组清空,其实同时就把边界给做了清理  
    for(int i=1; i<=n; i++)  
        cin>>weight[i]>>value[i];  
    //从1开始有讲究的因为涉及到dp[i-1][j],从0开始会越界  
    for(int i=1; i<=n; i++)//判断每个物品能否放进  
    {  
        for(int j=0; j<=m; j++)//对每个状态进行判断  
        //这边两重for都可以倒着写，只是需要处理最边界的情况，滚动数组不一样  
        {  
            if(j>=weight[i])//能放进  
                dp[i][j]=max(dp[i-1][j],dp[i-1][j-weight[i]]+value[i]);  
  
            else dp[i][j]=dp[i-1][j];//不能放进  
        }  
    }  
    cout<<dp[n][m]<<endl;  
    return 0;  
} 

// 滚动数组
for(int i=1; i<=n; i++)//对每个数判断，可反  
{  
    for(int j=m; j>=weight[i]; j--)//这里这个循环定死，不能反,反了就是完全背包  
    {  
        dp[j]=max(dp[j],dp[j-weight[i]]+value[i]);//其实不断在判断最优解，一层一层的  
    }  
}
```
---

### 完全背包

有n种物品与承重为m的背包。每种物品有无限多件，每个物品都有对应的重量weight[i]与价值value[i]，求解如何装包使得价值最大。

状态：$dp[i][j]$ 表示前i个物品，容量为j的背包的最大价值

状态转移方程：

```math
dp[i][j] = max( dp[i-1][j] , dp[i-1][ j - k * weight[i] ] + k * value[i])  0 \le k * weight[i] \le m
```

```cpp
for(int i=1; i<=n; i++)//对每个数判断，可反  
{  
    for(int j=weight[i]; j<=m; j++)  
    {  
        dp[j]=max(dp[j],dp[j-weight[i]]+value[i]);//其实不断在判断最优解，一层一层的  
    }  
}
```

---

### 多重背包

有n种物品与承重为m的背包。每种物品有有限件num[i]，每个物品都有对应的重量weight[i]与价值value[i]，求解如何装包使得价值最大

状态：`$dp[i][j]$` 表示前i个物品，容量为j的背包的最大价值

```math
dp[i][j] = max ( dp[i-1][j - k * weight[i]] +k * value[i] )     0<=k<=num[i]
```

```cpp
int main()  
{  
    int n,m;  
    cin>>n>>m;  
    memset(dp,0,sizeof(dp));  
    for(int i=1; i<=n; i++)  
        cin>>weight[i]>>value[i]>>num[i];  
          
    for(int i=1; i<=n; i++)//每种物品  
          
        for(int k=0; k<num[i]; k++)//其实就是把这类物品展开，调用num[i]次01背包代码  
          
            for(int j=m; j>=weight[i]; j--)//正常的01背包代码  
              
                dp[j]=max(dp[j],dp[j-weight[i]]+value[i]);  
      
    cout<<dp[m]<<endl;  
    return 0;
}
```

```cpp
int dp[N];  
int c[N],w[N],num[N];  
int n,m;  
  
void ZeroOne_Pack(int cost,int weight,int n)//吧01背包封装成函数  
{  
    for(int i=n; i>=cost; i--)  
        dp[i] = max(dp[i],dp[i-cost] + weight);  
}  
  
void Complete_Pack(int cost,int weight,int n)//把完全背包封装成函数  
{  
    for(int i=cost; i<=n; i++)  
        dp[i] = max(dp[i],dp[i-cost] + weight);  
}  
  
int Multi_Pack(int c[],int w[],int num[],int n,int m)//多重背包  
{  
    memset(dp,0,sizeof(dp));  
    for(int i=1; i<=n; i++)//遍历每种物品  
    {  
        if(num[i]*c[i] > m)  
            Complete_Pack(c[i],w[i],m);  
            //如果全装进去已经超了重量，相当于这个物品就是无限的  
            //因为是取不光的。那么就用完全背包去套  
        else  
        {  
            int k = 1;  
            //取得光的话，去遍历每种取法  
            //这里用到是二进制思想，降低了复杂度  
            //为什么呢，因为他取的1,2,4,8...与余数个该物品，打包成一个大型的该物品  
            //这样足够凑出了从0-k个该物品取法  
            //把复杂度从k变成了logk  
            //如k=11，则有1,2,4,4，足够凑出0-11个该物品的取法  
            while(k < num[i])  
            {  
                ZeroOne_Pack(k*c[i],k*w[i],m);  
                num[i] -= k;  
                k <<= 1;  
            }  
            ZeroOne_Pack(num[i]*c[i],num[i]*w[i],m);  
        }  
    }  
    return dp[m];  
}
```

---

### 最长公共子序列(LCS)

状态：表示a[0-i]和b[0-j]的公共子序列长度

```math
dp[i][j]
```

状态转移方程：

```math
dp[i+1][j+1] = dp[i][j]+1,a[i] == b[j]

dp[i+1][j+1] = max(dp[i+1][j]. dp[i][j+1])  ,else 
```

```cpp
int main()
{
    string a, b;
    int n, m;
    while(cin>>a>>b)
    {
        n = a.length();
        m = b.length();
        for(int i = 0; i < n; i++){
            for(int j = 0; j < m; j++){
                if(a[i] == b[j])dp[i+1][j+1] = dp[i][j]+1;
                else dp[i+1][j+1] = max(dp[i+1][j], dp[i][j+1]);
            }
        }
        cout<<dp[n][m]<<endl;
    }

    return 0;
}
```

---

### 最长上升子序列(LIS)

状态：表示前i个数字的的最长上升子序列长度

```math
dp[i]
```

状态转移方程：

```math
dp[i] = max(dp[j]+1)   , 1 <= j < i, arr[j] < arr[i]
```

```cpp
// O(n^2)
for(int i = 0; i < n; i++){
    dp[i] = 1;
    for(int j = 0; j < i; j++){
        if(a[j] < a[i])
            dp[i] = max(dp[i], dp[j]+1);
    }
    ans = max(ans, dp[i]);
}
```

`$O(nlogn)$`解法

```cpp
int arr[N], dp[N]; // dp[i]表示长度为i的lis序列，末尾的最小值

int lis(int n){
    int len = 1;
    dp[1] = arr[0];
    for(int i = 1; i < n; i++){
        if(arr[i] > dp[len])
            dp[++len] = arr[i];
        else{
            int pos = lower_bound(dp, dp+len, arr[i])-dp;
            dp[pos] = arr[i];
        }
    }
    return len;
}
```

---

### 最大字段和

```cpp
#define ll long long

ll arr[N];

ll dp(ll n){
    ll ans = 0;
    ll sum = 0;
    for(int i = 0; i <n; i++){
        sum += arr[i];
        ans = max(ans, sum);
        if(sum < 0)sum = 0;
    }
    return ans;
}
```

---

### 最大子矩阵

---

## 区间DP

### 括号匹配

状态：表示区间 `$l-r$` 中括号匹配数

```math
dp[l][r]
```

状态转移方程：

```math
dp[l][r] = max(dp[l][r], dp[l+1][r-1]+2) ,str[l] -match- str[r]

dp[l][r] = max(dp[l][r], dp[l][k]+dp[k+1][r]) , else
```

```cpp
memset(dp, 0, sizeof(dp));
for(int len = 1; len < n; len++){
    for(int i = 0; i+len < n; i++){
        int j = i+len;
        if((str[i] == '(' && str[j] == ')') || (str[i] == '[' && str[j] == ']'))
            dp[i][j] = max(dp[i][j], dp[i+1][j-1]+2);
        for(int k = i; k <= j; k++)
            dp[i][j] = max(dp[i][j], dp[i][k]+dp[k+1][j]);
    }
}
cout<<dp[0][n-1]<<endl;
```

---

### 矩阵连乘

---

## 状态压缩DP

### 旅行商问题

---

### 铺砖块问题

在 `$n*m$` 的矩阵中铺上 `$1*2$` 的砖块，有多少种铺法。

```cpp
#include <cstdio>
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 15;
int n, m;
long long dp[N][1<<N];//dp[col][state]表示第col列，在state状态下（即前一列对该列的影响）的方法数。

//dfs表示当前处理到col列的第i个格子，state状态下，对下一列的影响nex
void dfs(int col, int i, int state, int nex){
    if(i == n){
        dp[col+1][nex] += dp[col][state];
        return;
    }
    if((state&(1<<i)) > 0)//这个格子已经被上一列填过
        dfs(col, i+1, state, nex);
    if((state&(1<<i)) == 0)//格子没有被填充，尝试横着放一块砖
        dfs(col, i+1, state, nex|(1<<i));
    if(i+1<n && (state&(1<<i))==0 && (state&(1<<(i+1)))==0)//尝试竖着放一块砖
        dfs(col, i+2, state, nex);
}

int main(){
    while(cin>>n>>m){
        if(!n && !m)break;
        memset(dp, 0, sizeof(dp));
        dp[1][0] = 1;
        for(int col = 1; col <= m; col++){
            for(int state = 0; state < (1<<n); state++){
                if(dp[col][state])
                    dfs(col, 0, state, 0);
            }
        }
        cout<<dp[m+1][0]<<endl;//答案为第m+1列，前一列对其影响为0的方法数。
    }

    return 0;
}
```

---

## 刷表法

填表法 ：就是一般的动态规划，当前点的状态，可以直接用状态方程，根据之前点的状态推导出来。

刷表法：由当前点的状态，更新其他点的状态。需要注意：只用当每个状态所依赖的状态对它的影响相互独立。