
## 1. 闭合圈涂色 (BFS / Flood Fill)

**💡 核心点拨（逆向思维与连通块搜索）**：
* **难点分析**：直接用代码判断一个 0 是不是“被包围”的非常困难，因为我们不知道闭合圈的具体形状。
* **破局思路（逆向思维）**：既然找“圈内”很难，那我们就去找“圈外”！只要是在圈外的 0，它们一定与方阵的边界相通。
* **具体步骤**：
    1.  **扩充边界**：在原本的矩阵外围，人为地加上一圈“虚拟的 0”，让所有圈外的 0 连通成一个大区块。
    2.  **全面蔓延**：从左上角 `(0, 0)` 这个虚拟起点开始，像“油漆桶”一样进行广度优先搜索 (BFS)。
    3.  **结果结算**：只要是 BFS 能搜到的 0 都是圈外的；等 BFS 结束后，原图里那些依旧**没被访问过**的 0，就是被死死包围的目标，直接替换成 2 即可。

**⏱️ 复杂度**：时间 $O(n^2)$，空间 $O(n^2)$。

```cpp
#include <iostream>
#include <queue>
using namespace std;

const int MAXN = 1005;
int grid[MAXN][MAXN];
bool vis[MAXN][MAXN];
// 上下左右四个方向
int dx[4] = {-1, 1, 0, 0}, dy[4] = {0, 0, -1, 1};

int main() {
    int n;
    if (!(cin >> n)) return 0;
    
    // 从下标 1 开始读入，巧妙空出 0 行/列和 n+1 行/列作外围
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            cin >> grid[i][j];

    queue<pair<int, int>> q;
    q.push({0, 0});
    vis[0][0] = true;

    // BFS 寻找所有圈外的 0
    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();
        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i], ny = y + dy[i];
            // 搜索范围包含外围的虚拟 0
            if (nx >= 0 && nx <= n + 1 && ny >= 0 && ny <= n + 1) {
                if (grid[nx][ny] == 0 && !vis[nx][ny]) {
                    vis[nx][ny] = true;
                    q.push({nx, ny});
                }
            }
        }
    }

    // 遍历原图，没被 BFS 访问到的 0 填 2
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            if (grid[i][j] == 1) cout << 1 << " ";
            else if (vis[i][j]) cout << 0 << " ";
            else cout << 2 << " ";
        }
        cout << "\n";
    }
    return 0;
}
```

---

## 2. 01 平衡字符串 (DFS 暴力回溯)

**💡 核心点拨（找规律与搜索树）**：
* **寻找规律**：题目要求“连续子串 01 和 10 数量相等”。随手写几个串就会发现：中间的字符无论怎么翻转，01 和 10 的数量必定同增同减，**它们的差值根本不会变**！
* **核心结论**：“平衡”完全等价于**“首尾字符相同”**。
* **算法选择**：因为题目给的字符串长度极小（$n \le 10$），直接暴力破局！
    1.  **DFS 爆搜**：用深度优先搜索把所有的 `?` 模拟成 0 或 1（相当于遍历一棵二叉树）。
    2.  **终点结算**：当搜到字符串末尾时，检查首尾字符是否相同。
        * 若相同 $\rightarrow$ 原串平衡，中间任意位置翻转都行。
        * 若不同 $\rightarrow$ 原串不平衡，只能翻转头部或尾部。

**⏱️ 复杂度**：时间 $O(2^p)$（$p$ 为问号数量），空间 $O(n)$。

```cpp
#include <iostream>
#include <string>
using namespace std;

const int MOD = 1e9 + 7;
long long total_val = 0;
int n;

void dfs(int index, string& s) {
    // 终点结算：当所有问号都填满后
    if (index == n) {
        if (n == 1) total_val = (total_val + 1) % MOD;
        else {
            // 规律：只看首尾是否相同
            if (s[0] == s[n - 1]) total_val = (total_val + n - 2) % MOD;
            else total_val = (total_val + 2) % MOD;
        }
        return;
    }

    // 遇到问号，分别尝试填 0 和 1（回溯思想）
    if (s[index] == '?') {
        s[index] = '0'; dfs(index + 1, s);
        s[index] = '1'; dfs(index + 1, s);
        s[index] = '?'; // 恢复现场，不影响其他分支
    } else {
        dfs(index + 1, s);
    }
}

int main() {
    int t;
    if (cin >> t) {
        while (t--) {
            cin >> n;
            string s;
            cin >> s;
            total_val = 0; 
            dfs(0, s);
            cout << total_val << "\n";
        }
    }
    return 0;
}
```

