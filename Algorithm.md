



算法

1. 上课 学习思想 
2. 课下 背过★  + 题目

# 代码时间复杂度分析

一般ACM或者笔试题的时间限制是1秒或2秒。
在这种情况下，C++代码中的操作次数控制在 10^7^ ~10^8^ 为最佳。

下面给出在不同数据范围下，代码的时间复杂度和算法该如何选择：

[由数据范围反推算法复杂度以及算法内容 - AcWing](https://www.acwing.com/blog/content/32/)



**空间复杂度**

1 Byte = 8 bit

1 KB = 1024 Byte

1 MB = 1024 * 1024 Byte

1 GB = 1024 * 1024 * 1024 Byte

64 MB = 2 ^ 26(1024 = 2^10 10+10+6) Byte    2^24 / 4   int 4 Byte, char 1 Byte, double / long long 8 Byte



# 第一章 基础算法



## 1. 快速排序——基于分治

给定你一个长度为 n 的整数数列。请你使用快速排序对这个数列按照从小到大进行排序。

快排是一种不稳定的排序算法，排序算法稳定指的是原序两值相对顺序相同。

- 确定分界点：q[l], q[(l+r)/2], q[r] 随机 一般选中点

- ★调整区间：          ≤x                          ≥x 

  ​				 |————————|——————|

- 递归处理左、右两段

  ![快排动画](https://gitee.com/lxxdao/image/raw/master/algorithm/1.1快排动画.gif)

```
#1		3	1	2	3	5 		x=2	
	i						j		
		3	1	2	3	5	此时q[i]>=x q[j]>=x 
		i		j			i<j交换值 退出循环 
递归左边	
#2		2   1	3	3	5   	x=1	
	i				j	
		2   1	3	3	5   此时q[i]>=x q[j]>=x 
		i	j				i<j交换值 退出循环 

#3		1	2	3	3	5		x=3
	i			j
		1	2	3	3	5		x=3
			ij				i=j不交换 退出循环		
```

快排属于分治算法，分治算法都有三步：

1. 分成子问题

2. 递归处理子问题

3. 子问题合并

   细节[AcWing 785. 快速排序算法的证明与边界分析 - AcWing](https://www.acwing.com/solution/content/16777/)

**时间复杂度**

平均时间复杂度 $O(nlogn)$，最坏情况下 $O(n^2)$，在数组已排好序的情况下出现，可以通过随机化或者取中点来避免最差情况。

```c++
//左闭右闭 quick_sort(q, 0, n-1) 双指针思想
void quick_sort(int q[], int l, int r) {
    //递归的终止情况
    if (l >= r) return; //判断边界
    //第一步：分成子问题  以j为划分时，x不能选q[r] (若以i为划分,则x不能选q[l])
    
    //以j为划分，x不能去取右边界；以i为划分，x不能取左边界
    int x = q[l + r >> 1];
    int i = l - 1, j = r + 1;//取分界点x i,j为两个指针
    //int i = l - 1, j = r + 1, x = q[l + r + 1 >> 1];//用i做模板
    
    while (i < j) {
        do i ++ ; while (q[i] < x);//只要没到达，i向右探测
        do j -- ; while (q[j] > x);//只要没到达，j向左探测
        if (i < j) swap(q[i], q[j]);
    }
    //第二步：递归处理子问题
    quick_sort(q, l ,j);
    quick_sort(q, j + 1, r);
    //quick_sort(q, l, i - 1), quick_sort(q, i, r);//用i做模板
    
    //第三步：子问题合并.快排这一步不需要操作，但归并排序的核心在这一步骤
}
int main() {
    int n;
    cin >> n;
    for (int i = 0; i < n; i ++ ) cin >> q[i];
    quick_sort(q, 0 , n - 1);
    for (int i = 0; i < n; i ++) cout << q[i] << ' ';
    return 0;
}
```

```c++
    // 非do while实现
    int x = q[l + r >> 1], i = l,  j = r;
    while (1) {
        while (q[i] < x) i = i + 1;
        while (q[j] > x) j = j - 1;
        if (i >= j) break;
        swap(q[i], q[j]);
        i = i + 1;
        j = j - 1;
    }    
    
```

常规快排，需要添加随机因子交换第一个数据，不然极端情况会造成O(n^2^)

```C++
//STL版本 左闭右开 quick_sort(nums, 0, nums.size());
void quick_sort(vector<int> &nums, int l, int r) {
    if (l + 1 >= r) return;
    int x = nums[l + r / 2], i = l , j = r-1;
    while (i < j){
    	while (i < j && nums[j] >= x) {
        	--j;
    	}
    	nums[i] = nums[j];
   		while (i < j && nums[i] <= x) {
        	++i;
    	}
    	nums[j] = nums[i];
	}    
    nums[i] = x;
    quick_sort(nums, l , i);
    quick_sort(nums, i + 1, r);
}
```





------



## 2. 归并排序——分治	

给定你一个长度为 n 的整数数列。请你使用归并排序对这个数列按照从小到大进行排序。

归并排序是稳定排序

​			    left	              right

​	|——————|——————|

1. 确定分界点：mid=(l + r) / 2
2. 递归排序  left、right	[l, mid] [mid + 1, r]
3. ★归并——合二为一
	1. 主体合并
	    至少有一个小数组添加到 tmp 数组中
	2. 收尾
	    可能存在的剩下的一个小数组的尾部直接添加到 tmp 数组中
	3. 复制回来
	    tmp 数组覆盖原数组

![归并动画](https://gitee.com/lxxdao/image/raw/master/algorithm/1.2归并动画.gif)

```c++
int q[N];
int tmp[N];   	 	// 需要开额外数组！！！
//左闭右闭
void merge_sort(int q[], int l, int r) {
    if (l >= r) return;

    int mid = l + r >> 1;
    //归并左右两边
    merge_sort(q, l, mid);
    merge_sort(q, mid + 1, r);
	
    //归并的过程
    int k = 0, i = l, j = mid + 1;//i是左半边起点，j是右半边起点
    while (i <= mid && j <= r)
        if (q[i] <= q[j]) tmp[k ++ ] = q[i ++ ];
        else tmp[k ++ ] = q[j ++ ];
    //扫尾
    while (i <= mid) tmp[k ++ ] = q[i ++ ];
    while (j <= r) tmp[k ++ ] = q[j ++ ];
	
    //物归原主
    for (i = l, j = 0; i <= r; i ++, j ++ ) q[i] = tmp[j];
}
```

```c++
//STL
void merge_sort(vector<int> &q, int l, int r, vector<int> &tmp) {
	if (l >= r) return;
	int mid = l + r >> 1;

	merge_sort(q, l, mid, tmp);
	merge_sort(q, mid + 1, r, tmp);

	int k = 0, i = l, j = mid + 1; //i是左半边起点，j是右半边起点
	while (i <= mid && j <= r) {
		if (q[i] <= q[j]) tmp[k++] = q[i++];
		else tmp[k++] = q[j++];
	}
	while (i <= mid) tmp[k++] = q[i++];
	while (j <= r) tmp[k++] = q[j++];
	for (i = l, j = 0; i <= r; i++, j++) q[i] = tmp[j];
}
```



------



## 3. 二分法

有单调性一定可以二分，二分不一定需要单调性

二分的本质：给定某个区间，在区间上定义了某种性质，使得整个区间一分为二，一半区间满足性质，另一半不满足性质

```
	l			|mid   r	
	|·······||—————————|
	寻找中间的的两个分界点
```

先写 `check` 函数  想想 `true` 的更新区间是 `r=mid` 还是 `l=mid`

最后根据是 `r` 还是 `l` 来判断 `mid` 是否要 `+1`

### 1. 整数二分

```c++
bool check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
int bsearch_1(int l, int r) //寻找右区间的的左端点
{
    while (l < r)
    {
        int mid = l + r >> 1;		//小的数找左端点
        if (check(mid)) r = mid;    //check()判断mid是否满足性质
        else l = mid + 1;			//找的是右区间所以check要用来判断是否满足右区间
    }
    return l;
}
// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
int bsearch_2(int l, int r) //寻找作左区间的右端点
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;	//大的数找右端点
        if (check(mid)) l = mid;	//找的是左区间所以check要用来判断是否满足左区间
        else r = mid - 1;
    }
    return l;
}
```

#### 数的范围

给定一个按照升序排列的长度为 n 的整数数组，以及 q 个查询。

对于每个查询，返回一个元素 k 的起始位置和终止位置（位置从 0 开始计数）。

如果数组中不存在该元素，则返回 `-1 -1`。

```c++
#include <iostream>
using namespace std;

const int N=100010;
int n, m;
int q[N];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 0; i < n; i ++) scanf("%d", &q[i]);  
    while (m --){
        int x;
        scanf("%d", &x);
        
        int l = 0, r = n - 1;
        while (l < r) {					// 右区间的左端点
            int mid = l + r >> 1;		// 如果中点大于等于(等于的话可能左边还有相等点)寻找值，说明寻找值在左边
            if (q[mid] >= x) r = mid;
            else l = mid +1 ;
        }
        
        if (q[l] != x) cout << "-1 -1" <<endl; //循环结束 l=r
        else {
            cout << l<<' ';
            int l = 0, r = n - 1;
            while (l < r) {				// 左区间的右端点
                int mid = l + r + 1 >> 1;
                if (q[mid] <= x)  l = mid;	// 如果中点小于等于(等于的话可能右边还有相等点)寻找值，说明寻找值在右边
                else r = mid - 1 ;
            }
            
            cout << l <<endl;
            
        }
    }
    return 0;
}
```



### 2. 浮点数二分

精度一般比要求大2位

```C++
bool check(double x) {/* ... */} // 检查x是否满足某种性质

double bsearch_3(double l, double r)
{
    const double eps = 1e-6;   // eps 表示精度，取决于题目对精度的要求，一般比要求大2位
    while (r - l > eps)
    {
        double mid = (l + r) / 2;
        if (check(mid)) r = mid;
        else l = mid;
    }
    return l;
}
```

#### 数的三次方根

给定一个浮点数 n，求它的三次方根。

数据范围：−10000 ≤ n ≤ 10000，结果保留 6 位小数。

```c++
#include <iostream>
using namespace std;

int main() {
    double x;
    cin >> x; 
    double l = -10000, r = 10000;
    while (r - l > 1e-8) {
        double mid = (l + r) / 2;
        if (mid * mid * mid >= x) r = mid;
        else l = mid;
    } 
    printf("%lf\n", l);
    return 0;
}
```



------



## 4. 高精度

1. **高精度加法**

   用数组储存数，由个位开始储存，个位对应数组下标0

   eg:	1234567		

   ​		 7 6 5 4 3 2 1     

   ​	a [ 0 1 2 3 4 5 6 ]

```c++
// C = A + B, A >= 0, B >= 0
vector<int> add(vector<int> &A, vector<int> &B) {
    if (A.size() < B.size()) return add(B, A);

    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size(); i ++ ) {
        t += A[i]; 
        if (i < B.size()) t += B[i]; //进位加上a和b第i位上的数
        C.push_back(t % 10);
        t /= 10;
    }

    if (t) C.push_back(t);
    return C;
}

```

2. **高精度减法**

```c++
//判断正负
bool cmp(vector<int> &A, vector<int> &B) {             
    
    if( A.size() != B.size()) return A.size() > B.size();
    for (int i = A.size()- 1; i >= 0;i --)
        if (A[i] != B[i]) return A[i] > B[i];
    return true;
}

//C = A - B
vector<int> sub(vector<int> &A, vector<int> &B) {
    
    vector<int> C;
    for(int i = 0,t = 0; i < A.size(); i++) {
        t = A[i] - t;
        if (i < B.size()) t -= B[i];
        C.push_back((t + 10) % 10); // +10是为了负数借位减
        if (t < 0) t = 1;
        else t = 0;
    }
    
    while (C.size() > 1 && C.back() == 0) C.pop_back(); //去掉前导0；
    return C;

}

int main(){
    string a, b;
    vector<int> A, B;
    
    cin >> a >> b; // a = "123456"
    for (int i = a.size() - 1; i >= 0; i--) A.push_back(a[i]-'0'); //A = [6,5,4,3,2,1]
    for (int i = b.size() - 1; i >= 0; i--) B.push_back(b[i]-'0'); 
    
    if (cmp(A, B)){
        auto C = sub(A, B);
        for (int i = C.size() - 1; i >= 0 ;i --) cout << C[i] ;
    }else{
        auto C = sub(B, A);
        cout << '-';
        for (int i = C.size() - 1; i >= 0 ;i --) cout << C[i] ;
    }
    
    return 0;
}
```

3. **高精度乘法**

```c++
vector <int> mul(vector <int> & A, int b) {
    vector <int> C;

    int t = 0;
    for (int i = 0; i < A.size(); i ++) {
        t += A[i] * b;       // t + A[i] * b = 7218
        C.push_back(t % 10); // 只取个位 8
        t /= 10;             // 721 看作 进位
    }

    while (t) {            // 处理最后剩余的 t
        C.push_back(t % 10);
        t /= 10;
    }

    while (C.size() > 1 && C.back() == 0) C.pop_back();

    return C;
}
```

4. **高精度除法**

```C++
// A / b = C ... r, A >= 0, b > 0   r为余数
vector<int> div(vector<int> &A, int b, int &r) {
    vector<int> C;
    r = 0;
    for (int i = A.size() - 1; i >= 0; i -- ) //高位开始除 136 A=[631]
    {	
        r = r * 10 + A[i];	//	 1		13		 15
        C.push_back(r / b);	// 1/3=0   13/3=4	16%3=5	045
        r %= b;				// 1%3=1   13%3=1	16%3=1
    }
    reverse(C.begin(), C.end());		//540
    while (C.size() > 1 && C.back() == 0) C.pop_back();//54
    return C;
}

```



------



## 5. 前缀和

### 1. 一维前缀和

可以迅速求出第 l 个数到第 r 个数的和。

原n			`a1 , a2 , a3 , ... , an`

前缀和	 `Si = a1 + a2 + a3 + ... + ai       S0 = 0`

区间和	 ` sum(l, r) = al + al+1 + al+2 + ... + ar = Sr - Sl-1`

如何求 Sv，时间复杂度：

$[l, r]$     		$O(n)$
$Sr - S(l-1)$		$O(1)$

```c++
/*
S[i] = a[1] + a[2] + ... a[i]
a[l] + ... + a[r] = S[r] - S[l - 1]
*/
// 下标由 1 开始
for(int i = 1; i <= n; i++) cin >> a[i];
for(int i = 1; i <= n; i++) s[i] = s[i-1] + a[i];
//区间范围（l, r)
cout << s[r] - s[l - 1] << endl;
```



### 2. 二维前缀和

矩阵左上角所有元素的和:

```s[i][j] = s[i-1][j] + s[i][j-1] - s[i-1][j-1] + a[i][j]```

![前缀和1](https://gitee.com/lxxdao/image/raw/master/algorithm/1.5二维前缀和1.png)

紫色面积是指 `(1,1)` 左上角到 `(i,j-1)` 右下角的矩形面积, 绿色面积是指 `(1,1)` 左上角到 `(i-1, j)` 右下角的矩形面积。

每一个颜色的矩形面积都代表了它所包围元素的和。

**因此二维前缀和的结论为：**

`(x1,y1)`为左上角和以`(x2,y2)`为右下角的矩阵的元素的和:
```s[x2, y2] - s[x1 - 1, y2] - s[x2, y1 - 1] + s[x1 - 1, y1 - 1]```

![前缀和2](https://gitee.com/lxxdao/image/raw/master/algorithm/1.5二维前缀和2.png)

```c++
vector<vector<int>> a(N, vector<int>(N, 0));
vector<vector<int>> s(N, vector<int>(N, 0));

cin >> n >> m;
for (int i = 1; i <= n; i++) {
     for (int j = 1; j <= m; j++) {
            cin >> a[i][j];
     }
}
for (int i = 1; i <= n; i++) {
     for (int j = 1; j <= m; j++) {
          s[i][j] = s[i-1][j] + s[i][j-1] - s[i-1][j-1] + a[i][j];
     }
}
int x1, y1, x2, y2;
cin >> x1 >> y1 >> x2 >> y2;
cout << s[x2][y2] - s[x1-1][y2] - s[x2][y1-1] + s[x1-1][y1-1] << endl;
```



------



## 6. **差分**

### 1. 一维差分

类似于数学中的求导和积分，差分可以看成前缀和的逆运算。

**差分数组：**

首先给定一个原数组	`a:a[1], a[2], a[3], ..., a[n];`

然后我们构造一个数组 `b:b[1], b[2], b[3], ..., b[i];`

使得 `a[i] = b[1] + b[2] + b[3] + ... + b[i]`

也就是说，`a` 数组是 `b` 数组的前缀和数组，反过来我们把 `b` 数组叫做 `a` 数组的差分数组。

换句话说，每一个 `a[i]` 都是 `b` 数组中从头开始的一段区间和。

**构造：**

```
a[0]= 0;

b[1] = a[1] - a[0];

b[2] = a[2] - a[1];

b[3] = a[3] - a[2];

........

b[n] = a[n] - a[n-1];
```

我们只要有 `b` 数组，通过前缀和运算，就可以在 `O(n)`  的时间内得到 `a` 数组 。

**差分数组作用：**

给定区间 `[l ,r]` ，让我们把`a`数组中的 `[l, r]` 区间中的每一个数都加上 `c`,

即` a[l] + c, a[l+1] + c, a[l+2] + c,..., a[r] + c`

暴力做法是 `for` 循环 `l` 到 `r` 区间，时间复杂度 `O(n)` ，如果我们需要对原数组执行 `m` 次这样的操作，时间复杂度就会变成 `O(n*m)`

**始终要记得，a数组是b数组的前缀和数组**

比如对 `b` 数组的 `b[i]` 的修改，会影响到 `a` 数组中从 `a[i]` 及往后的每一个数。

首先让差分 `b` 数组中的 `b[l] + c` , `a` 数组变成 `a[l] + c, a[l+1] + c, ... ,a[n] + c`

然后我们打个补丁，`b[r+1] - c`, `a` 数组变成  `a[r+1] - c, a[r+2] - c, ... ,a[n] - c;`

**为啥还要打个补丁？**

![差分1](https://gitee.com/lxxdao/image/raw/master/algorithm/1.6差分1.png)

`b[l] + c`，效果使得 `a` 数组中 `a[l]` 及以后的数都加上了 c(红色部分)，但我们只要求 `l` 到 `r` 区间加上 `c` , 因此还需要执行 `b[r+1] - c` ,让 `a` 数组中 `a[r+1]` 及往后的区间再减去 `c` (绿色部分)，这样对于 `a[r]` 以后区间的数相当于没有发生改变。



```c++
for (int i = 1; i <= n; i++) {
    scanf("%d", &a[i]);
    cin >> a[i];				 	//原数组
	b[i] = a[i] - a[i - 1];      	//构建差分数组
}
int l, r, c;
while (m--) {
   	scanf("%d%d%d", &l, &r, &c);
    cin >> l >> r >> c;
	b[l] += c;    			   		//将序列中[l, r]之间的每个数都加上c
	b[r + 1] -= c;
}
for (int i = 1; i <= n; i++) {
    a[i] = b[i] + a[i - 1];    		//前缀和运算
    cout<< a[i] << ' ';
}
```

```c++
void insert(int l, int r, int c) {	//数组[l,r]区间加c
    b[l] += c;
    b[r + 1] -= c;
}
int main(){
    cin >> n >> m;
    for(int i = 1; i <= n; i++) {
        cin >> a[i];			  	//数组为a[i]
    }
    for(int i = 1; i <= n; i++) {
        insert(i, i, a[i]);		   	//元素组全为0，插入a[i] 等于计算差分b[i]
    }
    while(m--) {
        int l, r, c;
        cin >> l >> r >>c;
        insert(l, r, c);		   	//将序列中[l, r]之间的每个数都加上c
    }
    for(int i = 1; i <= n; i++) {
        a[i] = b[i] + a[i - 1];    	//前缀和运算
    	cout<< a[i] << ' ';
      //或者用b[i]储存结果
      //b[i] += b[i-1];
      //cout << b[i] <<' ';
    }
    return 0;
}
```



### 2. 差分矩阵

`a[][]` 数组是 `b[][]` 数组的前缀和数组，那么 `b[][]` 是 `a[][]` 的差分数组

使得 `a` 数组中 `a[i][j]` 是 `b` 数组左上角 `(1,1)` 到右下角 `(i,j)` 所包围矩形元素的和。

始终要记得，`a` 数组是 `b` 数组的前缀和数组，比如对b数组的 `b[i][j]` 的修改，会影响到 `a` 数组中从 `a[i][j]` 及往后的每一个数。

假定我们已经构造好了 `b` 数组，类比一维差分，我们执行以下操作来使被选中的子矩阵中的每个元素的值加上 `c` ，才能使其恢复。

![差分2](https://gitee.com/lxxdao/image/raw/master/algorithm/1.6差分2.png)

```c++
b[x1][y1] + = c;		//对应图1,让整个a数组中蓝色矩形面积的元素都加上了c。

b[x1][y2+1] - = c;		//对应图2,让整个a数组中绿色矩形面积的元素再减去c，使其内元素不发生改变

b[x2+1][y1] - = c;		//对应图3,让整个a数组中紫色矩形面积的元素再减去c，使其内元素不发生改变

b[x2+1][y2+1] + = c;	//对应图4,让整个a数组中红色矩形面积的元素再加上c，红色内的相当于被减了两次，再加上一次c，才能使其恢复。
//等价于操作
for (int i = x1; i <= x2; i++)
  for (int j = y1; j <= y2; j++)
    a[i][j] += c;
```



```c++
void insert(int x1, int y1, int x2, int y2, int c) {
    b[x1][y1] += c;
    b[x2 + 1][y1] -= c;
    b[x1][y2 + 1] -= c;
    b[x2 + 1][y2 + 1] += c;
}
int main() {
    int n, m, q;
    cin >> n >> m >> q;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> a[i][j];
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            insert(i, j, i, j, a[i][j]);      //构建差分数组
    
    while (q--) {
        int x1, y1, x2, y2, c;
        cin >> x1 >> y1 >> x2 >> y2 >> c;
        insert(x1, y1, x2, y2, c);
    }
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            b[i][j] += b[i - 1][j] + b[i][j - 1] - b[i - 1][j - 1];  //二维前缀和
            cout << b[i][j] << " ";
        }
        cout << endl;
    }
}

```



------



## **7. 双指针**

先想暴力怎么写，观察i, j是否有单调关系，根据单调关系降低时间复杂度

```c++
for (int i = 0, j = 0; i < n; i ++ ) {
    
    while (j < i && check(i, j)) j ++ ;
    // 具体问题的逻辑 将O(n2) 优化为 O(n)
}
/*
常见问题分类：
    (1) 对于一个序列，用两个指针维护一段区间
    (2) 对于两个序列，维护某种次序，比如归并排序中合并两个有序序列的操作
*/
```

### 最长连续不重复子序列

给定一个长度为 n 的整数序列，请找出最长的不包含重复的数的连续区间，输出它的长度。

输入样例：

```
5
1 2 2 3 5
```

```c++
#include<iostream>
#include<vector>
#include<algorithm>

using namespace std;

const int N = 1e5 + 10;

vector<int> a(N,0);     //输入数组
vector<int> s(N,0);     //该数据用于储存元素个数

int main() {
    int n;
    cin >> n;
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }
    int res = 0;
    for(int i = 0,j = 0; i < n; i++) {	// i维护当前元素 j维护记录当前最长序列的首元素
        s[a[i]]++;
        while (s[a[i]] > 1) {   // i指针指向元素元素个数为2的时候
            s[a[j]]--;          // 清空数组已存元素的个数
            j++;
        }
        res = max(res, i - j + 1);
    }
    cout << res << endl; 
    return 0;
}
```

### 数组元素的目标和

给定两个升序排序的有序数组 A 和 B，以及一个目标值 x。

数组下标从 0 开始，请你求出满足 $A[i]+B[j]=x$ 的数对 $(i,j)$，数据保证有唯一解。

```c++
#include<iostream>
using namespace std;
const int N = 1e5 + 10;
int a[N], b[N];

int main() {
    int n, m, x;
    cin >> n >> m >> x;
    for (int i = 0; i < n; i++) cin >> a[i];
    for (int j = 0; j < m; j++) cin >> b[j];
    
    for (int i = 0, j = m - 1; i < n; i++) {	 
        // 找到a[i]+a[j]小于x的j最大的数在移动a[i] 反复这个过程
        while (j >= 0 && a[i] + b[j] > x) j--;    // O(n+m)
        if (a[i] + b[j] == x) {
            cout << i << ' ' << j;
            break;
        }
    }
    return 0;
}
```

### 判断子序列

给定一个长度为 n 的整数序列 $a1,a2,…,an$ 以及一个长度为 m 的整数序列 $b1,b2,…,bm$。

请你判断 a 序列是否为 b 序列的子序列。

子序列指序列的一部分项按**原有次序排列**而得的序列，例如序列 ${a1,a3,a5}$ 是序列 ${a1,a2,a3,a4,a5}$ 的一个子序列。

```c++
#include<iostream>
using namespace std;

const int N = 1e5 + 10;
int a[N], b[N];
int main() {
    int n, m;
    cin >> n >> m;
    for (int i = 0; i < n; i++) cin >> a[i];
    for (int i = 0; i < m; i++) cin >> b[i];
    int i = 0;
    for (int j = 0; j < m; j++) {
        if (i < n && a[i] == b[j]) i++;
    }
    if (i == n) cout << "Yes" <<endl;
    else cout << "No" << endl;

    return 0;
}
```



------



## 8. 位运算

常见用法：

- 判断奇偶数 num & 1

- 判断某个数的第 `n` 位是 0 还是 1

1. 先把第K位移到最后一位 `x >> k`

2. 看个位是几 `x & -x`

   求x的第k位数字: 	 x >> k & 1
   返回x的最后一位1： lowbit(x) = x & -x  //	x & -x = x & (~x + 1)取反+1

   ```
   x = 1010		lobit(x) = 10	 返回最后一位1 故返回10
   x = 101000		lobit(x) = 1000		
   ```

### 二进制中1的个数

给定一个长度为 n 的数列，请你求出数列中每个数的二进制表示中 1 的个数。

```c++
#include<iostream>
using namespace std;

int lowbit(int n) {
    return n & -n;
}

int main() {
    int n;
    cin >> n;
    while (n--) {
        int x;
        cin >> x;
        int res = 0;
        while (x) {			// eg: x:101100 x1=100, x:101000 x2=1000, x:100000 x3=10000, x = 0 
            x -= lowbit(x); //每次减去最后一位1，所有1减去之后，值为0，退出循环
            res++;
        }
        cout << res << ' ';
    }
    return 0;
}
```



------



## 9. 离散化 

离散化的本质，是映射，将间隔很大的点，映射到相邻的数组元素中。减少对空间的需求，也减少计算量。

```
a[]: 1, 3, 100, 2000, 50000
	 0	1	2	  3		4
```

1. `a[]` 中可能重复元素   		**去重**
2. 如何算出 `x`离散化后的值    **二分**

```C++
vector<int> alls; 				// 存储所有待离散化的值
sort(alls.begin(), alls.end()); // 将所有值排序
alls.erase(unique(alls.begin(), alls.end()), alls.end());   // 去掉重复元素

// 二分求出x对应的离散化的值
int find(int x)  // 找到第一个大于等于x的位置
{
    int l = 0, r = alls.size() - 1;
    while (l < r) 
    {
        int mid = l + r >> 1;
        if (alls[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return r + 1; // 映射到1, 2, ...n
}
```

### 区间和

假定有一个无限长的数轴，数轴上每个坐标上的数都是 $0$。

现在，我们首先进行 $n$ 次操作，每次操作将某一位置 $x$ 上的数加 $c$。

接下来，进行 mm 次询问，每个询问包含两个整数 $l$ 和 $r$，你需要求出在区间 $[l,r]$ 之间的所有数的和。

```c++
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;

const int N = 300010;   //n次插入和m次查询相关数据量的上界

typedef pair<int,int> PII;
int n,m;
vector<int> a(N,0);     //存储坐标插入的值
vector<int> s(N,0);     //存储数组a的前缀和

vector<int> alls;       //存储（所有与插入和查询有关的）坐标
vector<PII> add, query; //存储插入和询问操作的数据

int find(int x) {       //返回的是输入的坐标的离散化下标
    int l = 0, r = alls.size() - 1;
    while (l < r) {
        int mid = l + r >> 1;
        if (alls[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return r + 1; //前缀和1开始容易处理边界
}
int main() {
    cin >> n >> m;
    for (int i = 0; i < n ; i++){
        int x, c;
        cin >> x >> c;
        add.push_back({x, c});
        
        alls.push_back(x); 
    }
    
    for (int i = 0; i < m; i++) {
        int l, r;
        cin >> l >> r;
        query.push_back({l,r});
        alls.push_back(l);
        alls.push_back(r);
    }
    //去重
    sort(alls.begin(),alls.end());
    alls.erase(unique(alls.begin(), alls.end()), alls.end());
    
    //处理插入
    for (auto item : add){
        int x = find(item.first);
        a[x] += item.second;
    }
    
    //预处理前缀和
    for (int i = 1; i <= alls.size(); i++) {
        s[i] = s[i-1] + a[i];
    }
    
    //处理询问
    for (auto item : query){
        int l = find(item.first), r = find(item.second);
        cout << s[r] - s[l - 1] << endl;
    }
    return 0;
}
```



------



## 10. 区间合并

1. 按区间左端点合并
2. 扫描

```c++
typedef pair<int,int>  PII;
// 将所有存在交集的区间合并
void merge(vector<PII> &segs) 
{
    vector<PII> res;//临时区间集

    sort(segs.begin(), segs.end()); 	//将segs内的区间集按左端点排序

    int st = -2e9, ed = -2e9;			//ed代表区间结尾，st代表区间开头
    for (auto seg : segs)
    {
        if (ed < seg.first) 			//如果维护区间右端点严格在枚举的区间左边，没有交集
        {    
            if (st != -2e9) res.push_back({st, ed});//不能是初始区间 将区间加入答案
            st = seg.first, ed = seg.second;		//更新维护区间的左右端点
        } 
        else ed = max(ed, seg.second);	//当前区间和维护区间有交集 更新区间右端点
    }
    if (st != -2e9) res.push_back({st, ed});		//输入区间集为空时 插入一个 
	//循环结束时的st,ed变量，此时的st,ed变量不需要继续维护，只需要放进res数组即可。
    segs = res;
}

```



------



# 第二章 数据结构



## 1. 链表

结构体指针方式实现链表

```C++
struct Node {
	int val;
	Node *nextl
}; //面试题用的比较多，笔试题用的少
new Node();//非常慢
```

主要用数组模拟链表

单链表；邻接表：存储图和树

双链表：

### **单链表**

```c++
//head存储链表头
//e[i]存储节点i的值
//ne[i]存储节点i的next指针
//idx表示当前用到了哪个节点 下标值
int head, e[N], ne[N], idx;

// 初始化
void init()
{
    head = -1;
    idx = 0;		//索引从0开始
}

// 在链表头插入一个数x
void insert(int x)
{
   	e[idx] = x;		//把x的值存到数组e[]的idx点
    ne[idx] = head;	//x插入在头节点前,故x的next指针为头节点的值
    head = idx;		//更新新的头结点索引
    idx++;			//因为当前idx使用了 故索引值+1
}

//在x插入下标是k的点后面
void add(int k, int x) {
    e[idx] = x;
    ne[idx] = ne[k];
    ne[k] = idx;
    idx++;
}

//将下标是K的点后面的点删掉
void remove(int k) {
    ne[k] = ne[ne[k]];
}

// 将头节点删除，需要保证头结点存在
void remove()
{
    head = ne[head];
}
```



### **双链表**

```c++
//e[i]存储节点i的值
//l[i]存储节点i的左指针
//r[i]存储节点i的右指针
//idx表示当前用到了哪个节点 下标值
int e[N], l[N], r[N], idx;

//初始化
void init() {
	//0是左端点，1是右端点
    l[1] = 0, r[0] = 1;	//第一个点的右边是1， 第二个点的左边是0
    idx = 2;			//此时已经用掉两个点0，1 下个点为2
}

//在下标为k的点的右边插入x
void insert(int k, int x) {
    e[idx] = x;
    l[idx] = k;
    r[idx] = r[k];
    l[r[k]] = idx;
    r[k] = idx;
    idx++;
}
//在下标为k的点的左边插入x 调用右插函数
insert(l[k], x);


//删除下标为k的节点
void remove(int k) {
    r[l[k]] = r[k];
    l[r[k]] = l[k];
}
```



### 邻接表

**数组实现：**

```c++
int h[N], e[N], ne[N], idx;
memset(h, -1, sizeof(h));

// 添加边
void add(int a, int b) {
	e[idx] = b;
	ne[idx] = h[a];
	h[a] = idx++;
}

// 遍历该点对应所有边
for (int i = h[x]; i != -1; i = ne[i]) 
    int j = e[i];
```

**vector实现：**

```c++
vector<int> gra[N];
gra.resize(n + 10); 	// 初始化长度
gra[a].push_back(b);  	// 添加边

// 遍历邻接表
for (int i = 0; i < gra[x].size(); i++) 
    int j = gra[x][i];
```



------



## 2. 栈

### 数组模拟

```c++
// tt表示栈顶
int stk[N], tt = 0;

// 向栈顶插入一个数
stk[ ++ tt] = x;

// 从栈顶弹出一个数
tt -- ;

// 栈顶的值
stk[tt];

// 判断栈是否为空
if (tt > 0){
	not empty;
}else {
    empty;
}
```



### **单调栈**

常见模型：找出每个数左边离它最近的比它大/小的数

算法原理：	
用单调递增栈，当该元素可以入栈的时候，栈顶元素就是它左侧第一个比它小的元素。

以：3 4 2 7 5 为例，过程如下： 

![栈](https://gitee.com/lxxdao/image/raw/master/algorithm/2.2栈.gif)

```c++
int stk[N], tt = 0;			//时间复杂度O(n)
for (int i = 0; i < n; i ++ ) {				// 注意这里插入的下标还是值
    while (tt && check(stk[tt], i)) tt -- ; // 如果栈顶元素大于当前待入栈元素，则出栈
	if (tt) {
		cout << stk[tt] << ' '; // 如果栈空，则没有比该元素小的值。
    }else {
        cout << -1 << ' '; // 栈顶元素就是左侧第一个比它小的元素
    }    
    stk[ ++ tt] = i;
}
// stl版本
stackt<int> stk;
for (int i = 0; i < n; i++) {
    int x, cin >> x;
    while (!stk.empty() && stk.top() >= x) stk.pop();
    if (!stk.empty()) {
        cout << stk[tt] << ' ';
    } else {
        cout << -1 << ' ';
    }
    stk.push(x);
}
```



### 表达式求值

**[题目描述：](https://www.acwing.com/problem/content/3305/)**给定一个表达式，其中运算符仅包含 `+,-,*,/`（加 减 乘 整除），可能包含括号，请你求出表达式的最终值。

“表达式求值”问题，两个核心关键点：

（1）双栈，一个操作数栈，一个运算符栈；

（2）运算符优先级，栈顶运算符，和，即将入栈的运算符的优先级比较：
如果栈顶的运算符优先级低，新运算符直接入栈

如果栈顶的运算符优先级高，先出栈计算，新运算符再入栈

这个方法的时间复杂度为O(n)，整个字符串只需要扫描一遍。

运算符有 `+-*/()~^&` 都没问题，如果共有 n 个运算符，会有一个 n*n 的优先级表。

```c++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <stack>
#include <map>

using namespace std;

stack<int> num;         // 存数字
stack<char> op;         // 存运算符

// 定义运算符优先级
unordered_map<char, int> pr{{'+', 1}, {'-', 1}, {'*', 2}, {'/', 2}}; 
    
void eval() {           // 末尾运算符操作末尾两数
    auto b = num.top(); num.pop();  // 第二个操作数
    auto a = num.top(); num.pop();  // 第一个操作数
    auto c = op.top(); op.pop();    // 运算符
    int x;
    if (c == '+') x = a + b;
    else if (c == '-') x = a - b;
    else if (c == '*') x = a * b;
    else x = a / b;
    num.push(x);                    // 结果入栈
}

int main() {
       
    string str;
    cin >> str;
    for (int i = 0; i < str.size(); i++) {
        auto c = str[i];
        if (isdigit(c)) {   // 数字入栈
            int x = 0, j = i;
            while (j < str.size() && isdigit(str[j]))
                x = x * 10 + str[j++] - '0';
            i = j - 1;      // 更新 i 的位置
            num.push(x);
        } else if (c == '(') op.push(c);    // 左括号无优先级，直接入栈
        else if (c == ')') {                // 遇到右括号计算括号里面的
            while (op.top() != '(') eval(); // 一直计算到左括号
            op.pop();                       // 左括号出栈
        } else {
            // 待入栈运算符优先级低，则先计算
            while (op.size() && pr[op.top()] >= pr[c]) eval();
            op.push(c);                     // 操作符入栈
        }
    }
    while (op.size())eval();    // 最后把没有操作的运算符操作一遍
    cout << num.top() << endl;
    return 0;
    
}
```



------



## 3. 队列

### 数组模拟

```C++
// hh 表示队头，tt表示队尾
int q[N], hh = 0, tt = -1;

// 向队尾插入一个数
q[ ++ tt] = x;

// 从队头弹出一个数
hh ++ ;

// 队头的值
q[hh];

// 队尾的值
q[tt];

// 判断队列是否为空
if (hh <= tt){
	not empty;
}else {
    empty;
}
```



### **单调队列**

常见题型：**滑动窗口求窗口最值**

给定一个数组以及一个大小为 $k$ 的滑动窗口，它从数组的最左边移动到最右边。

确定滑动窗口位于每个位置时，窗口中的最大值和最小值。

| 窗口位置            | 单调队列过程               | 最小值 | 单调队列过程               | 最大值 |
| :------------------ | :------------------------- | :----- | -------------------------- | ------ |
| [1 3 -1] -3 5 3 6 7 | [1]        [1 3]      [-1] | -1     | [1]          [3]    [3 -1] | 3      |
| 1 [3 -1 -3] 5 3 6 7 | [-1]       [-3]            | -3     | [3 -1]      [3 -1 -3]      | 3      |
| 1 3 [-1 -3 5] 3 6 7 | [-3]       [-3 5]          | -3     | [3 -1 -3]  [5]             | 5      |
| 1 3 -1 [-3 5 3] 6 7 | [-3 5]    [-3 3]           | -3     | [5]           [5 3]        | 5      |
| 1 3 -1 -3 [5 3 6] 7 | [-3 3]    [3 6]            | 3      | [5 3]        [6]           | 6      |
| 1 3 -1 -3 5 [3 6 7] | [3 6]     [3 6 7]          | 3      | [6]           [7]          | 7      |

```c++
const int N = 1000010;
int a[N], q[N]; 	// q[N]存的是数组下标
int hh = 0, tt = -1;// hh队列头 tt队列尾
int n, k;			// n数组长度 k的窗口大小

int main() {
    cin >> n >> k;
    for (int i = 0; i < n; i++) cin >> a[i];
 	
    int hh = 0, tt = -1;
	//滑动窗口中的最小值
	for (int i = 0; i < n; i++) {
    	/*
    	维持滑动窗口的大小
   		当队列不为空(hh <= tt) 且 当当前滑动窗口的大小(i - q[hh] + 1)>我们设定的
    	滑动窗口的大小(k),队列弹出队列头元素以维持滑动窗口的大小
    	*/
    	if(hh <= tt && i - q[hh] + 1 > k) hh++;	// 若队首出窗口，hh加1
    	/*
    	构造单调递增队列
    	当队列不为空(hh <= tt) 且 当前元素(a[i])<=队列队尾元素时
    	那么队尾元素就一定不是当前窗口最小值,删去队尾元素
    	加入当前元素(q[ ++ tt] = i)
    	*/
    	while (hh <= tt && a[i] <= a[q[tt]]) tt--; 	// 若队尾不单调，tt减1
    	q[++tt] = i;	// 下标加到队尾
    	//i + 1 >= k 数据满k的时候才开始输出
    	if(i + 1 >= k) cout << a[q[hh]] << ' ';
	}
    
	int hh = 0, tt = -1;
	//滑动窗口中的最大值
	for (int i = 0; i < n; i++) {
    	if(hh <= tt && i - q[hh] + 1 > k) hh++;
    	while(hh <= tt && a[i] >= a[q[tt]]) tt--;
    	q[++tt] = i;
    	if(i + 1 >= k) cout << a[q[hh]] << ' ';
	}
}
```

**STL双端队列实现**

```c++
class Solution {
public:
    vector<int> maxInWindows(vector<int>& nums, int k) {
        vector<int> res;
        deque<int> q;	//队列存储的是下标，因为需要利用下标判断头元素是否划出窗口
        for (int i = 0;  i < nums.size(); i++) {
            if (q.size() && q.front() <= i - k) q.pop_front();	 // 超过则弹出队列头元素以维持滑动窗口的大小
            while (q.size() && nums[i] >= nums[q.back()]) q.pop_back();	// 队尾元素就一定不是当前窗口最大值,删去队尾元素
            q.push_back(i);
            if (i + 1 >= k) res.push_back(nums[q.front()]);
        }
        return res;
    }
};
```



### 循环队列

循环队列是一种线性数据结构，其操作表现基于 FIFO（先进先出）原则并且队尾被连接在队首之后以形成一个循环。它也被称为“环形缓冲器”。

循环队列的一个好处是我们可以利用这个队列之前用过的空间。在一个普通队列里，一旦一个队列满了，我们就不能插入下一个元素，即使在队列前面仍有空间。但是使用循环队列，我们能使用这些空间去存储新的值。

支持如下操作：

- `MyCircularQueue(k)`: 构造器，设置队列长度为 k 。
- `Front`: 从队首获取元素。如果队列为空，返回 -1 。
- `Rear`: 获取队尾元素。如果队列为空，返回 -1 。
- `enQueue(value)`: 向循环队列插入一个元素。如果成功插入则返回真。
- `deQueue()`: 从循环队列中删除一个元素。如果成功删除则返回真。
- `isEmpty()`: 检查循环队列是否为空。
- `isFull()`: 检查循环队列是否已满。

**算法**

数组模拟$O(1)$

1. 我们用数组来模拟队列，指定两个下标 `hh` 和 `tt` 代表队列的头和尾。
2. 初始时，`hh = tt = 0`，且队列最大长度为 `n = k + 1`。当 `hh` 或 `tt` 达到 `n` 时，将其置 `0`。
3. `hh == tt` 表示队列为空，`tt + 1 == hh` 表示队列为满。
4. 我们也可以用一个额外变量来记录队列中元素的个数，这样就队列的最大长度可以为 `k`。

当两个指针不在同一个位置的时候，数据存储在 `[hh, tt-1]` 间，`tt` 的位置是不存元素的。

所以当 `tt` 走到 `hh−1` 的位置的时候，就没有地方可以放元素了，因为 `tt` 位置不放元素，而下一个位置就已经是 `hh` 了，所以这个时候就是队满了。

```c++
class MyCircularQueue {
public:
    int hh = 0, tt = 0;	// 队头和队尾
    vector<int> q;		// 数组模拟循环队列

    /** Initialize your data structure here. Set the size of the queue to be k. */
    MyCircularQueue(int k) {
        q.resize(k + 1);	// 多开一个位置 [0, k]
    }

    /** Insert an element into the circular queue. Return true if the operation is successful. */
    bool enQueue(int value) {
        if (isFull()) return false;
        q[tt ++ ] = value;			// 入队顺时针走队尾
        if (tt == q.size()) tt = 0;	// 走出最后就回到0
        return true;
    }

    /** Delete an element from the circular queue. Return true if the operation is successful. */
    bool deQueue() {
        if (isEmpty()) return false;
        hh ++ ;						// 删除队头
        if (hh == q.size()) hh = 0;
        return true;
    }

    /** Get the front item from the queue. */
    int Front() {
        if (isEmpty()) return -1;
        return q[hh];				// 返回队头
    }

    /** Get the last item from the queue. */
    int Rear() {
        if (isEmpty()) return -1;
        int t = tt - 1;				// 队尾指针tt不存元素，注意其上一个位置才是队尾
        if (t < 0) t += q.size();	// 如果 tt=0,t=-1,则队列满是最后一个元素
        return q[t];
    }

    /** Checks whether the circular queue is empty or not. */
    bool isEmpty() {
        return hh == tt;			// 两个指针在同一个位置时候为空
    }

    /** Checks whether the circular queue is full or not. */
    bool isFull() {
        return (tt + 1) % q.size() == hh;	// 队尾指针的下一个位置是队头时候为满
    }
};
```



### 双端循环队列

在循环队列的基础上，增加了 将元素增加到队列头部 及 从头部删除一个元素

```c++
class MyCircularDeque {
public:
    int hh = 0, tt = 0;
    vector<int> q;
    MyCircularDeque(int k) {
        q.resize(k + 1);
    }
    
    int get(int x) {
        return (x + q.size()) % q.size();	// 先加后模是为了防止复数
    }

    bool insertFront(int value) {		// 插对头
        if (isFull()) return false;
        hh = get(hh - 1);
        q[hh] = value;
        return true;
    }
    
    bool insertLast(int value) {	 	// 插队尾
        if (isFull()) return false;
        q[tt ++] = value;
        tt = get(tt);
        return true;
    }
    
    bool deleteFront() {				// 删对头
        if (isEmpty()) return false;
        hh = get(hh + 1);
        return true;
    }
    
    bool deleteLast() {					// 删队尾
        if (isEmpty()) return false;
        // 队尾指针tt不存元素，注意其上一个位置才是队尾
        tt = get(tt - 1);				
        return true;
    }
    
    int getFront() {
        if (isEmpty()) return -1;
        return q[hh];
    }
    
    int getRear() {
        if (isEmpty()) return -1;
        return q[get(tt - 1)];
    }
    
    bool isEmpty() {
        return hh == tt;
    }
    
    bool isFull() {
        return get(tt + 1) == hh;	// 队尾的下一个元素为对头为满
    }
};
```



------



## 4. KMP

模板串 P 在模式串 S 中作为子串出现的次数

### **暴力做法：**

```c++
s[N], p[M]
for (int i = 1; i <= n; i ++) {
	bool flag = true;
	for (int j = 1; j <= m; j++) {
		if(s[i + j -1] != p[j]){
			flag = false;
			break;
		}
	}
}
```



### 1. KMP基本概念

1. `s` 是模式串，即比较长的字符串。下标由 `1` 开始
2. `p` 是模板串，即比较短的字符串。下标由 `1` 开始
3. 非平凡前缀：指除了最后一个字符以外，一个字符串的全部头部组合，简称前缀
4. 非平凡后缀：指除了第一个字符以外，一个字符串的全部尾部组合，简称后缀
5. 部分匹配值：前缀和后缀的**最长共有**元素的**长度**。
6. `next[ ]` 是“部分匹配值表”，即next数组，它存储的是每一个下标对应的“部分匹配值”，是KMP算法的核心。



### 2. next数组含义

`next[i]`  = `j`  :含义是 `p[1,j] == p[i-j+1,i]` 

是 `p[1, j]` 串中前缀和后缀相同的最大长度（部分匹配值）

`p = “abcab”`

|      p       |  a   |  b   |  c   |      a      |        b        |
| :----------: | :--: | :--: | :--: | :---------: | :-------------: |
|   **下标**   |  1   |  2   |  3   |      4      |        5        |
| **next[ ]**  |  0   |  0   |  0   |      1      |        2        |
| **匹配情况** |  无  |  无  |  无  | p[1] = p[4] | p[1:2] = p[4:5] |

```
对next[1]：前缀 = 空集—————后缀 = 空集—————next[1] = 0;

对next[2]：前缀 = {a}—————后缀 = {b}—————next[2] = 0;

对next[3]：前缀 = {a, ab}—————后缀 = {c, bc}—————next[3] = 0;

对next[4]：前缀 = {a, ab, abc}—————后缀 = {a , ca, bca}—————next[4] = 1;

对next[5]：前缀 = {a, ab, abc, abca}————后缀 = {b, ab, cab, bcab}————next[ 5 ] = 2;
```

```c++
s:	"ababaeaba" // 比如s中的"ababa"子串，对标为[3~5]的"aba"
p: 	"ababacd"   // 和p中下标为[1~3]的"aba"相同,此时可以滑动j-k位,即j=j-k。
                // (其中j是pattern中"c"的下标,k是"abc"的长度)。
    	"ababaeaba"   // main[6]为"e"和pattern[6]为"c"不匹配
        "ababacd"     // 但是两个串中都有相同的"aba"前缀
              |       // 所以可以滑动j-k位
              ∨
      	"ababaeaba"   
          "ababacd"
              |        	// 滑动j-k位后发现main[5]和patterb[3]不相
              ∨		   //需要再次滑动
         "ababaeaba"   
              "ababacd"	// 滑动过程和上次类似。
```



### **3.  数组匹配思路**

`s` 串和 `p` 串都是从 `1` 开始的。

`i` 从 `1` 开始，`j` 从 `0` 开始，每次 `s[i]` 和 `p[j + 1]` 比较

![kmp匹配](https://gitee.com/lxxdao/image/raw/master/algorithm/2.4kmp匹配.PNG)

当匹配过程到上图所示时，

`s[a, b] = p[1,  j] && s[i] != p[j + 1]` 

此时要移动 `p` 串（不是移动1格，而是直接移动到下次能匹配的位置）

其中①为 `[1, next[j]]`，③为 `[j - next[j] + 1, j]`。

由匹配可知①等于③，③等于②。所以直接移动p串使①到③的位置即可。

这个操作可由 `j = next[ j ]` 直接完成。 如此往复下去，当 `j == m` 时匹配成功。

**代码如下：**

```c++
for(int i = 1, j = 0; i <= n; i++) {
    while(j && s[i] != p[j+1]) j = ne[j];
    //如果j有对应p串的元素， 且s[i] != p[j+1],则失配,移动p串
    //用while是由于移动后可能仍然失配，所以要继续移动直到匹配或整个p串移到后面(j = 0)
    if(s[i] == p[j+1]) j++;
    //当前元素匹配，j移向p串下一位
    if(j == m) {
        //匹配成功，进行相关操作
        j = next[j];  //继续匹配下一个子串
    }
}
```



### 4. next数组求法

next数组的求法是通过模板串自己与自己进行匹配操作得出来的（代码和匹配操作几乎一样）。

![next数组](https://gitee.com/lxxdao/image/raw/master/algorithm/2.4next数组.PNG)

**代码如下：**

```c++
for(int i = 2, j = 0; i <= m; i++) {
    while(j && p[i] != p[j+1]) j = next[j];
    if(p[i] == p[j+1]) j++;
    next[i] = j;
}
```

代码和匹配操作的代码几乎一样

关键在于每次移动 i 前，将 i 前面已经匹配的长度记录到next数组中。



### 5. 完整代码

```c++
// s是长文本，p是模式串，n是s的长度，m是p的长度
// 求模式串的Next数组：
s = ' ' + s;
p = ' ' + p;   //处理字符串让其下标从1开始

//求next的过程 
for (int i = 2, j = 0; i <= p.size(); i ++ ) {
    while (j && p[i] != p[j + 1]) j = ne[j];
    if (p[i] == p[j + 1]) j ++ ;
    ne[i] = j;
}

// 匹配
for (int i = 1, j = 0; i <= s.size(); i ++ ) {
    while (j && s[i] != p[j + 1]) j = ne[j];
    if (s[i] == p[j + 1]) j ++ ;
    if (j == m){
        j = ne[j];
        // 匹配成功后的逻辑
   	}
}
```



------



## 5. Trie树

Trie树：高效的存储和查找字符串集合的数据结构



### 1. son数组与idx

`son[N][x]` 是个二维数组

**第一维N是题目给的数据范围，一般为所有数据的长度和**

像在 Trie 树中的模板题 字符串统计 N为字符串的总长度（这里的总长度为所有的字符串的长度加起来）

```
eg: abc ef 需要的N为5
```

像在最大异或对，最大为N*31  （N为数据数 31为31进制）

其实根本达不到这么大，举个简单的例子假设用0和1编码，按照前面的计算最大的方法应该是4乘2=8但其实只有6个结点

```
01 10 11 10 四个数据 二进制 N = 4 * 2
实际使用：
0 00 01 1 10 11 N为6即可
```

**第二维x代表着儿子结点的可能性有多少**

字符串统计中是字符串，而题目本身又限定了均为小写字母所以只有26种可能性

在最大异或对中下一位只有0或者1两种情况所以为2

**idx**

而这个二维数组本身存的是当前结点的下标，就是N

所以总结的话 `son[N][x]` 存的就是第N的结点的x儿子的下标是多少，**然后idx就是第一个可以用的下标**



### 2. 储存形式与过程

![Tire1](https://gitee.com/lxxdao/image/raw/master/algorithm/2.5Trie1.PNG)

![Tire2](https://gitee.com/lxxdao/image/raw/master/algorithm/2.5Trie2.PNG)

```c++
假设有 abc、ef、abd 3个字符串 过程
插入abc：
1.p = 0 son[0][a] = 0, 新的节点 创建一个 son[0][a] = 1, p = 1; 使用idx=1 代表 a	  
2.p = 1 son[1][b] = 0, 新的节点 创建一个 son[1][b] = 2, p = 2; 使用idx=2 代表 ab
3.p = 2 son[2][c] = 0, 新的节点 创建一个 son[2][c] = 3, p = 2; 使用idx=3 代表 abc 
4.cnt[3] ++, 字符串abc计数为1
插入ef:
1.p = 0 son[0][e] = 0, 新的节点 创建一个 son[0][e] = 4, p = 4; 使用idx=4 代表 e
2.p = 4 son[4][f] = 0, 新的节点 创建一个 son[4][f] = 5, p = 5; 使用idx=5 代表 ef
3.cnt[5] ++，字符串ef计数为1
//在最坏情况下abc ef 所需的N为 abc + ef 字符串的长度 5  N共使用了 0~4
插入abd:
1.p = 0 son[0][a] = 1, 旧节点，索引下一个 p = 1;
2.p = 1 son[1][b] = 2, 旧节点，索引下一个 p = 2;
3.p = 2 son[2][d] = 0, 新的节点 创建一个 son[2][c] = 6, p = 6; 使用idx=6 代表 abd
3.cnt[6] ++，字符串abd计数为1

```



### **3. 代码如下**

```C++
int son[N][26], cnt[N], idx; 	
// 0号点既是根节点，又是空节点   son[N]的N代表所有字符串的长度
// son[N][26]存储树中每个节点的子节点 [26]表示只有26个字母
// cnt[]存储以每个节点结尾的单词数量	count [N]代表数据数量

// 插入一个字符串
void insert(string str) {
    int p = 0;	//从根节点开始遍历	类似指针，指向当前节点
    for (int i = 0; str[i] 或 i < str.size() ; i ++ )	//退出条件str[i]为空
    {
        int u = str[i] - 'a';	//把字母转化成数字
        if (!son[p][u]) son[p][u] = ++ idx;	//如果没有该子节点就创建一个
        p = son[p][u];			//走到p的子节点 使“p指针”指向下一个节点位置
    }
    cnt[p] ++ ;			//cnt相当于链表中的e[idx]
    					//结束时的标记，也是记录以此节点结束的字符串个数
}
// 查询字符串出现的次数
int query(string str) {
    int p = 0;
    for (int i = 0; str[i] 或 i < str.size(); i ++ )
    {
        int u = str[i] - 'a';
        if (!son[p][u]) return 0;//该节点不存在，即该字符串不存在
        p = son[p][u];
    }
    return cnt[p];				//返回字符串出现的次数
}

```



------



## 6. 并查集

涉及到集合合并

**操作：**近乎O(1)

1. 将两个集合合并
2. 询问两个元素是否在一个集合当中

**基本原理：**每个集合用一棵树来表示。树根的编号就是整个集合的编号。每个节点存储它的父节点，p[x]表示x的父节点

问题1：如何判断树根：if (p[x] == x)

问题2：如何求x的集合编号： while (p[x] != x) x = p[x];

问题3：如何合并两个集合：px 是 x 的集合编号，py 是 y 的集合编号。p[x] = y

**优化：**路径压缩，把每个点指向根节点

### **思路图解**

**初始化**

```
for(int i = 0; i < 8; i ++) p[i] = i;
```

将当前数据的父节点指向自己：

![并查集1](https://gitee.com/lxxdao/image/raw/master/algorithm/2.6并查集1.png)

**查找 + 路径压缩**

find的功能是用于查找祖先节点：

```c++
int find(int x) {
  	if (p[x] != x) p[x] = find(p[x]);
 	return p[x];
}
```

![并查集2](https://gitee.com/lxxdao/image/raw/master/algorithm/2.6并查集2.png)

当我们在查找1的父节点的过程中,路径压缩的实现

针对 x = 1：

```c++
find(1) p[1] = 2  p[1] = find(2)
find(2) p[2] = 3  p[2] = find(3)
find(3) p[3] = 4  p[3] = find(4)
find(4) p[4] = 4  将p[4]返回

退到上一层
find(3) p[3] = 4  p[3] = 4 将p[3]返回
退到上一层
find(2) p[2] = 3  p[2] = 4 将p[2]返回
退到上一层
find(1) p[1] = 2  p[1] = 4 将p[1]返回

至此，我们发现所有的1，2，3的父节点全部置为了4，实现路径压缩；同时也实现了1的父节点的返回
nice!!
```

**合并操作**

```c++
if(op[0] == ‘M’) p[find(a)] = find(b); //将a的祖先点的父节点置为b的祖先节点
假设有两个集合
```

![并查集3](https://gitee.com/lxxdao/image/raw/master/algorithm/2.6并查集3.png)

```c++
//合并1, 5
find(1) = 3 find(5) = 4
p[find(1)] = find(5) –> p[3] = 4
```

如下图所示

![并查集4](https://gitee.com/lxxdao/image/raw/master/algorithm/2.6并查集4.png)



### 1.  朴素并查集

```c++
int p[N]; //存储每个点的祖宗节点

//返回x的祖宗节点 重点
int find(int x) {
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

//初始化，假定节点编号是1~n
for (int i = 1; i <= n; i++) p[i] = i;

//合并a和b所在的两个集合：
p[find(a)] = find(b);
```



### 2. 维护size的并查集

```C++
//p[]存储每个点的祖宗节点, count[]只有祖宗节点的有意义，表示祖宗节点所在集合中的点的数量
int p[N], count[N];

// 返回x的祖宗节点
int find(int x) {
  	if (p[x] != x) p[x] = find(p[x]);
 	return p[x];
}

// 初始化，假定节点编号是1~n
for (int i = 1; i <= n; i ++ ){
	p[i] = i;
 	count[i] = 1;
}

// 合并a和b所在的两个集合：
count[find(b)] += count[find(a)];
p[find(a)] = find(b);

```



### 3. 维护到祖宗节点距离的并查集

```C++
int p[N], d[N];
//p[]存储每个点的祖宗节点, d[x]存储x到p[x]的距离
//d[i]的正确理解，应是第 i 个节点到其父节点距离
//d[x]存储的永远是x到p[x]的距离，其目的是为了求x到根节点的距离

// 返回x的祖宗节点
int find(int x) {
	if (p[x] != x)
	{
		int tmp = find(p[x]);
		d[x] += d[p[x]];
		p[x] = tmp;
	}
	return p[x];
}

// 初始化，假定节点编号是1~n
for (int i = 1; i <= n; i++) {
	p[i] = i;
	d[i] = 0;
}

// 合并a和b所在的两个集合：
p[find(a)] = find(b);
d[find(a)] = distance; // 根据具体问题，初始化find(a)的偏移量
```



------



## 7. 堆

### 1. 堆排序

**堆的概念：**

![堆1](https://gitee.com/lxxdao/image/raw/master/algorithm/2.7堆1.jpg)

**代码如下：**

```C++
// h[N]存储堆中的值, h[1]是堆顶，x的左儿子是2x, 右儿子是2x + 1 count是节点数量
int h[N], count;
void down(int u) {
    
    // 递归实现
    int t = u;
    // 让t等于左右节点中最小的节点
    if (2 * u <= count && h[t] > h[2 * u])	t = 2 * u;
    if (2 * u + 1 <= count && h[t] > h[2 * u + 1])	t = 2 * u + 1;
    if (u != t) {		 //u!=t 说明该节点不是最小的 交换值后递归处理
        swap(h[u], h[t]);
        down(t);
    }
    
    // 循环实现
    while (2 * u <= count) {
        // 让t等于左右节点中最小的节点
        int t = 2 * u;
        if (t + 1 <= count && h[t + 1] < h[t]) t++;
        if (h[u] < h[t]) break;	 // 如果该节点小于子节点 退出循环
        swap(h[u], h[t]);
        u = t;
    }
    
}

// O(n)建堆
for (int i = n / 2; i; i -- ) down(i);

// 返回最小值并删除
	cout << h[1] << ' ';
	h[1] = h[cnt];
	count --;
	down(1);
```

**i 为什么从 n/2 开始 down ？**

首先要明确要进行down操作时必须满足左儿子和右儿子已经是个堆。

开始创建堆的时候，元素是随机插入的，所以不能从根节点开始down，而是要找到满足下面三个性质的结点：

1. 左右儿子满足堆的性质。

2. 下标最大（因为要往上遍历）

3. 不是叶节点（叶节点一定满足堆的性质）



### 2. 模拟堆

![堆2](https://gitee.com/lxxdao/image/raw/master/algorithm/2.7堆2.jpg)

**题目中第k个插入，这里的k相当于链表中的idx，是节点的唯一标识**

**1. 关于idx**

用 `ph` 数组来表示 `ph[idx] = k(idx到下标)`, 那么节点值为 `h[ph[idx]]`, 儿子为 `ph[idx] * 2`和`ph[idx] * 2 + 1` , 这样值和儿子结点不就可以通过`idx`联系在一起

**2. 理解hp与ph数组**

ph数组主要用于帮助从idx映射到下标k，似乎有了ph数组就可以完成所有操作了，但为什么还要有一个hp数组呢？

原因就在于在 `swap` 操作中我们输入是堆数组的下标，无法知道每个堆数组的 `k` 下标对应 `idx` （第idx个插入），所以需要 `hp` 数组方便查找 `idx`

```c++
void heap_swap(int a, int b) {
    swap(ph[hp[a]], ph[hp[b]]); 
    swap(hp[a], hp[b]);
    swap(h[a], h[b]);
}
```

**如何手写一个堆？实现功能**

1. 插入一个数		  		 `heap[++count] = x; up(count);`

   ```c++
   if (op == "I") {
       cin >> x;
       count ++ ;
       idx ++ ; //记录第几次插入（设置新的idx）
       ph[idx] = count, hp[count] = idx; //每次插入都是在堆尾插入（设置ph与hp）
       h[ph[idx]] = x; //记录插入的值 
       up(ph[idx]);
   }
   ```

2. 求集合当中的最小值    `heap[1];`

3. 删除最小值    		        `heap[1] = heap[size]; size--; down(1);`

4. 删除任意一个元素 	   `heap[k] = heap[size]; size--; down(k); up(k);`

   ```c++
   //删除第idx个插入元素 (up，down，swap操作的都输入都是下标)
   if (op == "D") {
       cin >> idx;
       k = ph[idx]; //必须要保存当前被删除节点的下标
       heap_swap(k, count);//第idx个插入的元素移到了堆尾，此时ph[idx]指向堆尾 
       count --;  //删除堆尾
       up(k);//k是之前记录被删除的结点的下标
       down(k);
   }
   ```

5. 修改任意一个元素        `heap[k] = x; down(k); up(k);`



**代码如下：**

```c++
// h[N]存储堆中的值, h[1]是堆顶，x的左儿子是2x, 右儿子是2x + 1
// ph[k]存储第k个插入的点在堆中的位置
// hp[k]存储堆中下标是k的点是第几个插入的
int h[N], count;
int ph[N], hp[N];
int idx;

// 交换两个点，及其映射关系
void heap_swap(int a, int b) {
    swap(ph[hp[a]],ph[hp[b]]);
    swap(hp[a], hp[b]);
    swap(h[a], h[b]);
}

void down(int u) {
    int t = u;
    if (u * 2 <= count && h[u * 2] < h[t]) t = u * 2;
    if (u * 2 + 1 <= count && h[u * 2 + 1] < h[t]) t = u * 2 + 1;
    if (u != t) {
        heap_swap(u, t);
        down(t);
    }
}
void up(int u) {
    while (u / 2 && h[u] < h[u / 2]) {
        heap_swap(u, u / 2);
        u /= 2;
    }
}

// 堆的插入操作
if (op == "I") {
    cin >> x;
    count ++ ;
    idx ++ ; //记录第几次插入（设置新的idx）
    ph[idx] = count, hp[count] = idx; //每次插入都是在堆尾插入（设置ph与hp）
    h[ph[idx]] = x; //记录插入的值 
    up(ph[idx]);
}

// 堆的删除第k个插入元素
if (op == "D") {
    cin >> k;
    k = ph[k]; //必须要保存当前被删除结点的下标
    heap_swap(k, count);//第idx个插入的元素移到了堆尾，此时ph[idx]指向堆尾 
    count --;  //删除堆尾
    up(k);//k是之前记录被删除的结点的下标
    down(k);
}

// 堆的修改第idx个插入元素
if (op == "C") {
     cin >> k >> x;
     k = ph[k];
     h[k] = x;
     down(k), up(k);
}
```



------



## 8. 哈希表

### 1. 哈希表

哈希表又称散列表，一般由哈希函数与链表结构共同实现。与离散化思想类似，当我们要对若干复杂信息进行统计时，可以**用哈希函数把这些复杂信息映射到一个容易维护的值域内**。因为值域变简单、范围变小，有可能造成两个不同的原始信息被Hash函数映射为相同的值，所以需要**处理这种冲突**情况。

**拉链法：**

![哈希表1](https://gitee.com/lxxdao/image/raw/master/algorithm/2.8哈希表1.jpg)



```c++
const int N = 1e5 + 3;  // 取大于数据范围的第一个质数，取质数冲突的概率最小
int h[N];				// 开一个槽 h  N个头节点
int e[n], ne[N], idx;	// 邻接表	链表被拆分了多个链

#include<cstring>
memset(h, -1, sizeof h);  //将槽先清空 空指针一般用 -1 来表示

// 向哈希表中插入一个数
void insert(int x) {
    // 复数取模也是负的 所以 加N 再 %N 就一定是一个正数
    int k = (x % N + N) % N;// k是哈希值
    e[idx] = x;				// 头插法
    ne[idx] = h[k];			//链表被拆分了多个链,每一个h[k]都是一个链表头
    h[k] = idx;				//h[k]存的是第一个点的下标 头指针
    idx++;					//将
}

// 在哈希表中查询某个数是否存在
bool find(int x) {
    //用上面同样的 Hash函数 讲x映射到 从 0-1e5 之间的数
    int k = (x % N + N) % N;
    for (int i = h[k]; i != -1; i = ne[i]) {
        if (e[i] == x) {
            return true;
        }
    }
    return false;
}
```

**开放寻址法：**

![哈希表2](https://gitee.com/lxxdao/image/raw/master/algorithm/2.8哈希表2.jpg)

```c++
//开放寻址法一般开 数据范围的 2~3倍, 这样大概率就没有冲突了
const int N = 2e5 + 3;        //大于数据范围的第一个质数
const int null = 0x3f3f3f3f;  //规定空指针为 null 0x3f3f3f3f
int h[N];
memset(h, 0x3f, sizeof h); //将槽先清空 空指针一般用 -1 来表示

int find(int x) {		//寻找下一个坑位
    int t = (x % N + N) % N;
    while (h[t] != null && h[t] != x) {
        t++;
        if (t == N) {
            t = 0;
        }
    }
    return t;  //如果这个位置是空的, 则返回的是他应该存储的位置
}

int k = find(x);
h[k] = x; //插入
//查询
if (h[k] != null) cout << "Yes" << endl; 
else cout << "No" << endl;
```



### 2. 字符串哈希

时间复杂度：O(n)+O(m)

全称字符串前缀哈希法，把字符串变成一个p进制数字（哈希值），实现不同的字符串映射到不同的数字。
对形如 ：
$$
X1X2X3⋯Xn−1XnX1X2X3⋯Xn−1Xn
$$
 的字符串,采用字符的 `ascii ` 码乘上 `P` 的次方来计算哈希值。

映射公式 ：
$$
(X1×Pn−1+X2×Pn−2+⋯+Xn−1×P1+Xn×P0)modQ
$$
**注意点：**

1. 任意字符不可以映射成0，否则会出现不同的字符串都映射成0的情况，比如A,AA,AAA皆为0
2. 冲突问题：通过巧妙设置P (131 或 13331) , Q (264)(264)的值，一般可以理解为不产生冲突。

问题是比较不同区间的子串是否相同，就转化为对应的哈希值是否相同。
求一个字符串的哈希值就相当于求前缀和，求一个字符串的子串哈希值就相当于求部分和。

前缀和公式
$$
h[i+1]=h[i]×P+s[i]h[i+1]=h[i]×P+s[i] i∈[0,n−1]i∈[0,n−1]
$$
h为前缀和数组，s为字符串数组
区间和公式 
$$
h[l,r]=h[r]−h[l−1]×Pr−l+1h[l,r]=h[r]−h[l−1]×Pr−l+1
$$
区间和公式的理解:  `ABCDE` 与 `ABC` 的前三个字符值是一样，只差两位，
乘上 `P²` 把 `ABC` 变为 `ABC00`，再用 `ABCDE - ABC00` 得到 `DE` 的哈希值。

**代码如下：**

```c++
// h[i]前i个字符的hash值	p[i]存储P的i次方的值
// 字符串变成一个p进制数字，体现了字符+顺序，需要确保不同的字符串对应不同的数字
// P = 131 或  13331 Q=2^64，在99%的情况下不会出现冲突
// 使用场景： 两个字符串的子串是否相同
typedef unsigned long long ULL;
const int N = 1e5+5,P = 131;//131 13331 
ULL h[N],p[N];

// 计算子串 str[l ~ r] 的哈希值  字符串从1开始
ULL query(int l,int r){
    return h[r] - h[l - 1] * p[r - l + 1];
}
string str;
str = ' ' + str; //前缀和一般从下标1开始 在字符串前加空格

p[0] = 1;		// 初始化
for (int i = 1; i <= n; i++) {
	p[i] = p[i - 1] * P;		  //存储p的i次方的值
 	h[i] = h[i - 1] * P + str[i]; //前缀和求整个字符串的哈希值
}
```



## 9. STL

系统为某一程序分配空间时，所需时间与空间大小无关，只与申请次数有关

### 1. vector

变长数组，倍增的思想

```
size()  返回元素个数
empty()  返回是否为空
clear()  清空
front()/back()
push_back()/pop_back()
begin()/end()
[]
支持比较运算，按字典序
max_element()/min_element() 	返回最大/最小元素的迭代器
*max_element()/*min_element()	返回最大/最小元素的值
```



### 2. pair<int, int>

```
first, 第一个元素
second, 第二个元素
支持比较运算，以first为第一关键字，以second为第二关键字（字典序）
```



### 3. string

字符串

```
size()/length()  返回字符串长度
empty()
clear()
substr(起始下标，(子串长度))  返回子串
c_str()  返回字符串所在字符数组的起始地址
```



### 4. queue

队列

```
size()
empty()
push()  	向队尾插入一个元素
emplace()	插入一个元素，更节省内存更快
front()  	返回队头元素
back()  	返回队尾元素
pop()  		弹出队头元素
```



### 5. priority_queue

优先队列，默认是大根堆

```
size()
empty()
push()  	插入一个元素
emplace()	插入一个元素，更节省内存更快
top()  		返回堆顶元素
pop()  		弹出堆顶元素
定义成小根堆的方式：priority_queue<int, vector<int>, greater<int>> q;
				或者插入负数
```



### 6. stack

栈

```
size()
empty()
push()  向栈顶插入一个元素
top()  返回栈顶元素
pop()  弹出栈顶元素
```



### 7. deque

双端队列		//效率最低

```
size()
empty()
clear()
front()/back()
push_back()/pop_back()
push_front()/pop_front()
begin()/end()
[]
```



### 8. set, map, multiset, multimap

基于平衡二叉树（红黑树），动态维护有序序列

```
size()
empty()
clear()
begin()/end()
++, -- 返回前驱和后继，时间复杂度 O(logn)

set/multiset：
	insert()  插入一个数
	find()  查找一个数	不存在返回end的迭代器
	count()  返回某一个数的个数
	erase()
		(1) 输入是一个数x，删除所有x   O(k + logn)
		(2) 输入一个迭代器，删除这个迭代器
	lower_bound()/upper_bound()
		lower_bound(x)  返回第一个大于等于x的迭代器
      	upper_bound(x)  返回第一个大于x的迭代器
      	
map/multimap：
 	insert()  插入的数是一个pair
  	erase()  输入的参数是pair或者迭代器
   	find()
  	[]  注意multimap不支持此操作。 时间复杂度是 O(logn)
	lower_bound()/upper_bound()
```



### 9. unordered_set, unordered_map, unordered_multiset, unordered_multimap

哈希表

```
和上面类似，增删改查的时间复杂度是 O(1)
不支持 lower_bound()/upper_bound()， 迭代器的++，--
```



### 10. bitset

圧位

```
bitset<10000> s;
~, &, |, ^
>>, <<
==, !=
[]

count()  返回有多少个1
any()  判断是否至少有一个1
none()  判断是否全为0
set()  把所有位置成1
set(k, v)  将第k位变成v
reset()  把所有位变成0
flip()  等价于~
flip(k) 把第k位取反
```



### 11. stirng

```
find()
substr()	
stot(string)	string转成int		 int
isdigit(char)	判断字符是否是十进制数 bool
reverse(s,begin(), s.end()) 
```



------



# 第三章 搜索与图论



## 1. DFS与BFS

DFS（Depth-First-Search）：深度优先搜。DFS用递归的形式，用到了栈结构，先进后出。

**数据结构：stack，空间O(h)，不具有最短性的概念**

BFS（Breath-First-Search）：广度优先搜索。BFS选取状态，用队列的形式，先进先出。

**数据结构：queue，空间O(2^h)，具有“最短路”的概念**        

只有边权重都是1的时候才能用BFS求最短路



### **1. DFS解决全排列**

算法：

- 用 path 数组保存排列，当排列的长度为 n 时，是一种方案，输出。
- 用 state 数组表示数字是否用过。当 state[i] 为 1 时：i 已经被用过，state[i] 为 0 时，i 没有被用过。
- DFS(i) 表示的含义是：在 path[i] 处填写数字，然后递归的在下一个位置填写数字。
- 回溯：第 i 个位置填写某个数字的所有情况都遍历后， 第 i 个位置填写下一个数字。

```c++
int n;
int path[N];   		// 从0到n-1共n个位置 存放一个排列
bool state[N];     	// 存放每个数字的使用状态 true表示使用了 false表示没使用过

void dfs(int u) {
    
    if (u == n) {  // 一个排列填充完成
        for (int i = 0; i < n; i ++) cout << path[i] <<' ';  
        cout << endl;  
        return;
    }

    for (int i = 1; i <= n; i ++) { // 枚举找到没有被用过的数
        if (!state[i]) {
            path[u] = i;        // 把 i 填入数字排列的位置上
            state[i] = true;    // 表示该数字用过了 不能再用
            dfs(u + 1);         // 这个位置的数填好 递归到右面一个位置
            state[ia] = false;  // 恢复现场 该数字后续可用
        }
    }
    // for 循环全部结束了 dfs(u)才全部完成 回溯
}
dfs(0);	// 在path[0]处开始填数
```



### 2. DFS解决n皇后问题

n 皇后问题是指将 n 个皇后放在 n∗n 的国际象棋棋盘上，使得皇后不能相互攻击到，即任意两个皇后都不能处于同一行、同一列或同一斜线上。

**算法1**

（DFS按行枚举） 时间复杂度O(n!)

代码分析

> 对角线 `dg[u+i]dg[u+i]`，反对角线 `udg[n−u+i]udg[n−u+i]` 中的下标 `u+i` 和 `n−u+i` 表示的是截距

```

```

**算法2**

（DFS按每个元素枚举）时间复杂度O(2^(n^2))
时间复杂度分析：每个位置都有两种情况，总共有 n^2 个位置

```c++
#include <iostream>
using namespace std;
const int N = 20;   // 对角线数为2*n-1，数组范围应该大于它
int n;				// 棋盘的长宽
char g[N][N];		// g[N][N]用来存路径
bool row[N], col[N], dg[N], udg[N]; //存储 行 列 对角 反对角
// bool数组用来判断搜索的下一个位置是否可行

// x,y表示坐标 s表示已经放上去的皇后个数
void dfs(int x, int y, int s) {	
    if (y == n) y = 0, x++;	// 处理超出边界的情况
    if (x == n) {	// x==n说明已经枚举完n^2个位置了
        if (s == n) { // s==n说明成功放上去了n个皇后
            for (int i = 0; i < n; i ++ ) cout << g[i] <<endl;
            cout << endl;
        }
        return;
    }
    
    // 分支1：放皇后 		udg[x - y + i]，+n是为了保证下标非负
    if (!row[x] && !col[y] && !dg[x + y] && !udg[x - y + n]) {
        g[x][y] = 'Q';
        row[x] = col[y] = dg[x + y] = udg[x - y + n] = true;
        dfs(x, y + 1, s + 1);
        // 恢复现场 这步很关键
        row[x] = col[y] = dg[x + y] = udg[x - y + n] = false;
        g[x][y] = '.';
    }    

    // 分支2：不放皇后
    dfs(x, y + 1, s);
}

int main() {
    cin >> n;
    for (int i = 0; i < n; i ++ )	//路劲初始化为.
        for (int j = 0; j < n; j ++ )
            g[i][j] = '.';
    dfs(0, 0, 0);
    return 0;
}
```



### 3.  BFS走迷宫

二维数组坐标：

```
	 0	  1	   2	y		
0	0,0  0,1  0,2		

1	1,0  1,1  1,2

2	2,0  2,1  2,2

x
```

**数组模拟队列：**

```c++
#include<iostream>
#include<algorithm>
#include<cstring>
using namespace std;

typedef pair<int, int> PII;

const int N = 110;

int n, m;
int g[N][N];	// 存放图
int d[N][N];	// 存每一个点到起点的距离
PII q[N * N];	// 模拟队列

int bfs() {
    int hh = 0, tt = 0;	
    q[0] ={0, 0};	// 起点在队列内	故队尾tt = 0;
    memset(d, -1, sizeof d);// 距离初始化为- 1表示没有走过
    d[0][0] = 0;	// 表示起点走过了
    
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};//x 方向的向量和 y 方向的向量组成的上、右、下、左
    
    while (hh <= tt) {		// 队列不空
        auto t = q[hh++];	// 取队头元素
        for(int i = 0; i < 4; i ++ ) {	// 枚举4个方向
            int x = t.first + dx[i], y = t.second + dy[i]; // x表示沿着此方向走会走到哪个点
            if(x >= 0 && x < n && y >= 0 && y < m && g[x][y] == 0 && d[x][y] == -1)	{	// 在边界内 并且是空地可以走 且之前没有走过
                d[x][y] = d[t.first][t.second] + 1;//到起点的距离
                q[ ++ tt ] = {x, y};//新坐标入队
            }
        }        
    }
    return d[n - 1][m - 1];	//输出右下角点距起点的距离即可
}


int main() {
    cin >> n >> m;
    for(int i = 0; i < n ; i ++) 
        for(int j = 0; j < m; j ++)
            cin >> g[i][j];
            
    cout << bfs() << endl;
}
```

**STL版本：**

```c++
#include<queue>
using namespace std;

typedef pair<int, int> PII;

const int N = 110;

int n, m;
int g[N][N];	// 存放图
int d[N][N];	// 存每一个点到起点的距离


int bfs() {
    queue<PII> q;
    q.push({0, 0});	// 起点在队列内	故队尾tt = 0;
    memset(d, -1, sizeof d);// 距离初始化为- 1表示没有走过
    d[0][0] = 0;	// 表示起点走过了
    
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};//x 方向的向量和 y 方向的向量组成的上、右、下、左
    
    while (q.size()) {		// 队列不空
        auto t = q.front();	// 取队头元素
        q.pop();
        for(int i = 0; i < 4; i ++ ) {	// 枚举4个方向
            int x = t.first + dx[i], y = t.second + dy[i]; // x表示沿着此方向走会走到哪个点
            if(x >= 0 && x < n && y >= 0 && y < m && g[x][y] == 0 && d[x][y] == -1)	{	// 在边界内 并且是空地可以走 且之前没有走过
                d[x][y] = d[t.first][t.second] + 1;//到起点的距离
                q.push({x, y});//新坐标入队
            }
        }        
    }
    return d[n - 1][m - 1];	//输出右下角点距起点的距离即可
}
```





###  4. BFS八数码

**矩阵与字符串的转换方式:**

```
	 0	  1	   2	y  		对应下标：		字符串：
0	0,0  0,1  0,2	   		0  1  2		  012345678

1	1,0  1,1  1,2	  	 	3  4  5		转换方式：下标 = x * 3 +y

2	2,0  2,1  2,2	   		6  7  8		x = 下标 / 3 , y = 下标 % 3 

x
```

![八数码](https://gitee.com/lxxdao/image/raw/master/algorithm/3.1八数码.JPG)

**代码：**

```c++
#include <iostream>
#include <unordered_map>
#include <algorithm>
#include <queue>
#include <string>
using namespace std;

int bfs(string str) {
    string end = "12345678x";           // 定义目标状态
    queue<string>q;                     // 定义队列存储状态
    unordered_map<string, int> d;       // 定义哈希map存储各个状态的距离
    q.push(str);
    d[str] = 0;
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};  //移动四个方位的坐标 上、右、下、左
    
    while (q.size()) {
        auto t = q.front();
        q.pop();
        int distance = d[t];            // 记录当前状态的距离
        if (t == end) return distance;  // 如果是最终状态则返回距离
        
        int k = t.find('x');            // 查询x在字符串中的下标
        int x = k / 3, y = k % 3;       // 转换为在矩阵中的坐标
        for (int i = 0; i < 4; i ++) {
            int a = x + dx[i], b = y + dy[i];   // 求转移后x的坐标
            if (a >=0 && a < 3 && b >= 0 && b < 3) {
                swap(t[k], t[a * 3 + b]);       // count返回key值出现的次数 哈希map只有0或1
                if (!d.count(t)) {              // 如果当前状态是第一次遍历 
                    d[t] = distance + 1;        // 记录距离
                    q.push(t);                  // 入队
                }
                swap(t[k], t[a * 3 + b]);       // 还原状态，为下一种转换情况(上下左右)做准备
            }
        }
    }
    return -1;
}


int main() {
    string str;
    for (int i = 0; i < 9; i++) {
        char a;
        cin >> a;
        str += a;
    }
    
    cout << bfs(str) << endl;
    return 0;
}

```



------



## 2. 树与图的遍历：拓扑排序

### 1. 树与图的存储

树是一种特殊的图，与图的存储方式相同。

图分为：有向图 a → b 和 无向图 a — b 

对于无向图中的边 a — b ，可以看成有向图，存储两条有向边a → b + b → a

因此我们可以只考虑有向图的存储。



**稠密图用邻接矩阵，稀疏图用邻接表**

一般边小于nlogn 就称为稀疏图 n是点的数量 

(1) 邻接矩阵：`g[a][b]` 存储边 a → b 

(2) 邻接表：各个点有一个单链表，存储该点能走到的点

```c++
// 对于每个点k，开一个单链表，存储k所有可以走到的点。h[k]存储这个单链表的头结点
int h[N], e[N], ne[N], idx;

// 添加一条边a->b
void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++ ;
}

// 遍历
for (int i = h[node]; i != -1; i = ne[i]) {
     int j = e[i];	
}

// 初始化
idx = 0;
memset(h, -1, sizeof h);
```

(3) STL实现邻接表

```c++
vector<vector<pair<int, int>>> gra;
cin >> n >> m;		// n点数量 m边数量
gra.resize(n + 1); 	// 初始化长度
    
for (int i = 0; i < m; i++) {
        int a, b, c;			// 点a到点b的有向边，边长为c
        cin >> a >> b >> c;		
        gra[a].push_back({ b,c });
}

for (int i = 0; i < gra[node].size(); i++) {	// 遍历邻接表
   	int newnode = gra[node][i].first;
    int len = gra[node][i].second;
}
```



### 2. 树与图的遍历

**时间复杂度 O(n+m), n 表示点数，m 表示边数**

**(1) 深度优先遍历**

```c++
vector<int> h(N,-1);	// 邻接表存储树，有n个节点，所以需要n个队列头节点
int e[M], ne[M], idx;	// 存储元素、next指针、下标值
bool st[N]; 			//记录节点是否被访问过，访问过则标记为true
int dfs(int u) {
    st[u] = true; // st[u] 表示点u已经被遍历过
    for (int i = h[u]; i != -1; i = ne[i]) {
        int j = e[i];
        if (!st[j]) dfs(j);
    }
}
```

**(2) 宽度优先遍历**

```c++
queue<int> q;	
st[1] = true; // 表示1号点已经被遍历过
q.push(1);

//数组模拟	
//int q[N];   
//int hh = 0, tt = 0;
//q[0] = 1;

while (q.size()) {
//while(hh <= tt)
    int t = q.front();
    q.pop();
	//int t = q[hh++];
    
    for (int i = h[t]; i != -1; i = ne[i]) {
        int j = e[i];
        if (!st[j]) {
            st[j] = true; // 表示点j已经被遍历过
            q.push(j);
            //q[++tt] = j;
        }
    }
}
```



### 3. 拓扑排序

**时间复杂度 O(n+m)，n 表示点数，m 表示边数**

**思路：**将入度为0的点入列，再删去该点指向的点的所有边

**拓扑图：**有向无环图：

**入度：**该点有多少条边进来

**出度：**该点有多少条边出去

**一个无环图一定至少存在一个入度为0的点**

**拓扑排序**：

- 一个有向图，如果图中有入度为 0 的点，就把这个点删掉，同时也删掉这个点所连的边。

- 一直进行上面出处理，如果所有点都能被删掉，则这个图可以进行拓扑排序。



```c++
int d[N];	// 存的是入度数
bool topsort() {
    int hh = 0, tt = -1;

    // d[i] 存储点i的入度
    for (int i = 1; i <= n; i ++ )
        if (!d[i])
            q[ ++ tt] = i;

    while (hh <= tt) {
        int t = q[hh ++ ];

        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            d[j]--;
            if (d[j] == 0)
                q[ ++ tt] = j;
        }
    }

    // 如果所有点都入队了，说明存在拓扑序列；否则不存在拓扑序列。
    return tt == n - 1;
}

//插入数据时候要更新入度
	add(a, b);
	d[b]++;

```



------



## 3.  最短路

**考察侧重点：建图。**如何把原问题抽象成一个最短路问题，如何定义点和边，用最短路问题解决。

### 最短路分类

**两大类：**

单源最短路：	一个点到其他所有点的最短距离

多源汇最短路：多个询问从其中一个点到另外一个点的最短距离

```
			 |				  	稠密图
			 |				  | 朴素Dijkstra算法		O(n^2)
			 |	所有边权都是正数
			 |				  |	堆优化版的Dijkstra算法	  O(mlogn)
			 |				  	稀疏图
		单源 
			 |					
			 |				  | Bellman-Ford		   O(nm)
			 |	存在负权边
			 |				  | SPFA			   一般O(m),最坏O(nm)
			 |

最短路

			 
		多源汇    Floyd算法		O(n^3)
			 

```

![最短路分类](https://gitee.com/lxxdao/image/raw/master/algorithm/3.3最短路1.png)



**Dijkstra-朴素 O(n^2)**

1. 初始化距离数组, dist[1] = 0, dist[i] = inf;

2. for n次循环 每次循环确定一个min加入S集合中，n次之后就得出所有的最短距离

3. 将不在S中dist_min的点->t

4. t->S加入最短路集合

5. 用t更新到其他点的距离

   

**Dijkstra-堆优化 O(mlogm)**

1. 利用邻接表，优先队列

2. 在priority_queue[HTML_REMOVED], greater[HTML_REMOVED] > heap;中将返回堆顶

3. 利用堆顶来更新其他点，并加入堆中类似宽搜

   

**Bellman-ford O(nm)**

1. 注意连锁想象需要备份, struct Edge{inta,b,c} Edge[M];
2. 初始化dist, 松弛dist[x.b] = min(dist[x.b], backup[x.a]+x.w);
3. 松弛k次，每次访问m条边
   

**SPFA O(n)~O(nm)**

1. 利用队列优化仅加入修改过的地方for k次
2. for 所有边利用宽搜模型去优化bellman_ford算法
3. 更新队列中当前点的所有出边
   

**Floyd O(n^3)**

1. 初始化d
2. k, i, j 去更新d



### 1. 朴素Dijkstra算法

​	**适合稠密图 时间复杂度O(n^2)**

**算法思路：**

1. 初始化 起点 dist[1] = 0, 其他点 dist[i] = 0x3f    S:当前已确定最短距离的点

2. for (int i = 1; i <= n; i++) {

   1. 寻找不在S中的距离最近的点 t
   1. 将 t 加到 s 中
   1. 用 t 更新其他点到**起点**的距离

   }



```c++
int n;      			 // 点的数量
int g[N][N];			 // 稠密阵用邻接矩阵存储每条边
memset(g,0x3f,sizeof(g)) // 初始化图 每个点初始为无限大
while(m--) {			 // m边的数量
   	int x,y,z;
   	cin>>x>>y>>z;
    g[x][y]=min(g[x][y],z);     //如果发生重边的情况则保留最短的一条边
}
    
int dist[N];		// 存储每个点到第一个点的距离
bool st[N];			// 存储每个点的最短路是否已经确定

int dijkstra() {
	memset(dist, 0x3f, sizeof(dist));	// 初始化距离 0x3f3f3f3f代表无限大
	dist[1] = 0;	// 第一个点到自身的距离为0
	
    // 有n个点所以要进行n次迭代 n次迭代就能确定最小值(记住即可) 
	for (int i = 0; i < n; i++) {		
		int t = -1;						// 当前访问的点的t需要更新
		// 是为了方便选择第一个还未确定最短路的点，避免无距离最小的点轮空
        
        // 遍历 dist 数组 在还未确定最短路的点中，寻找距离最小的点
        for (int j = 1; j <= n; j++) {	// 这里的j代表的是从1号点开始
            if (!st[j] && (t == -1 || dist[t] > dist[j]))
                t = j;
        }
            st[t] = true;				// 加入当前距离最小的点
        
        // 用t更新其他点的距离
        for (int j = 1; j <= n; j++) {	
           	dist[j] = min(dist[j], dist[t] + g[t][j]);
        }
	}
    if (dist[n] == 0x3f3f3f3f) return -1;	// 路径不存在
    return dist[n];
}
/*
寻找路径最短的点：O(n^2) 

加入集合S：O(n)

更新距离：O(m)

时间复杂是 O(n^2+m)，n 表示点数，m 表示边数

所以总的时间复杂度为O(n^2)
*/
```



### 2. 堆优化版Dijkstra算法

**适合稀疏图 时间复杂度O(mlogn)**

**算法思路：**

堆优化版的 Dijkstra 是对朴素版 Dijkstra 进行了优化，

在朴素版 Dijkstra 中时间复杂度最高的寻找距离最短的点O(n^2)可以使用最小堆优化。

1. 一号点的距离初始化为零，其他点初始化成无穷大。
2. 将一号点放入堆中。
3. 不断循环，直到堆空。每一次循环中执行的操作为：
   弹出堆顶（与朴素版 Dijkstra 找到 S 外距离最短的点相同，并标记该点的最短路径已经确定）。
   用该点更新临界点的距离，若更新成功就加入到堆中。





```c++
typedef pair<int, int> PII;
vector<int> h(N, -1);		  // w[N]用来存权重
int w[N], e[N], ne[N], idx;   // 稀疏表用邻接表存储所有边 
int dist[N];		// 存储每个点到第一个点的距离
bool st[N];			// 存储每个点的最短路是否已经确定

void add(int a, int b, int c) {
    e[idx] = b;
    w[idx] = c;
    ne[idx] = h[a];
    h[a] = idx++;
}

int dijkstra() {
	memset(dist, 0x3f, sizeof(dist));	// 初始化距离 0x3f3f3f3f代表无限大
	dist[1] = 0;	// 第一个点到自身的距离为0
	
    priority_queue<PII, vector<PII>, greater<PII>> heap; // 定义一个小根堆
    // 这里heap中为什么要存pair呢，首先小根堆是根据距离来排的，所以有一个变量要是距离
    // 其次在从堆中拿出来的时候要知道知道这个点是哪个点，以便更新邻接点，所以第二个变量要存点。
    
    heap.push({0, 1});      // first存储距离，second存储节点编号，根据距离排序
        					// pair排序时是先根据first，再根据second
    
	while (heap.size()) {
        auto t = heap.top();	// 取不在集合S中距离最短的点
        heap.pop();
        
        int node = t.second, distance = t.first;
        if (st[node]) continue;
        st[node] = true;
        
        for (int i = h[node]; i != -1; i = ne[i]) {
            int j = e[i];		// i只是个下标，e中在存的是i这个下标对应的点
            if (dist[j] > distance + w[i]) {
                dist[j] = distance + w[i];
                heap.push({dist[j], j});
            }
        }    
        
    }
    
    if (dist[n] == 0x3f3f3f3f) return -1;	// 路径不存在
    return dist[n];
}
/*
寻找路径最短的点：O(n)
加入集合S：O(n)
更新距离：O(mlogn)
*/
```



### 3. Bellman-Ford算法

**适合存在负环图 时间复杂度：O(nm), n 表示点数，m 表示边数**

Bellman - ford 算法是求含负权图的单源最短路径的一种算法，效率较低，代码难度较小。其原理为连续进行松弛，在每次松弛时把每条边都更新一下，若在 n-1 次松弛后还能更新，则说明图中有负环，因此无法得出结果，否则就完成。

(通俗的来讲就是：假设 1 号点到 n 号点是可达的，每一个点同时向指向的方向出发，更新相邻的点的最短距离，通过循环 n-1 次操作，若图中不存在负环，则 1 号点一定会到达 n 号点，若图中存在负环，则在 n-1 次松弛后一定还会更新)

**算法思路：**

for n 次							

​	for 所有边 a, b, w  a → b(w权重)	(松弛操作)

​			dist[b] = min(dist[b], dist[a] + w)  	// 松弛操作

dist[b] ≤ dist[a] + w	// 三角不等式

**限制经过边的次数，有负环也没关系，可以算出最短距离**

**代码：**

```c++
int n, m;       	// n表示点数，m表示边数
int dist[N];    	// dist[x]存储1到x的最短路距离

struct Edge {    	// 边，a表示出点，b表示入点，w表示边的权重
    int a, b, w;
}edges[M];

// 求1到n的最短路距离，如果无法从1走到n，则返回-1。
int bellman_ford() {
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;

    // 如果第n次迭代仍然会松弛三角不等式，就说明存在一条长度是n+1的最短路径，由抽屉原理，路径中至少存在两个相同的点，说明图中存在负权回路。
    for (int i = 0; i < n; i ++ ) {
        for (int j = 0; j < m; j ++ ) {	//遍历所有边
            int a = edges[j].a, b = edges[j].b, w = edges[j].w;
            if (dist[b] > dist[a] + w)
                dist[b] = dist[a] + w;
        }
    }
/*
    // 如果题目限定k条边，则需要backup数组  backup表示每次进入第2重循环的dist数组的备份
    for (int i = 0; i < n; i ++ ) {		// k次循环
		memcpy(backup,dist,sizeof dist);
        for (int j = 0; j < m; j ++ ) {	// 遍历所有边
            int a = edges[j].a, b = edges[j].b, w = edges[j].w;
            dist[b] = min(dist[b], backup[a] + w);
        }
    }
*/   
    
    if (dist[n] > 0x3f3f3f3f / 2) return -1;
    // ÷2的原因是 如果n-1号节点与n号节点为距离为 -2
    // 若n-1号节点距离起点INF 则n号节点为INF - 2 
    // 虽然INF-2<INF 但并不存在最短路，(在边数限制在k条的条件下)
    return dist[n];
}


```



### 4. SPFA算法

**时间复杂度：平均情况下O(m)，最坏情况下O(nm), n 表示点数，m 表示边数**

SPFA 算法是 Bellman-Ford算法 的队列优化算法的别称，通常用于求含负权边的单源最短路径，以及判负权环。SPFA一般情况复杂度是O(m) 最坏情况下复杂度和朴素 Bellman-Ford 相同，为O(nm)。

SPFA 也能解决权值为正的图的最短距离问题，且一般情况下比 Dijkstra算法还好

**算法思路：**

SPFA算法对Bellman-Ford算法第二部中所有边进行松弛操作进行了优化，原因是在Bellman-Ford算法中，即使该点的最短距离尚未更新过，但还是需要用尚未更新过的值去更新其他点，由此可知，该操作是不必要的，我们只需要找到更新过的值去更新其他点即可。

queue <– 1
while queue 不为空
		(1) t <– 队头
 			queue.pop()
 		(2)用 t 更新所有出边 t –> b，权值为w
 			queue <– b (若该点被更新过，则拿该点更新其他点)

**代码：**

```c++
int n, m;      	// 总点数 总边数
vector<int> h(N, -1);
int w[N], e[N], ne[N], idx;       // 邻接表存储所有边
int dist[N];    // 存储每个点到1号点的最短距离
bool st[N];     // 存储每个点是否在队列中 用于判重，队列中有重复的点没有意义

// 求1号点到n号点的最短路距离，如果从1号点无法走到n号点则返回-1
int spfa() {
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;

    queue<int> q;
    q.push(1);		// 1号节点加入队列
    st[1] = true;

    while (q.size()){
        auto t = q.front();
        q.pop();

        st[t] = false;

        for (int i = h[t]; i != -1; i = ne[i]) { // 遍历所有此点可以到达的点
            int j = e[i];
            if (dist[j] > dist[t] + w[i]) {	// 松弛操作
                dist[j] = dist[t] + w[i];
                if (!st[j]) {   	// 如果队列中已存在j，则不需要将j重复插入
                    q.push(j);		// 插入点j
                    st[j] = true;	// 标记点j在队列内
                }
            }
        }
    }

    if (dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}

```



用SPFA算法判断负环问题：

求负环的常用方法，基于SPFA，一般都用方法 2：

- 方法 1：统计每个点入队的次数，如果某个点入队n次，则说明存在负环
- 方法 2：统计当前每个点的最短路中所包含的边数，如果某点的最短路所包含的边数大于等于n，则也说明存在环

每次做一 `spfa()` 一定是正确的，但时间复杂度较高，可能会超时。初始时将所有点插入队列中可以按如下方式理解：
在原图的基础上新建一个虚拟源点，从该点向其他所有点连一条权值为0的有向边。那么原图有负环等价于新图有负环。此时在新图上做 `spfa` ，将虚拟源点加入队列中。然后进行 `spfa` 的第一次迭代，这时会将所有点的距离更新并将所有点插入队列中。执行到这一步，就等价于视频中的做法了。那么视频中的做法可以找到负环，等价于这次 `spfa` 可以找到负环，等价于新图有负环，等价于原图有负环。得证。

1. dist[x] 记录虚拟源点到x的最短距离
2. `cnt[x]` 记录当前x点到虚拟源点最短路的边数，初始每个点到虚拟源点的距离为0，只要他能再走n步，即 `cnt[x] >= n`，则表示该图中一定存在负环，由于从虚拟源点到x至少经过n条边时，则说明图中至少有n + 1个点，表示一定有点是重复使用
3. 若 `dist[j] > dist[t] + w[i]`,则表示从t点走到j点能够让权值变少，因此进行对该点j进行更新，并且对应 `cnt[j] = cnt[t] + 1`,往前走一步

```c++
int n;      // 总点数
vector<int> h(N, -1);
int w[N], e[N], ne[N], idx;       // 邻接表存储所有边
int dist[N], cnt[N];        // dist[x]存储1号点到x的最短距离，cnt[x]存储1到x的最短路中经过的点数
bool st[N];     // 存储每个点是否在队列中

// 如果存在负环，则返回true，否则返回false。
bool spfa() {
    // 不需要初始化dist数组
    // 原理：如果某条最短路径上有n个点（除了自己），那么加上自己之后一共有n+1个点，由抽屉原理一定有两个点相同，所以存在环。
    // 因为有负环的存在所以最终某些点的dist是数组的状态肯定是无穷小的，而无论dist的初始值为多少肯定都会往无穷小的方向区无限次的更新

    queue<int> q;	
    for (int i = 1; i <= n; i ++ ) { //1号点可能到不了有负环的点，故所有点都加入队列
        q.push(i);
        st[i] = true;
    }

    while (q.size()) {
        auto t = q.front();
        q.pop();

        st[t] = false;

        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            if (dist[j] > dist[t] + w[i]) {
                dist[j] = dist[t] + w[i];
                cnt[j] = cnt[t] + 1;
                if (cnt[j] >= n) return true;   // 如果从1号点到x的最短路中包含至少n个点（不包括自己），则说明存在环
                if (!st[j]) {
                    q.push(j);
                    st[j] = true;
                }
            }
        }
    }

    return false;
}

```



### 5. Floyd算法

**时间复杂度是 O(n^3) , n 表示点数**

基于动态规划

- `D[k, i, j]` 表示从 i 走到 j 的路径上除 i 和 j 点外只经过 1 到 k 的点的所有路径的最短距离。那么`D[k, i, j] = min(D[k - 1, i, j), D[k - 1, i, k] + f[k - 1, k, j]` 

  因此在计算第 `k` 层的 `D[i, j]` 的时候必须先将第 `k - 1`层的所有状态计算出来，所以需要把 `k` 放在最外层。

- 读入邻接矩阵，将次通过动态规划装换成从 i 到 j 的最短距离矩阵
- 在下面代码中，判断从 a 到 b 是否是无穷大距离时，需要进行 `if(t > INF/2)` 判断，而并非是 `if(t == INF)` 判断，原因是 `INF` 是一个确定的值，并非真正的无穷大，会随着其他数值而受到影响，`t` 大于某个与 `INF` 相同数量级的数即可

![最短路2](https://gitee.com/lxxdao/image/raw/master/algorithm/3.3最短路2.png)



**代码：**

```c++
const INF = 1e9;
//初始化：
    for (int i = 1; i <= n; i ++ )
        for (int j = 1; j <= n; j ++ )
            if (i == j) d[i][j] = 0;
            else d[i][j] = INF;

// 算法结束后，d[a][b]表示a到b的最短距离
void floyd() {
    for (int k = 1; k <= n; k ++ )
        for (int i = 1; i <= n; i ++ )
            for (int j = 1; j <= n; j ++ )
                d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
}

```



------



## 4. 最小生成树

### 关于图的几个概念定义

- **连通图**：在无向图中，若任意两个顶点vivi与vjvj都有路径相通，则称该无向图为连通图。
- **强连通图**：在有向图中，若任意两个顶点vivi与vjvj都有路径相通，则称该有向图为强连通图。
- **连通网**：在连通图中，若图的边具有一定的意义，每一条边都对应着一个数，称为权；权代表着连接连个顶点的代价，称这种连通图叫做连通网。
- **生成树**：一个连通图的生成树是指一个连通子图，它含有图中全部n个顶点，但只有足以构成一棵树的n-1条边。一颗有n个顶点的生成树有且仅有n-1条边，如果生成树中再添加一条边，则必定成环。
- **最小生成树**：在连通网的所有生成树中，所有边的代价和最小的生成树，称为最小生成树。 

![最小生成树1](https://gitee.com/lxxdao/image/raw/master/algorithm/3.4最小生成树1.png)

### 最小生树分类

			 				  				稠密图
			 |				  		  √	| 朴素版Prim算法		O(n^2)
			 |	普利姆算法(Prim)
			 |				  		  × | 堆优化版的Prim算法  O(mlogn)
			 				  				稀疏图
	最小生成树 
								
			 |							   	稀疏图		  
			 |	克鲁斯卡尔算法(Kruskal)  √   O(mlogm)
			 |				

![最小生成树2](https://gitee.com/lxxdao/image/raw/master/algorithm/3.4最小生成树2.png)





### 1. Prim算法

**时间复杂度是 O(n^2+m)，n 表示点数，m 表示边数**

**算法思路：**

1. 初始化 起点 dist[1] = 0, 其他点 dist[i] = 0x3f    S:当前已加入生成树的点

2. for (int i = 1; i <= n; i++) {

   1. 寻找不在S中的距离最近的点 t
   2. 将 t 加到 s 中
   3. 用 t 更新其他点到**集合**的距离

   }



```c++
int n;      		// n表示点数
int g[N][N];        // 稠密图用邻接矩阵，存储所有边
int dist[N];        // 存储其他点到当前最小生成树的距离
bool st[N];     	// 存储每个点是否已经在生成树中


// 如果图不连通，则返回INF(值是0x3f3f3f3f), 否则返回最小生成树的树边权重之和
int prim() {
    memset(dist, 0x3f, sizeof dist);

    int res = 0;
    for (int i = 0; i < n; i ++ ) {			// 算法要遍历n个点
        // 找一个最短边权的点
        int t = -1;							// 初始化为没有找到的点
        for (int j = 1; j <= n; j ++ )		// 寻找离集合S最近的点  
            if (!st[j] && (t == -1 || dist[t] > dist[j]))
                t = j;
        
		// 不是第一个取出的节点，并且当前节点的距离为INF,则表示没有和集合中点相连的边。
        if (i && dist[t] == INF) return INF;// 判断是否连通，有无最小生成树
		
        // 第一次个点比较特殊， 第一个点不附带任何边
        if (i) res += dist[t];
        st[t] = true;						//更新最新S的权值和
		// 用点t更新其余点到生成树的最短距离
        for (int j = 1; j <= n; j ++ ) dist[j] = min(dist[j], g[t][j]);
    }

    return res;
}
```





### 2. Kruskal算法

**时间复杂度是 O(mlogm), n 表示点数，m 表示边数**

**算法思路：**

1. 将所有边按权重从小到大排序     O(mlogm)

2. 枚举每条边 a, b 权重 c                 O(m)

   ​    if a, b 不连通

   ​	    将这条边加入集合中



```C++
int n, m;       // n是点数，m是边数
int p[N];       // 并查集的父节点数组

struct Edge {   // 存储边

    int a, b, w;
	// 重载小于
    bool operator< (const Edge &W)const {
        return w < W.w;
    }
}edges[M];

bool cmp(Edge x, Edge y) {	// 和重载<一个道理
    return x.w < y.w;		
}


int find(int x) {    // 并查集找祖宗
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

int kruskal() {
    sort(edges, edges + m);
    //sort(edges, edges + m, cmp);
    //sort(edges, edges + m, [](Edge x, Edge y){return x.w < y .w;});

    for (int i = 1; i <= n; i ++ ) p[i] = i;    // 初始化并查集

    int res = 0, cnt = 0;	// res记录最小生成树的树边权重之和
    						// cnt记录的是全部加入到树的集合中边的数量(可能有多个集合) 
    for (int i = 0; i < m; i ++ ) {	// 如果两个连通块不连通，则将这两个连通块合并
        int a = edges[i].a, b = edges[i].b, w = edges[i].w;

        a = find(a), b = find(b);	
        if (a != b) {   // 如果两个连通块不连通，则将这两个连通块合并
            p[a] = b;	// 合并a b集合
            res += w;	// 将边加入到权重
            cnt ++ ;	// 因为加入的是a-b的这一条边,将a,b所在的两个集合连接之后,全部集合中的边数加1
        }
    }
 	// 树中有n个节点便有n-1条边,如果cnt不等于n-1的话,说明无法生成有n个节点的树
    if (cnt < n - 1) return INF;	
    return res;		
}
```



------



## 5. 二分图

**二分图：所有点分成两个集合，所有边只出现在集合之间**

![二分图](https://gitee.com/lxxdao/image/raw/master/algorithm/3.5二分图1.png)

**二分图 ←充要→ 图中不含奇数环** 

			 |				  	
			 |	染色法 	O(n + m)
			 |
	二分图		 	
			 |				  	
			 |	匈牙利算法  O(mn),实际运行时间一般远小于O(mn)
			 |

![二分图分类](https://gitee.com/lxxdao/image/raw/master/algorithm/3.5二分图2.png)



### 1. 染色法

**时间复杂度是 O(n+m), n 表示点数，m 表示边数**

染色法判断图是否是二分图

若染色过程没有矛盾发生，是二分图；反之出现矛盾，则不是二分图；

**算法思路：**

染色可以使用 1 和 2 区分不同颜色，用 0 表示未染色

for (i = 1; i <= n; i++) 

​		if (i未染色) 	DFS(i, 1)    

​				因此只有某个点染色失败才能立刻break/return

​				染色失败相当于存在相邻的2个点染了相同的颜色



```c++
int n;      				// n表示点数
int h[N], e[M], ne[M], idx; // 邻接表存储图 无向图 顶点数N 边数2*N
int color[N];       		// 表示每个点的颜色，-1表示未染色，0表示白色，1表示黑
							
// 返回是否可以成功将u染色为c
bool dfs(int u, int c) { // 参数：u表示当前节点，c表示当前点的颜色
    color[u] = c;
    for (int i = h[u]; i != -1; i = ne[i]) {
        int j = e[i];
        if (color[j] == -1) {					// 如果color[j]没有染过色
            if (!dfs(j, !c)) return false;		// 如果不可以将j成功染成另一种色
            // 如果 u 的颜色c是1，则和 u 相邻的染成 0
            // 如果 u 的颜色c是0，则和 u 相邻的染成 1
        }
        else if (color[j] == c) return false;	// 如果染过颜色且和c相同
    }
    return true;
}

bool check() {
    memset(color, -1, sizeof(color));
    bool flag = true;
    for (int i = 1; i <= n; i ++ ) 	// 遍历点
        if (color[i] == -1) 		// 如果未染色
            if (!dfs(i, 0)) {		// a如果dfs返回false 说明出现矛盾
                flag = false;
                break;
            }
    return flag;
}

memset(h, -1, sizeof(h))
int add(int a, int b) {
    e[idx] = b;a
    ne[idx] = h[a];
    h[a] = idx++;
}

// 或者使用：0 未染色，1 是红色，2 是黑色
          //if (!dfs(j, 3 - c)) return false;	
            //（3 - 1 = 2， 如果 u 的颜色是2，则和 u 相邻的染成 1）
            //（3 - 2 = 1， 如果 u 的颜色是1，则和 u 相邻的染成 2）
```



### 2. 匈牙利算法

**时间复杂度是 O(nm), n 表示点数，m 表示边数**

**匹配及最大匹配**

> 匹配：在图论中，一个「匹配」是一个边的集合，其中任意两条边都没有公共顶点。
>
> 最大匹配：一个图所有匹配中，所含匹配边数最多的匹配，称为这个图的最大匹配。

下面是一些补充概念：

> 完美匹配：如果一个图的某个匹配中，所有的顶点都是匹配点，那么它就是一个完美匹配。
>
> 交替路：从一个未匹配点出发，依次经过非匹配边、匹配边、非匹配边…形成的路径叫交替路。
>
> 增广路：从一个未匹配点出发，走交替路，如果途径另一个未匹配点（出发的点不算），则这条交替 路称为增广路（agumenting path）。



```c++
int n1, n2;     	// n1表示第一个集合中的点数，n2表示第二个集合中的点数
int h[N], e[M], ne[M], idx;     // 邻接表存储所有边，匈牙利算法中只会用到从第一个集合指向第二个集合的边，所以这里只用存一个方向的边
int match[N];       // 存储第二个集合中的每个点当前匹配的第一个集合中的点是哪个
					// match[j]=a,表示女孩j的现有配对男友是a
bool st[N];    		// 表示第二个集合中的每个点是否已经被遍历过
					// st[]数组我称为临时预定数组，st[j]=a表示一轮模拟匹配中，女孩j被男孩a预定了

// 这个函数的作用是用来判断,如果加入x来参与模拟配对,会不会使匹配数增多
bool find(int x) {
    for (int i = h[x]; i != -1; i = ne[i]) {	// 遍历自己喜欢的女孩
        int j = e[i];
        if (!st[j]) {					// 如果在这一轮模拟匹配中,这个女孩尚未被预定
            st[j] = true;				// 那x就预定这个女孩了
            // 如果女孩j没有男朋友，或者她原来的男朋友能够预定其它喜欢的女孩。配对成功,更新match
            if (match[j] == 0 || find(match[j])) {
                match[j] = x;
                return true;
            }
        }
    }
	// 自己中意的全部都被预定了。配对失败。
    return false;
}

// 求最大匹配数，依次枚举第一个集合中的每个点能否匹配第二个集合中的点
int res = 0;
for (int i = 1; i <= n1; i ++ ) {
    // 因为每次模拟匹配的预定情况都是不一样的所以每轮模拟都要初始化
    memset(st, false, sizeof st);
    if (find(i)) res ++ ;
}
```





------



# 第四章 动态规划

![动态规划](https://gitee.com/lxxdao/image/raw/master/algorithm/4动态规划.png)

**优化一般就是优化状态转移方程**

**集合如何划分：**

- 一般原则:不重不漏,不重不一定都要满足(一般求个数时要满足)
- 如何将现有的集合划分为更小的子集,使得所有子集都可以计算出来

**时间复杂度：**

状态数量 * 转移计算量

## 1. 背包问题

**二维优化成一维：**

- 用上一层的状态更新，从大到小
- 用当前层的状态更新，从小到大



### 1. 01背包

**特点：**每件物品最多只用一次

N件物品，容量为V的背包。每件物品只能用一次。

#### 1. DP分析

一、状态表示：`f[i][j]`

1. 集合：从前 i 个物品中选，且总体积不超过 j 的所有方案的集合。
2. 属性：最大值

二、状态计算：

- **集合划分依据：**第 i 个物品是否选，选 和 不选 （不重复不遗漏）
  `f[i][j] = max(f[i - 1][j], f[i - 1][j - v[i]] + w[i]);`



#### 2.  二维

**重要变量&公式解释**

（1） `f[i][j]`：表示所有选法集合中,只从前i个物品中选,并且总体 `≤j` 的选法的集合,它的值是这个集合中每一个选法的最大值。

- 当前的状态依赖于之前的状态，可以理解为从初始状态 `f[0][0] = 0` 开始决策，有 n 件物品，则需要 n 次决策，每一次对第 i 件物品的决策，状态 `f[i][j]` 不断由之前的状态更新而来。

（2）当前背包容量不够 ( `j < v[i]` )，没得选，因此前 i 个物品最优解即为前 i−1 个物品最优解：

- 对应代码：`f[i][j] = f[i - 1][j]`

（3）当前背包容量够，可以选，因此需要决策选与不选第 i 个物品：

- 将 `f[i]` 表示的所有选法分成两大类（划分原则：不漏）

​	① 选法中不含 i ，即从 1 ~ i -1中选，且总体积不超过 j ，即 f[i-1]

​	② 选法中包含 i ，即从 1 ~ i 中选，包含 i ，且总体积不超过 j

​		可以先把第 i 个物品拿出来，即从第 1 ~ i-1中选，且总体积不超过 j - v[i]

​		即：`f[i - 1][j - v[i]] + w[i]`

- `f[i][j] = max(f[i - 1][j], f[i - 1][j - v[i]] + w[i]);`

```c++
int n, m;			// n物品数 m背包容积
int v[N], w[N];		// v体积	 w价值
int f[N][N];		// 二维数组状态表示

int main() {
    cin >> n >> m;
    for(int i = 1; i <= n; i++) cin >> v[i] >> w[i];
    
    for(int i = 1; i <= n; i++) {
        for(int j = 0; j <= m; j++) {
            f[i][j] = f[i-1][j];	// 默认装不下第i个物品
            // 能装，需进行决策是否选择第i个物品
            if(j>=v[i]) f[i][j] = max(f[i][j], f[i-1][j-v[i]]+w[i]);
        }
    }
    cout << f[n][m] << endl;
}
```



#### 3. 一维

`f[i][:]` 只依赖于 `f[i-1][:]`，所以根本没必要保留之前的 `f[i-2][:]` 等状态值；
使得空间从o(n*m)缩小到o(m)，n,m分别为物品个数和背包体积

**重要变量&公式解释**

（1）状态 `f[j]` 定义：n 件物品，背包容量 j 下的最优解。

（2）注意枚举背包容量 j 必须从 m 开始

（3）为什么一维情况下枚举背包容量需要逆序？

​		在二维情况下，状态 `f[i][j]` 是由上一轮 i - 1 的状态得来的，`f[i][j]` 与 `f[i - 1][j]` 是独立的。

​		而一维数组只有上一层的状态值，更新状态值只能原地滚动更改；

​		当我们更新索引值较大的状态值时，需要用到索引值较小的上一层状态值 `f[j - v[i]]`；

​		也就是说，在更新索引值较大的状态值之前，索引值较小的上一层状态值必须还在，还没被更新；

​		所以只能索引从大到小更新。

（4）一维情况正序更新状态 f[j] 需要用到前面计算的状态已经被「污染」，逆序则不会有这样的问题。

（5）状态转移方程为：`f[j] = max(f[j], f[j - v[i]] + w[i])`

```c++
int n, m;			// n物品数 m背包容积
int v[N], w[N];		// v体积	 w价值
int f[N];			// 二维数组状态表示

int main() {
    cin >> n >> m;
    for(int i = 1; i <= n; i++) cin >> v[i] >> w[i];
    
    for(int i = 1; i <= n; i++) 
        for(int j = m; j >= v[i]; j--) 	// 逆序，这里判断j和v[i]的关系 若超过直接退出该轮
            f[j] = max(f[j], f[j-v[i]]+w[i]);
    cout << f[m] << endl;
 	return 0;    
}
```



### 2. 完全背包

每件物品有无限个

N件物品，容量为V的背包。每件物品能用多次

#### 1. DP分析

一、状态表示：`f[i][j]`

1. 集合：从前 i 个物品中选，且总体积不超过 j 的所有方案的集合。
2. 属性：最大值

二、状态计算：

- **集合划分依据：**第 i 个物品选多少个，选1个、选2个、··· 、选k个
  `f[i][j] = max(f[i][j], f[i - 1][j - k * v[i]] + k * w[i])  k: 1 ~ k*v[i]<=j`
  
  `f[i][j] = max(f[i−1][j], f[i][j−v[i]] + w[i])  j: 1 ~ m && j>=v[i]`



#### 2. 直接做法（会超时）

**时间复杂度O(nm^2)**

- 状态 `f[i][j]` 定义（同「01背包问题」）：前 i 个物品，背包容量 j 下的最优解（最大价值）
- 每一轮循环 i 都可以看作是对第 i 件物品的决策——选择多少个（范围 0 ~ ⌊j / v⌋）第 i 件物品
- 稍微不同的是多重背包允许多次选择一个物品，所以计算状态方程时需要枚举选择第 i 个物品

```c++
for (int i = 1; i <= n; i++) 
   	for (int j = 1; j <= m; j++) 
		for (int k = 0; k * v[i] <= j; k++)
			f[i][j] = max(f[i][j], f[i - 1][j - k * v[i]] + k * w[i]);
```

 

#### 3. 优化时间

**时间复杂度O(n^2)**

状态转移方程推导如下：

`f[i][j] = max(f[i-1][j], f[i-1][j-v]+w, f[i-1][j-2*v]+2*w,···,f[i-1][j-⌊j/v⌋*v]+⌊j/v⌋*w)`

计算的是前 i 个物品 j 体积的最优解 `f[i][j]` ，而前 i−1 个物品的最优解 `f[i−1][j]` 在上一轮循环中都已计算完毕，只需判断选择几个第 i 种物品得到的价值最大

改变一下变量，将 j 变成 j − v[i]，则有：

`f[i][j-v[i]] = max(f[i-1][j-v], f[i-1][j-2*v]+w,···,f[i-1][j-⌊j/v⌋*v]+(⌊j/v⌋-1)*w)`

比较两式：

`f[i][j]   = max(f[i-1][j], f[i-1][j-v[i]]+w[i], f[i-1][j-2*v[i]]+2*w[i], f[i-1][j-3*v[i]]+3*w[i],···`

`f[i][j-v] = max(           f[i-1][j-v[i]],      f[i-1][j-2*v[i]]+w[i],	 f[i-1][j-3*v[i]]+2*w[i],···`

`f[i][j]   = max(f[i−1][j], f[i][j−v[i]] + w[i])`

故可以将第三层循环优化

```c++
for (int i = 1; i <= n; i++)
	for (int j = 1; j <= m; j++) {
		f[i][j] = f[i - 1][j];
		if (j >= v[i]) f[i][j] = max(f[i][j], f[i][j - v[i]] + w[i]);
	}
```



#### 4. 一维

因为和01背包代码很像，可以按照01背包问题的思路进一步优化

**区别：**

- 01背包是使用上一行的状态更新，故从大到小
- 完全背包是使用当前行的状态更新，故从小到大

```c++
for (int i = 1; i <= n; i++)
    for (int j = v[i]; j <= m; j++)
  		f[j] = max(f[j], f[j - v[i]] + w[i]);
```



### 3. 多重背包

每个物品限定多少个

N件物品，每件物品分别有S个，容量为V的背包。每件物品能用多次

#### 1. DP分析

一、状态表示：`f[i][j]`

1. 集合：从前 i 个物品中选,且总体积不超过 j 的所有方案的集合.
2. 属性：最大值

二、状态计算：

- **集合划分依据：**根据第 i 个物品有多少个来划分，含0个、含1个···含k个
  状态表示与完全背包朴素代码一样均为：
  `f[i][j] = max(f[i][j], f[i - 1][j - k * v[i]] + k * w[i])`



#### 2. 直接做法 

**时间复杂度O(n∗v∗s)**

```c++
for (int i = 1; i <= n; i++)
	for (int j = 1; j <= m; j++) 
		for (int k =0; k <= s[i] && k * v[i] <= j; k++)
			f[i][j] = max(f[i][j], f[i - 1][j - v[i] * k] + w[i] * k);
```



#### 2. 二进制优化 + 01背包

将多重背包问题转化成01背包问题 **时间复杂度o(NVlogs)**

**原理**

- 可以将s个物品打包成 logs 个新的物品组，用它们可以凑出从 0 ~ s 的任何一个数，就不用一个一个凑 0~s 中的数
- 时间复杂度：O(S) 优化为 O(log(s))

**打包方法**

 1 + 2 + 4 + 8 + 16 + ··· + 2^(k-1) + 2^k + C = s,  C < 2^(k + 1)

前面可以凑出 0 ~ 2^(k + 1) -1，加上 c 就可以凑出 c ~ s，又由于 c < 2^(k + 1)，所以可以凑出 0 ~ s 中任意一个数

```c++
const int N = 25000, M = 2010;	// N = 1000种 * log2000件
int n, m;
int v[N], w[N]; // 逐一枚举最大是N*logS
int f[M]; 		// 体积M

int main() {
    cin >> n >> m;
    int cnt = 0; // 分组的组别
    for(int i = 1;i <= n;i ++) {
        int a,b,s;
        cin >> a >> b >> s;
        int k = 1; // 组别里面的个数
        while(k <= s) {
            cnt ++ ; // 组别先增加
            v[cnt] = a * k ; 	// 整体体积
            w[cnt] = b * k; 	// 整体价值
            s -= k;  // s要减小
            k *= 2;  // 组别里的个数增加
        }
        //剩余的一组
        if(s>0) {
            cnt ++ ;
            v[cnt] = a*s; 
            w[cnt] = b*s;
        }
    }

    n = cnt ; //枚举次数正式由个数变成组别数

    //01背包一维优化
    for(int i = 1;i <= n ;i ++)
        for(int j = m ;j >= v[i];j --)
            f[j] = max(f[j],f[j-v[i]] + w[i]);

    cout << f[m] << endl;
    return 0;
}

```



### 4. 分组背包

每组最多选一个

N组物品，每组物品分别有S个，容量为V的背包。每组物品最多选一个

#### 1. DP分析

一、状态表示：`f[i][j]`

1. 集合：从前 i 组中选，且体积小于等于 j 的所有选法

   每一组物品有若干个，同一组物品最多选一个

2. 属性：最大值

二、状态计算：

- **集合划分依据：**枚举第 i 组物品选哪个，不选、选第 1 个 ··· 选第 k 个.
  `f[i][j] = max(f[i - 1][j], f[i - 1][j - v[i][k]] + w[i][k])`



#### 2. 二维

```c++
const int N=110;
int n,m;
int v[N][N],w[N][N],s[N];   // v为体积，w为价值，s代表第i组物品的个数
int f[N][N];  // 只从前i组物品中选，当前体积小于等于j的最大值


int main(){
    cin >> n >> m;
    for(int i = 1; i <= n; i++){
        cin>>s[i];
        for(int j = 0; j < s[i]; j++){
            cin >> v[i][j] >> w[i][j];  // 读入
        }
    }

    for(int i = 1;i <= n; i++) {
        for(int j = 0;j <= m; j++) {
            f[i][j]= f[i-1][j];  // 不选
            for(int k = 0; k < s[i]; k++) {
                if(j >= v[i][k])     
                    f[i][j] = max(f[i][j],f[i-1][j-v[i][k]]+w[i][k]);  
            }
        }
    }
    cout<<f[n][m]<<endl;
    return 0;
}
```



#### 3. 一维

```c++
const int N = 110;
int n, m;				
int v[N][N], w[N][N], s[N];	//v为体积，w为价值，s代表第i组物品的个数
int f[N];

int main() {
    cin >> n >>m;
    
    for (int i = 1; i <= n; i++) {
        cin >> s[i];
        for (int j = 0; j < s[i]; j++) {
            cin >> v[i][j] >> w[i][j];
        }
    }
    
    for (int i = 1; i <= n; i++) 
    	for (int j = m; j >= 0; j--) 
            for (int k = 0; k < s[i]; k++)
                if (j >= v[i][k])
                    f[j] = max(f[j], f[j - v[i][k]] + w[i][k]);
     
   	cout << f[m] << endl;

	return 0;  
}
```



------



## 2. 线性DP



### 1. 数字三角形

#### 1. DP分析

一、状态表示：`f[i][j]`

1. 集合：所有从起点，走到 (i, j) 的路径

2. 属性：最大值

二、状态计算：

- **集合划分依据：**当前点的上一步来自哪，左上 or 右下
  `f[i][j] = max(f[i - 1][j - 1] + a[i][j], f[i - 1][j] + a[i][j])`



#### 2. 从上往下二维

```C++
const int N = 510, INF = 1e9;
int n;
int a[N][N];  // 保存每个位置的值
int f[N][N];  // f[i][j]表示从(1,1)走到(i,j)的所有路径中，总和最大的那一条路径的总和

int main() {
    cin >> n;
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= i; j++)
            cin >> a[i][j];    
    
    for(int i = 1; i <= n; i++)
        // 注意这里j从0到i+1,因为对于边界点,它的上一层只有一条路径通向它
        for(int j = 0; j <= i+1; j++)  
            f[i][j]=-INF;      //初始化近似为-∞    
    
    // f[1][1]=a[1][1];  //由f[i][j]的定义，(1,1)点的f值就是本身 循环由第二层开始枚举
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= i; j++)
            f[i][j] = max(f[i - 1][j - 1] + a[i][j], f[i - 1][j] + a[i][j]);
    
    int res = -INF;
    for (int j = 1; j <= n; j++) res = max(res, f[n][j]);
    cout << res <<endl;
    
    return 0;
}
```



#### 3. 优化空间，一维

```c++
const int N = 510, INF = 1e9;
int n;
int f[N], a[N]; // f[j]表示从(1,1)走到(n,j)的所有路径中，总和最大的那一条路径的总和

int main() {
    cin >> n;
    // 注意这里j从0到i+1,因为对于边界点,它的上一层只有一条路径通向它
    for (int j = 0; j <= n + 1; j++) 
        f[j] = -INF;
    
    cin >> f[1];	// 由f[i][j]的定义，(1,1)点的f值就是本身
    for (int i = 2; i <= n; i++) {	// 从第二层开始枚举至第n层
        for (int j = 1; j <= i; j++) cin >> a[j]; //读入每层的值
        
        for (int j = i; j >= 1; j--) f[j] = max(f[j-1], f[j]) + a[j];
    }
    
    int res=-INF;
    for(int i=1;i<=n;i++) res=max(res,f[i]);  //最大值在第n层的某一个点取得
	cout << res << endl;
    
    return 0;
}
```



#### 4. 从下往上

倒序dp，更简单些，因为倒序不需要考虑边界问题

```c++
const int N=510;
int f[N][N];
int n;

int main() {
	cin >> n;
	for (int i = 1; i <= n; i++)
        for (int j = 1; j <= i; j++)
            cin >> f[i][j];
   	
    for (int i = n; i >= 1; i--)
        for (int j = i; j >= 1; j--)
            f[i][j] = max(f[i + 1][j], f[i + 1][j + 1]) + f[i][j];
    
    cout << f[1][1] << endl;;
}
```



### 2. 最长上升子序列

#### 1. DP分析

一、状态表示：`f[i]`

1. 集合：所有以第 i 个数结尾的上升子序列

2. 属性：最大值

二、状态计算：

- **集合划分依据：**当前数 j 是否小于上一个数 i，是则构成上升子序列

​		答案可以更新成 本身 和 与 j 构成的的子序列 中较大的那一个
​		`f[i] = max(f[i], f[j] + 1),j = 0,1,2,...,i - 1 `

**时间复杂度：O(n^2)**



#### 贪心 + 暴力枚举

 **O(n^2)**

```c++
int n;	  // 字符个数
int a[N]; // 输入用的数组
int f[N]; // 用来存储每个答案，在最后要for循环遍历找最大值

int main() {
    cin >> n;
    for (int i = 1; i <= n; i++) cin >> a[i];
    
    for (int i = 1; i <= n; i++) {
        f[i] = 1;	// 只有a[i]一个数，本身算一个序列
        for (int j = 1; j < i; j++) {
            if (a[j] < a[i]) {	// 如果符合“上升”的条件，就尝试更新f数组
                f[i] = max(f[i], f[j] + 1);
            }
        }
    }
    int res = 0;
    for (int i = 1; i <= n; i++) res = max(res, f[i]); // 找最大值
    cout << res << endl;
    
    return 0;
}
```



#### 贪心 + 二分 

**O(nlogn)**

基于暴力枚举的单调性：**找到一个最大的小于等于当前数的数**， 我们可以用 **二分** 来优化

最长上升子序列（LIS）

二分的思路：

1. `q[i]` 是所有长度为 `i` 的上升子序列末尾元素的最小值

2. `len` 的值为最长上升子序列长度的值，即数组 `q` 的长度
3. 二分过程为判断 a[i] 的值在当前 q[i]的位置
   - 如果 a[i] 的值比数组中所有的值都大，则数组长度增加
     - q[]：1 2 	   a[i] = 3  →  len = 3 q[]：1 2 3 
   - 如果 a[i] 的值在数组中的中，则覆盖当前值
     - q[]：1 2 3 	a[i] = 0  →  len = 3 q[]：0 2 3 
     - 此时 LIS 仍为 3(1 2 3)，但记录了LIS为 1 的 0
     - q[]：0 2 3     a[i] = 1  →  len = 3 q[]：0 1 3    记录了(1 2 3)以及(0 1)

```c++
int n;
int a[N];
int q[N];

int main() {
    cin >> n;
    for (int i = 0; i <= n; i++) cin >> a[i];
    
    int len = 0;
    q[0] = -2e9;                    // 省得考虑边界
    for (int i = 0; i < n; i++) {   // 从短到长解决各个子串的 LIS
        int l = 0, r = len;         // 二分找到a[i]在q数组中的位置
        while (l < r) {     
            int mid = l + r + 1 >> 1;
            if (q[mid] < a[i]) l = mid;
            else r = mid - 1;
            
        }
        len = max(len, r + 1);		// 若a[i]比数组中数都大，长度增加
        q[r + 1] = a[i];
    }
    
    cout << len << endl;
    return 0;
}
```



### 3. 最长公共子序列

#### 1. DP分析

一、状态表示：`f[i][j]`

1. 集合：所有在第一个序列的前 i 个字母，且在第二个序列的前 j 个字母中出现的子序列

2. 属性：最大值

二、状态计算：

- **集合划分依据：**a[i], b[j] 是否包含在子序列当中，划分成四类 00 01 10 11

​		不选 a[i]b[j]、不选 a[i] 选 b[j]、选 a[i] 不选 b[j]、选 a[i]b[j]

​		`f[i - 1][j - 1]`  `f[i - 1][j]`  `f[i][j - 1]`  `f[i][j]`

​		`f[i][j] = max(f[i - 1][j], f[i][j - 1])`		

​		`if (a[i] == b[j]) f[i][j] = max(f[i][j], f[i - 1][j - 1] + 1);`	

- **解析：**

​		`f[i - 1][j]` 指第一个序列的前 i-1个字母，且在第二个序列的前 j 个字母中出现的子序列

​		但不一定第 j 个字母，但 `f[i - 1][j]` 一定包含 01 这种情况，而且取max不会影响结果

​		`f[i - 1][j -1]` 包含在 `f[i - 1][j]` 里，故这一项可以省掉。

**时间复杂度：O(n^2)**     状态数量 O(n^2)，状态运算 3 次 O(1)

#### 2. 代码

```c++
int n, m;
char a[N], b[N];
int f[N][N];

int main() {
    
    cin >> n >> m;
    cin >> a + 1 >> b + 1;	// 让a,b字符串下标由1开始
    
    for (int i = 1; i <= n; i++)
        for (int j = 1; j<= m; j ++) {
            f[i][j] = max(f[i - 1][j], f[i][j - 1]); // 这两种情况无须判断
            // f[i -1][j - 1] + 1 情况需a[i] = b[i]的前提实现
            if (a[i] == b[j]) f[i][j] = max(f[i][j], f[i - 1][j - 1] + 1);
        }
    
    cout << f[n][m] << endl;
    
    return 0;
}
```



### 4. 编辑距离

#### 1. DP分析

一、状态表示：`f[i][j]`

1. 集合：所有将 `a[i][j]` 变成 `b[i][j]` 的操作方式

2. 属性：最小值

二、状态计算：

- **集合划分依据：**a[i], b[j] 是否相同，划分成三类 00 01 10 11

​		a[i]比b[i]多 → 删a[i]、a[i]比b[i]少 → 增a[i]、a[i]与b[i]不同 → 改a[i]

​		`f[i - 1][j] + 1`  、`f[i][j - 1] + 1`  、`f[i - 1][j - 1] + 1`  

​		`f[i][j] = min(f[i - 1][j] + 1, f[i][j - 1] + 1, f[i - 1][j - 1] + 1)`

1. 删除操作：把a[i]删掉之后 a[1~i] 和 b[1~j] 匹配

   所以之前要先做到 a[1~(i-1)] 和 b[1~j] 匹配     			    	`f[i-1][j] + 1`

2. 插入操作：插入之后a[i]与b[j]完全匹配，所以插入的就是b[j]

   所以之前 a[1~i] 和 b[1~(j-1)] 匹配								 	   `f[i][j-1] + 1` 

3. 替换操作：把 a[i] 改成 b[j] 之后想要 a[1~i] 与 b[1~j] 匹配 
   所以修改这一位之前，a[1~(i-1)] 应该与 b[1~(j-1)] 匹配    `f[i-1][j-1] + 1`
   但是如果本来 a[i] 与 b[j] 这一位相等，就不用改                `f[i-1][j-1] + 0`



#### 2. 代码O(n^2)

> 细节问题：初始化怎么搞
> 先考虑有哪些初始化
>
> 1. 你看看在for遍历的时候需要用到的但是你事先没有的
>    （往往就是什么0啊1啊之类的）就要预处理
> 2. 如果要找min的话别忘了INF
>      要找有负数的max的话别忘了-INF
>
> ok对应的： 
>
> 1. `f[0][i]` 如果a初始长度就是0，那么只能用插入操作让它变成b
>
>    `f[i][0]` 同样地，如果b的长度是0，那么a只能用删除操作让它变成
>
> 2. `f[i][j]` = INF //虽说这里没有用到，但是把考虑到的边界都写上还是保险

```c++
int n, m;
char a[N], b[N];
int f[N][N];	// 将a[1~i]变成b[1~j]的操作方式

int main() {
    
    cin >> n >> a + 1;
    cin >> m >> b + 1;
    
    for (int i = 0; i <= m; i++) f[0][i] = i;   // 将a[0](即没有字符)转变为b[i]需要添加i次
    for (int i = 0; i <= n; i++) f[i][0] = i;   // 将a[i]转变为b[0](没有字符),即需要删除i次
    
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++) {  // 先比较删除和添加的方案数,再比较替换的方案数
            f[i][j] = min(f[i - 1][j] + 1, f[i][j - 1] + 1);
            if (a[i] == b[j]) f[i][j] = min(f[i][j], f[i - 1][j - 1]);
            else f[i][j] = min(f[i][j], f[i - 1][j - 1] + 1);
        }
        
    cout << f[n][m] << endl;
    
    return 0;
}
```



## 3. 区间DP



### 1. 区间 DP 常用模版

所有的区间 dp 问题枚举时，第一维通常是枚举区间长度，a并且一般 len = 1 时用来初始化，枚举从 len = 2 开始；第二维枚举起点 i （右端点 j 自动获得，j = i + len - 1）

**模板代码如下：**

```c++
for (int len = 1; len <= n; len++) {         // 区间长度
    for (int i = 1; i + len - 1 <= n; i++) { // 枚举起点
        int j = i + len - 1;                 // 区间终点
        if (len == 1) {
            dp[i][j] = 初始值
            continue;
        }

        for (int k = i; k < j; k++) {        // 枚举分割点，构造状态转移方程
            dp[i][j] = min(dp[i][j], dp[i][k] + dp[k + 1][j] + w[i][j]);
        }
    }
}
```



### 2. 石子合并

#### 1. DP分析

一、状态表示：`f[i, j]`

1. 集合：所有将第 i 堆石子到第 j 堆石子合并成一堆石子的合并方式

2. 属性：最小值

二、状态计算：

**集合划分依据：**最后一次合并一定是左边连续的一部分和右边连续的一部分进行合并`

1. `i < j` 时，`f[i][j]=min(f[i][k] + f[k+1][j] + s[j] − s[i−1])  i ≤ k ≤ j - 1`
2. `i = j` 时, `f[i][i]=0` (合并一堆石子代价为 0)
3. 问题答案： `f[1][n]`



#### 代码

```c++
int n;
int s[N];
int f[N][N];

int main() {
    cin >> n;
    for (int i =1; i <= n; i++) {
        cin >> s[i];
        s[i] += s[i - 1];   
    }
    
    memset(f, 0x3f, sizeof f);                  
    // 区间 DP 枚举套路：长度+左端点 
    for (int len = 1; len <= n; len++) {         // len表示[i, j]的元素个数
        for (int i = 1; i + len - 1 <= n; i++) {
            int l = i, r = i + len - 1;          // l r 左右端点
            if (len == 1) {
                f[l][r] = 0;  // 边界初始化
                continue;
            }
            for (int k = l; k < r; k++) {       // 枚举分割点，构造状态转移方程
                f[l][r] = min(f[l][r], f[l][k] + f[k + 1][r] + s[r] - s[l - 1]);
            }
        }
    }
    cout << f[1][n] << endl;
    
    return 0;
}
```



## 4. 记数类DP



### 整数划分

**思路：**把1,2,3, … n分别看做n个物体的体积，这n个物体均无使用次数限制，问恰好能装满总体积为n的背包的总方案数（完全背包问题变形）v[i] = w[i] = i

### 1. 完全背包做法

#### 1. DP分析

一、状态表示：`f[i, j]`

1. 集合：从 1 ~ i 中选，总体积恰好是 j 的集合

2. 属性：数量

二、状态计算：

**集合划分依据：**第 i 个物品选多少个，选1个、选2个、··· 、选k个

`f[i][j] = f[i][j] + f[i - 1][j - k * i]  k: 1 ~ k* i<=j`

**完全背包角度状态转移方程：**

`f[i][j] = f[i - 1][j]`

`f[i][j] = f[i − 1][j] + f[i][j − i]  j: 1 ~ n && j>=i`

```c++
f[i][j] = f[i - 1][j]
if (j >= i)
   f[i][j] = (f[i - 1][j] + f[i][j - i])  j: 1 ~ n && j>=i 
```

**优化一维：**

`f[j] = f[j] + f[j - i]`



#### 2. 完全背包解法 O(n^3)

```C++
f[0][0] = 1;	// 一个数都不选是一种方案
for(int i = 1; i <= n; i++)
        for(int j = 0; j <= n; j++)
            for(int k = 0; k * i <= j; k++)
                f[i][j] = (f[i][j] + f[i - 1][j - k * i]) % mod;
```



#### 3. 二维优化 + 一维O(n^2)

```c++
for (int i = 0; i <= n; i ++) 
 	f[i][0] = 1; // 容量为0时，前 i 个物品全不选也是一种方案
// f[0][0] = 1;	好像初始化一个也没问题

for (int i = 1; i <= n; i ++) 
	for (int j = 0; j <= n; j ++) {
		f[i][j] = f[i - 1][j] % mod; // 特殊 f[0][0] = 1
        if (j >= i) f[i][j] = (f[i - 1][j] + f[i][j - i]) % mod;
    }

```

```c++
f[0] = 1;
for (int i = 1; i <= n; i ++) 
	for (int j = i; j <= n; j ++) 
		f[j] = (f[j] + f[j - i]) % mod;
```



### 2. 其他算法

#### 1. DP分析

一、状态表示：`f[i, j]`

1. 集合：所有总和是 i，并且恰好表示成 j 个数的和的方案

2. 属性：数量

二、状态计算：

**集合划分依据：**最小值是1、最小值大于1

`f[i - 1][j - 1]`、`f[i - j][j]`

**状态转移方程：**
`f[i][j] = f[i - 1][j - 1] + f[i - j][j]`

`ans = f[n][1] + f[n][2] + ... + f[n][n]`

#### 2. 代码O(n^2)

```c++
for (int i = 2; i <= n; i ++) 
	for (int j = 1; j <= i; j ++) 
		f[i][j] = (f[i - 1][j - 1] + f[i - j][j]) % mod;

int res = 0;
for (int i = 1; i <= n; i ++ ) res = (res + f[n][i]) % mod;
```



## 5. 数位统计DP



### 计数问题

强调**分类讨论：**

1 ~ n, x = 1  n = abcdefg

[1, abcdefg] 分别求出 1 在每一位上出现的次数

例如：求 1 在第 4 位上出现的次数	`1 <= xxx1yyy <= abcdefg`

1. `xxx = 000 ~ (abc -1)`, `yyy = 000 ~ 999`, `res += abc * 1000`

   如果前三位没填满，则后三位就可以随便填

2. `xxx = abc`   

   (1) `d < 1`, `abc1yyy > abc0efg`, `res += 0`

   (2) `d = 1`, `yyy = 000 ~ efg`, `res += efg + 1`

   (3) `d > 1`, `yyy = 000 ~ 999`, `res += 1000`
   
   如果前三位填满了，后三位怎么填取决于当前这一位

**但是有一些特殊情况**

1. x在第1位上出现的次数（不用考虑前半段）：

   `1yyyyyy`, `yyyyyy = 000000 ~ bcdefg `, `res += bcdefg+1`

3. 如果我们枚举的数是0的话 ：

   `0` 不能在第一位 
   而且枚举到的这一位前面不能全是 `0`，即
   `abc0efg`，`xxx = 001 ~ (abc-1)`



**代码：**

```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int get(vector<int> num, int l, int r) {	// 求前面位组成的数是多少
    int res = 0;							// 算出数组num[]第r位到第l位的数是多少
    for (int i = l; i >= r; i--)			// lr对应数据的左边到右边 l高位 r低位
      	res = res * 10 + num[i];
    return res;
}

int power10(int x) {		// 求10的x次方
    int res = 1;
    while (x--) res *= 10;
    return res;
}

int count(int n, int x) {
    if (!n) return 0;		// 当n=0,返回0	虽然a和b不会取到0，但是a-1会
    vector<int> num;		// 把n的每一位抠出来 
    while (n) {				// 个位 十位 百位...
        num.push_back(n % 10);
        n /= 10;
    }
    n = num.size();			// n的位数
    int res = 0;			// 存储答案
    for (int i = n - 1; i >= 0; i--) {
        if (i < n - 1) {	// 第一种情况 要找数字小于数n的位置
            res += get(num, n - 1, i + 1) * power10(i);
        }
        if (!x) res -= power10(i);	// 当x为0的时候 少一种情况
        
        //	第二种情况的三种可能
        if (num[i] == x) res += get(num, i - 1, 0) + 1;	// 2.2
        else if (num[i] > x) res += power10(i);	// 2.3
        else res += 0;		// 2.1
    }
    return res;
}

int main() {
    int a, b;
    while (cin >> a >> b, a||b) {
        if (a > b) swap(a, b);
        for (int i = 0; i < 10; i++)
            cout << count(b, i) - count(a - 1, i) << ' ';
        cout << endl;
    }
    return 0;
}
```



## 6. 状态压缩DP

所谓的状态压缩DP，就是用二进制数保存状态。

为什么不直接用数组记录呢？因为用一个二进制数记录方便作位运算。



### 蒙德里安的梦想

`n×m` 的棋盘可以摆放不同的 `1×2` 小方格的种类数。

**核心：**

先放横着的，再放竖着的。

总方案数，等于只放横着的小方块的合法方案数。

**如何判断，当前方案是否合法？**

所有剩余位置及，能否填充满竖着的小方块。

可以按列来看，每一列内部所有连续的空着的小方块，需要是偶数个。

#### 1. DP分析

一、状态表示：`f[i, j]` 化零为整

1. 集合：表示已经将前 `i - 1` 列摆好，且从第 `i - 1`列，伸出到第 `i` 列的状态是 `j` 的所有方案。

   其中 `j` 是一个二进制数，用来表示该列空格的状态（0代表空，1代表非空）

2. 属性：最大值

二、状态计算：

**集合划分依据：**`i-1, i` 列已经固定，所以集合划分是依据 `i-2` 列伸到 `i-1` 列的不同状态 `k` 来划分

划分最多有 `2^n` 个子集，每个子集都表示一个具体的状态 `k` 

问题：第 `i-2` 列伸到 `i-1` 列的状态为 `k` ， 是否能成功转移到第 `i-1` 列伸到 `i` 列的状态为 `j` ?

(1) (j & k) == 0

(2) 所有连续空着的位置的长度必须是偶数

`f[i][j] += f[i - 1][k]`

**最终方案：**`f[m][0]`



#### 2. 朴素版

时间复杂度：O(n*2^n * 2^n)
dp的时间复杂度 = 状态表示 × 状态转移 = (11 * 2^11)  * 2^11

```c++
#include <iostream>
#include <cstring>
#include <vector>

using namespace std;

const int N = 12, M = 1 << N;   //数据范围1~11
// 每一列的每一个空格有两种选择，放和不放，所以是2^n

int n, m;			// n行m列
long long f[N][M];  // 方案数比较大，所以要使用long long 类型
// f[i][j]表示 i-1 列的方案数已经确定，从i-1列伸出，并且第i列的状态是j的所有方案数
// 第 i-2 列伸到 i-1 列的状态为 k ， 是否能成功转移到第 i-1 列伸到 i 列的状态为 j
bool st[M];	// st[j|k]=true 表示能成功转移

int main() {
    while (cin >> n >> m, n || m) {
        // 第一部分：预处理st数组	储存的是一个二进制数中是否有连续奇数个零,如果有则为false,反之则为true
        // 对于每种状态，先预处理每列不能有奇数个连续的0
        for (int i = 0; i < 1 << n; i++) {
            st[i] = true;		// 记录这个状态被枚举过且可行
            int cnt = 0;		// 记录一列中0的个数
            for (int j = 0; j < n; j++)	// 从低位到高位枚举它的每一位
                // 通过位操作，i 状态下 j 行是否放置方格，
				// 0就是不放，1就是放
                if (i >> j & 1) {
                    if (cnt & 1) st[i] = false;	
                    // 如果之前连续0的个数是奇数，竖的方块插不进来，这种状态不行
                    cnt = 0;	// 清空计数器
                }
                else cnt ++;	// 如果不为1，计数器+1
                
            if (cnt & 1) st[i] = false;	// 到末尾再判断一下前面0的个数是否为奇数，更新st状态
        }
        memset(f, 0, sizeof f);	// 初始化状态数组f
        f[0][0] = 1;            // 空棋盘的时候是1,所有小方块竖着
        for (int i = 1; i <= m; i++)	// 遍历每一列
            for (int j = 0; j < 1 << n; j++)	// 枚举i列的每一种状态j
                for (int k = 0; k < 1 << n; k++)	// 枚举i-1列的每一种状态k
                    if ((j & k) == 0 && st[j | k])
                    // 如果它们没有冲突，i这一列被占位的情况也是合法的话
                        f[i][j] += f[i - 1][k];	// 该状态的方案数等于之前每种k状态数目的和
        
        cout << f[m][0] << endl;// 求的是第m-1行排满，并且第m-1行不向外伸出块的情况
        // 0~m-1行是题目中可以摆方块的范围
    }
    
    return 0;
}
```



#### 3. 优化版

**提前枚举出符合条件的状态**

```c++
#include<iostream>
#include<algorithm>
#include<cstring>
#include<vector>

using namespace std;

const int N = 12, M = 1 << N;

long long f[N][M];//f[i][j]中i表示的是列数,而j表示的是方案数与状态
vector<int> state[M];//例如state[j]存储的就是j|k后满足条件的k的集合,可以减少时间复杂度
bool st[M];

int main() {
	int n, m;
    while (cin >> n >> m, n || m) {
        for (int i = 0; i < 1 << n; i++) {
            int cnt = 0;
            bool is_value = true;
            for (int j = 0; j < n; j++)
            	if (i >> j & 1) {
                    if (cnt & 1) {
                        is_value = false;
                        break;
                    }
                    cnt = 0;
                } else cnt++;
            if (cnt & 1) is_value = false;
            st[i] = is_value;
        }
        
        for(int i = 0;i < 1 << n;i++) { // 将所有满足条件的状态都预处理出来,减少时间复杂度
            state[i].clear();
            for (int j = 0; j < 1 << n; j++)
            if ((i&j) == 0 && st[i|j])
            state[i].push_back(j);
        }
        
        memset(f,0,sizeof(f));
      	f[0][0]=1;
        for(int i=1;i<=m;i++)
         	for(int j=0;j<1<<n;j++)
          		for(auto k : state[j])
          			f[i][j]+=f[i-1][k];
        /*
        当状态f[i][j]和f[i-1][k]不冲突的时候说明状态f[i][j]可以由状态f[i-1][[k]
        一步转移过来，而f[i-1][k]中存放的是从初始状态到f[i-1][k]的方案数，故到
        达状态f[i][j]的方案中一定包含路径：初始状态-->f[i-1][k]-->f[i][j], 所有
        f[i][j] = f[i][j] + f[i-1][k]
        */
         cout << f[m][0] << endl;
    }


	return 0;
}
```





### 最短Hamilton路径

#### 1. DP分析

一、状态表示：`f[i, j]`		其中第 `i` 位为 `1/0` 表示 `是/否` 经过了点 `i`。

1. 集合：所有从 0 走到 j , 走过的所有点是 i 的所有路径

2. 属性：最小值

二、状态计算：

**集合划分依据：**倒数第二点来分类 0 1 2 ... n - 1

求法：0 → k → j    k 到 j 的距离可以找 `a[k][j]` 中最小值，目标让 0 → k 最短

`f[i][j] = min(f[i][j], f[i ^ (1 << j)][k] + w[k][j]) `

其中 `w[k][j]` 表示从点 `k`  到点 `j`  的距离，`^` 表示异或运算，`i` 为state点的集合
`i ^ (1 << j)` 是将 `i` 的第 `j` 位改变后的值，即

- 如果 `i` 的第 `j` 位是 `1` 那么将其改为 `0`
- 否则将 `i` 的第 `j` 位改为 `1`

由于到达点 `j` 的路径一定经过点 `j`，也就是说当 `i` 的第 `j` 位为 `1` 的时候，`f[i][j]` 才可以被转移

所以 `i ^ (1 << j)` 其实就是将 `i` 的第 `j` 位改为 0

这样也就符合了走到点 `k` 的路径就不能经过点 `j` 这个条件。



#### 2. 代码

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 20, M = 1 << N;

int f[M][N], w[N][N];	// w存每两个点之间的距离 

int main() {

    int n;
    cin >> n;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            cin >> w[i][j];
    
    memset(f, 0x3f, sizeof(f));	// 由于要求最小值，所以这里将 f 初始化为正无穷会更好处理一些
    f[1][0] = 0;	
    									// 从 0 到 111...11 枚举所有 state
    for (int i = 0; i < 1 << n; i++)	// i代表着是方案的集合，每个位置1和0代表着这个点是否经过
        for (int j = 0; j < n; j++)		// 枚举所有i到达的点
            if (i >> j & 1)				// 如果i集合中第j位是1，也就是到达过这个点，状态转移
                for (int k = 0; k < n; k++)	// 枚举到达j的点k
                    if ((i ^ (1 << j)) >> k & 1)	
                    // 如果从当前状态经过点集i中，去掉点j后，i仍然包含点k，那么才能从点k转移到点j
                        f[i][j] = min(f[i][j], f[i ^ (1 << j)][k] + w[k][j]);
                    // if(i>>k&1)
    				// f[i][j] = min(f[i][j], f[i - (i << j)][k] + w[k][j]);
    
    cout << f[(1 << n) - 1][n - 1] << '\n';	// 最后输出 f[111...11][n-1]
    // 位运算的优先级低于'+'-'所以有必要的情况下要打括号
    return 0;
}
```



## 7. 树形DP

![树形DP](https://gitee.com/lxxdao/image/raw/master/algorithm/4.7树形dp.jpg)

### 没有上司的舞会

#### 1. DP分析

一、状态表示：`f[u][0]`  `f[u][1]`

1. 集合：所有从以 u 为根的子树中选择，并且不选 u 这个点的方案

   ​			所有从以 u 为根的子树中选择，并且选择 u 这个点的方案

2. 属性：最大值

二、状态计算：

**集合划分依据：**不选 `u` 和 选 `u`

`f[u][0] = ∑max(f[Si][0], f[Si][1])`

`f[u][1] = ∑f[Si][0]`

```c++
void dfs(int u) {
    f[u][0] = 0;	// 初始的高兴度
    f[u][1] = w[u];
    for (int i = h[u]; i != -1; ne[i]) {	// 遍历树
    	int j = e[i];
        dfs(j);		// 回溯
        f[u][0] += max(f[j][1],f[j][0]);
        f[u][1] += f[j][0];  
    }
}
```

**时间复杂度：**O(n)



#### 2. 代码

```c++
#include <iostream>
#include <cstring>

using namespace std;

const int N = 6010;

int n;
int f[N][2];
int w[N];
int h[N], e[N], ne[N], idx; // 邻接表存树
bool has_father[N];			// 存储点是否有父节点

void add(int a, int b) {
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}

void dfs(int u) {
    f[u][0] = 0;	// 初始的高兴度
    f[u][1] = w[u];	// 1代表这个boss要来，先加上他来的利益
    for (int i = h[u]; i != -1; ne[i]) {	// 遍历树
    	int j = e[i];
        dfs(j);		// 回溯
        f[u][0] += max(f[j][1],f[j][0]);
        // boss不来，那小弟就是王了，但小弟要以利益为重，如果小小弟来可以获利更大，就让小小弟来
        f[u][1] += f[j][0]; 
        // boss来了！！！小弟都承让
    }
}

int main() {
    cin >> n;
    for (int i = 1; i <= n; i++) cin>>w[i];
    
    memset(h, -1, sizeof(h));
    for (int i = 0; i < n - 1; i++) {
        int a, b;
        cin >> a >> b;
        add(b, a);
        has_father[a] = true;
    }
    
    int root = 1;		// 找根节点
    while (has_father[root]) root ++;
    dfs(root); //从根节点开始搜索
    cout << max(f[root][0], f[root][1]) << endl;
    // 取董事长来和不来的最大利益
    return 0;
}

```



## 8. 记忆化搜索

### 滑雪

常规DFS
但是在某些数据超时

**记忆化：**

记录每个点的最大滑动长度
遍历每个点作为起点
然后检测该点四周的点 如果可以滑动到其他的点
那么该点的最大滑动长度 就是其他可以滑到的点的滑动长度+1
`f[i][j] = max(f[i][j-1],f[i][j+1],f[i-1][j],f[i+1][j])`

由于滑雪是必须滑到比当前低的点 所以不会存在一个点多次进入的问题
如果该点的dp[][] 不为初始化值 那么就说明计算过 不必再次计算

#### 1. DP分析

一、状态表示：`f[i, j]`  

1. 集合：所有从 (i, j) 开始滑的路径

2. 属性：最大值

二、状态计算：

**集合划分依据：**当前点下一步往哪滑 上、下、左、右

`f[i-1][j]+1`、`f[i+1][j]+1`、`f[i][j-1]+1`、`f[i][j+1]+1`

#### 2. 朴素代码

```c++
#include <iostream>
#include <vector>

using namespace std;

const int N = 310;
int n,m;
int maxlen = -1;
int h[N][N];

int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};

void dfs(int x,int y,int len)
{
    if(len > maxlen) maxlen = len;

    for(int i = 0; i < 4; i++){
        int a = x + dx[i];
        int b = y + dy[i];

        if(a >= 1 && a <= n && b >= 1 && b <= m &&
            h[a][b] < h[x][y])
        {
            dfs(a, b, len + 1);        
        }
    }
}

int main()
{
    cin >> n >> m;

    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> h[i][j];


    for(int i = 1; i <= n;i++){
        for(int j = 1;j <= m;j++){
            int len = 1;
            dfs(i, j, len);
        }
    }

    cout << maxlen << endl;

    return 0;
}
```



#### 3. 优化代码

```c++
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 310;

int n, m;		// 行、列
int f[N][N];	// 状态表示
int h[N][N];	// 网格滑雪场

int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};	// 四个方向

int dp(int x, int y) {
    int &v = f[x][y];	// 引用
    if (v != -1) return v;	// 如果已经计算过了，就可以直接返回答案
    v = 1;
    for (int i = 0; i < 4; i++) {
        int a = x + dx[i], b = y + dy[i];
        // 判断这点是否能走
        if (a >= 1 && a <= n && b >= 1 && b <= m && h[a][b] < h[x][y])	
            v = max(v, dp(a, b) + 1);
    }
    return v;
}


int main() {
    cin >> n >> m;
    
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> h[i][j];
    
    memset(f, -1, sizeof(f));
    
    int res = 0;
    for (int i = 1; i <= n; i++)	// 可以在任意一点开始滑，遍历滑雪场每个点
        for (int j = 1; j <= m; j++)
            res = max(res, dp(i, j));
    
    cout << res << '\n';
    return 0;
}
```





------



# 第五章 贪心

**做题思路：猜 + 证明**

## 1. 区间问题

### 1. 区间选点

**算法分析**

![区间选点](https://gitee.com/lxxdao/image/raw/master/algorithm/5.1区间选点.png)

1. 将每个区间按照右端点从小到大进行排序
2. 从前往后枚举区间，end值初始化为无穷小
   - 如果本次区间不能覆盖掉上次区间的右端点  `ed < range[i].l`
   - 说明需要选择一个新的点  `res ++ ; ed = range[i].r`
   - 如果本次区间可以覆盖掉上次区间的右端点，则进行下一轮循环

**时间复杂度 O(nlogn) （排序）**

**证明**

1. 证明 `ans<=cnt` ：`cnt` 是一种可行方案， `ans` 是可行方案的最优解，也就是最小值。

2. 证明 `ans>=cnt` ： `cnt`可行方案是一个区间集合，区间从小到大排序，两两之间不相交。

   所以覆盖每一个区间至少需要 `cnt` 个点。

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010;
int n;

struct Range {
    int l, r;
    //bool operator < (const Range &w)const {
    //    return r < w.r;
    //}
}range[N];

int main() {
    cin >> n;
    for (int i = 0; i < n; i++) {
        int l, r;
        cin >> l >> r;
        range[i] = {l, r};
    }
    
    //auto compare = [](Range a, Range b)->bool{return a.r < b.r;};
    //sort(range, range + n,compare);
    sort(range, range + n,[&](Range a, Range b)->bool{return a.r < b.r;});
    //sort(range, range + n,[&](Range a, Range b){return a.r < b.r;});
    //sort(range, range + n);
    int res = 0, ed = -2e9;
    for (int i = 0; i < n; i ++) {
        if (range[i].l > ed) {
            res ++;
            ed = range[i].r;
        }
    }
    
    cout << res << '\n';
    
    return 0;
}
```



### 2. 最大不相交区间数量

![最大不相交区间数量](https://gitee.com/lxxdao/image/raw/master/algorithm/5.1最大不相交区间数量.jpg)

**算法**

- 将每个区间按照右端点从小到大进行排序
- `ed` 表示当前放置在数轴上的点的位置，开始初始化为无穷小，表示没有放置，此时数轴上没有点
- 依次遍历排序好的区间。如果区间的左端点大于当前放置点的位置，说明当前点无法覆盖区间，则把点的位置更新成该区间的右端点，表示在该区间的右端点放置一个新的点，同时更新点的个数

**证明**

- 假设 `ans` 是最优解，表示最多有 `ans` 个不相交的区间；`cnt` 是可行解，表示算法求出 `cnt` 个不相交的区间，显然有 `ans≥cnt`
- 反证法证明 `ans≤cnt` 假设 `ans>cnt`，由最优解的含义知，最多有 `ans` 个不相交的区间，因此至少需要 `ans` 个点才能覆盖所有区间，而根据算法知，只需 `cnt` 个点就能覆盖全部区间，且 `cnt<ans` ，这与前边分析至少需要 `ans` 个点才能覆盖所有区间相矛盾，故 `ans≤cnt`

代码与 **区间选点** 相同



### 3. 区间分组

有若干个活动，第i个活动开始时间和结束时间是[Si,fi]，同一个教室安排的活动之间不能交叠，求要安排所有活动，少需要几个教室？

有时间冲突的活动不能安排在同一间教室，与该问题的限制条件相同，即最小需要的教室个数即为该题答案。

我们可以把所有开始时间和结束时间排序，遇到开始时间就把需要的教室加1，遇到结束时间就把需要的教室减1,在一系列需要的教室个数变化的过程中，峰值就是多同时进行的活动数，也是我们至少需要的教室数。

**算法分析**

从前往后枚举每个区间，判断此区间能否将其放到现有的组中

1. 如果一个区间的左端点比最小组的右端点要小，`ranges[i].l<=heap.top()` ， 就开一个新组 `heap.push(range[i].r);`

2. 如果一个区间的左端点比最小组的右端点要大，则放在该组，

   `heap.pop(); heap.push(range[i].r);`

   每组去除右端点最小的区间，只保留一个右端点较大的区间，这样heap有多少区间，就有多少组。

   ![区间分组](https://gitee.com/lxxdao/image/raw/master/algorithm/5.1区间分组.png)

**算法流程**

区间分组，在组内区间不相交的前提下，分成尽可能少的组。
而不是尽可能多的组，因为一个区间一组，就是尽可能多组的答案。

等效于把尽可能多的区间塞进同一组，要满足 `range[i].l > heap.top`

`heap` 存储的是每个组的最右的端点，由于是小根堆 `heap.top()` 是对应的最小的最右点。

那如果遇到，塞不进去的情况呢？

就是 `heap.top >= range[i].l`, 当前区间的左端点比最小的右端点还要小，放到任何一组都会有相交部分。

那就需要新开一组，`heap.push(range[i].r)`

1. 把所有区间按照左端点从小到大排序
2. 从前往后枚举每个区间，判断此区间能否将其放到现有的组中
3. `heap`有多少区间，就有多少组

```c++
#include <iostream>
#include <algorithm>
#include <queue>

using namespace std;

const int N = 1e5 + 10;
struct Range{
    int l, r;
    
}range[N];

int main() {
    int n;
    cin >> n;
    for (int i = 0; i < n; i++) {
        int l, r;
        cin >> l >> r;
        range[i] = {l,r};
        
    }
    sort(range, range + n, [&](Range a, Range b)->bool {return a.l < b.l;});

    priority_queue<int, vector<int>, greater<int>> heap;

    for (int i = 0; i < n; i++) {
        if(heap.empty() || heap.top() >= range[i].l) heap.push(range[i].r);
        else {
            int t = heap.top();
            heap.pop();
            heap.push(range[i].r);
        }
    }
    cout << heap.size() << '\n';
    
    return 0;
}
```



### 4. 区间覆盖

**算法分析**

![区间覆盖](https://gitee.com/lxxdao/image/raw/master/algorithm/5.1区间覆盖.jpg)

1. 将所有区间按左端点从小到大排序
2. 从前往后依次枚举每个区间，在所有能覆盖 `start` 的区间中，选择右端点最大的区间然后将 `start` 更新成右端点的最大值

**算法流程**

1. 将所有区间按左端点从小到大排序
2. 从前往后一次枚举每个区间：判断左端点在st之前的区间，循环找到最大右端点，如果右端点也在st之前，说明无法覆盖。下一次枚举的时候依旧用这个区间(i不变)
3. 如果找到左端点在st之前，右端点在st之后的区间，(i++)
4. 每循环一次，没有在前面跳出的话，说明找到了一个区间，res++
5. 如果这个区间右端点能覆盖end，说明能覆盖
6. 把start更新成right，保证后面的区间适合之前的区间有交集，从而形成对整个序列的覆盖
7. 如果遍历了所有的数组，还是没有覆盖最后的end，说明不能成功

**时间复杂度O(nlogn)**

**证明**

- 在剩下所有能覆盖 `start` 的区间中，选择右端点最大的区间，则一定会比前面的选择最优，更快达到`end`，所以该做法一定是最优

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1e5 +  10;

struct Range {
    int l, r;
}range[N];

int main() {
    int st, ed;
    cin >> st >> ed;
    int n;
    cin >> n;
    for (int i = 0; i < n; i ++) {
        int l, r;
        cin >> l >> r;
        range[i] = {l, r};
    }
    sort (range, range + n, [&](Range a, Range b)->bool{return a.l < b.l;});
    int res = 0;
    bool success = false;	// 判断是否成功，初值为失败
    //从前往后一次枚举每个区间
    for (int i = 0; i < n; i++) {
        int j = i, r = -2e9;
        // 如果每次不把r更新为-2e9的话，st和r相等，可能不会出现r<st的情况，如果无法覆盖的话，就会一直循环
        
        // 双指针算法来更新左端点小于st的线段中，能够到大的最远位置
        // 判断左端点在st之前的区间，循环找到最大右端点，如果右端点也在st之前，说明无法覆盖
        while (j < n && range[j].l <= st) { 
            r = max(r, range[j].r);
            j++;
        }
        if (r < st) {	// 当前右端点的最远位置不能到达下一线段的左端点，一定不能覆盖
            res = -1;
            break;
        }
        
        res ++;	// 每循环一次，没有在前面跳出的话，说明找到了一个区间，res++
        
        if (r >= ed) {	// 如果这个区间右端点能覆盖end，说明能覆盖
            success = true;   
            break;
        }
        
        // 把start更新成right，保证后面的区间适合之前的区间有交集，从而形成对整个序列的覆盖
        st = r;
        i = j - 1;
    }
    if (!success) res = -1;
    cout << res << endl;
    return 0;
}
```



## 2. Huffman树

### 合并果子

**哈夫曼树+优先队列** O(nlogn)
这道题目是哈夫曼树的典型模板,也就是每次选择最小的两个果堆,然后将他们合并起来,再次压入堆中。

**时间复杂度**

使用小根堆维护所有果子，每次弹出堆顶的两堆果子，并将其合并，合并之后将两堆重量之和再次插入小根堆中。

每次操作会将果子的堆数减一，一共操作 `n−1` 次即可将所有果子合并成1堆。每次操作涉及到2次堆的删除操作和1次堆的插入操作，计算量是 `O(logn)` 。因此总时间复杂度是 `O(nlogn)`。

**算法证明**
（反证法）最先合并的必定是合并次数最多的，合并次数越多意味着这两个数加的次数越多，加的数越大那么自然答案也会变大，这样就不是最优解了，所以每次必须合并两堆最小的。剩下每次合并同理。

**堆**
大根堆 `priority_queue<xxx> heap`根为最大值
小根堆 `priority_queue<xxx, vector<xxx>, greater<xxx>> heap 根为最小值`

```C++
#include <iostream>
#include <queue>

using namespace std;

int main() {
    int n;
    cin >> n;
    priority_queue<int, vector<int>, greater<int>> heap;
    while (n--) {
        int x;
        cin >> x;
        heap.push(x);
    }
    
    int res = 0;
    while (heap.size() > 1) {
        int a = heap.top();
        heap.pop();
        int b = heap.top();
        heap.pop();
        res += a + b;
        heap.push(a + b);
    }
    cout << res << endl;
    
    return 0;
}
```



## 3. 排序不等式

### 排队打水

**算法分析**

- 安排他们的打水顺序才能使所有人的等待时间之和最小，则需要将打水时间最短的人先打水
- `总时间 = t1 * (n - 1) + t2 * (n - 2) + ... + tn * 1`

**证明**（反证法）

假设不是按照小到大排序，是最优解

不妨设 `ti > ti+1`  

交换前：`ti * (n - i) + ti+1 * (n - i - 1)`

交换后：`ti+1 * (n - i) + ti * (n - i - 1)`

前减后：`ti - ti+1 > 0` 交换后总时间降低，与假设矛盾

```c++
#include <iostream>
#include <algorithm>

using namespace std;

typedef long long LL;

const int N = 1e5;
int q[N];

int main() {
    int n;
    cin >> n;
    for (int i = 0; i < n; i++) cin >> q[i];
    sort(q, q + n);
    LL res = 0; // 结果可能会爆int
    for (int i = 0; i < n; i++) res += q[i] * (n - i - 1);
 
    cout << res << endl;
    
    return 0;
}
```



## 4. 绝对不等式

### 货仓选址

**算法分析**

1. 把 `x[0]~x[N-1]` 排序，设货仓在 `X` 坐标处，`X` 左侧的商店有 `P` 家，右侧的商店有 `Q` 家 。若 `P < Q`，则每把仓库的选址向右移动 `1` 单位距离，距离之和就会变少 `Q - P` 。同理，若 `P > Q` ，则仓库的选址向左移动会使距离之和变小。当 `P==Q` 时为最优解。

2. 因此仓库应该建在中位数处，把x进行排序，

   当N为奇数时，货仓建在 `x[(N - 1)/2]` 处，

   当N为偶数时，仓库建在 `x[(N - 1)/2 + 1]` 处。

**证明**

`f(x) = |x[1]-x| + |x[2]-x| + |x[3]-x| + ... +|x[n]-x|`

`f(x) = (|x[1]-x| + |x[n]-x|) + (|x[2]-x| + |x[n-1]-x|) + ...` //分组的思想

我们取出其中一组 `|x[1]-x| + |x[n]-x|`
就是在 `x[1]` 和 `x[n]` 所在直线上找出一点 `x` ，使得这一点到两端点的距离和最小
这样可以看出 `x` 取在 `x[1]` 和 `x[n]` 之间比较优
此时 `原式==x[n]-x[1]`  取等号的前提是 `x` 在 `x[1]` 与 `x[n]` 之间

`f(x) >= (x[n] + x[n-1] + x[n-2] + ... + x[mid]) - (x[1] + x[2] + x[3] + x[mid])`

当且仅当 `x` 取到最中间的两个数之间的时候等号成立

也就是说，当 `x` 取到 `x[]` 序列的中位数的时候不等式取到最小值

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1e6 + 10;
int q[N];

int main() {
    int n;
    cin >> n;
    for (int i = 0; i < n; i++) cin >> q[i];
    sort(q, q + n);
    int res = 0;
    
    for (int i = 0; i < n; i++) {
        res += abs(q[i] - q[n / 2]);
    }
    
    cout << res << endl;    
    return 0;
}
```



## 5. 推公式

### 耍杂技的牛

这 `N` 头奶牛中的每一头都有着自己的重量 `Wi` 以及自己的强壮程度 `Si`。

一头牛支撑不住的可能性取决于它头上所有牛的总重量（不包括它自己）减去它的身体强壮程度的值，现在称该数值为风险值，风险值越大，这只牛撑不住的可能性越高。

您的任务是确定奶牛的排序，使得所有奶牛的风险值中的最大值尽可能的小。

**算法思路**

按照 `Wi + Si` 从小到大的顺序排，最大的危险系数一定是最小的

**证明**

贪心得到的答案 >= 最优解 显然

贪心得到的答案 <= 最优解

假设 `Wi + Si > W(i+1) + S(i+1)` 最优解不是从小到大排序的

```
				第 i 个位置上的牛				第 i + 1 个位置上的牛
交换前		 W1 + W2 +...+ W(i-1) - Si 		W1 + W2 +...+ Wi - S(i+1)
					-Si							Wi - S(i+1)
+Si+Si+1		   S(i+1)						Wi + Si
交换后		W1 + W2 +...+ W(i-1) - S(i+1)	W1 + W2 +...+ W(i-1) + w(i+1) - Si
					-S(i+1)						W(i+1) - Si
					Si							W(i+1) + s(i+1)
```

`W` 和 `S` 大于等于 0 ，`Wi + Si` 大于 `Si` ,已知 `Wi + Si > W(i+1) + S(i+1)`

交换后 `Si` 和 `W(i+1) + s(i+1)` 的最大值一定小于 `Wi + Si` 

即小于 `S(i+1)` 与 `W(i+1) + s(i+1)` 的最大值

所以交换后最大值严格变小，故最优解从小到大排序。

```C++
#include <iostream>
#include <algorithm>

using namespace std;

typedef pair<int, int> PII;

const int N =5e5 + 10;

int n;
PII cow[N];

int main() {
    cin >> n;
    for (int i = 0; i < n; i ++) {
        int w, s;
        cin >> w >> s;
        cow[i] = {w + s, w};
    }
    sort(cow, cow + n);

    
    int res = -2e9, sum = 0;
    for (int i = 0; i < n; i++) {
        int w = cow[i].second, s = cow[i].first - w;
        res = max(res, sum - s);
        sum += w;
    }
    cout << res << endl;
    return 0;
}
```





------



# 第六章 数学知识

## 1. 质数

质数定义：在大于 $1$ 的整数中，如果只包含 $1$ 和本身这来给你个约数，就被称为质数，或者叫素数。

### 1. 质数的判定—试除法

暴力枚举 $O(n^2)$

```c++
bool is_prime(int n){
    if (n < 2) return false; 		// 2是最小的质数，如果n小于2，那n肯定就不是质数
    for (int i = 2; i < n; i ++) 	// 这个很好理解，从最小的质数2开始枚举到n - 1
        if (n % i == 0){ 			// 如果可以被i整除，说明这个数不是质数
            return false; 	
    return true; 
}
```

**优化：**

$n$ 中最多只能包含一个 $\sqrt{n}$ 的质因子

一个数的因数都是成对出现的：例如12的因数有3和4,2和6。

所以我们可以只枚举较小的那一个，即 $\sqrt{n}$ ，假设较小的为 $d$，较大的为 $n/d$

$d ≤ \frac{n}{d}$ 、 $d^2 ≤ n$ 、$d ≤ \sqrt{n}$

但不建议用 `sqrt(n)`，运行速度慢；`i * i <= n` 也不建议，可能爆 int

使用 `i <= n / i` 避免上述情况

**时间复杂度**$O(\sqrt{n})$

```c++
bool is_prime(int n) {
    if (n < 2) return false;
    for (int i = 2; i <= n / i; i++) 
        if (n % i == 0)
            return false;
    return true;
}
```



### 2. 分解质因数—试除法

- $x$ 的质因子最多只包含一个大于 $\sqrt{x}$ 的质数。如果有两个，这两个因子的乘积就会大于 $x$，矛盾。
- $i$ 从 $2$ 遍历到 $\sqrt{x}$。 用 $x / i$，如果余数为 $0$，则 $i$ 是一个质因子。
- $s$ 表示质因子 $i$ 的指数，$x /= i$ 为 0，则 $s++$， $x = x / i$ 。
- 最后检查是否有大于 $x$ 的质因子，如果有，输出。

**时间复杂度**$O(\sqrt{n})$ 	O(logn)  ~ O(sqrt(n))

```c++
void divide(int x) {
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0) {
            int s = 0;
            while (x % i == 0) x /= i, s ++ ;
            cout << i << ' ' << s << endl;
        }
    if (x > 1) cout << x << ' ' << 1 << endl;
    cout << endl;
}
```



### 3. 筛质数

**朴素筛法$O(nlogn)$**

```c++
int primes[N], cnt;     // primes[]存储所有素数 cnt存储质数数量
bool st[N];             // st[x]存储x是否被筛掉

void get_primes(int n) {
    for(int i = 2; i <= n; i++){
    	if(!st[i]) primes[cnt++] = i;	// 把素数存起来
        for(int j = i + i; j <= n; j += i) st[j] = true;	
        // 不管是合数还是质数，都用来筛掉后面它的倍数
}
```

**诶氏筛法 $O(nloglogn)$**

```c++
void get_primes1() {
    for(int i = 2; i <= n; i++) {
        if(!st[i]) {
            primes[cnt++] = i;
            for(int j = i + i; j <= n; j += i) st[j] = true;
            // 可以用质数就把所有的合数都筛掉；
        }
    }
}
```

**线性筛法$O(n)$**

1. 因为 `prime` 中素数是递增的，所以如果 `i % prime[j] != 0` 代表 `i` 的最小质因数还没有找到，即 `i` 的最小质因数大于 `prime[j]` 。也就是说 `prime[j]`就是 `i * prime[j]`的最小质因数，于是 `i * prime[j]` 被它的最小质因数筛掉了。
2. 如果当 `i % prime[j] == 0` 时，代表 `i` 的最小质因数是 `prime[j]`，那么`i * prime[j+k]` 这个合数的最小质因数就不是 `prime[j+k]` 而是 `prime[j]`。所以 `i * prime[j+k]` 应该被 `prime[j]` 筛掉，而不是后续的 `prime[j+k]`。于是在此时break。
3. 综上所述达到了每个数仅筛一次的效果，时间复杂度$O(n) $

```c++
void get_primes(int n) {
    for (int i = 2; i <= n; i ++ ) {
        if (!st[i]) primes[cnt ++ ] = i;
        for (int j = 0; primes[j] <= n / i; j ++ ) {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}
```



## 2. 约数

### 1. 试除法求约数

**时间复杂度：$O(\sqrt{n})$**

```c++
vector<int> get_divisors(int n) {
    vector<int> res;
    for (int i = 1; i <= n / i; i ++ )
        if (n % i == 0) {
            res.push_back(i);
            if (i != n / i) res.push_back(n / i);	// 避免 i==n/i, 重复放入(n是完全平方数)
        }
    sort(res.begin(), res.end());
    return res;
}
```



### 2. 约数个数和约数之和 

$N = p_1^{α1}∗p_2^{α2}∗⋯∗p_k^{αk}$

约数个数： $res = (α_1 + 1)(α_2 + 1)...(α_k + 1)$

约数之和： $sum = (p_1^0 + P_1^1 +...+ p_1^{c}) * (p_2^0 + P_2^1 +...+ p_2^{c}) * ... * (p_k^0 + p_k^1 + ... + p_k^{ck})$

因为任何一个约数 $d$ 可以表示成 $d = p_1^{β1}∗p_2^{β2}∗⋯∗p_k^{βk} ， 0 ≤ β_i ≤ α_i$

每一项的 $β_i$ 如果不同，那么约数 $d$ 就不相同（算数基本定理，每个数的因式分解是唯一的）

所以 $n$ 的约数就跟 $β_i$ 的选法是一一对应的 

$β_1$ 一共有 $0∼α_1$ 种选法 、$β_2$ 一共有 $0∼α_2$ 种选法、$β_k$ 一共有 $0∼α_k$ 种选法

乘法原理，一共有 $res$ 个约数

**时间复杂度：$O(n)$**

```c++
unordered_map <int,int> primes; // 用来存每个质数的底数和指数
void get_divisors(int x) {
    for(int i = 2;i <= x / i;i ++) {
      	while(x % i == 0) { //分解质因子
      		x /= i;
  			primes[i] ++; //b把这个因数的指数加1
  		}
    }
    if(x > 1) primes[x] ++; //如果这个数还没被除完，那么吧这个数的指数加1
}
int main() {
    int x, cin >> x;
    get_divisors(x);
// 求个数
	LL res = 1;
	for (auto prime: primes) res = res * (prime.second + 1) % mod;

// 求和
    LL sum = 1;
    for (auto prime: primes) {
        int p = prime.first, a = prime.second;
        LL t = 1;
        while (a--) t = (t * p + 1) % mod;
        sum = sum * t % mod;
    }
    
    return 0;
}
```



### 3. 最大公约数

辗转相除法/欧几里得算法

核心公式：`(a, b) = (b, a mod b)`

```c++
int gcd(int a, int b) {
    return b ? gcd(b, a % b) : a;
}
```



## 3. 欧拉函数

### 1. 欧拉函数的定义

$1∼N$ 中与 $N$ 互质的数的个数被称为欧拉函数，记为 $ϕ(N)$。

若在算数基本定理中，$N=p_1^{a_1}p_2^{a_2}...p_m^{a_m}$，$p_i$ 为质数，则：

$ϕ(N) = N×\frac{p_1-1}{p1}×\frac{p_2-1}{p2}×…×\frac{p_m-1}{pm}$

证明：

![6.3欧拉函数](https://gitee.com/lxxdao/image/raw/master/algorithm/6.3欧拉函数.png)

```c++
int phi(int x) {
    int res = x;
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0) {
            res = res / i * (i - 1);
            while (x % i == 0) x /= i;
        }
    if (x > 1) res = res / x * (x - 1);
    return res;
}
```



### 2. 筛法求欧拉函数

基本思路：

欧拉函数是一个 积性函数 就是说 m,n互素 则：$φ(mn)=φ(m)∗φ(n)$

1. 如果 $x$ 是一个素数 $P$

    $x=p, φ(x)=x∗(1−\frac{1}{p})=p∗(\frac{p−1}{p})=p−1$

2. 不是素数的时候 用最小的素因子去计算

    1. `i % p == 0`      $gcd(i,p)=p$      $φ(i)=i∗(1−\frac{1}{p_1})∗…∗(1−\frac{1}{p_k})$

        因为 $p$ 是 $i$ 的因子所以在计算 $φ(i)$ 的已经算过 $p$ 了

        $φ(i∗p)=p∗i∗(1−\frac{1}{p_1})∗…∗(1−\frac{1}{p_k}) φ(i∗p)=p∗φ(i)$

    2. `i % p!=0`     $gcd(i,p)=1φ(i∗p)=φ(i)∗φ(p)=φ(i)∗(p−1)$





1. 质数 $i$ 的欧拉函数即为 $phi[i]=i-1$： `1~i-1` 均与 $i$ 互质，共 $i-1$ 个。
2. $phi[primes[j] * i]$ 分为两种情况：
    - `i % primes[j] == 0`时：$primes[j]$ 是 $i$ 的最小质因子，也是 $primes[j] * i$ 的最小质因子，因此 $1 - 1 / primes[j]$ 这一项在 $phi[i]$ 中计算过了，只需将基数 $N$ 修正为 $primes[j]$ 倍，最终结果为 $phi[i] * primes[j]$。
    - `i % primes[j] != 0`：$primes[j]$ 不是 $i$ 的质因子，只是 $primes[j] * i$ 的最小质因子，因此不仅需要将基数 $N$ 修正为 $primes[j]$ 倍，还需要补上 $1 - 1 / primes[j]$ 这一项，因此最终结果 $phi[i] * (primes[j] - 1)$。

```c++
int primes[N], cnt;			// primes[]存储所有素数,cnt存储素数个数
int phi[N];					// 存储每个数的欧拉函数
bool st[N];					// st[x]存储x是否被筛掉
LL get_eulers(int n) {
    phi[1] = 1;
    for (int i = 2; i <= n; i++) {
        if (!st[i]) {
            primes[cnt++] = i;
            phi[i] = i - 1;
        }
        for (int j = 0; primes[j] <= n / i; j++) {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0) {        
               phi[primes[j] * i] = phi[i] * primes[j];
               break; 
            }
            phi[primes[j] * i] = phi[i] * (primes[j] - 1);
        }
    }
    LL res = 0;
    for (int i = 1; i <= n; i++) res += phi[i];
    return res;
}
```





## 4. 快速幂

**基本思路**

1. 预处理出 ${a^2}^0$, ${a^2}^1$, ${a^2}^2$,…, ${a^2}^{logb}$ 这 $b$ 个数 

2. 将 $a^b$ 用  ${a^2}^0$, ${a^2}^1$, ${a^2}^2$,…, ${a^2}^{logb}$ 这 $b$ 种数来组合

    即组合成 $a^b = {a^2}^{x1} \times {a^2}^{x2} \times ... \times  {a^2}^{xt} = {a}^{2^{x1} + 2^{x2} + ... + 2^{xn}}$ 即用二进制表示

    为什么 $b$ 可用 ${a^2}^0$, ${a^2}^1$, ${a^2}^2$,…, ${a^2}^{logb}$  这 b 个数来表示？

    二进制可以表示所有数,且用单一用二进制表示时, $b$ 单一表示最大可表示为二进制形式的 $2^{logb}$

**注意**

- b&1就是判断 b 的二进制表示中第 0 位上的数是否为 1 ,若为 1 , b&1=true ,反之 b&1=false

**快速幂之迭代版** $O(n∗logb)$

```c++
#include <iostream>

using namespace std;

typedef long long LL;

LL qmi(int a, int b, int p) {
    LL res = 1;
    while (b) {
        if (b & 1) res = res * a % p;
        a = a * (LL)a % p;
        b >>= 1;
    }
    return res;
}

int main() {
    int n;
    cin >> n;
    while (n--) {
        int a, b, p;
        cin >> a >> b >> p;
        cout << qmi(a, b, p) << endl;
    }
    return 0;
}
```
