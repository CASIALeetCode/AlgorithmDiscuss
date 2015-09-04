##Array

###1. [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

	Given an array of n integers where n > 1, nums, return an array output such that output[i] is equal to the product of all the elements of nums except nums[i].

	Solve it without division and in O(n).

	For example, given [1,2,3,4], return [24,12,8,6].

	Follow up:
	Could you solve it with constant space complexity? (Note: The output array does not count as extra space for the purpose of space complexity analysis.)


####Code

	// method1
	// Time : O(2*n)
	// Space : O(2*n)
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> left(n, 1);
        vector<int> right(n, 1);
        for (int i = 1; i < n; ++i) {
            left[i] = left[i - 1] * nums[i - 1];
            right[n - 1 - i] = right[n - i] * nums[n - i];
        }
        for (int i = 0; i < n; ++i) {
            left[i] = left[i] * right[i];
        }
        return left;
    }
	// method2
	// Time : O(2*n)
	// Space : O(n)
	vector<int> productExceptSelf(vector<int>& nums) {
		int n = nums.size();
		vector<int> output(n, 1);
		for (int i = 1; i < n; ++i) output[i] = output[i - 1] * nums[i - 1];
		int prev = 1;
		for (int i = n - 1; i >= 0; --i) {
			output[i] = output[i] * prev;
			prev = prev * nums[i];
		}
		return output;
	}


###2. [Missing Number](https://leetcode.com/problems/missing-number/)

	Given an array containing n distinct numbers taken from 0, 1, 2, ..., n, find the one that is missing from the array.

	For example,
	Given nums = [0, 1, 3] return 2.

	Note:
	Your algorithm should run in linear runtime complexity. Could you implement it using only constant extra space complexity?

####Understand

	1. 无序
	2. 寻找第一个缺失的数

####Code

	// method 1 : 等差数列
	// Time : O(n)
	// Space : O(1)
    int missingNumber(vector<int>& nums) {
        int n = nums.size();
        int sum = 0;
        for (int i = 0; i < n; ++i) sum += nums[i];
        return 0.5 * (0 + n) * (n + 1) - sum;
    }

	// method 2 : 通用方法，bit manipulation
	// 主要利用异或操作的性质==> a ^ a = 0
	// Time : O(n)
	// Space : O(1)
	int missingNumber(vector<int>& nums) {
		int n = nums.size();
		int res = 0;
		for (int i = 0; i < n; ++i) {
			res ^= i ^ nums[i];
		}
		res ^= n;
		return res;
	}
####Why
	为什么位运算要快一些？

###Similar Problems
####1. [Single Number](https://leetcode.com/problems/single-number/)

	Given an array of integers, every element appears twice except for one. Find that single one.

	Note:
	Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

####2. [Single Number II](https://leetcode.com/problems/single-number-ii/)

	Given an array of integers, every element appears three times except for one. Find that single one.

	Note:
	Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

####3. [Single Number III](https://leetcode.com/problems/single-number-iii/)

	Given an array of numbers nums, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.

	For example:

	Given nums = [1, 2, 1, 3, 2, 5], return [3, 5].

	Note:
	The order of the result is not important. So in the above example, [5, 3] is also correct.
	Your algorithm should run in linear runtime complexity. Could you implement it using only constant space complexity?

####4. [First Missing Positive](https://leetcode.com/problems/first-missing-positive/)

	Given an unsorted integer array, find the first missing positive integer.

	For example,
	Given [1,2,0] return 3,
	and [3,4,-1,1] return 2.

	Your algorithm should run in O(n) time and uses constant space.


##Math

###1. [Number of Digit One](https://leetcode.com/problems/number-of-digit-one/)

	Given an integer n, count the total number of digit 1 appearing in all non-negative integers less than or equal to n.

	For example:
	Given n = 13,
	Return 6, because digit 1 occurred in the following numbers: 1, 10, 11, 12, 13.

	// naive method
	// Time : O(nlogn)
	// Space : O(1)
	int countOneInNumber(int n) {
		int res = 0;
		while (n) {
			if (n % 10 == 1) ++res;
			n = n / 10;
		}
		return res;
	}
	int countDigitOne(int n) {
		int res = 0;
		for (int i = 1; i <= n; ++i) {
			res += countOneInNumber(i);
		}
		return res;
	}

	// method 2
	// 总结规律：num
		// 一个位上的1的次数，可能有三个影响因素：这个位置上的数字，这个位置以下的数字，这个位置以上的数字（例如百位）
		// 百位为0, ones = (num / 1000) * 100
		// 百位为1， ones = (num / 1000) * 100 + (num % 1000 + 1)
		// 百位大于1，ones = (num / 1000 + 1) * 100

	// 特例：个位上的数字>=1时，都等于 (num / 10 + 1)
	// Time : O(n的位数) = O(logn)
	// Space : O(1)
	int countDigitOne(int n) {
		int res = 0;
		long long factor = 1;
		int lower = 0;
		int cur = 0;
		int higher = 0;

		while (n / factor) {
			cur = (n / factor) % 10;
			low = n - （n / factor） * factor;
			high = n / (factor * 10);
			switch(cur) {
				case 0: res += high * factor;
						break;
				case 1: res += high * factor + low + 1;
						break;
				default:res += (high + 1) * factor;
						break;
			}
			factor *= 10;
		}
		return res;
	}
	// Bug :
	// factor 可能溢出，使用long long类型
	// res <= n, 不可能溢出


