# Week 3 Problems(array)

数组题目以查找和remove类型为主。查找问题，对于已排序数组，一般以二分查找算法为主。对于未排序数组，同时要求O(n)复杂度，一般需要借助于hash map。

##Problem1： Valid Sudoku 
Determine if a Sudoku is valid, according to: Sudoku Puzzles - The Rules.

The Sudoku board could be partially filled, where empty cells are filled with the character '.'.

Note:
A valid Sudoku board (partially filled) is not necessarily solvable. Only the filled cells need to be validated.

[problem link](https://leetcode.com/problems/valid-sudoku/)

**Idea:**
根据Sudoku的要求，对于每一行，每一列，以及每一个submatrix 进行验证。
### CODE
```c++
class Solution {
public:
    bool process( vector<char> &visited, char digit  ) {
        if ( digit == '.' ) return true;
        int num = digit - '0';
        if( num< 1 || num>9 || visited[num-1] ) return false;
        visited[num-1] = 1;
        return true;
    }
    bool isValidSudoku(vector<vector<char>>& board) {  
        // to deal with each line
        for(int i=0; i<9; i++) {
            vector<char> visited(9, 0);
            for(int j=0; j<9; j++) {
                if( ! process( visited, board[i][j] ) ) return false;
            }
        }
        
        // to deal with each col
        for( int i=0; i<9; i++ ) {
            vector<char> visited(9, 0);
            for( int j=0; j< 9; j++ ) {
                if( ! process( visited, board[j][i] ) )return false;
            }
        }
        
        //to deal with each sub matrix
        for( int i=0; i<9; i+=3 ) {
            for( int j=0; j<9; j+=3 ) {
                vector<char> visited(9, 0);
                for( int k=0; k<9; k++ ) {
                    if( !process( visited, board[i+ k/3][j + k%3]  ) ) return false;
                }
            }
        }
        return true;
    }
};
```

参考：http://sudoku.com.au/TheRules.aspx 

##Problem2: Search in Rotated Sorted Array II 
Follow up for "Search in Rotated Sorted Array":
What if duplicates are allowed?

Would this affect the run-time complexity? How and why?

Write a function to determine if a given target is in the array.

[problem link](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)

**Idea:** 利用二分查找的思想，特殊情况是mid value 和 first value相等的情况。处理办法是前移first index。
### CODE
```c++
class Solution {
public:
    bool search(vector<int>& nums, int target) {
  		int first = 0,  last = nums.size();
		while( first != last){
			int mid = (first+last)/2;
			if( nums[mid] == target )
				return true;
			if( nums[first] < nums[mid] ) {
				if( nums[first] <= target && target< nums[mid] )
					last = mid;
				else
					first = mid+1;
			}else if( nums[first] == nums[mid] ) {
			    while( nums[first] == nums[first+1] && first < mid ) {
			        first++;
			    }
			    if( first == mid ) {
			        first=mid+1;
			    }else {
			        last = mid;
			    }
			        
			    
			}
			else {
				if( nums[mid]<target && target<= nums[last-1] )
					first = mid+1;
				else
					last = mid;
			}

		}
		return false;

   
    }
};
```
 
##Problem3:Median of Two Sorted Arrays
There are two sorted arrays nums1 and nums2 of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

[problem link](https://leetcode.com/problems/median-of-two-sorted-arrays/)

**Idea:**
由于要求时间复杂度为O(log (m+n)，因而直接想法是采用二分查找，对于该题困难之处是对于终止条件的判断以及设计递归模式。

### CODE
```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int total = nums1.size() + nums2.size();
        if(total &  0x1)
            return find_kth( A, B, total/2+1 );
        else
            return (find_kth( A, B, total/2 ) + find_kth( A, B, total/2+1 ) )/2.0;
        
    }
private:
    static int find_kth( A, B, int k ) {
        if( A.size() > B.size() ) return find_kth( B, A, k );
        if( A.size() ==0 ) return B[ k-1 ];
        if( k==1 ) return min( A[0], B[0] );
        
        //divide k into two parts
        int ia = min(k/2, A.size());
        int ib = k - ia;
        if( A[ia] < B[ib] ) {
            find_kth( A, )
        }
    }
};
```

# Week 3 Problems(list)

链表题目以去除重复，删除特定节点，反转链表，查找特定节点为主。为了处理方便，在head可能发生变化的情况下，可以引入dummy节点作为辅助。
##Problem1： Reverse Linked List II 
Reverse a linked list from position m to n. Do it in-place and in one-pass.

For example:
Given 1->2->3->4->5->NULL, m = 2 and n = 4,

return 1->4->3->2->5->NULL.

Note:
Given m, n satisfy the following condition:
1 ≤ m ≤ n ≤ length of list.
[problem link](https://leetcode.com/problems/reverse-linked-list-ii/)
**Idea:**
主要注意看守指针的设计，对于反转前的node进行看守。

### CODE
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
         if(m==n) return head;
         ListNode *fix_head = NULL, *p = head;
         
         for( int i=1; i< m; i++ ) {
             if( i== m-1 ) {
                 fix_head = p;
                 p = p->next;
                 break;
             }
              p = p-> next;
         }
        ListNode *p1 = p;
        ListNode *p2 = p1->next;
        ListNode *tmp = NULL;
        for( int i=1; i<= n-m; i++ ) {
            tmp = p2;
            p2 = p2->next;
            tmp->next = p1;
            p1 = tmp;
        }
        p->next = p2;
        if(m==1) 
            head = p1;
        else
            fix_head ->next = p1;
        return head;
    }
    
};
```

##Problem2：Linked List Cycle 
Given a linked list, determine if it has a cycle in it.

Follow up:
Can you solve it without using extra space?
 
[problem link](https://leetcode.com/problems/linked-list-cycle/)
**Idea:**
 主要是对于快慢指针的设计。如果出现环，快慢指针有一定会在环内相遇。
### CODE
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *slow = head, *fast = head;
         while( fast != NULL && fast->next != NULL ) {
             slow = slow->next;
             fast = fast->next->next;
             if( slow == fast ) return true;
         }
         return false;
    }
};
```

##Problem3：Copy List with Random Pointer 
A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.

[problem link](https://leetcode.com/problems/copy-list-with-random-pointer/)

**Idea:**
参考题解，这个任务可以比较巧妙地完成，基本思想是将每个节点进行copy并串入原有链内。最后将该链拆开恢复原有链以及copy的链。
### CODE
```c++

/**
 * Definition for singly-linked list with a random pointer.
 * struct RandomListNode {
 *     int label;
 *     RandomListNode *next, *random;
 *     RandomListNode(int x) : label(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    RandomListNode *copyRandomList(RandomListNode *head) {
        for( RandomListNode *cur = head; cur!= NULL;  ) {
            RandomListNode *node = new RandomListNode( cur->label );
            node->next = cur->next;
            cur ->next =node;
            cur = node->next;
        }
        for( RandomListNode *cur = head; cur!= NULL; ) {
            if( cur->random!= NULL ){
                cur->next->random = cur->random->next;
            }
            cur = cur->next->next;
        }
        // to divide and import a dummy node
        RandomListNode dummy(-1);
        for( RandomListNode *cur = head, *new_cur = &dummy; cur!= NULL;  ) {
            new_cur->next = cur->next;
            new_cur = new_cur->next;
            
            cur->next = new_cur->next;
            cur = cur->next;
        }
        return dummy.next;
    }
};

```
