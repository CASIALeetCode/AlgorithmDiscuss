# 一、查找知识点Review

查找方法：

*	线性表查找（顺序查找，二分查找，分块查找）
*	树表查找（二叉排序树，平衡二叉树，高级树：红黑树，B树，R树）
*	哈希查找


# 二、在各种数据结构上的查找
1. 数组中查找
2. 链表中查找
3. 矩阵中查找
4. 树中查找
5. 字符串中的查找
6. 图的查找

## 1. 数组中查找

	一些例题：
	*	例0 局部最大值
	*	例1 局部最小值
	*	例2 第一个缺失的正整数（leetCode41)
	*	例3 元素间的最大距离
	*	例4 只出现一次的数
	*	例5 众数问题（出现次数超过一定频率的数）
	*	例6 “前缀和”的应用
	*	例7 寻找最小的k个数
	*	例8 寻找和为定值的两个或以上的数

### 例0 局部最大值（LC162: Find Peak Element）
**Problem**
 
	A peak element is an element that is greater than its neighbors.
	Given an input array where num[i] ≠ num[i+1], find a peak element and return its index.
	The array may contain multiple peaks, in that case return the index to any one of the peaks is fine.
	You may imagine that num[-1] = num[n] = -∞.
	For example, in array [1, 2, 3, 1], 3 is a peak element and your function should return the index number 2.
	
	Note:
	Your solution should be in logarithmic complexity.
**Sulution**
	
	三种解法
**Code**

	//解法一：O(n)，比较次数2n
	/*一个元素e与其相邻左边元素l和右边元素r进行比较，若e>l&&e>r，则e是一个局部最大值，返回其下标；否则继续向后比较。
	若数组是降序的，第一次就满足了，返回第一个元素。
	若数组是升序的，在最后一个元素才满足。
	其他，在中间的某个peak返回。*/	
	class Solution {
	public:
	    int findPeakElement(const vector<int> &num) {//stupid O(n), compare 2n times.
	        int sz = num.size();
			for(int index = 0; index < sz; index++)
			{
				bool isleft = true;
				bool isright = true;
				if(index > 0) isleft = num[index-1] < num[index] ? true : false;
				if(index <= sz-2) isright = num[index] > num[index+1] ? true : false;
				if(isleft && isright) return index;
			}
			return -1;
	    }
	};
	
	
	/*方法2：顺序遍历。O(n)，比较次数n
	本题的一个重要特点是，从第一个元素开始，若其大于相邻的后续元素，则第一个元素就是一个局部最大值，返回即可。
	若其小于相邻的后续元素，则第二个元素大于第一个元素。如此，一一遍历数组，第一次出现，第i个元素若大于其相邻后续元素，
	则该元素就是一个局部最大值，返回即可。代码如下*/
	class Solution {
	public:
	    int findPeakElement(const vector<int> &num) {
	        for(int i=1;i<num.size();i++){
	            if(num[i]<num[i-1])
	                return i-1;
	        }
	        return num.size()-1;
	    }
	};
	
	
	
	/*
	方法3：二分查找, O(logN)
	思路：如果中间元素大于其相邻后续元素，则中间元素左侧(包含该中间元素）必包含一个局部最大值。如果中间元素小于其相邻后续元素，则中间元素右侧必包含一个局部最大值。*/
	class Solution {
	public:
	    int findPeakElement(const vector<int> &num) {
	        int left=0,right=num.size()-1;
	        while(left<=right){
	            if(left==right)
	                return left;
	            int mid=(left+right)/2;
	            if(num[mid]<num[mid+1])
	                left=mid+1;
	            else
	                right=mid;
	        }
	    }
	};

### 例1 局部最小值：
**Problem**

	一个给定的不包含相同元素的整数数组，每个局部极小值的定义是一个值比左右相邻的（如果存在）都小的值，求它的一个局部最小值 （国外某公司面试题）
	
**Sulution**

	分析： 局部最小值的存在性，全部数组的最小值显然是一个解。 O(n)?
	复杂度 O(logn)的解法：
	我们规定数组下标a[1..n]，并定义a[0] = a[n + 1] = ∞, 我们有a[1] < a[0], a[n] < a[n + 1]
	结论： 子数组a[x..y] 若 a[x] < a[x – 1] , a[y] < a[y + 1]，则它包含一个局部极小值

	mid = (x + y) / 2,二分，两个子数组a[x..mid], a[mid + 1..y]
	若a[mid] < a[mid + 1], 则子数组a[x..mid]满足a[x] < a[x - 1], a[mid] < a[mid + 1]
	反之a[mid] > a[mid + 1], 则子数组a[mid + 1..y]满足a[mid + 1] < a[mid], a[y] < a[y + 1]
	