##List

###1.[Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)

	Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.

	For example,
	Given 1->2->3->3->4->4->5, return 1->2->5.
	Given 1->1->1->2->3, return 2->3.

####Code

	ListNode* deleteDuplicates(ListNode* head) {
		if (!head || !head -> next) return head;
		ListNode* dummy = new ListNode(0);
		ListNode* cur = dummy;
		while (head) {
			int v = head -> val;
			if (!head -> next || head -> next -> val != v) {
				cur -> next = head;
				cur = cur -> next;
				// BUG:
				// cur -> next = NULL; 放在head = head -> next的后面可以！
				head = head -> next;
			} else {
				while (head && head -> val == v) {
					ListNode* temp = head;
					head = head -> next;
					delete temp;
				}
			}
		}
		// BUG: 必须把最后一个元素的next赋值为NULL ????
		cur -> next = NULL;
		return dummy -> next;
    }


##string

###1. [Integer to English Words](https://leetcode.com/problems/integer-to-english-words/)

	Convert a non-negative integer to its english words representation. Given input is guaranteed to be less than 2G - 1.

	For example,
	123 -> "One Hundred Twenty Three"
	12345 -> "Twelve Thousand Three Hundred Forty Five"
	1234567 -> "One Million Two Hundred Thirty Four Thousand Five Hundred Sixty Seven"

	Hint:

	Did you see a pattern in dividing the number into chunk of words? For example, 123 and 123000.
	Group the number by thousands (3 digits). You can write a helper function that takes a number less than 1000 and convert just that chunk to words.
	There are many edge cases. What are some good test cases? Does your code work with input such as 0? Or 1000010? (middle chunk is zero and should not be printed out)


