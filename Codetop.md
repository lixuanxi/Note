# Codetop



## 3. 无重复字符的最长子串

**双指针**
定义两个指针 $i,j(i<=j)$，表示当前扫描到的子串是 $[i,j]$ (闭区间)。
扫描过程中维护一个哈希表，表示 $[i,j]$ 中每个字符出现的次数。

**时间复杂度：**

由于 $i,j$ 均最多增加 $n$ 次，且哈希表的插入和更新操作的复杂度都是 $O(1)$

总时间复杂度 $O(n)$

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> hash;
        int res = 0;
        for (int i = 0, j = 0; i < s.size(); i++) {
            hash[s[i]]++;
            while (hash[s[i]] > 1) hash[s[j++]]--;
            res = max(res, i - j + 1);
        }
        return res;
    }
};
```



## 206. 反转链表

**迭代**

翻转即将所有节点的next指针指向前驱节点。

由于是单链表，我们在迭代时不能直接找到前驱节点，所以我们需要一个额外的指针保存前驱节点。同时在改变当前节点的next指针前，不要忘记保存它的后继节点。

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;		  // prev 为 p1 第一个节点
        ListNode* cur = head;			 // cur 为 p2 第二个节点
        while (cur) {
            ListNode* tmp = cur->next;	  // 先记录前驱节点
            cur->next = prev;			 // 后面指向前面，p2->next = p1 
            prev = cur;					// 移动2个指针
            cur = tmp;
        }							   // 循环退出，p2指向空，p1指向最后一个点
        return prev;					// 最后cur指向空，prev指向最后一个节点
    }
};
```

**递归**

首先我们先考虑 `reverseList` 函数能做什么，它可以翻转一个链表，并返回新链表的头节点，也就是原链表的尾节点。
所以我们可以先递归处理 `reverseList(head->next)`，这样我们可以将以 `head->next` 为头节点的链表翻转，并得到原链表的尾节点 `tail`，此时 `head->next` 是新链表的尾节点，我们令它的 `next` 指针指向`head`，并将 `head->next` 指向空即可将整个链表翻转，且新链表的头节点是 `tail`。

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head || !head->next) return head;
        ListNode* tail = reverseList(head->next);
        head->next->next = head;
        head->next = nullptr;
        return tail;
    }
};
```



## 145. LRU 缓存

使用双向链表和一个哈希表

- `双向链表` 用来将 `Node` 根据 `使用时间` 进行排序。靠近 `左端` 表示 `最近使用`，靠近 `右端` 表示 `较长时间没有` 使用。
- 哈希表存储节点的 `key` 和 `value` 

```c++
class LRUCache {
public:
    struct Node {
        int key, val;
        Node *left, *right; // 双链表的两个指针
        Node(): key(0), val(0), left(NULL), right(NULL) {}
        Node(int _key, int _val): key(_key), val(_val), left(NULL), right(NULL) {}
    }*L, *R;
    // *L, *R 是 结构体变量, L 是双向链表的头, R 是双向链表的尾. L, R 是固定不动的
    unordered_map<int, Node*> hash;
    int n;          // 记录容量

    LRUCache(int capacity) {
        n = capacity;
        // 上面声明定义成全局变量, 这里要用 new 初始化 
        L = new Node(-1, -1), R = new Node(-1, -1); 
        L->right = R, R->left = L;
    }
    
    void remove(Node* p) {
        p->right->left = p->left;
        p->left->right = p->right;
    }

    void insert(Node* p) {
        p->right = L->right;
        p->left = L;
        L->right->left = p;
        L->right = p;
    }

    int get(int key) {
        if (!hash.count(key)) return -1;
        auto p = hash[key];
        remove(p);
        insert(p);
        return p->val;
    }
    
    void put(int key, int value) {
        if (hash.count(key)) {
            auto p = hash[key];
            p->val = value;
            remove(p);
            insert(p);
        } else {
            if (hash.size() == n) {
                auto p = R->left;
                remove(p);
                hash.erase(p->key);
                delete p;
            }
            auto p = new Node(key, value);
            hash[key] = p;
            insert(p);
        }
    }
};
```



## 215. 数组中的第K个最大元素

**常规快排**

```c++
class Solution {
public:
    int quick_sort(vector<int>& nums, int l, int r, int k) {
        if (l == r) return nums[l];
        int s = rand() % (r - l + 1) + l;       //随机生成left到right的整数
        swap(nums[l],nums[s]);                  //随机更换key值避免最差情况
        int x = nums[l + r >> 1], i = l - 1, j = r + 1;
        while (i < j) {
            do i++; while (nums[i] < x);
            do j--; while (nums[j] > x);
            if (i < j) swap(nums[i], nums[j]);
        }
        int sl = j - l + 1;
        if (k <= sl) return quick_sort(nums, l, j, k);
        else return quick_sort(nums, j + 1, r, k - sl);
    }
    int findKthLargest(vector<int>& nums, int k) {
        int n = nums.size();
        return quick_sort(nums, 0, n - 1, n - k + 1);	//第k大 = 第n-k+1小
    }
};
```



## 15. 三数之和

**双指针**

因为没有三指针算法，所以为了处理三个数的和的问题，我们需要先固定一个元素，然后对剩下的数进行双指针算法。

双指针算法的应用前提是要求数组的元素有序，所以需要对原数组进行排序。

同时为了避免答案重复，在枚举过程中需要跳过相同元素。

1. 枚举每个数，先确定 $nums[i]$，在排序后的情况下，通过双指针 $l$，$r$ 分别从左边 $l = i + 1$ 和右边 $r = n - 1$
    往中间靠拢，找到 `nums[i] + nums[l] + nums[r] == 0` 的所有符合条件的搭配
2. 判重处理
    当 `i>0(i不是第一个数) && nums[i] == nums[i - 1]`，表示当前确定好的数与上一个一样，需要直接 `continue`；
    双指针内当 `sum == 0` 要跳过相同的 $j$ 和 $k$, 当 `sum < 0` 移动 $j$, 当 `sum > 0` 移动 $k$ 。

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        int n = nums.size();
        for (int i = 0; i < n - 2; i++) {
            if(i > 0 && nums[i] == nums[i - 1]) continue;
            int j = i + 1, k = n - 1;
            /* if (nums[i] + nums[i + 1] + nums[i + 2] > 0) break; 
            // 最小的三个数都大于0了，所以跳过
            if (nums[i] + nums[k - 1] + nums[k] < 0) continue; 
            // 最大的三个数都小于0了，所以跳过 */
            while (j < k) {
                int num = nums[i] + nums[j] + nums[k];
                if (num == 0) {
                    res.push_back({nums[i], nums[j], nums[k]});
                    j++, k--;
                    while (j < k && nums[j] == nums[j - 1]) j ++; //跳过相同的j
                    while (j < k && nums[k] == nums[k + 1]) k --; //跳过相同的k
                } else if (num < 0)	j++;
                else	k--; 
            }
        }
        return res;
    }
};
```



## 25. K 个一组翻转链表

**链表**

步骤：

1. 增加虚拟头结点 dummy，并且令 p 指针指向 dummy。
2. 循环记录 q 判断是否够 k 个节点，如果够此时 q 指向第 k 个节点
3. 令 `p1，p2` 分别指向翻转链表的第一和第二个
4. 翻转其中的链表，循环退出时 `p1` 为最后一个元素 `p2` 为下一组的第一个元素
5.  p->next 为第一个元素，翻转后为最后一个，即下一轮翻转的第 0 个，记录 p->next 为 c
6. 修改外部指向，p 指向 `p1`(最后翻转成第一)，c 指向 `p2` (第0指向第一)
7. 移动指针进行下一轮，p = c

```c++
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        auto dummy = new ListNode(-1);
        dummy->next = head;         // 虚拟头
        for (auto p = dummy;;) {    // p 第0个
            auto q = p;             // 找k个节点
            for (int i = 0; i < k && q; i++) q = q->next;
            if (!q) break;      // 如果够，q指向第k个
            auto p1 = p->next, p2 = p1->next;  // a指向第一个、b指向第二个
            while (p1 != q) {
                auto temp = p2->next;
                p2->next = p1;
                p1 = p2;
                p2 = temp;
            }

            // 退出循环时候 p1 为最后一个元素 p2为下一组的第一个元素 
            // 此时组内元素翻转，p1翻转后为第一个元素

            auto c = p->next;       // 第一个元素，翻转后为最后一个元素
            p->next = p1, c->next = p2; // p1为第一个元素，最后一个元素指向下一个第一个
            p = c;                  // 修改指针指向，循环下一步
        }
        return dummy->next;
    }
};
```

如果面试官要求，最后不足 k 个也要翻转，则需要额外判断