---

## 3. 男女配对 (前缀和与相对差值)

**💡 核心点拨（相对差值转化为 0）**：
* **问题转化**：直接数区间内男女数量必定超时。巧妙做法是**赋予正负值**：女生看作 -1，男生看作 1。这样“男女人数相等”就等价于**“区间数字总和为 0”**！
* **前缀和技巧**：怎么快速判断一段区间和为 0？
    1.  维护一个前缀和变量，边遍历边累加。
    2.  如果 `当前前缀和` 在之前出现过，说明两次出现之间的这段区间，和没有发生变化（即增加了 0，男女数量抵消了）。
* **贪心记录**：为了让合法区间拉到最长，我们总是用当前下标去减去这个前缀和**第一次**出现的下标。

**⏱️ 复杂度**：时间 $O(n)$，空间 $O(n)$。

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    int n;
    if (!(cin >> n)) return 0;

    // 前缀和范围是 [-n, n]，为了防止数组出现负数下标，统一加上偏移量 n
    // 大小开 2n+5 确保安全
    vector<int> first_pos(2 * n + 5, -2); 
    int current_sum = 0, max_len = 0;
    
    // 前缀和为 0 的初始位置在虚拟的下标 0 处，代表没选任何人时和为 0
    first_pos[n] = 0; 

    for (int i = 1; i <= n; i++) {
        int x; cin >> x;
        current_sum += (x == 0 ? -1 : 1); // 女生 -1，男生 1
        
        int index = current_sum + n; 
        if (first_pos[index] != -2) {
            // 发现该前缀和之前出现过，说明中间这段区间和为 0
            max_len = max(max_len, i - first_pos[index]); 
        } else {
            // 贪心：只记录第一次出现的位置，保证区间拉到最长
            first_pos[index] = i; 
        }
    }
    cout << max_len << "\n";
    return 0;
}
```

---

## 4. 序列操作大模拟 (C++ STL 容器熟练度)

**💡 核心点拨（拒绝手写造轮子）**：
* **避坑指南**：纯模拟题不考高级算法，专考代码基本功。切忌使用原生的 C 语言数组和 for 循环去手动移动元素，极易引发“数组越界”惨剧。
* **STL 神器调用**：
    1.  **替换子串**：用 `<algorithm>` 里的 `std::search` 找到目标，调用 `erase` 删掉，再用 `insert` 把新串塞进去。
    2.  **插入新数字**：不要在原来的 `vector` 上边遍历边插入（容易导致迭代器失效），直接开一个**新 `vector`**，存好旧元素和平均数后，最后直接覆盖原数组。
    3.  **翻转区间**：调用 `std::reverse` 一行搞定。

**⏱️ 复杂度**：时间 $O(M \times L)$（$L$ 为动态变化的数组长度），空间 $O(L)$。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    int n, m;
    if (!(cin >> n >> m)) return 0;

    vector<int> a(n);
    for (int i = 0; i < n; ++i) cin >> a[i];

    while (m--) {
        int op; cin >> op;
        if (op == 1) { // 查找与替换
            int l1; cin >> l1;
            vector<int> seq1(l1); for (int i = 0; i < l1; ++i) cin >> seq1[i];
            int l2; cin >> l2;
            vector<int> seq2(l2); for (int i = 0; i < l2; ++i) cin >> seq2[i];

            // 查找子串第一次出现的位置
            auto it = search(a.begin(), a.end(), seq1.begin(), seq1.end());
            if (it != a.end()) {
                int pos = distance(a.begin(), it);
                a.erase(it, it + l1); // 删掉原序列匹配段
                a.insert(a.begin() + pos, seq2.begin(), seq2.end()); // 插入新序列
            }
        } else if (op == 2) { // 插入相邻偶数和的平均数
            vector<int> next_a; // 重新开个数组，安全又省事
            for (size_t i = 0; i < a.size(); ++i) {
                next_a.push_back(a[i]);
                if (i + 1 < a.size() && (a[i] + a[i + 1]) % 2 == 0) {
                    next_a.push_back((a[i] + a[i + 1]) / 2); 
                }
            }
            a = next_a;
        } else if (op == 3) { // 翻转区间
            int l, r; cin >> l >> r;
            // 注意 C++ 迭代器是左闭右开的，而且题目给的是 1-based 下标
            reverse(a.begin() + l - 1, a.begin() + r); 
        }
    }

    // 按要求输出格式
    for (size_t i = 0; i < a.size(); ++i) cout << a[i] << (i == a.size() - 1 ? "" : " ");
    cout << "\n";
    return 0;
}
```

