# 数据结构

## 二叉树

节点
```cpp
struct Node{
    int value;
    Node *lson, *rson;
    Node(int _value): value(_value), lson(NULL), rson(NULL){}
};
```

### 遍历

先序遍历

```cpp
void preOrder(Node *root){
    if(root != NULL){
        cout<<root->value<<endl;
        preOrder(root->lson);
        preOrder(root->rson);
    }
}
```

中序遍历


```cpp
void inOrder(Node *root){
    if(root != NULL){
        inOrder(root->lson);
        cout<<root->value<<endl;
        inOrder(root->rson);
    }
}
```

后序遍历

```cpp
void postOrder(Node *root){
    if(root != NULL){
        postOrder(root->lson);
        postOrder(root->rson);
        cout<<root->value<<endl;
    }
}
```

### 重建二叉树

> 包括中序的两种遍历顺序才能唯一的确定一棵二叉树

由后序遍历和中序遍历重建二叉树
```
Node* buildTree(int *postArr, int *inArr, int n){
    if(n < 1)return NULL;
    Node *rt = new Node(postArr[n-1]);
    if(n == 1)return rt;
    int R;
    for(int i = 0; i < n; i++){
        if(inArr[i] == postArr[n-1]){
            R = i;
            break;
        }
    }
    rt->lson = buildTree(postArr, inArr, R);
    rt->rson = buildTree(postArr+R, inArr+R+1, n-R-1);
    return rt;
}
```

### 二叉排序树

## 线段树

```cpp
#define ll long long
#define mid ((st[id].l+st[id].r)>>1)
#define lson (id<<1)
#define rson ((id<<1)|1)
```

```cpp
const ll N = 100005;
ll arr[N];
struct Node{
    ll l, r, sum, lazy;
}st[N<<2];
```

```cpp
void build(int id, int l, int r)
{
    st[id].l = l; st[id].r = r; st[id].lazy = 0;
    if(l == r){
        st[id].sum = arr[l];
        return;
    }
    build(lson, l, mid);
    build(rson, mid+1, r);
    st[id].sum = st[lson].sum+st[rson].sum;
}
```

```cpp
void push_down(int id)
{
    if(st[id].lazy != 0){
        st[lson].lazy += st[id].lazy;
        st[rson].lazy += st[id].lazy;
        st[id].sum += (st[id].r-st[id].l+1)*st[id].lazy;
        st[id].lazy = 0;
    }
    return;
}
```

```cpp
ll query(int id, int l, int r)
{
    if(st[id].l == l && st[id].r == r)return st[id].sum+(r-l+1)*st[id].lazy;
    push_down(id);
    if(r <= mid)return query(lson, l, r);
    else if(l > mid)return query(rson, l, r);
    else return query(lson, l, mid)+query(rson, mid+1, r);
}
```

```cpp
void update(int id, int l, int r, int w)
{
    if(st[id].l == l && st[id].r == r){
        st[id].lazy += w;
        return;
    }
    st[id].sum += (r-l+1)*w;
    if(r <= mid)update(lson, l, r, w);
    else if(l > mid)update(rson, l, r, w);
    else{
        update(lson, l, mid, w);
        update(rson, mid+1, r, w);
    }
}
```

## 树状数组

![btree](https://www.cnblogs.com/images/cnblogs_com/Penn000/1316422/o_tree.jpg)

```cpp
int tree[N]; // tree的起始下标为1
```

x+lowbit(x)：得到x节点的父节点，比如x=4时；就能得到8；

x-lowbit(x)：得到x节点的管辖区间的下个区间的管辖点，比如x
=7，循环依次得到6，4，0；

```cpp
int lowbit(int x){return x&(-x);}
```

```cpp
int add(int pos, int tt)
{
    for(int i = pos; i <= n; i+=lowbit(i))
      tree[i] += tt;
    return 0;
}
```

```cpp
int query(int pos)
{
    int sum = 0; 
    for(int i = pos; i > 0; i-=lowbit(i))
      sum+=tree[i];
    return sum;
}
```

```cpp
// 单点更新，加上t
add(pos, t);

// 区间[1, pos]查询
int val = query(pos);
```

## 加权并查集

```cpp
int fa[N], rk[N];
```

```cpp
void init(){
    for(int i = 0; i < N; i++){
        fa[i] = i;
        rk[i] = 0;
    }
}
```

```cpp
int getfa(int x){
    if(x == fa[x])return x;
    int pre = fa[x];
    fa[x] = getfa(fa[x]);
    rk[x] += rk[pre];
    return fa[x];
}
```

```cpp
bool merge_(int a, int b, int val){
    int af = getfa(a);
    int bf = getfa(b);
    if(af != bf){
        fa[af] = bf;
        rk[af] = rk[b]-rk[a]+val;
        return true;
    }else{
        return rk[a]-rk[b] == val;
    }
}
```

---

## 单调栈

---
### 矩形面积并/交

```cpp
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
#define lson (id<<1)
#define rson ((id<<1)|1)
using namespace std;
```

```cpp
const int N = 2000;
const double EPS = 0.0001;
```

```cpp
struct Node{
	double sum;
}tree[N<<2];
int cnt[N], len;
double x[N];
```

```cpp
void build(){
	memset(tree, 0, sizeof(tree));
}
```

```cpp
void update(int id, int L, int R, int pos, int val){
	if(L==R){
		cnt[pos] += val;
		// 矩形面积交
		if(cnt[pos]>1)tree[id].sum = x[pos+1]-x[pos];
		// 矩形面积并
		// if(cnt[pos])tree[id].sum = x[pos+1]-x[pos];
		else tree[id].sum = 0;
		return;
	}
	int mid = (L+R)/2;
	if(pos <= mid)update(lson, L, mid, pos, val);
	else update(rson, mid+1, R, pos, val);
	tree[id].sum = tree[lson].sum+tree[rson].sum;
}
```

```cpp
struct Line{
	double x0, x1, y;
	int fg;
	bool operator<(const Line l) const{
		return y < l.y;
	}
}line[N<<2];
```

```cpp
int main()
{
	int T, n, kase = 0;
	scanf("%d",&T);
	while(T--){
		scanf("%d", &n);
		double x0, y0, x1, y1;
		len = 0;
		for(int i = 1; i <= n; i++){
			scanf("%lf%lf%lf%lf", &x0, &y0, &x1, &y1);
			int a = i*2-1, b = i*2;
			line[a].x0 = x0, line[a].x1 = x1, line[a].y = y0, line[a].fg = 1;
			line[b].x0 = x0, line[b].x1 = x1, line[b].y = y1, line[b].fg = -1;
			x[++len] = x0, x[++len] = x1;
		}
		n*=2;
		sort(x+1, x+1+n);
		sort(line+1, line+1+n);
		memset(cnt, 0, sizeof(cnt));
		build();
		double ans = 0;
		// 扫面线从下往上扫
		for(int i = 1; i <= n; i++){
			ans += tree[1].sum*(line[i].y-line[i-1].y);
			int l = lower_bound(x+1, x+1+n, line[i].x0)-x;
			int r = lower_bound(x+1, x+1+n, line[i].x1)-x;
			for(int j = l; j < r; j++){
				// 线段树叶子节点i表示线段x_i,x_i+1的长度，因此叶子节点共有n-1个，每次只需更新[l,r)的线段
				update(1, 1, n-1, j, line[i].fg);
			}
		}
		printf("%.2lf\n", ans+EPS);
	}

	return 0;
}
```