**Code**

	略

**similar Problem** 

	循环有序数组(或叫旋转数组）最小值、查找元素x（Leetcode 153,154)。

#### 1.1 LC153 Find Minimum in Rotated Sorted Array

**Problem**
	
	Suppose a sorted array is rotated at some pivot unknown to you beforehand.
	(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).
	Find the minimum element. You may assume no duplicate exists in the array.
	Hide Tags Array Binary Search

	（未旋转的sorted array是升序的）

**Code**

	//【法一】直接遍历一遍数组进行查找，时O(n),空O(1)
	class Solution {
	public:
	    int findMin(vector<int>& nums) {
	        if(nums.size()==0)  return -1;
	        
	        int res = 0;
	        for(int i=0; i<nums.size()-1; ++i){
	            if(nums[i]<nums[i+1])
	                continue;
	            else{
	                res = i+1;
	                break;
	            }
	        }
	        return nums[res];
	    }
	};
	
	// 【法二】二分查找. 时O(logn),空O(1)
	http://www.cnblogs.com/ganganloveu/p/4081438.html
	class Solution {
	public:
	    int findMin(vector<int>& nums) {
	        if(nums.empty())
	            return 0;
	        if(nums.size() == 1)
	            return nums[0];
	        int n = nums.size();
	        int low = 0;
	        int high = n-1;
	        while(low < high && nums[low] > nums[high])
	        {
	            int mid = low + (high-low)/2;
	            if(nums[mid] < nums[low])   // mid is in second part
	                high = mid;
	            else if(nums[mid] == nums[low]) // since (low<high)-->(low+1==high)
	                return nums[high];  // nums[low]>nums[high]
	            else
	                low = mid+1;
	        }
	        return nums[low];
	    }
	};

#### 1.2 LC154 Find Minimum in Rotated Sorted Array II

**problem**

	Follow up for "Find Minimum in Rotated Sorted Array":
	What if duplicates are allowed?
	Would this affect the run-time complexity? How and why?
	Suppose a sorted array is rotated at some pivot unknown to you beforehand.
	
	(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).
	Find the minimum element. The array may contain duplicates.
	Hide Tags Array Binary Search

**Code**

	【法一】：直接法，未旋转的sorted array是升序的。时O(n), 空O(1)
	class Solution {
	public:
	    int findMin(vector<int>& nums) {
	        if(nums.size()==0)  return -1;
	        
	        int res = 0;
	        for(int i=0; i<nums.size()-1; ++i){
	            if(nums[i]<=nums[i+1])
	                continue;
	            else{
	                res = i+1;
	                break;
	            }
	        }
	        return nums[res];
	    }
	};
	
	
	【法二】二分查找时O(logn), 空O(1)
	http://www.cnblogs.com/ganganloveu/p/4081438.html
	与Find Minimum in Rotated Sorted Array对照看，一共有两处修改。
	1、在无重复元素时，首尾元素相等代表指向同一个位置，因此程序直接返回即可。
	然而当存在重复元素时，该条件并不能表示指向同一个位置，因此
	nums[low] > nums[high]改为nums[low] >= nums[high]
	2、在无重复元素时，中间元素与首元素相等，表示一共只有两个元素，low与high各指向一个。
	由于while循环中限制的大小关系，因此返回nums[high]即为最小值。
	然而当存在重复元素时，该条件并不能表示一共只有low和high指向的两个元素，
	而是说明low指向的元素重复了，因此删除其一，low ++即可。
	class Solution {
	public:
	    int findMin(vector<int>& nums) {
	        if(nums.empty())
	            return 0;
	        if(nums.size() == 1)
	            return nums[0];
	        int n = nums.size();
	        int low = 0;
	        int high = n-1;
	        while(low < high && nums[low] >= nums[high])
	        {
	            int mid = low + (high-low)/2;
	            if(nums[mid] < nums[low])   // mid is in second part
	                high = mid;
	            else if(nums[mid] == nums[low])
	                low ++;
	            else
	                low = mid+1;
	        }
	        return nums[low];
	    }
	};



