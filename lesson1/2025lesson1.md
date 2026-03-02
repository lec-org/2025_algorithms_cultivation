# 学习方法
#### 推荐学习顺序
+ 简单贪心
+ 二分查找，二分答案，三分
+ 前缀和与差分，双指针，离散化
+ 链表，栈，队列，堆，并查集
+ dfs，bfs，简单剪枝
+ 位运算，整除理论，简单组合计数
+ 图论基础，拓扑排序，最短路，最小生成树
+ 线性dp，背包dp，区间dp，树形dp，状压dp
+ 单调栈，单调队列
+ 字符串哈希，KMP，trie树

学习新算法的同时一定要多刷题，模板不需要背，重在理解。
可以到 [洛谷](https://www.luogu.com.cn/) 找以【模板】开头的题目题解学习，然后做官方和用户分享的题单中的简单题。
在这里推荐牛客周赛[牛客](https://ac.nowcoder.com/)，可以到比赛页面筛选出牛客周赛系列，比赛时间一般为每周六19:00-21:00，有时间的同学可以参加一下。
所有人加入vjudge平台上的团队[LEC - Virtual Judge](https://vjudge.net/group/lec_algorithm_cultivation)，以后会在上面发布一些推荐题目。
还有洛谷上的团队[lec](https://www.luogu.com.cn/team/70711)，同样有一些推荐题单。
引流[算法论坛](https://acm.lucius7.dev/)，任何问题都可以发表出来，一定会得到回复。

## 时间复杂度
衡量算法快慢的函数。大 $O$ 记号表示上界。规定计算机进行一次基本运算（加减乘除模、swap、比较等）所用时间为一个小常数。

+ 冒泡、选择排序：$O(n^2)$
	
+ 快速、归并排序：$O(n\log n)$

1s  1  $10^7-10^8$ 


试分析以下代码的时间复杂度
+ $O(n)$
```cpp
for (int i = 1; i <= n; ++i) {
    x += 1;
}
```

+ $O(n^2)$
```cpp
  for (int i = 1; i <= n; ++i) {
    for (int j = 1; j <= n; ++j) {
        x += 1;
    }
  }
```

+ $O(n^2*len)$
```cpp
  string s[n + 1], t[n + 1];
  char c[n + 1][n + 1];
  s[i]
  s[0] = "abcdsaddsadsdadqd";
  s[1] = "qqqqqqqqqqw";
  s[2] = "qqqqqqqqqqd";
  
  // s[i], t[i] 长度 <= n
  for (int i = 1; i <= n; ++i) {
      for (int j = 1; j <= n; ++j) {
          if (s[i] == t[j]) ++cnt;
      }
  }
```

+ $O(\sqrt n)$
```cpp
  for (int i = 2; i * i <= n; ++i) {
      if (n % i == 0) break;
  }
  ```

+ $O(n\log n)$
 ```cpp
for (int i = 1; i <= n; ++i) {
    for (int j = 1; j * i <= n; ++j) {
        x += 1;
    }
    // 调和级数
}
```
n + n / 2 + n / 3 + n / 4, ... , 1
**![[a7ec364a1b72afdc1c75902e8c59afd2_720.png]]**

## 二分

> Stop learning useless algorithms, go and solve some problems, learn how to use binary search.

前期学习中的一个**重难点**

使用二分要满足两个条件：
+ 答案满足单调性 

+ 容易验证一个解是否是满足题意的。

### 二分答案

需要注意以下内容

+ 及时return `check` 函数有时会避免一些可能的错误
+ 考虑边界问题
	
常见的可以二分的问题：

- 最值最化

[P2249 【深基13.例1】查找 - 洛谷](https://www.luogu.com.cn/problem/P2249)

[路标设置](https://www.luogu.com.cn/problem/P3853)

[数字组合](https://ac.nowcoder.com/acm/problem/235260)

## 位运算

按位与 &
按位或 |
按位异或 ^
按位取反 ~
左移 <<
右移 >> 

可以思考，不需要硬记

![img1.png](https://s2.loli.net/2024/12/20/VBuSDUYc6HAJXpt.png)

```cpp
//判断一个数字是否是2的幂次
{
    int x;
    bool f = (x & (x - 1)) == 0;
}

//计算二进制有几个1
{
    int x, c = 0;
    for(;x;c++){
        x = x & (x - 1)
    }
}

void swap(){
    b ^= a;
    a ^= b;
    b ^= a;
}

//得到右边第一个1和后面的0构成的数
int lowbit(int x){
    return x & (-x);
}

//判断两数符号是否相同
{
    bool f = ((x ^ y) < 0);
}

{
    if(x == y) ->  x ^ y == 0;
    0 | x == x
}

a + b =  (a ^ b) + ((a & b) << 1)

a - b = a + (~b + 1)

a ^ b == ~(a & b) & (a | b)

for (int i = x; i; i = (i - 1) & x) //子集枚举
```

## STL

群里发过C++基础知识，可以参考学习，网上资料也很多
- `vector`
- `stack`
- `queue`
- `map`
- `set`
... ..... .. . . . . .. . . 
![[0ecd9f96a80d0d53bd7dd878d4ab6248.png]]