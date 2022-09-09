# LeetCode代码

## 1. 两数之和

**哈希表**

**时间复杂度：**

由于只扫描一遍，且哈希表的插入和查询操作的复杂度是 $O(1)$总

时间复杂度是 $O(n)$。

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> res;
        unordered_map<int,int> hash;
        for (int i = 0; i < nums.size(); i ++ ) {
            int another = target - nums[i];
            if (hash.count(another)) {
                res = vector<int>({hash[another], i});
                break;
            }
            hash[nums[i]] = i;
        }
        return res;
    }
};

```



## 2. 两数相加

**链表+模拟**

做有关链表的题目，有个常用技巧：添加一个虚拟头结点：
`ListNode *dummy = new ListNode(-1);`
`auto cur = dummy;` 用cur操作
可以简化边界情况的判断。

**时间复杂度：**

由于总共扫描一遍，所以时间复杂度是 $O(n)$。

```c++
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* dummy = new ListNode(-1);
        auto cur = dummy;
        int carry = 0;  //表示进位
        while (l1 || l2) {
            int n1 = l1 ? l1->val : 0;
            int n2 = l2 ? l2->val : 0;
            int sum = n1 + n2 + carry;
            carry = sum / 10;
            cur->next = new ListNode(sum % 10);
            cur = cur->next;
            if (l1) l1 = l1->next;
            if (l2) l2 = l2->next;
        }
        if (carry) cur->next = new ListNode(1);
        return dummy->next;
    }
};
```



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



## 4. 寻找两个正序数组的中位数

**递归**

> 将问题直接转化成求两个数组中第k小的元素

如果该问题可以解决，那么第 (n+m)/2(n+m)/2 小数就是我们要求的中位数.
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

**时间复杂度：**

$k=(m+n)/2$，且每次递归 $k$ 的规模都减少一半总时

间复杂度 $O(log(m+n))$

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
        if (nums1.size() - i > nums2.size() -j) return findnum(nums2, j, nums1, i, k);
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



## 5. 最长回文子串

**双指针(中心扩散)**

1. 枚举数组当中的每个位置`i`，从当前位置向两边扩散，分奇数和和偶数两个情况
2. 奇数时，从 `l = i`, `r = i`往两边扩散 `(s[l]==s[j]),i++,j++)`
3. 偶数时，从 `l = i`, `r = i + 1`往两边扩散 `(s[l]==s[j]),i++,j++)`
4. 返回当前以 `i`为中心的回文子串长度(r - l + 1),存储最大值

**时间复杂度：**

枚举每个位置$O(n)$,求回文子串$O(n)$

总时间复杂度$O(n^2)$

**图示:**
 ![](https://cdn.acwing.com/media/article/image/2022/08/19/29688_ad47f6901f-image-20210529111940128.png) 
循环退出时`s[l]!=s[r]`,故长度`r - l - 1`

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        string res;
        for (int i = 0; i < s.size(); i++) {
            int l = i, r = i;
            while (l >= 0 && r < s.size() && s[l] == s[r]) l-- , r++;   
            if (res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);   
            l = i, r = i + 1;
            while (l >= 0 && r < s.size() && s[l] == s[r]) l --, r ++ ;
            if (res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
        }
        return res;
    }
};
```



## 6. Z字形变换

**模拟**
这种按某种形状打印字符的题目，一般通过手画小图找规律来做。
我们先画行数是4的情况：

```
0     6       12
1   5 7    11 ..
2 4   8 10
3     9
```
从中我们发现，对于行数是 $n$ 的情况：
1. 对于第一行和最后一行，是公差为 $2(n−1)$ 的等差数列，首项是 $0$ 和 $n−1$；
2. 对于第 $i$ 行 $(0<i<n−1)$，在每一个等差数字数后面有一个 $j + 2(n-1) - 2i$的数字
    `eg:`  `1` 后面是 `5` ：$5 = 1 + 6 - 2 * 1$,  `2` 后面是 `4`：$4 = 2 + 6 - 2 * 2$

**时间复杂度：**

每个字符遍历一遍，所以时间复杂度是 $O(n)$