#### 1.3 一个严格单增的数组，查找a[x] == x的位置
**Solution**

	二分查找。mid = (low+high)/2
	若a[mid]==x, return mid;
	若a[mid]<x, 在右半部分查找。low = mid+1;
	若a[mid]>x, 在左半部分查找。high = mid-1;
**Code**

	略

### 例2 第一个缺失的正整数（leetCode41)
**Problem**

给一个数组，找到从1开始第一个不在里面的正整数。 例如［3,4,-1,1]输出2。 

**Sulution**

	桶排序问题
	方法一：
		本质上是桶排序 (bucket sort)，每当 A[i]!= i+1 的时候，将 A[i] 与 A[A[i]-1] 交换，直到无法交换为止，终止条件是 A[i]== A[A[i]-1]。A[i]==A[A[i]-1]说明数组中存在两个A[i]的值。		
	方法二：基于交换，更简洁。
		分析: 数组下标从0开始，让a[i] == i + 1，每次循环:
    		要么i ＋ 1
    		要么n – 1
    		要么有一个数被放到正确的位置。
		说明：
		[0,...,i-1]是满足条件的区间，i代表最后一个满足条件的数的下一个位置。
		n从后往前扫，代表不满足条件的数的下标。每次(A[i]<=i|| A[i]>n || A[i]==A[A[i]-1])时把A[i]=A[--n];
	
**Code**

	/******* method 1: bucket sort. 时间复杂度 O(n)，空间复杂度 O(1)******/
	class Solution {
	public:
	    int firstMissingPositive(vector<int>& A) {
	        int n = A.size();
	        
	        bucket_sort(A, n);
	        for (int i = 0; i < n; ++i)
	            if (A[i] != (i + 1))
	                return i + 1;
	        return n + 1;
	    }
	private:
	    static void bucket_sort(vector<int>& A, int n) {
	        for (int i = 0; i < n; i++) {
	            while (A[i] != i + 1) {
	                if (A[i] <= 0 || A[i] > n || A[i] == A[A[i] - 1])
	                    break;
	                swap(A[i], A[A[i] - 1]);
	            }
	        }
	    }
	};

	/******* method 2: 时间复杂度 O(n)，空间复杂度 O(1)******/
	class Solution {
	public:
	    int firstMissingPositive(vector<int>& A) {
	        //[0,i) is 1...i
	        int n = A.size();
	        for(int i=0; i<n; ){
	            if(A[i]==i+1)   ++i;
	            else if(A[i]<=i|| A[i]>n || A[i]==A[A[i]-1])
	                A[i] = A[--n];
	            else
	                swap(A[i], A[A[i]-1]);
	        }
	        return n+1;
	    }
	};


### 例2 元素间的最大距离（leetCode164：Maximum Gap)
**Problem**

	Given an unsorted array, find the maximum difference between the successive elements in its sorted form.	
	Try to solve it in linear time/space.	
	Return 0 if the array contains less than 2 elements.	
	You may assume all elements in the array are non-negative integers and fit in the 32-bit signed integer range.
	
	Tags: Sort

**Sulution**

	方法一：先排序O(nlogn)，再一次遍历，得到maxGap.虽然不满足O(n)的时间要求，但是最直观的想法。
	方法二：用桶排序
    // 算出相邻两个桶之间的最大差值
    // 如果是平均分布，则桶的数目和元素的数目相同时，其排序的时间复杂度是0(n)
    // 我们假设桶的个数和元素的数目相同，若是平均分布，则每个桶里有一个数，而若某个桶里有两个以上的桶时，这时必有至少一个是空桶，那么最大间隔可能就落在空桶的相邻两个桶存储的数之间，最大间隔不会落在同一个桶的数里，因此我们不需要对每个桶再排一次序，只需要记录同一个桶的最大值和最小值，算出前一个有最大值的桶和后一个有最小值的桶之差，则可能是最大间隔
    //步骤：1.算好用的桶的个数，用最大元素和最小元素算出平均间隔，记录在平均间隔上的最大值和最小值，
    // 2. 再算出前一个间隔里的最大值和后一个间隔里的最小值之差，取最大的一个，