####Code

	// Time : o(logn)
	// Space : O(1)
    unordered_map<int, string> dict{
        {1000000000, "Billion"},
        {1000000, "Million"},
        {1000, "Thousand"},
        {100, "Hundred"},
        {90, "Ninety"},
        {80, "Eighty"},
        {70, "Seventy"},
        {60, "Sixty"},
        {50, "Fifty"},
        {40, "Forty"},
        {30, "Thirty"},
        {20, "Twenty"},
        {19, "Nineteen"},
        {18, "Eighteen"},
        {17, "Seventeen"},
        {16, "Sixteen"},
        {15, "Fifteen"},
        {14, "Fourteen"},
        {13, "Thirteen"},
        {12, "Twelve"},
        {11, "Eleven"},
        {10, "Ten"},
        {9, "Nine"},
        {8, "Eight"},
        {7, "Seven"},
        {6, "Six"},
        {5, "Five"},
        {4, "Four"},
        {3, "Three"},
        {2, "Two"},
        {1, "One"}
    };
    vector<int> base{1000000000, 1000000, 1000, 1};
    string numberToWords(int num) {
        if (num == 0) return string("Zero");
        string res;
        int n = base.size();
        //bool flag = false;
        for (int i = 0; i < n; ++i) {
            if (num / base[i]) {
                int cur = num / base[i];
                while (cur) {
                    if (cur >= 100) {
                        res += dict[cur / 100] + " " + dict[100] + " ";
                        cur = cur % 100;
                    } else if (cur >= 10) {
                        if (dict.count(cur)) {
                            res += dict[cur] + " ";
                            break;
                        }
                        res += dict[cur - cur % 10] + " ";
                        cur = cur % 10;
                    } else {
                        if (cur != 0) res += dict[cur] + " ";
                        break;
                    }
                }
                if (base[i] == 1) break;
                res += dict[base[i]] + " ";
                num = num % base[i];
                //flag = true;
            }
        }
        res.pop_back();
        return res;
    }
	// bug: 不需要加And

	// Better method
	// 这道题让我们把一个整型数转为用英文单词描述，就像在check上写钱数的方法，我最开始的方法特别复杂，因为我用了几个switch语句来列出所有的单词，但是我看网上大神们的解法都是用数组来枚举的，特别的巧妙而且省地方，膜拜学习中。题目中给足了提示，首先告诉我们要3个一组的进行处理，而且题目中限定了输入数字范围为0到231 - 1之间，最高只能到billion位，3个一组也只需处理四组即可，那么我们需要些一个处理三个一组数字的函数，我们需要把1到19的英文单词都列出来，放到一个数组里，还要把20,30，... 到90的英文单词列出来放到另一个数组里，然后我们需要用写技巧，比如一个三位数n，百位数表示为n/100，后两位数一起表示为n%100，十位数表示为n%100/10，个位数表示为n%10，然后我们看后两位数是否小于20，小于的话直接从数组中取出单词，如果大于等于20的话，则分别将十位和个位数字的单词从两个数组中取出来。然后再来处理百位上的数字，还要记得加上Hundred。主函数中调用四次这个帮助函数，然后中间要插入"Thousand", "Million", "Billion"到对应的位置，最后check一下末尾是否有空格，把空格都删掉，返回的时候检查下输入是否为0，是的话要返回'Zero'。

    string numberToWords(int num) {
    	string res = convertHundred(num % 1000);
    	vector<string> v = {"Thousand", "Million", "Billion"};
    	for (int i = 0; i < 3; ++i) {
        	num /= 1000;
        	res = num % 1000 ? convertHundred(num % 1000) + " " + v[i] + " " + res : res;
    	}
    	while (res.back() == ' ') res.pop_back();
    	return res.empty() ? "Zero" : res;
	}
    string convertHundred(int num) {
        vector<string> v1 = {"", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten", "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen", "Eighteen", "Nineteen"};
        vector<string> v2 = {"", "", "Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", "Eighty", "Ninety"};
        string res;
        int a = num / 100, b = num % 100, c = num % 10;
        res = b < 20 ? v1[b] : v2[b / 10] + (c ? " " + v1[c] : "");
        if (a > 0) res = v1[a] + " Hundred" + (b ? " " + res : "");
        return res;
    }


##Binary Search

###1. [Sqrt(x)](https://leetcode.com/problems/sqrtx/)

	Implement int sqrt(int x).

	Compute and return the square root of x.
	
	// Time : O(log2(n))
    int mySqrt(int x) {
        if (x <= 1) return x;
        int low = 0;
        int high = x;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (mid > x / mid) {
                high = mid - 1;
            } else if ((mid + 1) <= x / (mid + 1)) {
                low = mid + 1;
            } else {
                return mid;
            }
        }
    }

##Tree

###1. [Count Complete Tree Nodes](https://leetcode.com/problems/count-complete-tree-nodes/)

	Given a complete binary tree, count the number of nodes.

####Code

	// naive method: level order
	// Time: O(N)
	// 超时
    int countNodes(TreeNode* root) {
        if (!root) return 0;
        queue<TreeNode*> q;
        q.push(root);
        q.push(NULL);
        int res = 0;
        while (!q.empty()) {
            TreeNode* node = q.front();
            q.pop();
            if (node) {
                ++res;
                if (node -> left) q.push(node -> left);
                if (node -> right) q.push(node -> right);
            } else {
                if (q.empty()) break;
                q.push(NULL);
            }
        }
        return res;
    }

	// method 2 : 先计算（h - 1）层满树节点的个数，再二分查找最后一层的个数
	// Time : O(log2(n) + log2(n/2)*log2(n)) = O(h^2)
	// Space : O(1)

    int countNodes(TreeNode* root) {
        if (!root) return 0;
        int res = 0;
        int h = 0;
        TreeNode* cur = root;
        while (cur) {
            ++h;
            cur = cur -> right;
        }
        res += (1 << h) - 1;
        int low = 0;
        int high = (1 << h) - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int mask = 1 << (h - 1);
            cur = root;
            while (mask) {
                if (mask & mid) {
                    cur = cur -> right;
                } else {
                    cur = cur -> left;
                }
                mask >>= 1;
            }
            if (cur) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return res + high + 1;
    }

	// method 3
	// 递归左右子树：高度相同，直接返回结点个数，否则return 1 + countNodes(root -> left) + countNodes(root -> right);
	// Time : O(h^2)
	// Space : O(h)
	
    int countNodes(TreeNode* root) {
        if (!root) return 0;
        int lh = 0;
        int rh = 0;
        TreeNode* cur = root;
        while (cur) {
            ++lh;
            cur = cur -> left;
        }
        cur = root;
        while (cur) {
            ++rh;
            cur = cur -> right;
        }
        if (lh == rh) return (1 << lh) - 1;
        return 1 + countNodes(root -> left) + countNodes(root -> right);
	}