```c++
class Solution {
public:
    string convert(string s, int numRows) {
        if (numRows <= 1 || numRows >= s.size()) return s;
        int n = s.size();
        int step = 2 * (numRows - 1);
        string res = "";
        for (int i = 0; i < numRows; i++) {
            for (int j = i; j < s.size(); j+= step) {
                res.push_back(s[j]);
                int step2 = i ? step - 2 * i : 0;
                if (step2 && j + step2 < n) {
                    res.push_back(s[j + step2]);
                }
            }
        }
        return res;
    }
};
```



## 7. 整数反转

**数学**

注意：
C++中负数取模得到的值为负数，和实际不一样
`eg`: $-4 mod 10=-4$
`int` 存数据，溢出情况有两种

1. `x>0`：`10*res + x % 10 > max` 即 `res > (max - x % 10) / 10` 最大值正数减正数不会溢出
2. `x<0`：`10*res + x % 10 < min` 即 `res < (min - x % 10) / 10` 最小值负数减负数不会溢出

**时间复杂度：**

一共有 $O(logn)$ 位，对于每一位的计算量是常数级的

总时间复杂度是 $O(logn)$

```c++
class Solution {
public:
    int reverse(int x) {
        int res = 0;
        while (x) {
            if (x > 0 && res > (INT_MAX - x % 10) / 10) return 0;
            if (x < 0 && res < (INT_MIN - x % 10) / 10) return 0;
            res = res * 10 + x % 10;
            x /= 10;
        }
        return res;
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



## 9. 回文数 

**数学**
借鉴7.整数反转，一个正整数的字符串表示是回文串，当且仅当它的逆序和它本身相等。
但如果直接这么做，会存在整数溢出的问题，比如`1111111119`的逆序是`9111111111`(大于INT_MAX)。
所以我们要对其改进：我们只需先算出后一半的逆序值，再判断是否和前一半相等即可。
偶数时候：前一半=后一半； 奇数时候：前一半/10 = 后一半。

**时间复杂度**

int型整数在十进制表示下最多有10位，对于每一位的计算是常数级的

总时间复杂度是$O(1)$。

```c++
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0 || x && x % 10 == 0) return false;
        int s = 0;
        while (s <= x) {
            s = s * 10 + x % 10;
            if (s == x || s == x / 10) return true;
            x /= 10; 
        }
        return false;
    }
};
```
其他做法：转成字符串
```c++
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0) return false;
        string s = to_string(x);
        return s == string(s.rbegin(), s.rend());
    }
};
```



## 10. 正则表达式匹配

**动态规划**

状态表示：$f[i][j]$ 表示 $s[i,...]$ 和 $p[j,...]$ 相匹配
    	属性：是否存在

状态转移：

1. $p[j]$ 是正常字符, `f[i][j] = s[i] == p[j] && f[i - 1][j - 1]`;

2. $p[j]$ 是 `'.'` ,`f[i][j] = f[i - 1][j - 1]`;

3. $p[j]$ 是 `'*'`
    1. `f[i][j] = f[i][j - 2]` 表示丢弃这一次的  `'*'` 和它之前的那个字符；
    
    2. `s[i] == p[j - 1]` 表示这个字符可以利用这个 '*'，则可以从 $f[i−1,j]$ 转移 
    
      `f[i][j] == f[i][j] || f[i - 1][j]`
      `f[i - 1][j]` 字符串后移 `1` 字符，模式不变，即继续匹配字符下一位，因为 “星号” 可以匹配多位

**时间复杂度分析：**

$n$ 表示 $s$ 的长度，$m$ 表示 $p$ 的长度，总共 $nm$ 个状态，状态转移复杂度 $O(1)$

总时间复杂度是 $O(nm)$。

**线性DP**

```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        vector<vector<int>> f(s.size() + 5, vector<int>(p.size() + 5));
        s = ' ' + s;
        p = ' ' + p;
        
        f[0][0] = 1;
        int n = s.size(), m = p.size();
        
        for (int i = 0; i <= n; i++) {
            for (int j = 1; j <= m; j++) { 
                if (i >= 1 && (p[j] == '.' || p[j] == s[i]) && f[i - 1][j - 1]) f[i][j] = 1;
                if (p[j] == '*') {
                    if (j >= 2 && f[i][j - 2]) f[i][j] = 1;
                    // 匹配0个字符说明 j 和 j-1 都舍弃 f[i][j] = f[i][j - 2]
                    if (i >= 1 && (p[j - 1] == s[i] || p[j - 1] == '.') && f[i - 1][j]) f[i][j] = 1; 
                    // f[i - 1][j] 字符串后移1字符，模式不变，即继续匹配字符下一位，因为 “星号” 可以匹配多位
                    // 注意这里 f[i][j] == f[i - 1][j] 是错的，因为它可能已经为1，但又符合条件被赋为0
                    // 改写方法 f[i][j] == f[i][j] || f[i - 1][j]
                }
            }
        }
        return f[n][m];
     
    }
};
```

**记忆化搜索**

```c++
class Solution {
public:
    vector<vector<int>> f;
    int n, m;
    bool isMatch(string s, string p) {
        n = s.size();
        m = p.size();
        f = vector<vector<int>>(n + 1, vector<int>(m + 1, - 1));
        return dp(0, 0,s, p);
    }
    
