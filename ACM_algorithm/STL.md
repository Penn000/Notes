## sort

```cpp
// 从大到小排
bool operator<(const Node &x) const {
    return a < x.a;
}

// 从小到大排
bool operator<(const Node &x) const {
    return a > x.a;
}

// 返回值为 true 时，即表示第一个参数将会排在第二个参数之前
bool cmp(int a, int b){
    return a < b;
}
sort(arr, arr+n, cmp);

// c++11 lambda
sort(arr, arr+n, [](int a, int b){
    return a > b;
});
```

## string

```cpp
#include <cstring>

string str;

// 构造
string str2 (str, idx, len) // 用表示str从第idx个开始的之后的len个字符(不包含第idx个字符) 

// 按行读入，舍弃换行符 
getline(cin, str);

str.push_back('.');//加入单字符，不能加入字符串 
str.append("hello world!");//加入字符串
str.assign("dreams come true");//重新赋值 

// c_str()函数返回一个指向正规C字符串的指针
char* chs = stc.c_str();

// 返回str在字符串中第一次出现的位置（从index开始查找）。如果没找到则返回string::npos
unsigned int pos = str.find("qwe");
if(pos != string::npos){
    cout<<pos<<endl;
}

// string按空格划分
#include <sstream>
#include <vector>

string line, str;
getline(cin, line);

vector<string> vec_str;
stringstream sstr(line);
while(sstr>>str){
    vec_str.push_back(str);
}
```

---

## pair

```cpp
// 构造
pair<int, int> _pair;          // 默认构造
pair<int, int> _pair(1, 2);    // 赋初值构造
pair<int, int> _pair(_pair2);  // 拷贝构造

// 访问元素
_pair.first = 1;
_pair.second = 2;

// operator =
pair<int, int> _pair = mak_pair(1, 2);
_pair = _pair1;
```

---

## set

```cpp
#include <set>

set<int> st;

// 在集合中插入元素
st.insert(val);
// 返回值是pair<set<int>::iterator,bool>，bool标志着插入是否成功，
// 而iterator代表插入的位置，若key_value已经在set中，则iterator表示的key_value在set中的位置。
pair<set<int>::iterator,bool> pr = st.insert(val);

// 删除迭代器iterator指向的值
st.erease(iter);

// 返回一个指向被查找到元素的迭代器，没找到返回st.end()
set<int>::iterator iter = st.find(val);
if(iter != st.end()){
    cout<<*iter<<endl;
}

// 遍历set中的所有元素
for(iter = st.begin(); iter != st.end(); iter++){
    cout<<*iter<<endl;
}
for(auto &x: st){
    cout<<x<<endl;
}

set<int>::iterator iter;
// 返回指向大于或等于key_value的第一个元素的迭代器
iter = st.lower_bound(key_value);
// 返回最后一个大于等于key_value的迭代器
iter = st.upper_bound(key_value);

// set可以看作是一个有序列表
int first = *(st.begin()); // 访问第一个元素
int last = *(--st.end());  // 访问最后一个元素

```

---

## map

```cpp
#include <map>

// 构造
map<int, string> mp;

// 插入
// 当map中有这个关键字时，insert操作是插入数据不了的
mp.insert(pair<int, string>(1, "hello world"));
mp.insert(map<int, string>::value_type(1, "hello world");
// 用数组方式可以覆盖以前该关键字对应的值
mp[1] = "hello world";

// 删除所有元素
mp.clear();

// map中元素的个数
int len = mp.size();

// 遍历
map<int, string> iter;
for(iter = mp.begin(); iter != mp.end(); iter++)
    cout<<iter->first<<" "<<iter->second<<endl;
    
// 查找
mp.count();

iter = mp.find(key);
if(iter != mp.end())
    cout<<iter->second<<endl;
    
// 删除
mp.erease(key);

// map中的元素是自动按Key升序排序

```

---

## multimap

```cpp
#include <map>

multimap<int, string> mp;

multimap<int, string>::iterator iter;

// 遍历同一key下的所有value值
pair<multimap<int, string>::iterator, multimap<int, string>::iterator> eqiter;
eqiter = mp.equal_range(key);
for(iter = eqiter.first; iter != eqiter.second; iter++)
    cout<<iter->first<<" "<<iter->second<<endl;
```

---

## biset

```cpp
#include <bitset>

// 构造
bitset<16> bs;
bitset<16> bs(10);
bitset<16> bs("1010");

// 取对应位
bs[pos];

// 值为1的个数
int len = bs.count();

bs.set();      // 全部位置1
bs.set(pos);   // pos位处的二进制位置1, 与1做或运算\
bs.reset();    // 全部位置0
bs.reset(pos); // pos位处的二进制位置0
bs.flip();     // 全部位逐位取反
bs.flip(pos);  // pos处的二进制位取反
bs.to_ulong(); // 将二进制转换为unsigned long输出
bs.to_string();// 将二进制转换为字符串输出
```

---

## 二分查找

```cpp
lower_bound()   // 第一个大于等于
lower_bound(a.begin(), a.end(), x)     // 返回一个迭代器
lower_bound(a, a+n, x)     // 返回找到元素的指针

upper_bound()   // 第一个大于
```

---

## 数组去重

unique()并没有将重复的元素删除，而是把重复的元素放到数组的最后面藏起来了

```cpp
sort(arr, arr+n);

// 去重前数组需有序
unique(arr, arr+n); // 返回指针
unique(vec.begin(), vec.end()); // 返回迭代器

// 去重后数组长度
int len = unique(arr, arr+n)-arr;
```

---

## 全排列

```cpp
// int类型next_permutation
int a[3]={1,2,3};
do{
    cout<<a[0]<<" "<<a[1]<<" "<<a[2]<<endl;
}while(next_permutation(a,a+3));

// char类型next_permutation
char ch[205];
cin>>ch;
sort(ch,ch+strlen(ch));
char *first =ch;
char *last=ch+strlen(ch);
do{
    cout<<ch<<endl;
}while(next_permutation(first,last));
```

---

## priority_queue

```cpp
#include <queue>

// 采用默认优先级构造队列
priority_queue<int> pq;

// 最小值优先
priority_queue<int, vector<int>, greater<int> > pq; // 不能写成>>

// 最大值优先
priority_queue<int, vector<int>, less<int> > pq;

// 自定义结构体优先
struct Node{
    int x;

    bool operator< (const Node &node) const{
        return x < node.x;
    }
}
priority_queue<Node> pq;
```

---
