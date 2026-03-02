
# 1.赵佳琦
题解7 - 1 : 

1.这是一道计算子矩阵和最大的前缀和问题 ; 
2.可以套用模板 1 ) b[i][j] = a[i][j] + b[i - 1][j] +b[i][j -1 ] - b[i-1][j - 1] ;
              2 ) b[x2][y2] - b[x1 -1 ][y2] - b[x2][y1 -1] + b[x1 -1][y1 -1 ] ;
3.接下来是边界问题:1)给出数据坐标从 0 开始 ,计算前缀和时会出现数组越界问题(i - 1 , j - 1 , x1 -1 , y1 -1 )在x, y为0时:设置a[x+1][y+1] += v;
                  2)计算有效数据范围:先用max取x,y的最大值,因为我们在存值时使用的是x+1 , y+1所以有效数值边界在max+1 ;
                  3)两个for循环的初始为i = 1 , j = 1 还是因为前缀和计算时的减一越界问题
4.不需要考虑m会越界问题 : 若考虑可加  if(m > max_m + 1) m = max_m + 1;           

ACcode:
```C++
#include<bits/stdc++.h>  //万能头在gcc环境下适用
using namespace std ; 
#define int long long   //会报int,使用这个会自动把int转化为long long型(注意:1.注意int和long long的顺序 2.末尾不加( ; ) 3.int main要改写为signed main )

const int N = 5e3 + 17 ; 
int a[N][N] , b[N][N];

int qian_he(int x1 , int y1 , int x2 , int y2){     //前缀和的板子
    return b[x2][y2] - b[x1 -1 ][y2] - b[x2][y1 -1] + b[x1 -1][y1 -1 ] ; 
}

signed main(){  //修改signed
    int n , m ; 
    cin>>n >> m ;
    int max_m  =  0 ;  //取合适的有效范围
    
    for(int i = 0 ; i < n ; i++){
        int x , y , v ;
        cin>>x>>y>>v ;
        max_m = max(max_m , x);
        max_m = max(max_m , y);
        a[x+1 ][y+1 ] += v ;    //注意传值的是0,防止在前缀和计算中出现段错误;
    }

    if(m > max_m + 1) m = max_m + 1; //可以考虑

    for(int i = 1 ; i <= max_m+1; i++){    //存值时有效范围是x+1 , y+1 ,用max_m+1来卡边界
        for(int j = 1 ; j <= max_m + 1 ; j++){
            b[i][j] = a[i][j] + b[i - 1][j] +b[i][j -1 ] - b[i-1][j - 1] ; 
        }
    }

    int sum = 0 ;     

    for(int x1 = 1 ; x1 + m -1  <= max_m + 1; x1++){
        for(int y1 = 1 ; y1 + m - 1 <= max_m + 1; y1++){
            int num = qian_he(x1, y1 , x1 + m - 1 , y1 + m - 1) ; 
            sum = max(sum , num) ; 
        }
    }

    cout<<sum <<'\n'; 

    return 0 ;
}
```


# 2.林佳政
根据数学知识可知建立在中位数时为最小距离。
想象你从左向右移动一个点。
当这个点位于最左边的点左侧时，
向右移动会减少总距离（因为离右边所有点都近了）。
当你经过一个点时，
这个点对总距离的贡献从“正”变“负”
但只要左边的点数少于右边
继续向右移动仍然能减少总距离。
直到左右两边点数相等（或差1），此时达到最小值。

```C++

#include <bits/stdc++.h>

using namespace std;

int main() {

    int N;

    cin >> N;

    vector<int> arr(N);

    for(int i = 0; i < N; i ++ ) cin >> arr[i];

    sort(arr.begin(), arr.end());

    int median = arr[N / 2];

    long long total_count = 0;

    for(int i = 0; i < N; i ++ ) total_count += abs(arr[i] - median);

    cout << total_count <<endl;

    return 0;
}

```



# 3.赵亦康
### 判断是否为质数
判断质数的函数模板
```cpp
bool isprime(int x)
{
    for(int i = 2; i <= sqrt(x); i ++)   //注意这里是<=
    {
        if(x % i == 0) return false;
    }
    return true;
}
```

### 解题

首先输入n
```cpp
int n;
cin >> n;
```