```c++
if (!q) {
  	reverse(p);	// p是虚拟头
  	break; 
}		

void reverse(ListNode* p) {
	ListNode* p1 = p->next;
    ListNode* p2 = p1->next;
    while (p2) {
        auto temp = p2->next;
        p2->next = p1;
        p1 = p2;
        p2 = temp;
    }
    auto c = p->next;
    p->next = p1, c->next = p2;
}
```



## 53. 最大子数组和

**动态规划**

$f[i]$ 所有以 $i$ 结尾的子数组的和的最大值

$f[i] = max(f[i-1]+nums[i], nums[i])$

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        vector<int> f(nums.size() + 3);
        int res = -1e9;
        for (int i = 1; i <= nums.size(); i++) {
            f[i] = max(f[i - 1] + nums[i - 1], nums[i - 1]);
            res = max(res, f[i]);
        }
        return res;
    }
};
```



## 21. 合并两个有序链表

**链表**

**迭代**

1. 新建头部的保护结点 `dummy`，设置 `cur` 指针指向 `dummy`
2. 若当前 $l1$ 指针指向的结点的值 `val` 比 $l2$ 指针指向的结点的值 `val` 小，则令 `cur` 的 `next` 指针指向 $l1$，且 $l1$ 后移；否则指向 $l2$，且 $l2$ 后移
3. 然后 `cur` 指针按照上一部设置好的位置后移
4. 循环以上步骤直到 $l1$ 或 $l2$ 为空
5. 将剩余的 $l1$ 或 $l2$ 接到 `cur` 指针后边。

**时间复杂度**
两个链表各遍历一次，所以时间复杂度为 $O(n)$
```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        auto dummy = new ListNode(-1);
        auto cur = hair;
        while (list1 && list2) {
            if (list1->val < list2->val) {
                cur->next = list1;
                list1 = list1->next;
            } else {
                cur->next = list2;
                list2 = list2->next;
            }
            cur = cur->next;
        }
        if (list1) cur->next = list1;
        if (list2) cur->next = list2;
        return dummy->next;
    }
};
```

**递归**
```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        if (list1 == nullptr) return list2;
        if (list2 == nullptr) return list1;

        if (list1->val < list2->val) {
            list1->next = mergeTwoLists(list1->next, list2);
            return list1;
        } else {
            list2->next = mergeTwoLists(list1, list2->next);
            return list2;
        }
    }
};
```



## 1. 两数之和

**哈希表**

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> hash;
        for (int i = 0; i < nums.size(); i++) {
            if (hash.count(target - nums[i])) return {hash[target - nums[i]], i};
            hash[nums[i]] = i;
        }
        return {};
    }
};
```



## 102. 二叉树的层序遍历

**队列**

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        queue<TreeNode*> q;
        if (root) q.push(root);
        while (q.size()) { 
            int n = q.size();
            vector<int> temp;
            for (int i = 0; i < n; i++) {
                auto t = q.front();
                q.pop();
                temp.push_back(t->val);
                if (t->left) q.push(t->left);
                if (t->right) q.push(t->right);
            }
            res.push_back(temp);
        }
        return res;
    }
};
```

**递归**

```c++
class Solution {
public:
    void order(TreeNode* cur, vector<vector<int>>& res, int depth) {
        if (cur == NULL) return;
        if (res.size() == depth) res.push_back(vector<int>());
        res[depth].push_back(cur->val);
        order(cur->left, res, depth + 1);
        order(cur->right, res, depth + 1); 
    }
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        int depth = 0;
        order(root, res, depth);
        return res;
    }
};
```





## 20. 有效的括号

**栈 + 哈希表**

```c++
class Solution {
public:
    bool isValid(string s) {
        int n = s.size();
        if (n & 1) return false;
        unordered_map<char, char> hash;
        hash['('] = ')', hash['['] = ']', hash['{'] = '}';
        stack<char> stk;
        for (auto c: s) {
            if (c == '(' || c == '[' || c == '{') stk.push(c);
            else {
                if (stk.size() && hash[stk.top()] == c) stk.pop();
                else return false;
            }
        }
        return stk.empty();
    }
};
```





## 33. 搜索旋转排序数组

**二分**

题目中有两段升序序列，其中第二段的最大值比第一段的最小值小

使用两次二分，第一次找出两段序列的起始，然后根据数值范围，选择区间第二次二分找值

第一次二分：左区间全部大于等于第一个数，右区间全部小于第一个数

第二次二分：右区间的值全部大于等于目标值，左区间的值全部小于目标值

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        while (l < r) {					// 找到最后一个比nums[0]大的数
            int mid = l + r + 1 >> 1;	 // 左区间满足所有数都>=nums[0]
            if (nums[mid] >= nums[0]) l = mid;
            else r = mid - 1;
        }
        // 循环推出 l = r 为最后一个大于nums[0]的下标
        if (target >= nums[0]) l = 0;			// 如果目标数比第nums[0]大，则第一段区间找
        else l = r + 1, r = nums.size() - 1;	// 否则在第二段升序区间找
        while (l < r) {
            int mid = l + r >> 1;
            if (nums[mid] >= target) r = mid;	// 右区间满足所有数都>=target
            else l = mid + 1;
        }
        // 这里不能 返回l, 因为假如数组只有一段升序 l=r+1, nums[l]可能溢出
        if (nums[r] == target) return r;	
        
        return -1;
    }
};
```



## 5. 最长回文子串

**双指针(中心扩散)**

1. 枚举数组当中的每个位置`i`，从当前位置向两边扩散，分奇数和和偶数两个情况
2. 奇数时，从 `l = i`, `r = i`往两边扩散 `(s[l]==s[j]),i++,j++)`
3. 偶数时，从 `l = i`, `r = i + 1`往两边扩散 `(s[l]==s[j]),i++,j++)`
4. 返回当前以 `i`为中心的回文子串长度(r - l + 1),存储最大值

循环退出时`s[l]!=s[r]`,故长度`r - l - 1`

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        string res;
        int n = s.size();
        for (int i = 0; i < s.size(); i++) {
            int l = i, r = i;
            while (l >= 0 && r < n && s[l] == s[r]) l--, r++;
            if (res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
            l = i, r = i + 1;
            while (l >= 0 && r < n && s[l] == s[r]) l--, r++;
            if (res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
        }
        return res;
    }
};
```



## 121. 买卖股票的最佳时机

**贪心**

记录最小值，如果当前值比最小值大，统计获利
```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0;
        int n = prices.size();
        int num = prices[0];
        for (int i = 1; i < n; i++) {
            if (prices[i] > num)
                res = max(res, prices[i] - num);
            num = min(num, prices[i]);
        }
        return res;
    }
};
```



## 200. 岛屿数量

DFS

```c++
class Solution {
public:
    int n = 0, m = 0;
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
    void dfs(vector<vector<char>>& g, int x, int y) {
        g[x][y] = '0';
        for (int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if (a >= 0 && a < n && b >= 0 && b < m && g[a][b] == '1')
                dfs(g, a, b);
        }
    }

    int numIslands(vector<vector<char>>& grid) {
        int res = 0;
        n = grid.size(), m = grid[0].size();
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++) 
                if (grid[i][j] == '1') {
                    dfs(grid, i, j);
                    res ++;
                }        
        return res;
    }
};
```

BFS

```c++
class Solution {
public:
    int n = 0, m = 0;
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
    void bfs(vector<vector<char>>& grid, int x, int y) {
        grid[x][y] = '0';
        queue<pair<int, int>> q;
        q.push({x, y});
        while (q.size()) {
            auto t = q.front();
            q.pop();
            for (int i = 0; i < 4; i++) {
                int a = dx[i] + t.first, b = dy[i] + t.second;
                if (a < 0 || a >= n || b < 0 || b >= m) continue;
                if (grid[a][b] == '0') continue;
                q.push({a, b});
                grid[a][b] = '0';
            }
        } 
    }
    int numIslands(vector<vector<char>>& grid) {
        int res = 0;
        n = grid.size(), m = grid[0].size();
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == '1') {
                    bfs(grid, i, j);
                    res ++;
                }
            }
        return res;
    }
};
```



## 141. 环形链表

**链表**

两个指针，第一个每次走一步，第二个每次走两步

如果快指针和其指向不为空，一直遍历，每次判断快慢指针是否指向相同

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *p1 = head;
        ListNode *p2 = head;
        while (p2 && p2->next) {
            p1 = p1->next;
            p2 = p2->next->next;
            if (p1 == p2) return true;
        }
        return false;
    }
};
```

找到环的入口：