##Graph

###1.[gCampus](https://code.google.com/codejam/contest/4284486/dashboard#s=p2)

	Company G has a main campus with N offices (numbered from 0 to N - 1) and M bidirectional roads (numbered from 0 to M - 1). The ith road connects a pair of offices (Ui, Vi), and it takes Ci minutes to travel on it (in either direction).
	
	A path between two offices X and Y is a series of one or more roads that starts at X and ends at Y. The time taken to travel a path is the sum of the times needed to travel each of the roads that make up the path. (It's guaranteed that there is at least one path connecting any two offices.)
	
	Company G specializes in efficient transport solutions, but the CEO has just realized that, embarrassingly enough, its own road network may be suboptimal! She wants to know which roads in the campus are inefficient. A road is inefficient if and only if it is not included in any shortest paths between any offices.
	
	Given the graph of offices and roads, can you help the CEO find all of the inefficient roads?
	
	Input
	
	The first line of the input gives the number of test cases, T. T test cases follow. Each case begins with one line with two integers N and M, indicating the number of offices and roads. This is followed by M lines containing three integers each: Ui, Vi and Ci, indicating the ith road is between office Ui and office Vi, and it takes Ci minutes to travel on it.
	
	Output
	
	For each test case, output one line containing "Case #x:", where x is the test case number (starting from 1). Then output the road numbers of all of the inefficient roads, in increasing order, each on its own line. (Note that road 0 refers to the first road listed in a test case, road 1 refers to the second road, etc.)
	
	Limits
	
	0 < Ci ≤ 1000000.
	
	Small dataset
	
	1 ≤ T ≤ 10.
	1 ≤ N = M ≤ 100.
	Large dataset
	
	1 ≤ T ≤ 3.
	1 ≤ N ≤ 100.
	1 ≤ M ≤ 10000.
	Sample
	
	
	Input 
	 	
	Output 
	 
	2
	3 3
	0 1 10
	1 2 3
	2 0 3
	3 3
	0 1 10
	1 2 3
	2 1 3
	Case #1:
	0
	Case #2:


####Code


	// Time : O(n^2)
	#include <iostream>
	#include <vector>
	using namespace std;

	struct Edge {
		int to;
		int cost;
		int index;
		Edge(int t, int c, int i) : to(t), cost(c), index(i) {}
	};
	
	int extractMin(vector<bool>& visited, vector<int>& dis) {
		int minDis = INT_MAX;
		int minIndex = -1;
		for (int i = 0; i < dis.size(); ++i) {
			if (!visited[i] && dis[i] < minDis) {
				minDis = dis[i];
				minIndex = i;
			}
		}
		return minIndex;
	}
	
	void dijkstra(vector<vector<Edge> >& graph, vector<bool>& used, int start) {
		int N = graph.size();
		vector<int> dis(N, INT_MAX);
		vector<bool> visited(N, false);
		dis[start] = 0;
		for (int i = 0; i < N; ++i) {
			int u = extractMin(visited, dis);
			visited[u]= true;
			for (int j = 0; j < graph[u].size(); ++j) {
				if (dis[graph[u][j].to] > dis[u] + graph[u][j].cost) {
					dis[graph[u][j].to] = dis[u] + graph[u][j].cost;
				}
			}
		}
		for (int i = 0; i < N; ++i) {
			for (int j = 0; j < graph[i].size(); ++j) {
				if (dis[graph[i][j].to] == dis[i] + graph[i][j].cost) {
					used[graph[i][j].index] = true;
				}
			}
		}
	}
	
	void solve(int t) {
		cout << "Case #" << t << ":" << endl;
		int N, M;
		cin >> N >> M;
		vector<vector<Edge> > graph(N);
		vector<bool> used(M, false);
		for (int i = 0; i < M; ++i) {
			int from, to, cost;
			cin >> from >> to >> cost;
			graph[from].push_back(Edge(to, cost, i));
			graph[to].push_back(Edge(from, cost, i));
		}
		for (int i = 0; i < N; ++i) {
			dijkstra(graph, used, i);
		}
		for (int i = 0; i < M; ++i) {
			if (!used[i]) cout << i << endl;
		}
	}
	
	int main() 
	{
		freopen("C-large-practice.in", "r", stdin);
		freopen("C-large.out", "w", stdout);
		int T;
		cin >> T;
		for (int t = 1; t <= T; ++t) {
			solve(t);
		}
		system("pause");
		return 0;
	}