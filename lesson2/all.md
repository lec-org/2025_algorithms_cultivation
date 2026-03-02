### 第四题
分析操作
- **情况1**：C < A柱最上面的圈 ，直接放A柱
- **情况2**：C ≥ A柱最上面的圈
    - **2a**：B柱为空 或 C > B柱最上面的圈 ， 放B柱
    - **2b**：C ≤ B柱最上面的圈
        1. 将A柱当前宝塔作为一件成品
        2. 将B柱上所有比C大的圈移到A柱
        3. 将C放到A柱
收尾操作
- A柱剩余宝塔作为一件成品
- B柱剩余所有圈堆成一件成品（B柱始终从小到大排序，栈顶最大，取下后自然形成从大到小的宝塔）
  
  在a柱和b柱更新时统计成品数量以及最高高度
  
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int N;
    cin >> N;
    
    stack<int> A, B;  
    int tower_count = 0;
    int max_height = 0;
    
    int first;
    cin >> first;
    A.push(first);
    
    for (int i = 1; i < N; i++) {
        int C;
        cin >> C;
        
        if (C < A.top()) {
            A.push(C);
        } else {
            if (B.empty() || C > B.top()) {
                B.push(C);
            } else {
                tower_count++;
                max_height = max(max_height, (int)A.size());
                while (!A.empty()) A.pop();  
                
                stack<int> temp;  
                while (!B.empty()) {
                    if (B.top() > C) {
                        A.push(B.top());
                    } else {
                        temp.push(B.top());
                    }
                    B.pop();
                }
                while (!temp.empty()) {
                    B.push(temp.top());
                    temp.pop();
                }
                
                A.push(C);
            }
        }
    }
    
    if (!A.empty()) {
        tower_count++;
        max_height = max(max_height, (int)A.size());
    }
    
    if (!B.empty()) {
        tower_count++;
        max_height = max(max_height, (int)B.size());
    }
    
    cout << tower_count << " " << max_height << endl;
    return 0;
}
  
```

### 第七题题解
bfs题型
遇到陆地，bfs遍历所有陆地，标记访问过
更新岛屿数量，以及有宝藏的岛屿数量

```cpp
#include <bits/stdc++.h>
using namespace std;

const int dx[4] = {-1, 0, 1, 0};
const int dy[4] = {0, 1, 0, -1};

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int n, m;
    cin >> n >> m;
    
    vector<string> grid(n);
    for (int i = 0; i < n; i++) {
        cin >> grid[i];
    }
    
    int total_islands = 0;      
    int treasure_islands = 0;   
    
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (grid[i][j] != '0') {
                bool has_treasure = false;
                queue<pair<int, int>> q;
                
                q.push({i, j});
                if (grid[i][j] >= '2') has_treasure = true;
                grid[i][j] = '0'; 
                
                while (!q.empty()) {
                    auto [x, y] = q.front();
                    q.pop();
                    
                    for (int d = 0; d < 4; d++) {
                        int nx = x + dx[d];
                        int ny = y + dy[d];
                        
                        if (nx >= 0 && nx < n && ny >= 0 && ny < m && grid[nx][ny] != '0') {
                            if (grid[nx][ny] >= '2') has_treasure = true;
                            grid[nx][ny] = '0'; 
                            q.push({nx, ny});
                        }
                    }
                }
                

                total_islands++;
                if (has_treasure) treasure_islands++;
            }
        }
    }
    
    cout << total_islands << " " << treasure_islands << endl;
    return 0;
}
```