![环形链表2](https://www.acwing.com/media/article/image/2018/05/31/1_2a61ed7c64-QQ%E5%9B%BE%E7%89%8720180531162503.png)

第一次相遇时 `fast` 所走的距离是 $x+(y+z)∗n+y$, $n$ 表示圈数，同时 `fast` 走过的距离是 `slow` 的两倍，也就是 $2(x+y)$，所以我们有 $x+(y+z)∗n+y=2(x+y)$，所以 $x=(n−1)×(y+z)+z$。那么我们让 `slow` 从 c 点开始走，走 $x$ 步，会恰好走到 $b$ 点；让 `fast` 从 $a$ 点开始走，走 $x$ 步，也会走到 $b$ 点。
`fast` 走过的长度： $x+(y+z)∗n+y+x = x+y+n(y+z)+(n−1)(y+z)+z = x+y+z+(2n-1)(y+z)$
`slow` 走过的长度： $x + y + z$

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *p1 = head;
        ListNode *p2 = head;
        while (p2 && p2->next) {
            p1 = p1->next;
            p2 = p2->next->next;
            if (p1 == p2) {				
                p1 = head;
                while (p1 != p2) {
                    p1 = p1->next;
                    p2 = p2->next;
                }
                return p1;
            }
        }
        return nullptr;
    }
};
```



## 103. 二叉树的锯齿形层序遍历

**二叉树**

层序遍历，额外记录层数，偶数则翻转数组

```c++
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> res;
        queue<TreeNode*> q;
        if (root) q.push(root);
        int level = 0;

        while (q.size()) {
            int n = q.size();
            level++;
            vector<int> temp;
            for (int i = 0; i < n; i++) {
                auto t = q.front();
                q.pop();
                temp.push_back(t->val);
                if (t->left) q.push(t->left);
                if (t->right) q.push(t->right);
            }
            if (level % 2 == 0) reverse(temp.begin(), temp.end());
            res.push_back(temp);
        }
        return res;
    }
};
```



## 88. 合并两个有序数组

**双指针**

题目所给连个数组是升序的，而且数组一预留了两个数组的空间

所以从数组的结尾开始，每一步添加两个数组中的最大的数，移动下标

最后扫尾操作，把数组二的剩余元素是否全部移动到数组一中

```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int k = n + m - 1;			// 数组一的末尾下标
        int i = m - 1, j = n - 1;	// 两数组实际元素的下标
        while (i >= 0 && j >= 0)
            if (nums1[i] >= nums2[j]) nums1[k--] = nums1[i--];
            else nums1[k--] = nums2[j--];			
        // 退出循环时候，可能数组二仍有元素比数组一的小，如果数组一的小则保留在原数组
        while (j >= 0) nums1[k--] = nums2[j--];		// 扫尾操作
    }
};
```



## 236. 二叉树的最近公共祖先

直接拿本身这个函数进行递归，本身这个函数的含义是在 root 这棵树找到 p 和 q 的最近公共祖先

- 若当前节点 root == p，则表示 q 点一定在 root 的左右子树其中一处，则最近的公共结点肯定是 root
- 若当前节点 root == q，则表示 p 点一定在 root 的左右子树其中一处，则最近的公共结点肯定是 root
- 若 1 和 2 情况都不是，则 p 和 q 的最近公共祖先要么在 root 的左子树，要么在 root 的右子树，则直接递归到root->left 和 root->right 进行搜索，若递归完后，左子树返回 null 表示没找到，那答案肯定是在右子树，同理，右子树返回 null 表示没找到，那答案肯定是在左子树
- 若3情况中左右子树都找不到 p 和 q 的最近公共祖先，则表示 p 点和 q 点分别在不同的左右子树，则 root 就是他们的最近公共祖先

```c++
class Solution {
public:
    TreeNode* res = NULL;
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        dfs(root, p, q);
        return res;
    }
    int dfs(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root) return 0;
        int state = dfs(root->left, p, q);
        if (root == p) state |= 1;
        else if (root == q) state |= 2;
        state |= dfs(root->right, p, q);
        if (state == 3 && !ans) ans = root;
        return state;
    }
};
```



## 46. 全排列

**DFS**

```c++
class Solution {
public:
    vector<vector<int>> res;
    vector<bool> st;
    vector<int> temp;
    void dfs(vector<int>& nums, int u) {
        if (u == nums.size()) {
            res.push_back(temp);
            return;
        }

        for (int i = 0; i < nums.size(); i++) {
            if (!st[i]) {
                temp[u] = nums[i];
                st[i] = true;
                dfs(nums, u + 1);
                st[i] = false;
            }
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        st.resize(nums.size());
        temp.resize(nums.size());
        dfs(nums, 0);
        return res;
    }
};
```

**重复元素**

如果有重复元素的存在，首先需要排序，另外需要额外判断信息：

对于相同数，我们人为定序，就可以避免重复计算：我们在dfs时记录一个额外的状态，记录上一个相同数存放的位置 start，我们在枚举当前数时，只枚举 `start+1,start+2,…,start+1,start+2,…,n` 这些位置。

```c++
// 两种写法
// 第一种记录额外状态
void dfs(vector<int>& nums, int u, int start) ...
for (int i = start; i < nums.size(); i ++ ) {
    if (!st[i]) {
        st[i] = true;
    	path[i] = nums[u];
        	// 下一个数和当前摆放的数不同，可以选择任意位置；如果相同，必须摆放在该数后面
    	if (u + 1 < nums.size() && nums[u + 1] != nums[u]) 
			dfs(nums, u + 1, 0);
  		else 
			dfs(nums, u + 1, i + 1);    
        	// 对于相同的数，只用第一个没有用过的！！！
        	// 所以下一次遍历从 i+1 开始选 这样就能按顺序用
        st[i] = false;
    }
}

// 第二种根据排序后的性质
for (int i = 0; i < nums.size(); i ++ ) {
    if (!st[i]) {
    	if (i && nums[i - 1] == nums[i] && !st[i - 1]) continue;
    	// 前一个数也是皇子,即当前不是第一个没有被用过的数,相同的前几个数没有被用过则跳过
    	st[i] = true;
    	path[u] = nums[i];
    	dfs(nums, u + 1);
    	st[i] = false;
	}
}
```



## 160. 相交链表

**链表**

1. 用两个指针分别从两个链表头部开始扫描，每次分别走一步；
2. 如果指针走到null，则从另一个链表头部开始走；
3. 当两个指针相同时，
    - 如果指针不是null，则指针位置就是相遇点；
    - 如果指针是 null，则两个链表不相交；

```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode *p1 = headA;
        ListNode *p2 = headB;
        while (p1 != p2) {
            if (p1) p1 = p1->next;
            else p1 = headB;
            if (p2) p2 = p2->next;
            else p2 = headA;
        }
        return p1;
    }
};
```



## 54. 螺旋矩阵

**模拟**

熟悉矩阵的四个方向

```c++
int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1}; 	// 上右下左(默认)
int dx[4] = {0, 1, 0, -1}, dy[4] = {1, 0, -1, 0};   // 右下左上
```

螺旋矩阵先向游走，然后逆时针转动

每次转动是在越出边界或者是遇到已经遍历的元素（状态数组）

每次到了新元素判断是否越出边界，是否重复元素，判断是否需要改向

每次改向则是方向数组的下一个，可以通过 $+1\%4$ 来做方向的轮转

```c++
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> res;
        int n = matrix.size();
        if (n == 0) return res;
        int m = matrix[0].size();
        //int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1}; // 上右下左
        int dx[4] = {0, 1, 0, -1}, dy[4] = {1, 0, -1, 0};   // 右下左上
        vector<vector<bool>> st(n, vector<bool> (m));
        for (int i = 0, x = 0, y = 0, d = 0; i < n * m; i++) {
            res.push_back(matrix[x][y]);
            st[x][y] = true;
            int a = x + dx[d], b = y + dy[d];
            if (a < 0 || a >= n || b < 0 || b >= m || st[a][b]) {
                d = (d + 1) % 4;
                a = x + dx[d], b = y + dy[d];
            }
            x = a, y = b;
        }
        return res;
    }
};
```



## 23. 合并K个升序链表

前置题，合并两个有序链表

**优先队列**

使用优先队列把每个链的头节点排序，定义虚拟头节点 `dummy`，和移动指针 `tail`

每次更新取最短的，修改指向 `tail->next = t`，往后移动指针 `tail = tail->next`

并把该点的下一个节点插入堆中 `if (t->next) heap.push(t->next);`，重复过程

```c++
class Solution {
public:
    struct Cmp {
        bool operator() (ListNode* a, ListNode* b) {
            return a->val > b->val;
        }
    };
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        priority_queue<ListNode*, vector<ListNode*>, Cmp> heap; // 节点的值排序
        auto dummy = new ListNode(-1), tail = dummy;   // dummy虚拟头节点，tail移动的节点
        for (auto l: lists) if (l) heap.push(l);    // 把每个链表的头节点插入