---

## 5. 聚餐 AA 制选人 (二分答案 + 贪心校验)

**💡 核心点拨（单调性与二分搜索）**：

- **寻找单调性**：这道题最核心的观察是——如果我们可以请 $m$ 个人吃饭，那么请 $m-1$ 个人也一定行（只需踢掉那个点菜最贵的人）。既然答案满足单调性，我们就可以**二分搜索**最大人数 $m$。
- **如何校验 (Check)**：给定要邀请的人数 $m$，我们要看能不能找到 $m$ 个同学满足条件。
    1. 先把所有人按底线 $r$ 从大到小排序。
    2. 当我们遍历到第 $i$ 个人时，假设他是这 $m$ 个人里底线最低的（即 $r_{min} = r_i$）。
    3. 为了让均摊费用最低，我们一定是从前 $i$ 个人里选出**点菜最便宜的 $m$ 个人**。
    4. 我们用一个**大根堆 (priority_queue)** 实时维护前 $i$ 个人里最便宜的 $m$ 道菜。
    5. 只要满足 `这 m 道菜的总和 <= m * r[i]`，说明 $m$ 个人是可行的。
**⏱️ 复杂度**：时间 $O(N \log^2 N)$，空间 $O(N)$。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <queue>

using namespace std;

typedef long long i64;

struct Student {
    int w, r;
};

// 校验函数：检查能否邀请 m 个人
bool check(int m, int n, const vector<Student>& students) {
    if (m == 0) return true;

    priority_queue<int> pq; // 大根堆，维护当前最便宜的 m 道菜
    i64 current_sum = 0;

    for (int i = 0; i < n; i++) {
        pq.push(students[i].w);
        current_sum += students[i].w;

        // 如果堆里超过了 m 个人，就把最贵的那个踢掉
        if (pq.size() > m) {
            current_sum -= pq.top();
            pq.pop();
        }

        // 当堆里正好有 m 个人时，校验当前的最低底线 students[i].r
        if (pq.size() == m) {
            // 只要总价 <= 人数 * 当前最低底线，方案就合法
            if (current_sum <= (i64)m * students[i].r) {
                return true;
            }
        }
    }
    return false;
}

void solve() {
    int n;
    if (!(cin >> n)) return;
    vector<Student> students(n);
    for (int i = 0; i < n; i++) cin >> students[i].w;
    for (int i = 0; i < n; i++) cin >> students[i].r;

    // 关键：按心理底线从大到小排序，保证遍历到 i 时，r[i] 是当前底线的瓶颈
    sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
        return a.r > b.r;
    });

    // 二分搜索最大人数
    int low = 1, high = n, ans = 0;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (check(mid, n, students)) {
            ans = mid;
            low = mid + 1; // 尝试更多人
        } else {
            high = mid - 1; // 人太多了，减少人数
        }
    }
    cout << ans << "\n";
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

---

## 6. 画展买门票 (滑动窗口 / 双指针)

**💡 核心点拨（尺取法）**：
* **痛点分析**：暴力枚举左右端点时间复杂度高达 $O(n^2)$。
* **单调性利用**：如果一个长区间已经满足“集齐名师”，那么比它更长的超集必定也满足，但多花了冤枉钱。我们只想要**最短合法区间**。
* **双指针操作（滑动窗口）**：
    1.  **右指针（负责找齐）**：向右滑动，把路过的画作吞进窗口，直到所有名师的画都至少有一幅。
    2.  **左指针（负责省钱）**：一旦集齐，左指针开始向右收缩踢人。只要踢掉后仍满足“集齐”条件，左指针就继续缩，并不断更新最短记录。直到某位名师的画只剩一幅被踢掉，右指针再接力往右找。