**Code**

	//方法一：先排序O(nlogn)，再一次遍历，得到maxGap.虽然不满足O(n)的时间要求，但是最直观的想法。
	class Solution {
	public:
	    int maximumGap(vector<int>& nums) {
	        if(nums.empty() || nums.size() == 1)
	            return 0;
	        sort(nums.begin(), nums.end());
	        int ret = 0;
	        for(int i = 1; i < nums.size(); i ++)
	            ret = max(ret, nums[i]-nums[i-1]);
	        return ret;
	    }
	};

	//方法二：用桶排序。time:o(n), space:O(n)
	class Solution {
	public:
	    int maximumGap(vector<int> &num) {
	        if (num.size() < 2) return 0;
	        // 1. 算出用的桶数：取平均间隔，再用最大值和最小值之差除以间隔，得到桶数
	        // 因为假设所有值都是平均分布的时候，如此取桶数可得时间复杂度是0(n)
	        auto maxVal = *max_element(num.begin(), num.end());
	        auto minVal = *min_element(num.begin(), num.end());
	        int agGap = ceil((double)(maxVal - minVal) / (num.size()-1)); // 平均间隔
	        int bucketCount = ceil((double)(maxVal - minVal) / agGap);
	        // 2. 记录每个桶的最大值和最小值
	        vector<pair<int, int> > buckets(bucketCount, make_pair(INT_MIN, INT_MAX)); // 初始化桶
	        for (auto val : num){
	            if (val == maxVal || val == minVal) continue;
	            int bucketNum = (val - minVal) / agGap;
	            if (val > buckets[bucketNum].first) 
	                buckets[bucketNum].first = val; // 存储最大值
	            if (val < buckets[bucketNum].second) buckets[bucketNum].second = val; // 存储最小值
	        }
	        // 3. 算出最大间隔
	        int maxGap(0), lastMax(minVal);
	        for (auto bucket : buckets){
	            if (bucket.first == INT_MIN) continue; // 空桶
	            int curMax(bucket.first), curMin(bucket.second);
	            maxGap = max(maxGap, curMin - lastMax);
	            lastMax = curMax;
	        }
	        maxGap = max(maxGap, maxVal - lastMax); //maxVal - lastMax：防止所有元素在一个桶里
	        return maxGap;
	    }
	};

**Similar Problem**

	有N个自然数（1-N），乱序存放在下标为1~n的数组空间中，实现对数组从小到大排序，不能直接使用赋值操作，要求时间复杂度O(n)，空间复杂度O(1)。（一道百实习生度面试题）
**Sulution**

	本质上是桶排序
	这个条件比较特殊，所以可以实现这个条件比较特殊，所以可以实现O(n)的算法，也可以不交换2个数。如果a[j]!=j，则把a[j]中的元素放到它应在的位置，同时把要被覆盖的元素取出来。不停循环，一直到a[j] = j;

**Code**

	简单写一下代码：
	for (i=1; i<=n; i++)
	{
	    j = i;
	    while (a[j] != j)
	    {
	        cache = a[a[j]];
	        a[a[j]] = a[j];
	        a[j] = cache;
	    }
	}
	虽然for 中嵌套了一个while，但最多循环2次。


### 例4 只出现一次的数
**Problem**
	一个数组，所有元素都出现了两次，只有两个数只出现了一次，求这两个数。

**Sulution**

	分析：所有数做异或，则出现两个次的数相抵消，那么最终的结果就是那两个出现一次的数x和y的异或结果，即x xor y ，且这个值非0
	既然x xor y非0，我们可以找到二进制表示中某一个为1的位（bit）（例如最低位），把所有的数按这位为1和为0分开。
	在该位为0和为1的数中，各有一个数只出现一次。 （一个是x，另一个是y)

**Code**

	伪代码:
	int xXory = 0;
	for (int i = 0; i < n; ++i) xXory ^= a[i];
	int mask = 1;
	for (; (xXory & mask) == 0; mask <<= 1);
	int x = 0, y = 0;
	for (int i = 0; i < n; ++i) 
			if (a[i] & mask) x ^= a[i];
			else y ^= a[i];

 **思考题**

	1、Leetcode 137 除一个外，所有数出现了3次，求那个数＊ （难）
	2、1-100，缺少了两个数，求这两个数？ 位运算？解方程？
		求x+y，和x^2 +y^2的值，解方程。


### 例5 众数问题（出现次数超过一定频率的数）

#### 1、Majority Element（Leetcode169)：出现次数超过一半的数	