        while (heap.size()) {
            auto t = heap.top();
            heap.pop();
            tail = tail->next = t; // 两步，第一步修改节点指向，第二步移动节点
            if (t->next) heap.push(t->next);
        }
        return dummy->next;
    }
```



**分治合并**

- 将 $k$ 个链表配对并将同一对中的链表合并
- 第一轮合并后，$k$ 个链表被合并成 $\frac{k}{2}$，平均长度为 $\frac{2n}{k}$，然后是 $\frac{k}{4}$...
- 重复这一个过程

```c++
class Solution {
public:
    ListNode* mergetwo(ListNode* l1, ListNode* l2) {
        ListNode *dummy = new ListNode(0);
        ListNode *cur = dummy;
        while (l1 && l2) {
            if (l1->val < l2->val) {
                cur->next = l1;
                l1 = l1->next;
            } else {
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        }
        cur->next = (l1 != NULL ? l1 : l2);
        return dummy->next;       
    }
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        int n = lists.size();
        if (n == 0) return NULL;
        if (n == 1) return lists[0];

        int mid = n / 2;
        vector<ListNode*> left = vector<ListNode*>(lists.begin(), lists.begin() + mid);
        vector<ListNode*> right = vector<ListNode*>(lists.begin() + mid, lists.end());
        ListNode* l1 = mergeKLists(left);
        ListNode* l2 = mergeKLists(right);
        return mergetwo(l1, l2);
    }
};
```



## 92. 反转链表 II

**链表**

与k个一组翻转链表相似

先找到翻转区间的前一个节点和最后一个节点，区间内部翻转，区间外部修改指向

```c++
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if (left == right) return head;
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        auto p = dummy, q = dummy;
        for (int i = 0; i < left - 1; i++) p = p->next;    // 找到需要反转的前一个
        for (int i = 0; i < right; i++) q = q->next;       // 找到翻转区间最后一个
        auto p1 = p->next, p2 = p1->next;
        while (p1 != q) {
            auto temp = p2->next;
            p2->next = p1;
            p1 = p2;
            p2 = temp;
        }
        auto c = p->next;								// 修改区间外部指向
        p->next = p1;
        c->next = p2;
        return dummy->next;
    }
};
```

```c++
	void reverse(ListNode* p, ListNode* end) {      // 翻转链表模板 p为需要翻转的前一个节点
        ListNode* p1 = p->next;                     // end为需要翻转的最后一个节点
        ListNode* p2 = p1->next;
        while (p1 != end) {
            auto temp = p2->next;
            p2->next = p1;
            p1 = p2;
            p2 = temp;
        }
        auto c = p->next;                           
        p->next = p1, c->next = p2;
    }

```





## 415. 字符串相加

**高精度**

```c++
class Solution {
public:
    vector<int> add(vector<int> &A, vector<int> &B) {
        if (A.size() < B.size()) return add(B, A);
        vector<int> C;
        int t = 0;
        for (int i = 0; i < A.size(); i++) {
            t += A[i];
            if (i < B.size()) t += B[i];
            C.push_back(t % 10);
            t /= 10;
        }
        if (t) C.push_back(t);
        return C;
    }
    string addStrings(string num1, string num2) {
        vector<int> A, B;
        for (int i = num1.size() - 1; i >= 0; i--) A.push_back(num1[i] - '0');
        for (int i = num2.size() - 1; i >= 0; i--) B.push_back(num2[i] - '0');
        auto C = add(A, B);
        string res;
        for (int i = C.size() - 1; i >= 0; i--) {
            res += (C[i] + '0'); 
        }
        return res;
    }
};
```



## 142. 环形链表 II

找到环的入口：

![环形链表2](https://www.acwing.com/media/article/image/2018/05/31/1_2a61ed7c64-QQ%E5%9B%BE%E7%89%8720180531162503.png)

第一次相遇时 `fast` 所走的距离是 $x+(y+z)∗n+y$, $n$ 表示圈数，同时 `fast` 走过的距离是 `slow` 的两倍，也就是 $2(x+y)$，所以我们有 $x+(y+z)∗n+y=2(x+y)$，所以 $x=(n−1)×(y+z)+z$。那么我们让 `slow` 从 c 点开始走，走 $x$ 步，会恰好走到 $b$ 点；让 `fast` 从 $a$ 点开始走，走 $x$ 步，也会走到 $b$ 点。
`fast` 走过的长度： $x+(y+z)∗n+y+x = x+y+n(y+z)+(n−1)(y+z)+z = x+y+z+(2n-1)(y+z)$
`slow` 走过的长度： $x + y + z$

```
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if (!head) return head;
        ListNode* p1 = head;
        ListNode* p2 = head;
        while (p2 && p2->next) {
            p1 = p1->next;
            p2 = p2->next->next;
            if (p1 == p2) {
                p1 = head;
                while (p1 != p2) {
                    p1 = p1->next;
                    p2 = p2->next;
                }
                return p1;
            }
        }
        return nullptr;
    }
};
```



## 300. 最长递增子序列

一、状态表示：`f[i]`

1. 集合：所有以第 i 个数结尾的上升子序列

2. 属性：最大值

二、状态计算：

- **集合划分依据：**当前数 j 是否小于上一个数 i，是则构成上升子序列

​		答案可以更新成 本身 和 与 j 构成的的子序列 中较大的那一个
​		`f[i] = max(f[i], f[j] + 1),j = 0,1,2,...,i - 1 `

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> f(n + 1, 1);

        int res = 0;
        for (int i = 1; i <= nums.size(); i++) {
            f[i] = 1;
            for (int j = 1; j < i; j++) {
                if (nums[j - 1] < nums[i - 1]) f[i] = max(f[i], f[j] + 1);
            }
            res = max(res, f[i]);
        }
        return res;
    }
};
```

**贪心+二分**

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> q(n + 1);
        q[0] = -2e9;
        int len = 0;
        for (int i = 0; i < n; i++) {
            int l = 0, r = len;
            while (l < r) {
                int mid = l + r + 1 >> 1;
                if (q[mid] < nums[i]) l = mid;
                else r = mid - 1;
            }
            len = max(len, r + 1);
            q[r + 1] = nums[i];
        }
        return len;
    }
};
```



## 42. 接雨水

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)

**线性扫描**

1. 观察整个图形，考虑对水的面积按 列 进行拆解
2. 注意到，每个矩形条上方所能接受的水的高度，是由它左边 最高的 矩形，和右边 最高的 矩形决定的。具体地，假设第 i 个矩形条的高度为 height[i]，且矩形条左边 最高的 矩形条的高度为 left_max[i]，右边 最高的 矩形条高度为 right_max[i]，则该矩形条上方能接受水的高度为 min(left_max[i], right_max[i]) - height[i]。
3. 需要分别从左向右扫描求 left_max，从右向左求 right_max，最后统计答案即可。
4. 注意特判 n 为 0。

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        int res = 0;
        vector<int> left(n), right(n);
        left[0] = height[0];
        right[n - 1] = height[n - 1];
        for (int i = 1; i < n; i++) {
            left[i] = max(left[i - 1], height[i]);
        }
        for (int i = n - 2; i >= 0; i--) {
            right[i] = max(right[i + 1], height[i]);
        }
        for (int i = 0; i < n; i++) {
            res += min(left[i], right[i]) -  height[i];
        }
        return res;
    }
};
```

**单调栈**

