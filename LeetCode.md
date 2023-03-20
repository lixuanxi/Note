# LeetCode代码

## 1. 两数之和

**哈希表**

**时间复杂度：**

由于只扫描一遍，且哈希表的插入和查询操作的复杂度是 $O(1)$

总时间复杂度是 $O(n)$。

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
`eg`: $-4\ mod\ 10=-4$
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



## 13. 罗马数字转整数

**哈希表**

1. 定义映射，将单一字母映射到数字。
2. 从前往后扫描，如果发现 s[i+1] 的数字比 s[i] 的数字大，那么累计 -s[i] 差值即可，并将 i 多向后移动一位；否则直接累计 +s[i] 的值。

**时间复杂度**

仅遍历一次整个字符串，故时间复杂度为 $O(n)$。

```c++
class Solution {
public:
    int romanToInt(string s) {
        unordered_map<char, int> hash;
        hash['I'] = 1, hash['V'] = 5;
        hash['X'] = 10, hash['L'] = 50;
        hash['C'] = 100, hash['D'] = 500;
        hash['M'] = 1000;

        int res = 0;
        for (int i = 0; i < s.size(); i++) {
            if (i + 1 < s.size() && hash[s[i]] < hash[s[i + 1]])
                res -= hash[s[i]];
            else res += hash[s[i]];
        }
        return res;
    }
};
```



## 14. 最长公共前缀

**字符串**

暴力枚举方法很简单：先找到所有字符串的最短长度 m，然后从长度 1 到 m 依次枚举判断是否所有字符串的前缀是否都相等。

**时间复杂度**

最坏情况下，对于 $n$ 个字符串，都需要遍历到最短长度

总时间复杂度为 $O(nm)$。

```C++
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        string res;
        for (int i = 0;; i++) {
            if (i >= strs[0].size()) return res;
            char c =strs[0][i];
            for (auto& str: strs) {
                if (str.size() <= i || str[i] != c)	// 防止越界
                    return res;
            }
            res += c;
        }
        return res;
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

**时间复杂度**

排序时间复杂度是 $O(nlogn)$，枚举的时间复杂的是 $O(n^2)$

总时间复杂度是 $O(n^2)$

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

**另一版本写法**

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        sort(nums.begin(), nums.end());
        for (int i = 0; i < nums.size(); i ++ ) {
            if (i && nums[i] == nums[i - 1]) continue;
            for (int j = i + 1, k = nums.size() - 1; j < k; j ++ ) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                while (j < k - 1 && nums[i] + nums[j] + nums[k - 1] >= 0) k -- ;
                if (nums[i] + nums[j] + nums[k] == 0) {
                    res.push_back({nums[i], nums[j], nums[k]});
                }
            }
        }
        return res;
    }
};

```



## 16. 最接近的三数之和

**双指针**