**Problem**

	Majority Element（Leetcode169)
	Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.
	You may assume that the array is non-empty and the majority element always exist in the array.

**Sulution**

	七种方法：
	(1)时间复杂度: O(n2) — 蛮力法: 依次检查每一个元素是否为众数
	(2)时间复杂度: O(n), 空间复杂度: O(n) — 哈希表: 维护一个每一个元素出现次数的哈希表, 然后找到出现次数最多的元素
	(3)时间复杂度: O(n log n) — 排序: 在排序后找出连续重复出现次数最多的元素
	(4)平均时间复杂度: O(n), 最坏复杂度: 无穷大 — 随机算法: 随机选取一个元素计算其是否为众数. 如果不是, 就重复上一步骤直到找到为止。 由于选出众数的概率 > 1 / 2, 因此期望的尝试次数 < 2
	(5)时间复杂度: O(n log n) — 分治法: 将数组拆成2半, 然后找出前一半的众数A和后一半的众数B。则全局众数要么是A要么是B。 如果 A == B, 则它自然而然就是全局众数。 如果不是, 则A和B都是候选汇众数, 则至多只需要检查这两个元素的出现次数即可。 时间复杂度, T(n) = T(n/2) + 2n = O(n log n).
	(6)时间复杂度: O(n) — Moore投票算法: 我们维护一个当前的候选众数和一个初始为0的计数器。遍历数组时，我们看当前的元素x:
	    如果计数器是0, 我们将候选众数置为 x 并将计数器置为 1
	    如果计数器非0, 我们根据x与当前的候选众数是否相等对计数器+1或者-1
	    一趟之后, 当前的候选众数就是所求众数. 时间复杂度 = O(n).
	(7)时间复杂度: O(n) — 位操作法: 我们需要32次迭代, 每一次计算所有n个数的第i位的1的个数。由于众数一定存在，那么或者1的个数 > 0的个数 或者反过来(但绝不会相同)。 众数的第i位一定是计数较多数字。

**Code**


	/******哈希，记录每个次数，O(n)的时间和空间。*********/
	class Solution {
	public:
	     int majorityElement(vector<int> &num) {
	         unordered_map<int, int> umap;
	         for (int i = 0; i < num.size(); i++)
	         {
	            umap[num[i]]++;
	         }
	         for (unordered_map<int, int>::iterator itr = umap.begin(); itr != umap.end(); itr++)
	         {
	            if (itr -> second > num.size()/2)
	                return itr -> first;
	         }
	     }
	};


	/*摩尔投票法 Moore Voting，需要O(n)的时间和O(1)的空间，比前一种方法更好。这种投票法先将第一个数字假设为众数，然后把计数器设为1，比较下一个数和此数是否相等，若相等则计数器加一，反之减一。然后看此时计数器的值，若为零，则将当前值设为候选众数。以此类推直到遍历完整个数组，当前候选众数即为该数组的众数。*/
	////注意有的题目要数一下x出现次数是否确实超过一半。（众数可能不存在）
	class Solution {
	public:
	    int majorityElement(vector<int> &num) {
	        int Idx = 0, count = 1;
	        for (int i = 1; i < num.size(); ++i) {
	            num[Idx] == num[i] ? ++count : --count;
	            if (count == 0) {
	                Idx = i;
	                count = 1;
	            }
	        }
	        return num[Idx];
	    }
	};

	/***位操作法***/
	//因为给定的是32位数，并且众数一定存在，那么每一位去考虑的话，对于每一位1或者0多的肯定是属于众数的。这样就知道众数了。
	class Solution {
	public:
	     int majorityElement(vector<int> &num) {
	         int bitCnt[32];
	         memset(bitCnt, 0, sizeof(bitCnt));
	         
	         for (int i = 0; i < num.size(); i++)
	         {
	             for (int j = 0; j < 32; j++)
	             {
	                if (num[i] & (1 << j))
	                    bitCnt[j]++;
	             }
	         }
	         
	         int ans = 0;
	         for (int i = 0; i < 32; i++)
	         {
	            if (bitCnt[i] > num.size()/2)
	                ans += (int)pow(2, i);
	         }
	         return ans;
	    }
	};


#### 1、Majority Element II（Leetcode229)：出现次数超过1/3的数	