1. 考虑每个位置左边和右边 第一个 比自身不低的矩形条，以及三个矩形条构成的 U 型，相当于对水的面积按 行 进行拆解。
2. 维护严格单调递减的单调栈。在每次检查栈顶要出栈时，i 为右边第一个比 st.top() 不低的矩形，st.top() 弹出栈顶，并将其记为 top。
3. 假设此时栈中仍然存在矩形，现在 st.top()（弹栈后的栈顶）、top 与 i 三个位置构成一个 U 型，其中 top 位置代表 U 型的底部，此时可以计算出该 U 型所能接受的水的面积为 (i - st.top() - 1) * (min(height[st.top()], height[i]) - height[top])。
4. 最后当前矩形进栈。

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size(), res = 0;
        stack<int> stk;
        for (int i = 0; i < n; i++) {
            while (!stk.empty() && height[stk.top()] <= height[i]) {
                int top = stk.top();
                stk.pop();				// 此时左边是top元素上一个大的数
                if (stk.empty()) break;	 // 右边是top元素下一个大的数
                res += (i - stk.top() - 1) *(min(height[stk.top()], height[i]) - height[top]);
                // i - stk.top() - 1 长度 * 高度 （左右两边的最小值 - 当前高度）
            }
            stk.push(i);
        }
        return res;
    }
};
```



## 143. 重排链表

**链表**

我们可以先把链表的后半部分反转过来，然后就可以从两端开始遍历，将末尾节点插入第 k 个节点后面。

时间复杂度：查找中间节点 $O(n)$，反转后半部分链表 $O(n)$，依次插入后半部分的节点 $O(n)$，总的时间复杂度为 $O(n)$


```c++
class Solution {
public:
    void reorderList(ListNode* head) {
        if(!head || !head->next || !head->next->next) return;
        ListNode* p1 = head;
        ListNode* p2 = head;
        // 找到中间节点（如果有两个中间节点,p1是靠后的那个）
        while (p2 && p2->next) {  
            p1 = p1->next;
            p2 = p2->next->next;
        }
        // 翻转p1及后面所有节点     反转链表模板
        ListNode *pre = nullptr;
        ListNode *cur = p1;
        while (cur) {
            ListNode* temp = cur->next;
            cur->next = pre;
            pre = cur;
            cur = temp;
        }
        // 此时pre是最后一个节点。跳出循环有两种情况：
        // 总共有偶数个节点，最后两个节点就不用反转了；head->next != pre
        // 总共有奇数个节点，最后一个节点不用反转。head!= pre
        while(head->next != pre && head!= pre) {
            ListNode* temp = pre->next;         // 尾节点的下一个
            ListNode* body = head->next;	   // 头结点的下一个
            pre->next = head->next;
            head->next = pre;
            pre = temp;                 // 尾节点移动
            head = body;                // 头节点移动    或者 head = head->next->next;
        }

    }
};
```





## 124. 二叉树中的最大路径和

**二叉树**

递归+数的遍历

树中每条路径，都存在一个离根节点最近的点，我们把它记为割点，用割点可以将整条路径分为两部分：从该节点向左子树延伸的路径，和从该节点向右子树延伸的部分，而且两部分都是自上而下延伸的。

递归遍历整棵树，递归时维护从每个节点开始往下延伸的最大路径和。
对于每个点，递归计算完左右子树后，我们将左右子树维护的两条最大路径，和该点拼接起来，就可以得到以这个点为割点的最大路径。
然后维护从这个点往下延伸的最大路径：从左右子树的路径中选择权值大的一条延伸即可。

```c++
class Solution {
public:
    int res;
    int maxPathSum(TreeNode* root) {
        res = INT_MIN;
        dfs(root);
        return res;
    }
    int dfs(TreeNode* root) {
        if (!root) return 0;						// 如果为空返回0
        int left = max(0, dfs(root->left));		 	// 递归计算左边子树的最长路径
        int right = max(0, dfs(root->right));		// 如果小于0，则不选，长度为0
        res = max(res, left + right + root->val);	// 答案等于左右子树长度+自身
        return root->val + max(left, right);		// 从该点的最长距离为该点+左右子树的最大值
    }
};
```



## 94. 二叉树的中序遍历

**递归**

中序遍历：左中右

```c++
class Solution {
public:
    vector<int> res;
    vector<int> inorderTraversal(TreeNode* root) {
        dfs(root);
        return res;
    }
    void dfs(TreeNode* root) {
        if (!root) return;
        dfs(root->left);
        res.push_back(root->val);
        dfs(root->right);
    }
};
```



## 232. 用栈实现队列

**模拟**

两个栈实现，一个栈存数据，一个做缓存

需要 弹出/返回 第一个元素，先把元素全移动到缓存栈内，弹出/返回 后，在全部移动到数据栈

```c++
class MyQueue {
public:
    stack<int> stk, tmp;
    MyQueue() {

    }
    
    void push(int x) {
        stk.push(x);
    }
    
    int pop() {
        while (!stk.empty()) {
            int x = stk.top();
            stk.pop();
            tmp.push(x);
        }
        int x = tmp.top();
        tmp.pop();
        while (!tmp.empty()) {
            int x = tmp.top();
            tmp.pop();
            stk.push(x);
        }
        return x;
    }
    
    int peek() {
        while (!stk.empty()) {
            int x = stk.top();
            stk.pop();
            tmp.push(x);
        }
        int x = tmp.top();
        while (!tmp.empty()) {
            int x = tmp.top();
            tmp.pop();
            stk.push(x);
        }
        return x;
    }
    
    bool empty() {
        return stk.empty();
    }
};
```



## 19. 删除链表的倒数第 N 个结点

**快慢指针**
快指针先走 $k$，然后两个指针同步走，当快指针走到头(最后一个值)时，慢指针就是链表倒数第 $k + 1$ 个节点

将倒数第 $k - 1$ 个节点指向倒数第 $k + 1$ 个节点，实现删除倒数第 $k$ 个节点

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        auto fast = dummy;
        auto slow = dummy;
        while (n--) fast = fast->next;
        while (fast->next) {
            fast = fast->next;
            slow = slow->next;
        }
        slow->next = slow->next->next;
        return dummy->next;
    }
};
```





## 72. 编辑距离

**动态规划**

状态表示：`f[i][j]`

- 集合：所有将 `a[i][j]` 变成 `b[i][j]` 的操作方式
- 属性：最小值

状态计算：

- 集合划分的依据：a[i], b[j] 是否相同，划分成三类 00 01 10 11`

    `a[i]` 比 `b[i]` 多 → 删`a[i]`、`a[i]` 比 `b[i]` 少 → 增 `a[i]` 、`a[i]` 与 `b[i]` 不同 → 改 `a[i]`

    `f[i - 1][j] + 1`、`f[i][j - 1] + 1`、`f[i - 1][j - 1]`

    `f[i][j] = min(f[i - 1][j] + 1, f[i][j - 1] + 1, f[i - 1][j - 1] + 1)`

1. 删除操作：把`a[i]`删掉之后 `a[1~i]` 和 `b[1~j]` 匹配

    所以之前要先做到 `a[1~(i-1)]` 和 `b[1~j]` 匹配     			    	`f[i-1][j] + 1`

2. 插入操作：插入之后 `a[i]` 与 `b[j]` 完全匹配，所以插入的就是 `b[j]`

    所以之前 `a[1~i]` 和 `b[1~(j-1)]` 匹配								 	   `f[i][j-1] + 1` 

3. 替换操作：把 `a[i]` 改成 `b[j]` 之后想要 `a[1~i]` 与 `b[1~j]` 匹配 
    所以修改这一位之前，`a[1~(i-1)]` 应该与 `b[1~(j-1)]` 匹配    `f[i-1][j-1] + 1`
    但是如果本来 `a[i]` 与 `b[j]` 这一位相等，就不用改                `f[i-1][j-1] + 0`

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int n = word1.size(), m = word2.size();
        word1 = ' ' + word1;
        word2 = ' ' + word2;
        vector<vector<int>> f (n + 1, vector<int> (m + 1));

        for (int i = 0; i <= m; i++) f[0][i] = i;
        for (int i = 0; i <= n; i++) f[i][0] = i;

        for (int i = 1; i <= n; i++) 
            for (int j = 1; j <= m; j++) {
                f[i][j] = min(f[i - 1][j] + 1, f[i][j - 1] + 1);
                if (word1[i] == word2[j]) f[i][j] = min(f[i][j], f[i - 1][j - 1]);
                else f[i][j] = min(f[i][j], f[i - 1][j - 1] + 1);
            }
        return f[n][m];
    }
};
```





## 704. 二分查找

