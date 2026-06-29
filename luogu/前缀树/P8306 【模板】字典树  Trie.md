## P8306 【模板】字典树 / Trie

### 题目描述

给定 $n$ 个模式串 $s_1, s_2, \dots, s_n$ 和 $q$ 次询问，每次询问给定一个文本串 $t_i$，请回答 $s_1 \sim s_n$ 中有多少个字符串 $s_j$ 满足 $t_i$ 是 $s_j$ 的**前缀**。

一个字符串 $t$ 是 $s$ 的前缀当且仅当从 $s$ 的末尾删去若干个（可以为 $0$ 个）连续的字符后与 $t$ 相同。

输入的字符串大小写敏感。例如，字符串 `Fusu` 和字符串 `fusu` 不同。

### 输入格式

**本题单测试点内有多组测试数据**。  

输入的第一行是一个整数，表示数据组数 $T$。

对于每组数据，格式如下：  
第一行是两个整数，分别表示模式串的个数 $n$ 和询问的个数 $q$。  
接下来 $n$ 行，每行一个字符串，表示一个模式串。  
接下来 $q$ 行，每行一个字符串，表示一次询问。

### 输出格式

按照输入的顺序依次输出各测试数据的答案。  
对于每次询问，输出一行一个整数表示答案。

### 输入输出样例 #1

### 输入 #1

```
3
3 3
fusufusu
fusu
anguei
fusu
anguei
kkksc
5 2
fusu
Fusu
AFakeFusu
afakefusu
fusuisnotfake
Fusu
fusu
1 1
998244353
9
```

### 输出 #1

```
2
1
0
1
2
1
```

### 说明/提示

#### 数据规模与约定

对于全部的测试点，保证 $1 \leq T, n, q\leq 10^5$，且输入字符串的总长度不超过 $3 \times 10^6$。输入的字符串只含大小写字母和数字，且不含空串。

#### 说明
std 的 IO 使用的是关闭同步后的 cin/cout，本题不卡常。

------

## 解题

```cpp
#include <bits/stdc++.h>
using namespace std;

using ll = long long;
static const int N = 3000000+5;
int son[N][62];
int pass[N];
int idx;

int get(char c)
{
    if('a'<=c&&c<='z') return c-'a';
    else if('A'<=c&&c<='Z') return c-'A'+26;
    else return c-'0'+52;
}

void clearTrie()
{
    // memset(pass,0,sizeof pass);
    // memset(son,0,sizeof son);  
    for(int i = 0; i <= idx; ++i)
    {
        pass[i] = 0;
        memset(son[i], 0, sizeof son[i]);
    }
    idx=0;
}

void insert(string s)
{
    int p=0;
    pass[p]++;
    for(char ch:s)
    {
        int c=get(ch);
        if(son[p][c]==0) son[p][c]=++idx;
        p=son[p][c];
        pass[p]++;
    }
}

int countwordStart(string s)
{
    int p=0;
    for(char ch:s)
    {
        int c=get(ch);
        if(son[p][c]==0) return 0;
        p=son[p][c];
    }
    return pass[p];
}


void solve() {
    clearTrie();
    int n,q;
    cin>>n>>q;
    for(int i=0;i<n;++i)
    {
        string s;
        cin>>s;
        insert(s);
    }
    for(int i=0;i<q;++i)
    {
        string s;
        cin>>s;
        cout<<countwordStart(s)<<"\n";
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int T;
    cin >> T;

    while (T--) {
        solve();
    }

    return 0;
}
```

本题是前缀树的模版题，其中N的定义是 所有插入字符串的长度+1，而题中已然给了最长总字符串长度，也就不需要使用：

```
字符串数量*每一个字符串最大长度+1
```

题中要接收 小写字母，大写字母，数字，只需要开阔 62(26+26+10) 的范围就好了，在获得索引时，判断类型，再去获得对应的索引操作。

```cpp
int son[N][62];
int get(char c)
{
    if('a'<=c&&c<='z') return c-'a';
    else if('A'<=c&&c<='Z') return c-'A'+26;
    else return c-'0'+52;
}
```

本题中的数组过大，如果将其放进类对象里，再去 solve 里创建，就会爆栈。

所以要把数组设为全局变量，同时不要类，将方法直接建到外面，直接操作全局变量数组即可。

```cpp
memset(pass,0,sizeof pass);
memset(son,0,sizeof son); 
```

本题的clear不适宜使用此方法清理数组，因为大多数时用不了这么多节点，而每次清理要将所有的节点全部清空，会很慢。

所以只清理用过的节点即可。