**Problem**

	Given an integer array of size n, find all elements that appear more than ⌊ n/3 ⌋ times. The algorithm should run in linear time and in O(1) space.	
	Hint:	
	How many majority elements could it possibly have?
	Do you have a better hint? Suggest it!
**Solution**
	
	摩尔投票法
	/*
	这道题让我们求出现次数大于n/3的众数，而且限定了时间和空间复杂度，那么就不能排序，也不能使用哈希表，这么苛刻的限制条件只有一种方法能解了，那就是摩尔投票法 Moore Voting，这种方法在之前那道题Majority Element 求众数中也使用了。题目中给了一条很重要的提示，让我们先考虑可能会有多少个众数，经过举了很多例子分析得出，任意一个数组出现次数大于n/3的众数最多有两个，使用投票法的核心是找出两个候选众数进行投票，需要两遍遍历，第一遍历找出两个候选众数，第二遍遍历重新投票验证这两个候选众数是否为众数即可，选候选众数方法和前面那篇Majority Element 求众数一样，由于之前那题题目中限定了一定会有众数存在，故而省略了验证候选众数的步骤，这道题却没有这种限定，即满足要求的众数可能不存在，所以要有验证。	*/
**Code**

	//time:O(n),space:O(1)
	class Solution {
	public:
	    vector<int> majorityElement(vector<int>& nums) {
	        vector<int> res;
	        int m = 0, n = 0, cm = 0, cn = 0;
	        for (auto &a : nums) {
	            if (a == m) ++cm;
	            else if (a ==n) ++cn;
	            else if (cm == 0) m = a, cm = 1;
	            else if (cn == 0) n = a, cn = 1;
	            else --cm, --cn;
	        }
	        cm = cn = 0;
	        for (auto &a : nums) {
	            if (a == m) ++cm;
	            else if (a == n) ++cn;
	        }
	        if (cm > nums.size() / 3) res.push_back(m);
	        if (cn > nums.size() / 3) res.push_back(n);
	        return res;
	    }
	};

#### 3、如何找到所有出现次数严格大于总数1 / k的数？

 	提示： 保存(k – 1)个数，如何查找？hash? map? http://blog.csdn.net/pi9nc/article/details/9355293


### 例6 “前缀和”的应用	

**Problem**

**Solution**

**Code**


### 例7 寻找最小的k个数

**Problem**

	给定一个乱序数组，返回最小的k个数。（剑指offer30:P167)

**Solution**

	（以前讨论时讲过）
	法一：利用Partition的方法，time:O（n),会修改输入数组.
	法二：创建一个大小为k的容器，time:O(nlogk),不会修改输入数组，特别适合处理海量数据

**Code**
	
	略


### 例8 寻找和为定值的两个或者多个数

**Problem**
	
	寻找和为定值的多个数(Two Sum, 3Sum, 3Sum Closest ,4Sum)

**Solution**
	
	（在数组和链表中讲过这个问题）
	法1：排序，再两边夹。
	法2：hashtable

**Code**
	
	略


## 2. 链表中查找

	常见题目：
	*	查找倒数第k个节点
	*	找到环的入口节点
	*	...	

**Solution**
	
	链表和很多题目都是利用快慢两个指针，或多个指针。


## 3. 矩阵中查找——排序（或部分排序）矩阵上的搜索问题

### Search a 2D Matrix(LeetCode3)
**[问题描述]**
	
	整个矩阵按行完全排好序，在矩阵中搜索是否含有某个值，含有返回true,否则返回false.
	（加入图）
 
**[思路]**

	由于整个矩阵完全排好序，其实可以直接把这个二维数组看成一个M*N大小的一维数组，这样一次二分搜索就可以了。
	代码更加简洁。时间复杂度是log(M * N) = logM + logN。

**[代码实现]**

	public boolean find(int[][] matrix, int target) {
	    int numRows = matrix.length;
	    if (numRows == 0) return false;
	    int numCols = matrix[0].length;
	    int low = 0, high = numRows * numCols - 1;
	    int mid, col, row;
	 
	    while (low <= high) {
	        mid = low + (high - low) / 2;
	        col = mid % numCols;
	        row = mid / numCols;
	 
	        if (matrix[row][col] == target) {
	            return true;
	        } else if (matrix[row][col] > target) {
	            high = mid - 1;
	        } else {
	            low = mid + 1;      
	        }
	    }
	 
	    return false;
	}