**⏱️ 复杂度**：时间 $O(n)$，空间 $O(m)$。

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    int n, m;
    if (!(cin >> n >> m)) return 0;
    vector<int> a(n + 1);
    for (int i = 1; i <= n; i++) cin >> a[i];

    // count 用来记录当前窗口内，每位画家的画作数量
    vector<int> count(m + 1, 0); 
    int collected = 0, min_len = 1e9, ans_l = -1, ans_r = -1;
    int left = 1; 

    for (int right = 1; right <= n; right++) {
        // 如果这位画家的画是第一次进入窗口，收集进度 +1
        if (count[a[right]] == 0) collected++; 
        count[a[right]]++;

        // 当收集满所有画家的画后，开始尝试从左边压缩窗口省钱
        while (collected == m) {
            if (right - left + 1 < min_len) { // 更新当前找到的最短合法区间
                min_len = right - left + 1;
                ans_l = left; ans_r = right;
            }
            // 尝试扔掉最左边的画作
            count[a[left]]--;
            // 如果某画家的画作彻底没了，打破循环，右指针需要继续去寻找补充
            if (count[a[left]] == 0) collected--; 
            left++; 
        }
    }
    cout << ans_l << " " << ans_r << "\n";
    return 0;
}
```

---

## 7. 龙之吐息 (网格对角线预处理)

**💡 核心点拨（数学规律换取时间）**：
* **时间刺客**：如果对每个格子都向四个方向写循环暴力求和，最坏时间复杂度会达到 $O(N \times M \times \max(N,M))$，必定超时。
* **网格图隐藏规律**：十字形的攻击范围其实就是**两条贯穿全图的对角线**！
    * **主对角线 (`\`)**：所有格子满足 **行号 - 列号 (`i - j`)** 是固定常数。
    * **副对角线 (`/`)**：所有格子满足 **行号 + 列号 (`i + j`)** 是固定常数。
* **算法优化**：
    1.  **第一遍扫图**：算出每一条对角线上怪物数量的总和，存在数组里。
    2.  **第二遍算答案**：枚举龙在 `(i, j)` 点吐息，威力直接 $O(1)$ 算出：`主线总和 + 副线总和 - 中心点自身值（去重）`。

**⏱️ 复杂度**：时间 $O(N \times M)$，空间 $O(N + M)$。

```cpp
#include <iostream>
#include <vector>
using namespace std;

void solve() {
    int n, m; cin >> n >> m;
    vector<vector<long long>> a(n, vector<long long>(m));
    // 偏移量 m 用来防止 i - j 出现负数索引
    vector<long long> main_diag(n + m + 5, 0); 
    vector<long long> anti_diag(n + m + 5, 0); 

    // 第一遍预处理：把怪兽数量分别上交累加到它们所属的两条对角线阵营里
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> a[i][j];
            main_diag[i - j + m] += a[i][j]; 
            anti_diag[i + j] += a[i][j];     
        }
    }

    long long max_ans = 0;
    // 第二遍算最大值：两条线相加，减去重复相加的十字中心点
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            long long current_power = main_diag[i - j + m] + anti_diag[i + j] - a[i][j];
            max_ans = max(max_ans, current_power);
        }
    }
    cout << max_ans << "\n";
}

int main() {
    int t; if (cin >> t) while (t--) solve();
    return 0;
}
```

---

## 8. 斐波那契字符串 (DP 动态规划递推)

**💡 核心点拨（状态转移的拆分）**：
* **严禁暴力**：字符串呈指数级增长，$S_{40}$ 就已经存不下了，**绝对不能用代码去拼接真实字符串**！
* **状态拆解**：根据公式 $S_n = S_{n-2} + S_{n-1}$，大串里的“逆序对”无非藏在三个地方：
    1.  完全属于左半段 $S_{n-2}$ 内部的逆序对。
    2.  完全属于右半段 $S_{n-1}$ 内部的逆序对。
    3.  **跨界逆序对**：拼接缝隙处产生的新逆序对。数量等于 **左段的 `1` 的数量 $\times$ 右段的 `0` 的数量**！
* **DP 预处理**：维护三个数组（0的数量、1的数量、逆序对总数），从 3 一路递推到 100000。采用“预处理打表”思想，查询时 $O(1)$ 直接出结果。

**⏱️ 复杂度**：时间 $O(N)$ 预处理打表，查询 $O(1)$；空间 $O(N)$。

```cpp
#include <iostream>
using namespace std;

