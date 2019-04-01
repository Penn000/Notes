# 基础数学

## % 运算

对于a%b

> 1. 首先计算出 a 的绝对值被 b 的绝对值除所得的余数，
> 2. 再使该余数的符号与 a 保持一致。即若 a 为正数，则该表达式结果必为非负数（可能为 0）；若 a为负数，则表达式结果必为非正数（可能为 0）。
> 3. 而表达式结果与b 的符号没有直接关系，即 a % -b 与 a % b 的结果相同。

性质

除法不满足

```math
(a+b)\%MOD = (a\%MOD + b\%MOD)\%MOD

(a*b)\%MOD = (a\%MOD * b\%MOD)\%MOD
```

## 高精度


# 数论

## 快速幂

以 `$log_2(n)$` 的时间复杂度计算

```math
a^n \% MOD
```

```cpp
ll qucik_pow(ll a, ll n, ll MOD){
    ll ans = 1, base = a;
    while(n){
        if(n&1) ans = ans*base%MOD;
        base = base*base%MOD;
        n>>=1;
    }
    return ans;
}
```

---

---

## 素数

判定
```cpp
bool isPrime(int x){
    for(int i = 2; i*i <= x; i++)
        if(x%i==0)
            return false;
    return true;
}
```

素数筛
```cpp
const int N = 10000;
bool notPrime[N];
int prime[N], cnt;
// 筛出[1, n]之间的所有质数
void getPrime(int n){
    memset(notPrime, 0, sizeof(notPrime));
    notPrime[0] = notPrime[1] = 1;
    cnt = 0;
    for(int i = 2; i <= n; i++){
        if(notPrime[i])continue;
        prime[cnt++] = i;
        // j从i×i开始，因为i * k （ k < i）必已经在求得 k 的某个素因数（必小于 i）时被标记过了
        for(int j = i*i; j <= n; j+=i)
            notPrime[j] = 1;
    }
}
```

## 分解质因数

* n 至多只存在一个大于 sqrt（n）的素因数（否则两个大于 sqrt（n）的数相乘即大于 n）。这样，我们只需将n所有小于 sqrt（n）的素数从n中除去，剩余的部分必为该大素因数。正是由于这样的原因，我们不必依次测试sqrt（ n）到n的素数，而是在处理完小于sqrt（ n）的素因数时，就能确定是否存在该大素因数，若存在其幂指数也必为 1。

* 对`$n!$`质因数分解，质因子`$p$`的次幂为`$n/p+n/(p*p)+n/(p*p*p)+...$`

## GCD

```cpp
int gcd(int a, int b){
    return b==0 ? a : gcd(b, a%b);
}
```

## LCM

```cpp
int lcm(int a, int b){
    return a/gcd(a, b)*b;
}
```

## 扩展欧几里得

* 同余运算

```math
a \equiv b \pmod{n} \Leftrightarrow a - kn = b
```

返回 `$gcd(a, b)$`

函数ex_gcd所求得的 `$x_0,y_0$` 为下述方程的一组特殊的解

```math
a*x+b*y=gcd(a,b)
```

该方程的通解为：

```math
x=x_0+\frac{b}{gcd}*t

y=y_0+\frac{a}{gcd}*t
```

```cpp
int ex_gcd(int a, int b, int& x, int& y){
    if(b==0){
        x = 1;
        y = 0;
        return a;
    }
    int ans = ex_gcd(b, a%b, x, y);
    int tmpx = x;
    x = y;
    y = tmpx - a/b * y;
    return ans;
}
```

解不定方程

若方程为：

```math
a*x+b*y=c
```

方程有解的条件是 `$gcd(a,b)|c$`, 即 `$c \% gcd(a, b)=0$`

解法：对 `$a*x+b*y=gcd$` 两边同时乘上 `$\frac{c}{gcd}$`,得：

```math
a*\frac{c*x_0}{gcd}+b*\frac{c*y_0}{gcd}=c
```

所以通解为：

```math
x = \frac{c}{gcd}*x_0 

y = \frac{c}{gcd}*y_0
```

方程的最小正整数解：

我们只需要让解得的 `$x$` 不断减 `$\frac{b}{gcd}$`，直到再减就为负数时，所得的x就是我们要的解。 其实这个过程就是模运算，所以最小正整数解就是：

```math
x = (\frac{c}{gcd}*x_0)mod \frac{b}{gcd} 

y = (\frac{c}{gcd}*y_0) mod \frac{a}{gcd} 
```

```cpp
d = gcd(a, b);
x_ans = ((x*c/d)%(b/d)+(b/d))%(b/d);
```

改不定方程还有以下形式：

```math
a*x \equiv b \pmod{n}
```

---

## 乘法逆元

```math
ax \equiv 1 \pmod{n}
```

称 `$x$` 为 `$a$` 关于 `$n$` 的乘法逆元

存在条件：`$gcd(a,n)=1$`

```cpp
// x即为a对n的逆元
ll inv(ll a, ll n){
    ll x, y;
    ex_gcd(a, n, x, y);
    return x;
}
```

---

## 费马小定理

若 `$p$` 是一个素数， `$gcd(a,p)=1$` （因为p是素数，所以a，p显然互质，a!=p），则：

```math
a^{p-1} \equiv 1 \pmod{p}

a * a^{p-2} \equiv 1 \pmod{p}
```

即 `$a^{p-2}$` 是 `$a$` 对 `$p$` 的逆元。

```cpp
ll inv(ll a, ll n){
    return quick_pow(a, n-2);
}
```

---

## 欧拉函数

在数论，对正整数n，欧拉函数 `$\phi(n)$` 是小于n的正整数中与n互质的数的数目

对于n的因数 `$p_1, p_2, ... , p_k$` ，欧拉函数如下：

```math
phi(n)=n*(1-\frac{1}{p_1})*(1-\frac{1}{p_2})*...*(1-\frac{1}{p_k})
```

```cpp
int phi(int n){
    int ans = n;
    for(int i = 2; i <= n; i++){
        if(n%i==0){
            ans -= ans/i;
            while(n%i==0)
                n /= i;
        }
        if(n == 1)break;
    }
    return ans;
}
```

---

## 欧拉定理

对任意整数n：

```math
a^{\phi(n)} \equiv 1 \pmod{n}
```

所以 `$gcd(a, n)=1$` 的条件下， `$a^{\phi(n)-1}$` 是a对n的逆元。

---

## 离散对数

```math
a^x \equiv b \pmod{n}

s \equiv log_ab \pmod{n}
```

x为在模n下以a为底b的对数

---

# 线性代数

## 高斯消元

## 矩阵快速幂