### 杨氏矩阵查找
**[问题描述]**

	在一个m行n列二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。（剑指offer3）
	例如：下面的二维数组就是每行、每列都递增有序。如果在这个数组中查找数字6，则返回true；如果查找数字5，由于数组不含该数字，则返回false。
 
**[解法一分析]**   
                                                            
	沿着对角线进行线性查询：因为每行和每列都排好序，以矩阵右上角为起点，沿着对角线扫描。
	1）如果该元素等于目标元素，则找到目标；
	2）如果该元素大于目标元素，则可以排除当前列，坐标往左移1个单位；
	3）如果该元素小于目标元素，则可以排除当前行，坐标往下移1个单位。
	例外一种查找策略是从左下角开始查找，与上述策略恰好相反。
	 
	（图片）
	算法实现——时间复杂度为O(m+n)，空间复杂度为O(1).

**Code**
	
	public boolean find(int[][] matrix, int elem) {
	    // start from the top right element
	    int row = 0, col = matrix[0].length - 1;
	    while (row < matrix.length && col >= 0) {
	        if (matrix[row][col] == elem) {
	            return true;
	        } else if (matrix[row][col] > elem) {
	            col--;
	        } else {
	            row++;
	        }
	    }
	 
	    return false;
	}

### 杨氏矩阵查找一题的扩展（Google面试题）
**Problem**

给定 n×n 的实数矩阵，每行和每列都是递增的，求这 n^2 个数的第k大个数.

**[思路1]**

利用Partition算法。
经典的Partition算法可以在线性时间内找到一维数组内的第K个元素。如果这里忽略已有的排序性质，把这个矩阵当成一个乱序的1维数组，那么直接利用Partition算法可以得到O(M*N)的解。

**[思路2]**

	利用部分排序的性质和最小堆。
	
	如果使用最小堆，如何使得插入的次数最少呢？换句话说，我们应该只插入需要插入的元素，或者说”刚好“只比当前元素大一点的元素。按照这个思路，我们仍然应该以第一个元素作为起点。
	那么对于任意一个元素，“刚好”比它大一点的元素有哪些呢？
		1.右边邻居；
		2.下边邻居；
		3.下边邻居的某些左侧元素。
	前两种情况比较直观，第三种情况就只有靠标记了。具体做法如下：
	初始化将第一个元素放入最小堆中。
	1）每次弹出当前堆中的最小值，并将其右邻居和下邻居插入堆中，并标额外记右邻居和下邻居已经在堆中。如果之前某邻居已经被标记，则不再处理；（注意下邻居很可能之前是某元素的右邻居，要避免重复处理）
	2）如果弹出的元素总数为K，则停止。
	按照这个思路，处理K个元素最多也就将2K个元素插入堆中，堆的大小不超过2K，，所以算法复杂度是O(K*logK)。
	
	下面代码实现是google 一道面试题“Given a N*N Matrix. All rows are sorted, and all columns are sorted. Find the Kth Largest element of the matrix.”。与这道题本质上一样的。解法没有进行额外标记已经在堆中的数据。

**[代码实现]**

	#include <vector>
	#include <algorithm>
	#include <functional>
	
	using namespace std;
	
	struct Entry {
	    int value;
	    int x;
	    int y;
	
	    bool operator < (const Entry& other) {
	        return this->value > other.value;
	    }
	};
	bool getKthNumber(int* matrix, int row, int col, int k, int* result){
	    if(matrix == NULL || row <= 0 || col <= 0 || result == NULL)
	        return false;
	    if(k <= 0 || k > row * col)
	        return false;
	
	    vector<Entry> minHeap;
	    Entry first = {matrix[0], 0, 0};
	    minHeap.push_back(first);
	    make_heap(minHeap.begin(), minHeap.end());
	
	    for(int i = 0; i < k; ++i){
	        first = minHeap[0];
	        int x = first.x;
	        int y = first.y;
	        if(first.y == 0 && first.x < row - 1){
	            Entry next = {matrix[(x + 1) * col], x + 1, y};
	            minHeap.push_back(next);
	            push_heap(minHeap.begin(), minHeap.end());
	        }
	        if(first.y < col - 1){
	            Entry next = {matrix[x * col + y + 1], x, y + 1};
	            minHeap.push_back(next);
	            push_heap(minHeap.begin(), minHeap.end());
	        }
	
	        pop_heap(minHeap.begin(), minHeap.end());
	        minHeap.pop_back();
	    }
	
	    *result = first.value;
	    return true;
	}