由题目可得我们下标用1 - n ;
所以从第n位开始看  i = n，第n位需要一个数，我们设这个数为j；
i + j 需要是质数，所以选则用一个循环让j = 1;; j ++，直到i + j为质数
此时这个i + j作为第p(i)  + i 到第p(j) + j 的和
所以用一个循环将第 j 位到第 i 位 赋值
```cpp
for(int k = j; k <= i; k ++)
{
    a[k] = i + j - k;
}
```

举个栗子：
假设n = 7

| a[1] | a[2] | a[3] | a[4] | a[5] | a[6] | a[7] |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|      |      |      |      |      |      | i    |

然后
j = 1 ----->1 + 7 = 8 非质数
j = 2 ----->2 + 7 = 9 非质数
...
j = 4 ----->4 + 7 = 11 质数
所以i + j = 11

|      |      |      | 7    | 6    | 5    | 4    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| a[1] | a[2] | a[3] | a[4] | a[5] | a[6] | a[7] |
|      |      |      | j    |      |      | i    |


然后 因为第 j 到第 i 都已经完成了，所以令 i = j - 1; break;进入下次循环
用代码实现：
```
    for(int i = n;i >= 1;)
    {
        for(int j = 1;;j ++)
        {
            if(isprime(i + j))
            {
                for(int k = j; k <= i; k ++)
                {
                    a[k] = i + j - k;
                }
                i = j - 1;
                break;
            }
        }
    }
```


|      |      |      | 7    | 6    | 5    | 4    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| a[1] | a[2] | a[3] | a[4] | a[5] | a[6] | a[7] |
|      |      | i    |      |      |      |      |


|      | 3    | 2    | 7    | 6    | 5    | 4    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| a[1] | a[2] | a[3] | a[4] | a[5] | a[6] | a[7] |
|      | j    | i    |      |      |      |      |

| 1     | 3    | 2    | 7    | 6    | 5    | 4    |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- |
| a[1]  | a[2] | a[3] | a[4] | a[5] | a[6] | a[7] |
| i / j |      |      |      |      |      |      |

最后输出答案
```cpp
for(int i = 1; i <= n; i ++) cout << a[i] << ' ';
```

