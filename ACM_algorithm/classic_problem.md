## 日期问题

判断闰年

```cpp
// 当年数不能被 100 整除时若其能被 4 整除则为闰年，或者其能被400 整除时也是闰年
bool isLeap(int yyyy){
    return (yyyy%100!=0 && yyyy%4==0) || yyyy%400==0;
}
// 闰年2月29天，平年2月28天
```

日期处理
```cpp
// 将"20180910"样式的输入读到yyyy，mm，dd中
scanf("%4d%2d%2d", &yyyy, &mm, &dd);

// 12个月份英文
string month[12] = {
    "January",
    "February",
    "March",
    "April",
    "May",
    "June",
    "July",
    "August",
    "September",
    "October",
    "November",
    "December"
};

// 星期
string weekday[7] = {
    "Sunday",
    "Monday",
    "Tuesday",
    "Wednesday",
    "Thursday",
    "Friday",
    "Saturday"
};
```

蔡勒公式

> 随便给一个日期，就能用这个公式推算出是星期几

---

---

## 表达式求值

### 中缀表达式求值

1. 设立两个堆栈，一个用来保存运算符，另一个用来保存数字。

2. 在表达式首尾添加标记运算符，该运算符运算优先级最低。

3. 从左至右依次遍历字符串，若遍历到运算符，则将其与运算符栈栈顶元素进行比较，若运算符栈栈顶运算符优先级小于该运算符或者此时运算符栈为空，则将该运算符压入堆栈。遍历字符串中下一个元素。

4. 若运算符栈栈顶运算符优先级大于该运算符，则弹出该栈顶运算符，再从数字栈中依次弹出两个栈顶数字，完成弹出的运算符对应的运算得到结果后，再将该结果压入数字栈，重复比较此时栈顶运算符与当前遍历到的运算符优先级，视其优先级大小重复步骤 3 或步骤 4。

5. 若遍历到表达式中的数字，则直接压入数字栈。

6. 若运算符堆栈中仅存有两个运算符且栈顶元素为我们人为添加的标记运算符，那么表达式运算结束，此时数字堆栈中唯一的数字即为表达式的值。

![image](https://img-blog.csdn.net/20170220135001467)

> 表中需要注意的是θ1为opter的栈顶元素，θ2位从表达式中读取的操作符

```cpp
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
#include <stack>

using namespace std;

int op_to_id(char op){
	int id = 0;
	switch(op){
		case '+': id = 0; break;
		case '-': id = 1; break;
		case '*': id = 2; break;
		case '/': id = 3; break;
		case '(': id = 4; break;
		case ')': id = 5; break;
		case '#': id = 6; break;
		default: break;
	}
	return id;
}

// 表中需要注意的是op1为符号栈栈顶元素，op2为从表达式中读取的操作符
// 即中缀表达式中op1在op2前面
char cmp(char op1, char op2){
	const char priority[][7] = {
	//    +    -    *    /    (    )    #
		{'>', '>', '<', '<', '<', '>', '>'},
		{'>', '>', '<', '<', '<', '>', '>'},
		{'>', '>', '>', '>', '<', '>', '>'},
		{'>', '>', '>', '>', '<', '>', '>'},
		{'<', '<', '<', '<', '<', '=', 'x'},
		{'>', '>', '>', '>', 'x', '>', '>'},
		{'<', '<', '<', '<', '<', 'x', '='}
	};
	return priority[op_to_id(op1)][op_to_id(op2)];
}

double calculate(string str){
	stack<double> num_stack;
	stack<char> op_stack;
	str.push_back('#');
	op_stack.push('#');
	for(int i = 0; i < str.length();){
		if(str[i] >= '0' && str[i] <= '9'){
			double num = 0;
			while(str[i] >= '0' && str[i] <= '9'){
				num *= 10;
				num += str[i]-'0';
				i++;
			}
			num_stack.push(num);
		}else if(str[i] == ' '){
			i++;
			continue;
		}else{
			if(cmp(op_stack.top(), str[i]) == '<'){
				op_stack.push(str[i]);
				i++;
			}else if(cmp(op_stack.top(), str[i]) == '='){
				op_stack.pop();
				i++;
			}else if(cmp(op_stack.top(), str[i]) == '>'){
				// 重复比较当前运算符与符号栈栈顶运算符的优先级
				char op = op_stack.top();
				op_stack.pop();
				double num2 = num_stack.top();
				num_stack.pop();
				double num1 = num_stack.top();
				num_stack.pop();
				double num;
				switch(op){
					case '+': num = num1+num2; break;
					case '-': num = num1-num2; break;
					case '*': num = num1*num2; break;
					case '/': num = num1/num2; break;
					default: break;
				}
				num_stack.push(num);
			}
		}
	}
	return num_stack.top();
}

int main()
{
	string str;
	while(cin>>str){
		cout<<calculate(str)<<endl;
	}

	return 0;
}

```

### 中缀表达式转逆波兰式

---

---

## 整数划分

指把一个正整数n写成多个大于等于1且小于等于其本身的整数的和，则其中各加数所构成的集合为n的一个划分。

状态：

```math
f(n, m)
```

将n拆分成不超过m个数之和的方法数

递归方程：

n为苹果数，m为盘子数

1. 当n<m时，那么就是将n个苹果分到n个盘的方法

2. n>=m时，那么
   * 至少其中一个盘不放，那么就是n个苹果放到m-1个盘的方法: `$f(n, m-1)$`
   * 每个盘放一个，然后就是n-m个放在m个盘的放法: `$f(n-m, m)$`

综上，递归方程如下：

```math
f(n, m) = \left\{
\begin{aligned}
& 1 & ,n==0,1 || m == 1 \\
& f(n, n) & , n<m \\
& f(n-m,m)+f(n,m-1) & , n>=m
\end{aligned}
\right.
```

代码：

```cpp
int f(int n, int m)
{
    if(n==1 || n==0 || m==1)return 1;
    else if(n < m)return f(n, n);
    else return f(n-m, m)+f(n, m-1);
}

```

## N皇后问题

```cpp
int cnt;

void dfs(int row){
    if(row == n)cnt++;
    else{
        for(int i = 0; i < n; i++){
            bool fg = true;
            line[row] = i;
            for(int j = 0; j < row; j++){
                if(line[row]==line[j] || line[row]-line[j]==row-j || line[row]-line[j]==j-row){
                    fg = false;
                    break;
                }
            }
            if(fg)dfs(row+1);
        }
    }
}
```

## 树状数组求逆序对

```cpp
int tree[N], arr[N], n;// tree的起始下标为1

int lowbit(int x){
	return x&(-x);
}

void add(int pos, int val){
	for(int i = pos; i < N; i += lowbit(i))
		tree[i] += val;
}

int query(int pos){
	int sum = 0;
	for(int i = pos; i > 0; i -= lowbit(i))
	  sum += tree[i];
	return sum;
}

int inversion(){
	int ans = 0;
	memset(tree, 0, sizeof(tree));
	for(int i = n-1; i >= 0; i--){
		add(arr[i], 1);
		ans += query(arr[i]-1);
	}
	return ans;
}
```