类似 [LeetCode 15.三数之和](#15. 三数之和) 

每三个数更新差的绝对值的最小值,双指针移动判断。

**时间复杂度**

排序时间复杂度是 $O(nlogn)$，枚举的时间复杂的是 $O(n^2)$

总时间复杂度是 $O(n^2)$
```c++
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        int res = 1e9, n = nums.size();
        sort(nums.begin(), nums.end());
        for (int i = 0; i < n - 2; i++) {
            if (i && nums[i] == nums[i - 1]) continue;
            int j = i + 1, k = n - 1;
            while (j < k) {
                int num = nums[i] + nums[j] + nums[k];
                if (abs(num - target) < abs(res - target)) res = num;
                if (num < target) j++;
                else if (num > target) k--;
                else return target;
            }
        }
        return res;
    }
};
```



## 17. 电话号码的字母组合

**DFS回溯搜素**

1. 可以通过手工或者循环的方式预处理每个数字可以代表哪些字母。
2. 通过递归尝试拼接一个新字母。
3. 递归到目标长度，将当前字母串加入到答案中。

**时间复杂度**
一数字最多有 $4$ 种情况，假设有 $n$ 个数字，因此 $4^n$ 种情况是一个上限
总时间复杂度是 $O(4n)$

**递归函数设计：**
`void dfs(string& digits, int u, string path)` 
`digits` 字符串数组，`u` 表示枚举到 `digitis` 的第 `u` 个位置，`path` 用来记录路径。

```c++
class Solution {
public:
    vector<string> res;
    vector<string> str = {
        "", "", "abc", "def",
        "ghi", "jkl", "mno",
        "pqrs", "tuv", "wxyz"
    };
    vector<string> letterCombinations(string digits) {
        if (digits.size() == 0) return res;
        dfs(digits, 0, "");
        return res;
    }

    void dfs(string digits, int u, string path) {
        if (u == digits.size()) res.push_back(path);
        else {
            for (auto c: str[digits[u] - '0'])
                dfs(digits, u + 1, path + c);
        }
    }
};
```



## 18. 四数之和

**双指针**

完全基于 [LeetCode 15.三数之和](#15. 三数之和) 算法的思路，排序后再增加一重循环即可

**时间复杂度**

排序时间复杂度是 $O(nlogn)$，枚举的时间复杂的是 $O(n^3)$

总时间复杂度是 $O(n^3)$

```c++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        int n = nums.size();
        vector<vector<int>> res;
        if (n < 4) return res;
        for (int i = 0; i < n - 3; i++) {
            if (i && nums[i] == nums[i - 1]) continue;
            for (int j = i + 1; j < n - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                int l = j + 1, r = n - 1;
                while (l < r) {
                    if (nums[i] + nums[j] == target - nums[l] - nums[r]) {
                        res.push_back({nums[i], nums[j], nums[l], nums[r]});
                        do l++; while (l < r && nums[l] == nums[l - 1]);
                        do r--; while (l < r && nums[r] == nums[r + 1]);
                    } else if (nums[i] + nums[j] < target - nums[l] - nums[r]) l++;
                    else r--;
                }
            }
        }
        return res;
    }
};
```



## 19. 删除链表的倒数第N个节点

**链表**

**时间复杂度**

遍历两次或者一次链表，时间复杂度 $O(n)$



**遍历两次**
第一次遍历得到链表总长度 $n$，下标由 $1$ 开始算

删除链表的倒数第 $k$ 个节点，相当于正数第 $n−k+1$ 个节点。

因为是删除第 $k$ 个节点，所以倒数第 $k + 1$ 指向 倒数第 $k-1$个节点

即正数第 $n−k$ 指向  $n−k-1$ 

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        auto hair = new ListNode(-1);
        hair->next = head;
        int num = 0;
        for (auto p = head; p; p = p->next) num++;		// 求长度从头节点开始
        auto p = hair;
        for (int i = 0; i < num - n; i++) p = p->next;
        p->next = p->next->next;
        auto res = hair->next;
        delete hair;
        return res;
    }
};
```

**快慢指针**
快指针先走 $k$，然后两个指针同步走，当快指针走到头(最后一个值)时，慢指针就是链表倒数第 $k + 1$ 个节点

将倒数第 $k - 1$ 个节点指向倒数第 $k + 1$ 个节点，实现删除倒数第 $k$ 个节点

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        auto hair = new ListNode(-1);
        hair->next = head;
        auto p = hair, q = hair;
        while (n--) p = p->next;
        while (p->next) {
            p = p->next;
            q = q->next;
        }
        q->next = q->next->next;
        auto res = hair->next;
        delete hair;
        return res;
    }
};
```





## 20. 有效的括号

**栈**
从前往后枚举每个字符

1. 当遇到左括号，则将元素压进栈中
2. 当遇到右括号时
    - 如果栈为空，`return false`
    - 如果栈顶元素是对应的左括号，说明这是匹配的符号，将栈顶元素pop出
    - 否则，表示不匹配，`return false`
3. 最后，若栈是空栈，表示所有括号都已经匹配好了，若不是空栈，表示还存在未能匹配的括号

**时间复杂度** $O(n)$

```c++
class Solution {
public:
    bool isValid(string s) {
        int n = s.size();
        if (n & 1) return false;
        unordered_map<char, char> hash;
        hash['('] = ')';
        hash['['] = ']';
        hash['{'] = '}';
        stack<char> stk;
        for (auto c: s) {
            if (c == '(' || c == '[' || c == '{') stk.push(c);
            else {
                if (!stk.empty() && hash[stk.top()] == c) stk.pop();
                else return false;
            }
        }
        return stk.empty();
    }
};
```



## 21. 合并两个有序链表

**链表**

**迭代**

1. 新建头部的保护结点 `hair`，设置 `cur` 指针指向 `hair`
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
        auto hair = new ListNode(-1);
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
        return hair->next;
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



## 22. 括号生成

**模拟、递归**

括号序列两个结论：

1. 任意前缀中 "(" 数量 ≥ “)" 数量
2. 左右括号数量相等

**卡特兰数**

给定 $n$ 个 $0$ 和 $n$ 个 $1$ ，它们按照某种顺序排成长度为 $2n$ 的序列，满足任意前缀中 $0$ 的个数都不少于 $1$ 的个数的序列的数量为： $Cat(n) = \frac {C(2n, n)} {(n + 1)}$

**时间复杂度**

$O(\frac {C(2n, n)} {(n + 1)})$

```C++
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



## 24.  两两交换链表中的节点

**模拟$O(n)$**

步骤：

1. 添加虚拟头结点 hair。
2. 定义 cur 指针初始指向 hair。
3. 确保后两个点存在，first, second
4. 每次两两交换当前指针的后两个点
5. 交换后 first 在 second 前面
6. 将 cur 指向修改后的 first，接着从第 3 步循环。

```c++
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        auto cur = dummy;
        while (cur->next && cur->next->next) {
            auto tmp1 = cur->next;
            auto tmp2 = cur->next->next;
            cur->next = tmp2;
            tmp1->next = tmp2->next;
            tmp2->next = tmp1;
            cur = tmp1;
        }
        return dummy->next;
    }
};
```



## 25. K 个一组翻转链表

**模拟**

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