二分模板

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        while (l < r) {
            int mid = l + r >> 1;
            if (nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        if (nums[l] == target) return l;
        else return -1;
    }
};
```



## 4. 寻找两个正序数组的中位数

**递归**

> 将问题直接转化成求两个数组中第k小的元素

如果该问题可以解决，那么第 `(n+m)/2` 小数就是我们要求的中位数.
先从简单情况入手，假设 $m,n≥k/2$，我们先从 `nums1` 和 `nums2` 中各取前 $k/2$ 个元素：

*  如果 $nums1[k/2−1]>nums2[k/2−1]$，则说明 `nums1` 中取的元素过多，`nums2` 中取的元素过少；因此 `nums2` 中的前 $k/2$ 个元素一定都小于等于第 $k$ 小数，所以我们可以先取出这些数，将问题归约成在剩下的数中找第 $k−⌊k/2⌋$ 小数。
*  如果 $nums1[k/2−1]≤nums2[k/2−1]$，同理可说明 `nums1` 中的前 $k/2$ 个元素一定都小于等于第 $k$ 小数，类似可将问题的规模减少一半.


现在考虑边界情况，如果 $m<k/2$，则我们从 `nums1` 中取$m$个元素，从 `nums2` 中取 $k/2$ 个元素（由于 $k=(n+m)/2$ ，因此 $m,n$ 不可能同时小于 $k/2$）：


* 如果 $nums1[m−1]>nums2[k/2−1]$，则 `nums2` 中的前 $k/2$ 个元素一定都小于等于第 $k$ 小数，我们可以将问题归约成在剩下的数中找第 $k−⌊k/2⌋$ 小
* 如果 $nums1[m−1]≤nums2[k/2−1]$，则 `nums1` 中的所有元素一定都小于等于第 $k$ 小数，因此第k小数是 $nums2[k−m−1]$.

每步递归先判断`nums1`和`nums2`剩余的的长度，如果`nums1`长则互换
边界的判断利用min函数` min(i + k / 2, int(nums1.size()))` 来判断属于哪种情况

1. 如果`nums1`所有元素都取出来了，即 `nums1.size() == i`,那么要找的元素即 `nums2[j + k - 1]`,因为 $size1 + size2 >= k$，若 `size1<k/2`，则必有`size2>k/2`
2. 如果 $k$ 这时候为 $1$，说明当前 i,j 中的最小值即为所要找的元素

```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {

        int total = nums1.size() + nums2.size();
        if (total % 2 == 0) {
            int left = findnum(nums1, 0, nums2, 0, total / 2);
            int right = findnum(nums1, 0, nums2, 0, total / 2 + 1);
            return (left + right) / 2.0;
        } else return findnum(nums1, 0, nums2, 0, total / 2 + 1);

    }
    int findnum(vector<int>& nums1, int i, vector<int>& nums2, int j, int k) {
        if (nums1.size() - i > nums2.size() - j) return findnum(nums2, j, nums1, i, k);
        if (nums1.size() == i) return nums2[j + k - 1];        
        if (k == 1) return min(nums1[i], nums2[j]);             
        int si = min(i + k / 2, int(nums1.size())), sj = j + k / 2;
        if (nums1[si - 1] > nums2[sj - 1])
            return findnum(nums1, i, nums2, sj, k - k / 2);
        else
            return findnum(nums1, si, nums2, j, k - (si - i)); 
    }
};
```



## 199. 二叉树的右视图

**层序遍历**

把每层的最后一个节点插入数组

```c++
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> res;
        if (!root) return res;
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            int n = q.size();
            for (int i = 0; i < n; i++) {
                auto t = q.front();
                q.pop();
                if (t->left) q.push(t->left);
                if (t->right) q.push(t->right);
                if (i == n - 1) res.push_back(t->val);
            }
        }
        return res;
    }
};
```



## 56. 合并区间

**贪心**

左端点排序，记录区间最大值，判断左端点是否在区间内

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end(), [&](vector<int> a, vector<int> b) {
            return a[0] < b[0];
        });
        vector<vector<int>> res;
        res.push_back(intervals[0]);
        int ed = intervals[0][1];
        for (int i = 1; i < intervals.size(); i++) {
            if (intervals[i][0] <= ed) {
                ed = max(intervals[i][1], ed);
                res[res.size() - 1][1] = ed;
            } else {
                res.push_back(intervals[i]);
                ed = intervals[i][1];
            }
        }
        return res;
    }
};
```



## 1143. 最长公共子序列

线性DP

```c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int n = text1.size(), m = text2.size();
        vector<vector<int>> f(n + 1, vector<int> (m + 1, 0));
        text1 = " " + text1, text2 = " " + text2;
        for (int i = 1; i <= n; i++) 
            for (int j = 1; j <= m; j++) {
                f[i][j] = max(f[i - 1][j], f[i][j - 1]);
                if (text1[i] == text2[j]) f[i][j] = max(f[i][j], f[i - 1][j - 1] + 1);
            }
        return f[n][m];
    }
};
```



## 82. 删除排序链表中的重复元素 II

前置题，删除链表中的重复元素，需要保留一个

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) return head;
        auto p = head;
        while (p) {
            auto q = p;
            while (q && q->val == p->val) q = q->next;	// 找到第一个不相等的
            p->next = q;	// 修改指向
            p = q;		    // 移动指针
        }
        return head;
    }
};
```

因为要把重复的全部删除，头节点也可能被删除，所以需要虚拟头节点

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        auto p = dummy;
        while (p->next) {
            auto q = p->next->next;
            while (q && q->val == p->next->val) q = q->next;  // q移动到下两个元素
            if (p->next->next == q) p = p->next; // 如果q正好是下两个元素，没有重复
            else p->next = q;                    // 否则有重复
        }
        return dummy->next;
    }
};
```



## 70. 爬楼梯

**递推**

```c++
class Solution {
public:
    int climbStairs(int n) {
        if (n < 2) return 1;
        int pre1 = 1, pre2 = 1;
        int res = 0;
        for (int i = 2; i <= n; i++) {
            res = pre1 + pre2;
            pre2 = pre1;
            pre1 = res;
        }
        return res;
    }
};
```





## 148. 排序链表

给一个单链表排序，要求时间 $O(nlogn)$，空间 $O(1)$

**归并排序：**

自顶向下递归形式的归并排序，由于递归需要使用系统栈，递归的最大深度是 $logn$，所以需要额外 $O(logn)$ 的空间，需要使用自底向上非递归形式的归并排序算法。
基本思路是这样的，总共迭代 $logn$ 次：

1. 第一次，将整个区间分成连续的若干段，每段长度是2：$[a_0,a_1],[a_2,a_3],…[a_{n−2},a_{n−1}]$， 然后将每一段内排好序，小数在前，大数在后；
2. 第二次，将整个区间分成连续的若干段，每段长度是4：$[a0,…,a3],[a4,…,a7],…[an−4,…,an−1]$，然后将每一段内排好序，这次排序可以利用之前的结果，相当于将左右两个有序的半区间合并，可以通过一次线性扫描来完成；
3. 依此类推，直到每段小区间的长度大于等于 $n$ 为止；

另外，当 $n$ 不是2的整次幂时，每次迭代只有最后一个区间会比较特殊，长度会小一些，遍历到指针为空时需要提前结束。

```c++
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        int n = 0;
        for (auto p = head; p; p = p->next) n++;
        ListNode* dummy = new ListNode(-1);
        dummy->next = head;		// 每次归并段的长度，每次长度依次为1,2,4,8...n/2
        for (int i = 1; i < n; i *= 2) {            // 自下向上 >=n时候已经排完
            auto cur = dummy;                       // 头节点可能会变
            for (int j = 1; j + i <= n; j += i * 2) {   // 分段 j代表每一段的开始
                auto p = cur->next, q = p;          // p和q为两段区间的头节点
                for (int k = 0; k < i; k++) q = q->next;
                int l = 0, r = 0;
                while (l < i && r < i && p && q) {
                    if (p->val <= q->val) cur = cur->next = p, p = p->next, l++;
                    else cur = cur->next = q, q = q->next, r++;
                }
                while (l < i && p) cur = cur->next = p, p = p->next, l++; // 扫尾
                while (r < i && q) cur = cur->next = q, q = q->next, r++;
                cur->next = q;
            }
        }
        return dummy->next;
    }
};
```

插入排序

```c++
class Solution {
public:
    ListNode* insertionSortList(ListNode* head) {
        auto dummy = new ListNode(-1);
        for (auto p = head; p;) {  //过程是建立一个新的链表，每次把数插到比它大的数的前面
            auto cur = dummy, next = p->next;
            while (cur->next && cur->next->val <= p->val) cur = cur->next;
            // 循环退出后cur->next为空或者为比p大的数
            // 把p插到这个点的前面
            p->next = cur->next;
            cur->next = p;
            p = next;
        }
        return dummy->next;
    }
};

```





## 31. 下一个排列

找规律$O(n)$

- 如果数组尾部是上升序列，则交换最后两个元素
- 如果数组尾部是下降序列，则以下降序列的开始作为临界
    1. 如果数组全是下降序列，则逆序
    2. 数组先上升后下降，将临界值前一个数与下降序列最后一个比它大的数交换，再把该位置之后整理为升序

找下一个排列就是从后往前寻找第一个出现降的地方，把这个地方的数字与后边某个比它大的的数字交换，再把该位置之后整理为升序。

```c++
0 1 2 3 4
2 4 6 5 3   
k = 2 通过找前一个数比当前数大，找到临界值6，数组先增后降
需要从后面找到最后一个比前一个数大的交换
t = 4 此时t-1位最后一个比k-1大的数
2 5 6 4 3
此时后半部分仍保持降序，逆序变成升序位结果
2 5 3 4 6
```

1. 从数组末尾往前找，找到第一个下降元素的位置 $k$，使得 $nums[k - 1] < nums[k]$ 

2. 如果不存在这样的 $k$，则说明数组是不递增的，直接将数组逆转即可。