const int MOD = 1e9 + 7;
const int MAXN = 100005;
long long c0[MAXN], c1[MAXN], inv[MAXN]; 

void init() {
    // 初始状态奠基
    c0[1] = 1; c1[1] = 0; inv[1] = 0; 
    c0[2] = 0; c1[2] = 1; inv[2] = 0; 

    // 按状态转移方程一路推平过去
    for (int i = 3; i < MAXN; i++) {
        c0[i] = (c0[i - 2] + c0[i - 1]) % MOD;
        c1[i] = (c1[i - 2] + c1[i - 1]) % MOD;
        // 计算跨界新增加的逆序对 = 左半部的 1 遇到右半部的 0
        long long cross_inv = (c1[i - 2] * c0[i - 1]) % MOD;
        // 总逆序对 = 左右各自原有的逆序对之和 + 新产生的跨界逆序对
        inv[i] = (inv[i - 2] + inv[i - 1] + cross_inv) % MOD;
    }
}

int main() {
    init(); // 提前打表
    int t; if (cin >> t) {
        while (t--) {
            int n; cin >> n;
            cout << inv[n] << "\n"; // O(1) 瞬间回答
        }
    }
    return 0;
}
```

---

## 9. 骑车赶派对 (分层图最短路 / 二维状态 Dijkstra)

**💡 核心点拨（最短路升维：增加“潜力”维度）**：
* **传统误区**：普通 Dijkstra 认为“到达时间越早越优”。但本题中，晚一点到达某城市，可能换来一辆速度奇快的自行车，从而后来居上。因此，“到达得早，不如手里车子好”。
* **升维打击**：既然时间不是唯一标准，就把**【所在的城市】**和**【手里最快的车子系数】**组合起来，构成一个**二维状态节点**！
    * 设 `dist[u][s]` 代表：到达城市 $u$，且揣着最快系数为 $s$ 的车时，所需的最短时间。
* **状态转移**：在堆中扩展时，不是简单地走向下个城市，而是拿着车子一起走。到了新城市，比较手里的车和当地的车，**更新拿到的最好车子**，再把新状态存入优先队列。

**⏱️ 复杂度**：时间 $O(M \times S \log(N \times S))$，空间 $O(N \times S)$。

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

const long long INF = 1e18;
struct Edge { int to; long long w; };
struct State {
    long long time; 
    int u, s;
    // 小根堆重载：让花费时间小的优先出队处理
    bool operator>(const State& other) const { return time > other.time; }
};

void solve() {
    int n, m; cin >> n >> m;
    vector<vector<Edge>> adj(n + 1);
    for (int i = 0; i < m; i++) {
        int u, v; long long w; cin >> u >> v >> w;
        adj[u].push_back({v, w}); adj[v].push_back({u, w});
    }

    vector<int> s(n + 1);
    for (int i = 1; i <= n; i++) cin >> s[i];

    // 二维距离数组 dist[u][s]: 到达城市 u，且有速度系数为 s 的车的最小时间
    vector<vector<long long>> dist(n + 1, vector<long long>(1005, INF));
    vector<vector<bool>> vis(n + 1, vector<bool>(1005, false));
    priority_queue<State, vector<State>, greater<State>> pq;

    dist[1][s[1]] = 0;
    pq.push({0, 1, s[1]});

    while (!pq.empty()) {
        State curr = pq.top(); pq.pop();

        // 根据 Dijkstra 的贪心特性，第一次把终点城市弹出堆时，必定是全局最优时间
        if (curr.u == n) { 
            cout << curr.time << "\n";
            return;
        }

        if (vis[curr.u][curr.s]) continue;
        vis[curr.u][curr.s] = true;

        // 向四周连通的城市探索
        for (auto& edge : adj[curr.u]) {
            int v = edge.to; long long w = edge.w;
            // 走到下一个城市，拿手里现有的车和当地的车比一比，带上更快的那个（系数更小的）
            int next_s = min(curr.s, s[v]); 
            // 路上花的时间 = 道路长度 * 手里目前骑的车子的系数
            long long next_time = curr.time + w * curr.s; 

            if (next_time < dist[v][next_s]) {
                dist[v][next_s] = next_time;
                pq.push({next_time, v, next_s});
            }
        }
    }
}

int main() {
    int t; if (cin >> t) while (t--) solve();
    return 0;
}
```