### 完整代码如下

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 1e6 + 10;
int a[N];
bool isprime(int x)
{
    for(int i = 2; i <= sqrt(x); i ++)
    {
        if(x % i == 0) return false;
    }
    return true;
}
int main()
{
    int n;
    cin >> n;
    for(int i = n;i >= 1;)
    {
        for(int j = 1;;j ++)
        {
            if(isprime(i + j))
            {
                for(int k = j; k <= i; k ++)
                {
                    a[k] = i + j - k;
                }
                i = j - 1;
                break;
            }
        }
    }
    for(int i = 1; i <= n; i ++) cout << a[i] << ' ';
}
```

# 4.汪美琳
思路:
1. 简化条件为：\(x(a+b) \leq 2ab\) 且 \(a^2 + b^2 \leq 2y^2\)（a,b≥1）。
2. 若x>y，直接输出0（无满足条件的组）。
3. 遍历a（1到y），b从max(a,x)开始（减少无效遍历），直到\(a^2 + b^2 > 2y^2\)。
4. 满足条件时，a=b则计数+1，否则+2（区分有序组(a,b)和(b,a)）。
代码优化说明:
5. 内层循环通过 \(\max(a, x)\) 限制 \(b\) 的起始值，减少无效遍历；
6. 用整数运算替代浮点数（如 \(x(a + b) \leq 2ab\) 替代 \(\frac{2ab}{a+b} \geq x\)），避免精度误差；
7. 终止条件直接使用 \(a^2 + b^2 \leq 2y^2\)，确保平方平均数符合要求。
```C++
#include <bits/stdc++.h>
using namespace std;
int main()
{
    int T, x, y, num;
    cin >> T;
    for (int i = 1; i <= T; i ++) {
        cin >> x >> y;
        if (x > y) {cout << "0" << endl; continue;}
        num = 0;
        for (int a = 1; a <= y; a ++) {
            for (int b = max (a, x); a * a + b * b <= 2 * y * y; b ++) {
                if (x * (a + b) <= 2 * a * b) {
                        if (a == b) num ++;
                        else num += 2;
                } else continue;//b可能小于正确值，所以不能是break
            }
        }
        cout << num << endl;
    }
}
```



# 5.付豪
```C++
#include <iostream>
#include <bits/stdc++.h>
using namespace std;
//本题使用二分思想来做
int main()
{
    int t;
    cin >> t;
    

    while(t--)
    {
        long long sum = 0;
        //定义左右两组边界
        long long l1, l2, r1, r2;
        long long x, y;
        cin >> x >> y;
        //a和mid为a，b测试组，2y缩小范围因为a加b小于等于2y
        for(long long a = 1; a < 2*y; a++)
        {
            l1 = 0, r1 = 2*y;
            long long mid;
            //二分思想证明左端a*mid*2 >= (a+mid)*x
            while(l1 < r1)
            {
                mid = (l1 + r1) / 2;
                if( a*mid*2 >= (a+mid)*x )
                    r1 = mid;
                else
                    l1 = mid + 1;
            }
            l2 = 0, r2 = 2*y;
            //二分思想证明右端a*a + mid*mid <= 2*y*y
            while(l2 < r2)
            {
                mid = (l2 + r2 + 1) / 2;
                if(a*a + mid*mid <= 2*y*y)
                    l2 = mid;
                else
                    r2 = mid - 1;
            }
            //要记得均值不等式使用条件哦：a,b≥1
            if(l1 < 1)
                l1 = 1;

            if(l2 - l1 + 1 > 0)
                sum += l2 - l1 + 1;
        }
        cout << sum << endl;
    }
}
```


# 7.黄禹霖
由题意得： $K = f(t)$，其中$K$是关于$t$的不减函数； 由单调性可考虑二分法求解。
```cpp
#include <bits/stdc++.h>
#define N 10010
using ll = long long;
using namespace std;
ll n,k;
ll t[N],l[N],w[N];
bool check(ll t1) {
    ll total = 0;  
    for (int i = 0; i < n; i++) {
        ll ti = t[i], li = l[i], wi = w[i];
        ll ct = ti * li + wi; //计算一次周期的时间
        total +=  t1 / ct * li; //计算经历了多少次周期
        if(t1 % ct > ti * li) total += li;//抛去停机时间
        else total += (t1 % ct) / ti;
        if(total >= k) return 1;//total ~= t1 * n 有可能爆long long
    }
    return total >= k;
}
int main(){
	int T;
	cin >> T;
	while(T--){
		cin >> n >> k;
		for(int i = 0;i < n;i++){
			cin >> t[i] >> l[i] >> w[i];
		}
		ll left = 0;
		ll right = 2e18;
		while(left + 1 < right){
			ll mid = left + right >> 1;
			if(check(mid)) right = mid;
			else left = mid;
		}
		cout << left + 1<< endl;
	}
}
```



# 9.铉绍晖
### donghedd ^ 2

#### 题目描述

donghedd很喜欢完全平方数，同时他也很喜欢递增的序列，所以他丢给了你这样一个题目。

你需要构造一个长度为 *n* 的整数序列 *a*1,*a*2,…,*a**n*，要求序列中任意连续 *m* 个数的和都是完全平方数，并且该序列中的数**严格递增**，即满足 *a*1<*a*2<⋯<*a**n*。

#### 输入格式

一行两个整数 *n*, *m*（1≤*m*≤*n*≤106），意义同题面。

#### 输出格式

一行 *n* 个整数，表示构造的序列 *a*1,*a*2,…,*a**n*。序列中任意连续 *m* 个数的和都是完全平方数，并且该序列中的数严格递增。

序列中的所有数 *a**i* 都应满足 −1012≤*a**i*≤1012，如果有多种答案，输出任意一种即可。

---

#### 思路

首先开一个足够大的数组num用来储存答案

*（为了方便解释，数组从1开始计数）*

*（数据范围很大，给储存答案的数组开个long long）*

##### 初始化数组：

题目要求每m个数的和为完全平方数，所以先对前m-1个数进行递增初始化，保证符合最基本的“递增”的条件，

新建变量sum，用来记录目前区间内所有数的总和（暂时不包括最后一个数，即sum初始为第1到m-1个数的总和）

```c++
int n,m;
cin >> n >> m;
LL sum = 0;
for (int i = 1; i <= m - 1; i++) {
	num[i] = num[i-1] + 1;
	sum += num[i];
}
```

##### 正式进入循环：

新建变量k用来计算完全平方数

首先while：如果k^2^ <  sum，那么k++，直到k^2^ >= sum，此时可以保证第i个数可以通过num[ i ] = k^2^ - sum成为一个最小的值来使sum达成k^2^这个完全平方数，在此之上仍需保证递增（即num[ i ] > num[ i - 1]），所以有了||后面的限制条件

num[ i ] = k^2^ - sum，成功求出这个num[ i ]

```c++
while (k*k < sum || k*k - sum <= num[i-1]) {
	k++;
}
num[i] = k*k - sum;
```

接下来将sum加上求出的这个num[i]，减去结尾的num[i-m+1]，为下一轮循环做准备

```c++
sum += num[i];
sum -= num[i-m+1];
```

直到i推进到m，成功求完所有m个数

最后依次输出即可

---

#### AC代码

```c++
#include <iostream>
#include <cmath>