3. 如果存在这样的 $k$，则从末尾找到第一个位置 $t > k$，使得 $nums[t] <= nums[k - 1]$

    找到第一个比 $k-1$ 小的数，则 $t-1$ 为最后一个比 $k-1$ 大的数

4. 交换 $nums[k-1]$ 与 $nums[t-1]$，然后将数组从 $k$ 到末尾部分逆转。

```c++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        //next_permutation(nums.begin(), nums.end());	// 库函数
        int k = nums.size() - 1;
        while (k > 0 && nums[k - 1] >= nums[k]) k--;
        if (k <= 0) reverse(nums.begin(), nums.end());
        else {
            int t = k;
            while (t < nums.size() && nums[t] > nums[k - 1]) t++;
            swap(nums[t - 1], nums[k - 1]);
            reverse(nums.begin() + k, nums.end());
        }
    }
};
```





## 93. 复原IP地址

**模拟**

暴力模拟，分别摘出四段，检查是否符合标准

```c++
class Solution {
public:
    vector<string> restoreIpAddresses(string s) {
        vector<string> res;
        for (int a = 1; a < 4; a++) 
            for (int b = 1; b < 4; b++)
                for (int c = 1; c < 4; c++)
                    for (int d = 1; d < 4; d++) {
                        if (a + b + c + d != s.size()) continue;
                        string s1 = s.substr(0, a);
                        string s2 = s.substr(a, b);
                        string s3 = s.substr(a + b, c);
                        string s4 = s.substr(a + b + c);
                        if (check(s1) && check(s2) && check(s3) && check(s4)) {
                            string ip = s1 + '.' + s2 + '.' + s3 + '.' + s4;
                            res.push_back(ip);
                        }
                    }
        return res;
    }

    bool check(string s) {
        if (stoi(s) <= 255) 
            if (s[0] != '0' || (s[0] == '0' && s.size() == 1)) return true;
        
        return false;
    }

};
```

**回溯**

(暴力搜索) $O(C_{n−1}^3)$
直接暴力搜索出所有合法方案。
合法的IP地址由四个0到255的整数组成。我们直接枚举四个整数的位数，然后判断每个数的范围是否在0到255。

```c++
class Solution {
public:
    vector<string> res;
    vector<int> path;
    vector<string> restoreIpAddresses(string s) {
        dfs(0, 0, s);
        return res;
    }
    // u表示枚举到的字符串下标，k表示当前截断的IP个数，s表示原字符串
    void dfs(int u, int k, string &s) {
        if (u == s.size()) {
            if (k == 4) {
                string ip = to_string(path[0]);
                for (int i= 1; i < 4; i++) 
                    ip += '.' + to_string(path[i]);
                res.push_back(ip);
            }
            return;
        }
        if (k > 4) return;

        unsigned t = 0;
        for (int i = u; i < s.size(); i++) {
            t = t * 10 + s[i] - '0';
            if (t >= 0 && t < 256) {
                path.push_back(t);
                dfs(i + 1, k + 1, s);
                path.pop_back();
            }
            if (!t) break;      // 排除第一位是0的情况
        }
    }
};
```



## 2. 两数相加

**链表+模拟**

做有关链表的题目，有个常用技巧：添加一个虚拟头结点：
`ListNode *dummy = new ListNode(-1);`
`auto cur = dummy;` 用cur操作
可以简化边界情况的判断。

```c++
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int c = 0;
        ListNode* dummy = new ListNode(0);
        auto cur = dummy;
        while (l1 || l2) {
            int n1 = l1 ? l1->val : 0;
            int n2 = l2 ? l2->val : 0;
            int sum = n1 + n2 + c;
            c = sum / 10;
            cur->next = new ListNode(sum % 10);
            cur = cur->next;
            if (l1) l1 = l1->next;
            if (l2) l2 = l2->next;
        }
        if (c) cur->next = new ListNode(c);
        return dummy->next;

    }
};
```



## 22. 括号生成

括号序列两个结论：

1. 任意前缀中 "(" 数量 ≥ “)" 数量
2. 左右括号数量相等

**卡特兰数**
给定 $n$ 个 $0$ 和 $n$ 个 $1$ ，它们按照某种顺序排成长度为 $2n$ 的序列，满足任意前缀中 $0$ 的个数都不少于 $1$ 的个数的序列的数量为： $Cat(n) = \frac {C(2n, n)} {(n + 1)}$

**时间复杂度**
$O(\frac {C(2n, n)} {(n + 1)})$

```c++
class Solution {
public:
    vector<string> res;
    vector<string> generateParenthesis(int n) {
        dfs(n, 0, 0, "");
        return res;
    }
    void dfs(int n, int lc, int rc, string seq) {
        if (lc == n && rc ==n) res.push_back(seq);
        else {
            if (lc < n) dfs(n, lc + 1, rc, seq +'(');
            if (rc < n && lc > rc) dfs(n, lc, rc + 1, seq + ')');
        }
    }
};
```



## 69. x的平方根

(二分) $O(logx)$

二分出最大的 $y$，满足 $y2≤x$ 则 $y$ 就是答案。

时间复杂度分析：二分的时间复杂度是 $O(logx)$

```c++
class Solution {
public:
    int mySqrt(int x) {
        int l = 0, r = x;
        while (l < r) {
            int mid =  l + (r - l) / 2 + 1;
            if (mid <= x / mid) l = mid;
            else r = mid - 1;
        }
        return l;
    }
};
```



## 8. 字符串转换整数 (atoi)

**模拟**
注意：
int范围：`-2147483648 - 2147483647`
如果用res存的都是正数，最后用sign判断正负，很有可能负数没溢出正数溢出了
因此这种情况需要特判一下！！！

**时间复杂度：**

假设字符串长度是 $n$，每个字符最多遍历一次

总时间复杂度 $O(n)$

```c++
class Solution {
public:
    int myAtoi(string s) {
        int i = 0, n = s.size();
        while (i < n && s[i] == ' ') i++;
        if (i == n) return 0;
        
        int sign = 1;
        if (s[i] == '-') sign = -1, i++;
        else if (s[i] == '+') i++;

        int res = 0;
        while (i < n && s[i] >= '0' && s[i] <= '9') {
            int x = s[i] - '0';
            if (sign > 0 && res > (INT_MAX - x) / 10) return INT_MAX;
            if (sign < 0 && -res < (INT_MIN + x) / 10) return INT_MIN;
            if (-res * 10 - x == INT_MIN) return INT_MIN;      // -2147483648 - 2147483647 负数没溢出，正数溢出了。
            res = res * 10 + x;
            i++;
        }
        res *= sign;
        return res;
    }
};
```





## 41. 缺失的第一个正数

(桶排序) $O(n)$ Time, $O(1)$ Space

- 不用额外空间的桶排序：保证1出现在nums[0]的位置上，2出现在nums[1]的位置上，…，n出现在nums[n-1]的位置上，其他的数字不管。例如[3,4,-1,1]将被排序为[1,-1,3,4]
- 遍历nums，找到第一个不在应在位置上的1到n的数。例如，排序后的[1,-1,3,4]中第一个 nums[i] != i + 1 的是数字2（注意此时i =1）。

```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();

        for (int i = 0; i < n; i++) 
            while (nums[i] > 0 && nums[i] <= n
                    && nums[nums[i] - 1] != nums[i])
                swap(nums[i], nums[nums[i] - 1]);
        
        for (int i = 0; i < n; i++) 
            if (nums[i] != i + 1)
                return i + 1;
        
        return n + 1;
    }
};
```