    bool dp(int x, int y, string &s, string &p) {
        if (f[x][y] != -1) return f[x][y];
        if (y == m) return f[x][y] = (x == n);
        bool first_match = ( x < n && (s[x] == p[y] || p[y] == '.') );
        bool ans;
        if (y + 1 < m && p[y + 1] =='*') {
            ans = dp(x, y + 2, s, p) || first_match && dp(x + 1, y, s, p);
        } else {
            ans = first_match && dp(x + 1, y + 1, s, p);
        }
        return f[x][y] = ans;
    }
};
```



## 11. 盛最多的水

**双指针**
做法：

用两个指针 $i,j$ 分别指向首尾，如果 $ai<aj$，则 $i++$；否则 $j--$，直到 $i=j$ 为止，每次迭代更新最大值。

证明：

假设最优解对应的两条线的下标是 $i′,j′(i′<j′)$，在 $i,j$ 不断靠近的过程中，不妨假设 $i$ 先走到 $i′$，则此时有 $j′<j$。反证，如果此时 $ai≤aj$，设 $S$ 表示 $i,j$能盛多少水，，$S′$ 表示 $i′,j′$ 能盛多少水，则：
$S=min(ai,aj)∗(j−i)$

$=ai∗(j−i)$

$>ai∗(j′−i)$

$≥min(ai,aj′)∗(j′−i)=S′$
与 $S′$ 是最优解矛盾，因此 $ai>aj$，所以 $j$ 会一直走到 $j′$，从而得到最优解。

**时间复杂度分析：**

两个指针总共扫描 $n$ 次，因此总时间复杂度是 $O(n)$.

```C++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int l = 0, r = height.size() - 1;
        int res = 0;
        while (l < r) {
            int minlen = min(height[l], height[r]);
            res = max(res, minlen * (r - l));
            if (height[l] < height[r]) l++;
            else r--;
        }
        return res;
    }
};
```



## 12. 整数转罗马数字

**模拟**

罗马字符：

|  基本字符  |  I   |  V   |  X   |  L   |  C   |  D   |  M   |
| :--------: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| 阿拉伯数字 |  1   |  5   |  10  |  50  | 100  | 500  | 1000 |

可以将所有减法操作看做一个整体，当成一种新的单位。从大到小整理所有单位得到：

|  M   |  CM  |  D   |  CD  |  C   |  XC  |  L   |  XL  |  X   |  IX  |  V   |  IV  |  I   |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| 1000 | 900  | 500  | 400  | 100  |  90  |  50  |  40  |  10  |  9   |  5   |  4   |  1   |

此时我们可以将目标整数看成这些单位值的加和，且同一种单位不能使用超过3次。

所以我们尽可能优先使用值较大的单位即可。

**时间复杂度分析：**

计算量与最终罗马数字的长度成正比，对于每一位阿拉伯数字，罗马数字最多用 $4$  个字母表示（比如VIII=8）。

所以罗马数字的长度和阿拉伯数字的长度是一个数量级的，而阿拉伯数字的长度是 $O(logn)$。

总时间复杂度是 $O(logn)$。

```c++
class Solution {
public:
    string intToRoman(int num) {
        int values[] = {
            1000, 
            900, 500, 400, 100, 
            90, 50, 40, 10, 
            9, 5, 4, 1
        };
        string reps[] = {
            "M", 
            "CM", "D", "CD", "C", 
            "XC", "L", "XL", "X", 
            "IX", "V", "IV", "I"
        };
        string res;
        for (int i = 0; i < 13; i ++ )  // 贪心，从面值大的开始拼凑
            while(num >= values[i])
            {
                num -= values[i];
                res += reps[i];
            }
        return res;
    }
};
```