using namespace std;

typedef long long LL;

LL num[1000010];

int main (){

    int n,m;
    cin >> n >> m;
    LL sum = 0;
    
    //初始化数组前m-1项
    for (int i = 1; i <= m - 1; i++) {
        num[i] = num[i-1] + 1;
        sum += num[i];
    }
    
    LL k = 1;
    //从m项开始正式进入循环
    for (int i = m ; i <= n; i++) {
        while (k*k < sum || k*k - sum <= num[i-1]) {
            k++;
        }
        num[i] = k*k - sum;

        sum += num[i];
        sum -= num[i-m+1];
    }

    //输出答案
    for (int i = 1; i <= n; i++) {
        cout << num[i] << ' ';
    }
    

    return 0;
}
```


# 10.罗闽渝
**思路：**

这道题中，当其他石头堆数量为1，选择最后一堆数量为2的石头堆并分成两堆的人获胜。

那么可以通过分石头堆的总次数来找到最后一个分石头的人，从而判断谁获胜：

当总次数为奇数时，最后分石头的人是先手，先手获胜；当总次数为偶数时，先手失败。



找总次数：

假设一个石头堆里有a(a > 1)个石头，每次分两堆，不论每次怎么分，分到最后（每一堆都只有一个石头时），经历的步骤都是a - 1次。那么总次数就是  总石头数 - 石头堆数



**代码：**

```c++
#include <iostream>

using namespace std;

int main()
{
    int n;
    cin >> n;

    long long sum = 0; //石头总数 <= 4*1e10，爆int
    int b = n; 
    while(b --)
    {
        int a;
        cin >> a;
        sum += a;
    }

    if((sum - n) % 2 == 0) //判断分石头堆总次数的奇偶
        cout << "咦~";
    else
        cout << "负责东giegie太厉害啦";

    return 0;
}
```



# 11.王硕



# 12.代泽阳
### 思路
对每组数据进行处理，由测试样例可看出，每组数据是无序的，因此此处注意排序
输入数据并排序后进行操作，题目要求高度相差不会超过 K，两个数据差，因此该题采用双指针进行操作
注：用cin和cout会超时，这时可以使用 ios::sync_with_stdio(false)和cin.tie(nullptr)加快cin和cout输入输出效率
但是scanf和printf不会
### ac代码
```C++
#include <bits/stdc++.h>                //c++万能库包含大部分主流库
using namespace std;
const int N = 1e6+10;
int T,n,k,ans;
int a[N];        //定义全局变量，自动初始化为0，操作方便
int done (int a[N]) {
    int com=0;
    for (int i=1,j=1;i<=n;i++) {          //j为左指针，i为右指针进行移动
        while (a[i]-a[j]>k&&i>j) {       //条件：i在右边，且最高手办与最低手办相差大于k时
            j++;                                      //左指针右移
        }
        com=max(com,i-j+1);           //找到合适指针范围（相差高度刚好不超过k）求出此时手办数并求最大值
    }

    return com;                              //返回最大值
}
int main()
{
    scanf("%d",&T);
    for (int i=0;i<T;i++) {              //对每组数据操作
        scanf("%d %d",&n,&k);
        for (int j=1;j<=n;j++) scanf("%d",&a[j]);
        sort (a+1,a+n+1);
        ans=done(a);                      //求每组最大值
        printf("%d\n",ans);            //输出
    }

    return 0;
}
```



# 14.朱俊豪
---
tags:
  - 题解
---
## 题意理解
  根据题意，**保证对于任意的 5≤i≤n，有 ai​=(ai−1​×ai−2​)mod10**，知道a[5]及其以后的值根本不影响a[1],a[2]的判断。所以完全可以通过**遍历所给范围内所有的数字**，执行if判断，如果满足题目的条件，输出；不满足，继续遍历。当范围内所有数字遍历完成后依旧没有输出，那么输出两个-1.
```C++
		if ( n == 3 ) {
            for ( int i = l1; i <= r1; i ++ ) {
                for ( int j = l2; j <= r2; j ++ ) {
                    if ( i * j % 10 == a[3] ) {
                        cout << i << " " << j << endl;
                        goto home;
                    }
                }
            }
        } else {
            for ( int i = l1; i <= r1; i ++ ) {
                for ( int j = l2; j <= r2; j ++ ) {
                    if ( i * j % 10 == a[3] 
                    && j * a[3] % 10 == a[4] ) {
                        cout << i << " " << j << endl;
                        goto home;
                    }
                }
            }
        }