## 239. 滑动窗口最大值

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> q;
        vector<int> res;
        for (int i = 0; i < nums.size(); i++) {
            if (q.size() && i - q.front() + 1 > k) q.pop_front();		// 控制窗口大小
            while (q.size() && nums[i] > nums[q.back()]) q.pop_back();  // 队列头是最大值
            q.push_back(i);
            if (i + 1 >= k) res.push_back(nums[q.front()]);
        }
        return res;
    }
};
```



## 剑指 Offer 22. 链表中倒数第k个节点

返回的是倒数第k个节点，所以不需要虚拟头节点

从头开始，快指针先走k步，然后一起走到快指针为空

满指针指向就是倒数第k个节点

```c++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        auto fast = head, slow = head;
        while (k--) fast = fast->next;
        while (fast) {
            fast = fast->next;
            slow = slow->next;
        }
        return slow;
    }
};
```



## 76. 最小覆盖子串



```c++
class Solution {
public:
    string minWindow(string s, string t) {

        unordered_map<char, int> hs, ht;    // hw 存的是 滑动窗口内 各字符数量, ht 存的是 字符串t 的 各字符数量

        for (auto c: t) ht[c]++;            // 统计 字符串t 中 各字符 数量
        string res;
        int cnt = 0;                        // 注意: cnt 中 存的是 有效字符的数量
        for (int i = 0, j = 0; i < s.size(); i++) {
            // (1) 指针 i 先移动, hs[s[i]] ++, 然后看看 是否 会 增加 cnt, 通过 if (hs[s[i]] <= ht[s[i]]) 来判断
            hs[s[i]]++;
            if (hs[s[i]] <= ht[s[i]]) cnt++;

            // (2) j 就负责 看看 i 之前开辟的疆土里面有没有 冗余的字符, 有的话就回收, hs[s[j++]] -- 
            while (j <= i && hs[s[j]] > ht[s[j]]) hs[s[j++]]--;

            // (3) i 每次开辟完新的疆土 同时 j 也回收了 可能存在的 无价值的 冗余字符, 就可以判断 cnt == t.size()
            if (cnt == t.size()) {                          // 更新答案
                if (res.empty() || i - j + 1 < res.size())  // 注意 res.empty()
                    res = s.substr(j, i - j + 1);
            }
        }
        return res;
    }
};
```



## 165. 比较版本号



```c++
class Solution {
public:
    int compareVersion(string v1, string v2) {
        for (int i = 0, j = 0; i < v1.size() || i < v2.size();) {
            int a = i, b = j;
            while (a < v1.size() && v1[a] != '.') a++;
            while (b < v2.size() && v2[b] != '.') b++;
            int x = a == i ? 0 : stoi(v1.substr(i, a - i));	// 如果已经到了尽头，值为0
            int y = b == j ? 0 : stoi(v2.substr(j, b - j)); // stoi会忽略前导0
            if (x > y) return 1;
            if (x < y) return -1;
            i = a + 1, j = b + 1;
        }
        return 0;
    }
};
```



## 155. 最小栈

辅助栈，存储最小值

```c++
class MinStack {
public:
    stack<int> stk;
    stack<int> smin;
    MinStack() {

    }
    
    void push(int val) {
        stk.push(val);
        if (smin.empty() || smin.top() >= val) smin.push(val);
    }
    
    void pop() {
        int x = stk.top();
        if (smin.top() == x) smin.pop();
        stk.pop();
    }
    
    int top() {
        return stk.top();
    }
    
    int getMin() {
        return smin.top();
    }
};
```





## 322. 零钱兑换

**动态规划**

完全背包问题。
相当于有 n 种物品，每种物品的体积是硬币面值，价值是1。问装满背包最少需要多少价值的物品？

状态表示： $f[j]$ 表示凑出 $j$ 价值的钱，最少需要多少个硬币。
第一层循环枚举不同硬币，第二层循环从小到大枚举所有价值（由于每种硬币有无限多个，所以要从小到大枚举），然后用第 $i$ 种硬币更新 $f[j]$：$f[j] = min(f[j], f[j - coins[i]] + 1)$。

时间复杂度分析：令 $n$ 表示硬币种数，$m$ 表示总价钱，则总共两层循环，所以时间复杂度是 $O(nm)$。

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int m) {
        vector<int> f(m + 1, 1e8);
        f[0] = 0;
        for (auto v: coins) 
            for (int j = v; j <= m; j++)
                f[j] = min(f[j], f[j - v] + 1);
        if (f[m] == 1e8) return -1;
        return f[m];
    }
};
```



## 43. 字符串相乘

高精度，两个数乘法

```c++
class Solution {
public:
    string multiply(string num1, string num2) {
        vector<int> a, b;
        int n = num1.size(), m = num2.size();
        for (int i = n - 1; i >= 0; i--) a.push_back(num1[i] - '0');
        for (int i = m - 1; i >= 0; i--) b.push_back(num2[i] - '0');

        vector<int> c(n + m);
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++)
                c[i + j] += a[i] * b[j];
        
        int t = 0;
        for (int i = 0; i < c.size(); i++) {
            t += c[i];
            c[i] = t % 10;
            t /= 10;
        }
        int k = c.size() - 1;
        while (k > 0 && !c[k]) k--;		// 去除前导0
        
        string res;
        while (k >= 0) res += c[k--] + '0';
        return res;
    }
};
```



## 32. 最长有效括号

(栈) $O(n)$

1. 用栈维护当前待匹配的左括号的位置。同时用 start 记录一个新的可能合法的子串的起始位置。初始设为 0。
2. 遇到左括号，当前位置进栈。
3. 遇到右括号，如果当前栈不空，则当前栈顶出栈。出栈后，如果栈为空，则更新答案 `i - start + 1`；否则更新答案 `i - st.top()`。
4. 遇到右括号且当前栈为空，则当前的 start 开始的子串不再可能为合法子串了，下一个合法子串的起始位置是 `i + 1`，更新 `start = i + 1`。



```c++
class Solution {
public:
    int longestValidParentheses(string s) {
        int n = s.size();
        stack<int> st;

        int start = 0, res = 0;
        for (int i = 0; i < n; i++) {
            if (s[i] == '(') st.push(i);
            else {
                if (st.size()) {
                    st.pop();
                    if (st.size()) res = max(res, i - st.top());
                    else res = max(res, i - start + 1);
                } else {
                    start = i + 1;
                }
            }
        }
        return res;
    }
};
```



## 105. 从前序与中序遍历序列构造二叉树

**递归**
递归建立整棵二叉树：先递归创建左右子树，然后创建根节点，并让指针指向两棵子树。
具体步骤如下：

1. 先利用前序遍历找根节点：前序遍历的第一个数，就是根节点的值；
2. 在中序遍历中找到根节点的位置 k，则 k 左边是左子树的中序遍历，右边是右子树的中序遍历；
3. 假设左子树的中序遍历的长度是 l，则在前序遍历中，根节点后面的 l 个数，是左子树的前序遍历，剩下的数是右子树的前序遍历；
4. 有了左右子树的前序遍历和中序遍历，我们可以先递归创建出左右子树，然后再创建根节点；

时间复杂度分析：我们在初始化时，用哈希表`(unordered_map<int,int>)`记录每个值在中序遍历中的位置，这样我们在递归到每个节点时，在中序遍历中查找根节点位置的操作，只需要 $O(1)$ 的时间。此时，创建每个节点需要的时间是 $O(1)$，所以总时间复杂度是 $O(n)$。

```c++
class Solution {
public:
    unordered_map<int, int> pos;
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        for (int i = 0; i < n; i++) pos[inorder[i]] = i;
        return dfs(preorder, inorder, 0, n - 1, 0, n - 1);
    }

    TreeNode* dfs(vector<int>& preorder, vector<int>& inorder, int pl, int pr, int il, int ir) {
        if (pl > pr) return nullptr;
        auto root = new TreeNode(preorder[pl]);
        int k = pos[preorder[pl]];  // k 表示当前根结点在所给的中序遍历范围区间内的位置（分割左右子树）
        // 前序遍历根节点在第一个

        // k是位置 k-il 是左子树的长度
        auto left = dfs(preorder, inorder, pl + 1, pl + 1 + k - il - 1, il, k - 1);
        // 左子树
        // 前序 左节点为根节点的下一个，右节点为 pl + 1 + k -il - 1 = pl + k - il
        // 中序 左节点为原来的 il，右节点为根节点的前一个 k - 1
        
        auto right = dfs(preorder, inorder, pl + k - il + 1, pr, k + 1, ir);
        // 右子树
        // 前序 左节点为左树右节点的下一个 pl + k - il + 1，右节点为原来的pr
        // 中序 左节点为 k + 1,右节点为原来的 ir
        root->left = left, root->right = right;
        return root;
    }
};
```



## 151. 反转字符串中的单词

**字符串**
只是用额外$O(1)$的空间
分两步操作：

将字符串中的每个单词逆序，样例输入变为: "eht yks si eulb"；
将整个字符串逆序，样例输入变为："blue is sky the"；
```c++
class Solution {
public:
    string reverseWords(string s) {
        int k = 0;
        int n = s.size();
        for (int i = 0; i < n; ) {
            int j = i;
            while (j < n && s[j] == ' ') j++;       // 去除前导空格
            if (j == n) break;
            i = j;
            while (j < n && s[j] != ' ') j++;       // 找到单词
            reverse(s.begin() + i, s.begin() + j);  // 翻转单词
            if (k) s[k++] = ' ' ;// 如果当前处理字符串是第一个字符串，则无需在字符串开头添加空格，否则则增加一个空格
            while (i < j) s[k++] = s[i++]; // 对当前字符串进行复制，使得相邻字符串间只有一个空格

        }
        s.erase(s.begin() + k,s.end()); //删除结尾的所有空格
        reverse(s.begin(),s.end());
        return s;
    }
};
```
