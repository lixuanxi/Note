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