```
## 优化
  如果只根据上述思路写两重从l1到r1，l2到r2的for循环，**由于1≤L1​≤R1​≤1e9,1≤L2​≤R2​≤1e9,3≤n≤5×1e5**，所以会使得提交代码显示超时。于是我们进行优化。
```C++
	for ( int i = l1; i <= r1; i ++ ) {
		for ( int j = l2; j <= r2; j ++ ) {
			// 直接这样写会超时
		}
	}
```
  注意到数组中元素的规律是取前两个元素相乘的个位，多列几个数字相乘的式子，不难发现**积的个位数字只与两个乘数的个位数字有关**。

| 乘数  | 乘数  | 积   | 积mod10 |
| --- | --- | --- | ------ |
| 5   | 5   | 25  | 5      |
| 15  | 15  | 225 | 5      |
| 15  | 25  | 375 | 5      |
| 25  | 25  | 625 | 5      |
  据此，我们可以缩小遍历范围：
  可以将个位数字为0~9的数看为一个周期。所以20~29为一个周期，10~19为一个周期，一个周期的长度为10。
  读入l1,r1后我们对r1进行判断，如果超过了**l1 + 10就证明给出的区间超过了一个周期，会重复无用的判断**，所以让r1减小到**l1 + 10**保证区间只在一个周期内。
  当然，如果给出的区间本身就是一个周期内的，那么就无需改变。
```C++
	    if ( r1 > l1 + 10 ) r1 = l1 + 10;
        if ( r2 > l2 + 10 ) r2 = l2 + 10;
        // 通过这种方式可以将循环的遍历范围缩小，不会再产生超时问题。
```
## 代码实现
```C++
#include <iostream>
#define int long long // 会爆int

using namespace std;

signed main ( ) {
    int t;
    cin >> t;
    while ( t -- ) {
        int n, l1, r1, l2, r2;
        cin >> n >> l1 >> r1 >> l2 >> r2;
        int a[n + 1];
        a[0] = 0, a[1] = 0, a[2] = 0;
        for ( int i = 3; i <= n; i ++ ) {
            cin >> a[i];
        }
        // 从a[3]读入仅仅为了方便，从i = 0开始读入应该没有问题。
        
        if ( r1 > l1 + 10 ) r1 = l1 + 10;
        if ( r2 > l2 + 10 ) r2 = l2 + 10;
        // 简化循环
        
        if ( n == 3 ) {
            for ( int i = l1; i <= r1; i ++ ) {
                for ( int j = l2; j <= r2; j ++ ) {
                    if ( i * j % 10 == a[3] ) {
                        cout << i << " " << j << endl;
                        goto home;
                    }
                }
            }
        } else {
            for ( int i = l1; i <= r1; i ++ ) {
                for ( int j = l2; j <= r2; j ++ ) {
                    if ( i * j % 10 == a[3] 
                    && j * a[3] % 10 == a[4] ) {
                        cout << i << " " << j << endl;
                        goto home;
                    }
                }
            }
        }
        cout << "-1 -1" << endl;
        // 如果一直没有符合条件的数字，那么循环结束，会执行输出-1的语句。
        
        home : ;
        // 使用 goto 语句是因为i和j都是从小的开始遍历，一定会满足题目中字典序最小的要求。所以直接输出然后进行下一组数据的处理。或许可以将while循环内的所有代码改到一个自行写的solve函数中，将 goto 语句改为 return 语句应该更好。
        
    }
}
```