**[思路3]：O(M+N)算法**

	http://www.cse.yorku.ca/~andy/pubs/X+Y.pdf

**[思路4]：Frederickson和Johnson实现的O(K)算法**

	Greg N. Frederickson and Donald B. Johnson. Generalized Selection and Ranking: Sorted Matrices. SIAM J. Comput. 13, pp. 14-30. 
	http://epubs.siam.org/sicomp/resource/1/smjcat/v13/i1/p14_s1?isAuthorized=no


	算法选择需要看实际情况，取决于K和M，N之间的大小关系。后两种思路太复杂，我觉得前两种思路作为面试题的解法足够了。
	另外可以看看这个网页，http://zhiqiang.org/blog/science/computer-science/median-algorithm-of-ordered-matrix.html。



## 树中查找
### Kth Smallest Element in a BST 
**problem**

	Given a binary search tree, write a function kthSmallest to find the kth smallest element in it.
	
	Note: 
	You may assume k is always valid, 1 ≤ k ≤ BST's total elements.
	
	Follow up:
	What if the BST is modified (insert/delete operations) often and you need to find the kth smallest frequently? How would you optimize the kthSmallest routine?
	
	Hint:
	Try to utilize the property of a BST.
	What if you could modify the BST node's structure?
	The optimal runtime complexity is O(height of BST).

**Code**

	//法一：中序遍历递归1，time：O(n), space:O(n)
	class Solution {
	public:
	    int res = -1;
	    bool FindFlag = false;
	    int cnt = 0;//ISO forbid non-const static member
	    int kthSmallest(TreeNode* root, int k) {
	        if(!root || k<=0)   return res;
	        InorderFind(root,k);
	        
	        return res;
	    }
	    void InorderFind(TreeNode* root, int k){
	        if(FindFlag)    return;
	        if(!root)   return;
	        if(k==cnt){
	            FindFlag = true;
	            res = root->val;
	            return;
	        }
	        
	        InorderFind(root->left,k);
	        cnt++;
	        if(cnt==k){
	            FindFlag = true;
	            res = root->val;
	            return;
	        }
	        InorderFind(root->right,k);
	    }
	};


	
	//法一：中序遍历迭代版，time：O(n), space:O(n)
	class Solution {
	public:
	    int kthSmallest(TreeNode* root, int k) {
	        stack<TreeNode*> s;
	        TreeNode* p=root;
	        
	        s.push(p);
	        int cnt=0;
	        
	        while(!s.empty()){
	            while(p!=NULL && p->left!=NULL){
	                s.push(p->left);
	                p = p->left;
	            }
	            TreeNode* top=s.top();
	            s.pop();
	            cnt++;
	            if(cnt==k){
	                return top->val;
	            }
	            p=top->right;
	            if(p!=NULL) s.push(p);
	        }
	        return 0;
	    }
	};

    

	/* 
	法二：利用树的节点个数，复杂度为O(h)。
	如果BST节点TreeNode的属性可以扩展，则再添加一个属性leftCnt，记录左子树的节点个数
	记当前节点为node
	当node不为空时循环：
	若k == node.leftCnt + 1：则返回node
	否则，若k > node.leftCnt：则令k -= node.leftCnt + 1，令node = node.right
	否则，node = node.left
	上述算法时间复杂度为O(BST的高度)。
	
	但是我们不能修改TreeNode的数据结构，对于每个节点root，利用函数findNodesSum得到左子树的结点数，计作S。
	如果S+1==K，返回root->val;
	如果S+1 > K，在root的左子树里面查找第K小元素；
	如果S+1 <K,在root的右子树里面查找第K-S-1小元素；*/
	class Solution {
	public:
	    int kthSmallest(TreeNode* root, int k) {
	        int left=findNodesSum(root->left);
	        if(left+1==k){
	            return root->val;
	        }else if(left+1<k){ return kthSmallest(root->right, k-left-1);
	        }else{
	            return kthSmallest(root->left,k);
	        }
	    }
	    int findNodesSum(TreeNode* root){
	        if(!root){
	            return 0;
	        }
	        int sum = findNodesSum(root->left)+findNodesSum(root->right)+1;
	        return sum;
	    }
	};


## 字符串中的查找
最长公共子串，最长公共子序列

## 图的查找
BFS,DFS

