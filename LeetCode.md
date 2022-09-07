# LeetCode代码

## 1. 两数之和

**哈希表**

时间复杂度：由于只扫描一遍，且哈希表的插入和查询操作的复杂度是 $O(1)$
，所以总时间复杂度是 $O(n)$。

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
时间复杂度：由于总共扫描一遍，所以时间复杂度是 $O(n)$。

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
复杂度分析：由于 $i,j$ 均最多增加 $n$ 次
且哈希表的插入和更新操作的复杂度都是 $O(1)$，因此，总时间复杂度 $O(n)$

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

**时间复杂度分析：**
$k=(m+n)/2$，且每次递归 $k$ 的规模都减少一半
因此时间复杂度是 $O(log(m+n))$

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
总的时间复杂度$O(n^2)$

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
