## 数组
###1.[Word Ladder II](https://leetcode.com/problems/word-ladder-ii/)
    Given two words (start and end), and a dictionary's word list, find all shortest transformation sequence(s) from start to end, such that:
    
    Only one letter can be changed at a time
    Each intermediate word must exist in the word list
    For example,
    
    Given:
    start = "hit"
    end = "cog"
    wordList = ["hot","dot","dog","lot","log"]
    Return
      [
    ["hit","hot","dot","dog","cog"],
    ["hit","hot","lot","log","cog"]
      ]
    Note:
    All words have the same length.
    All words contain only lowercase alphabetic characters.

题目一：返回最短的长度
code：

	//广度优先 队列 2015-9-13 14:58:48
	//入队列前检测是否与endword匹配 2015-9-13 15:16:26
	
	class Solution {
	public:
	    int ladderLength(string beginWord, string endWord, unordered_set<string>& wordDict) {
	        if(beginWord.size() != endWord.size() || beginWord==endWord){
	            return 0;
	        }
	        
	        if(Check(beginWord,endWord) == true){
	            return 2;
	        }
	        int ret = 2;
	        queue<string> myqu;
	        unordered_set<string> usedFlag;
	        myqu.push(beginWord);
	        usedFlag.insert(beginWord);
	        
	        while(myqu.size() != 0){
	            int counter = myqu.size();
	            while(counter != 0){
	                string temp = myqu.front();
	                myqu.pop();
	                for(int i=0 ; i<temp.size() ; i++){
	                    for(char ch='a' ; ch <='z' ; ch++){
	                        swap(temp[i],ch);
	                        if(wordDict.find(temp)!=wordDict.end() && usedFlag.find(temp)==usedFlag.end()){
	                            if(Check(temp,endWord) == true){
	                                return ret+1;
	                            }
	                            else{
	                                myqu.push(temp);
	                                usedFlag.insert(temp);
	                            }
	                            
	                        }
	                        swap(temp[i], ch);
	                        
	                    }
	                    
	                }
	                
	                counter--;
	            }
	            ret ++;
	        }
	        return 0;
	        
	    }
	    
	    
	private:
	    bool Check(const string &str1,const string &str2){
	        if(str1.size() != str2.size()){
	            return false;
	        }
	        int counter = 0;
	        for(int i=0  ;i<str1.size() ; i++){
	            if(str1[i] == str2[i]){
	                continue;
	            }
	            counter ++;
	            if(counter > 1){
	                return false;
	            }
	        }
	        return counter == 1;
	    }
	};

这里在查找时采用遍历word的每一位，每一位从a~z变化，判断是否存在词典中和是否访问过的复杂度为O(1)，因此while循环内的复杂度为O(L*26)

使用遍历词典的方法会超时，判断词典内某个单词是否为进入队列的复杂度为O(n*L)，当n>>26时会超时。

题目二：求解最短长度时所有可能路径

	class Solution {
	public:
		vector<vector<string>> findLadders(string start, string end, unordered_set<string> &dict) {
			vector<vector<string> > ret;
			if (start.size() != end.size()){
				return ret;
			}
	
			if (check(start, end) == true){
				vector<string> oneresult;
				oneresult.push_back(start);
				oneresult.push_back(end);
				ret.push_back(oneresult);
				return ret;
			}
	
			int n = start.size();
			unordered_set<string>::iterator ittemp;
			unordered_map<string, vector<string> > father;//前驱节点，允许多个节点有同一个前驱节点，因此将某个string插入到queue中时要避免重复，最好使用一个set暂时保存所有不重复的待遍历的某层节点
	
			unordered_set<string> cur;
			unordered_set<string> next;
			unordered_set<string> visited;
	
			cur.insert(start);
			father[start].push_back(start);
			bool IsFound = false;
	
			while (cur.size() != 0 && IsFound == false){
				for (unordered_set<string>::iterator it = cur.begin(); it != cur.end(); it++){//mark cur has been visited
					visited.insert(*it);
					if (check(*it, end) == true){
						father[end].push_back(*it);
						IsFound = true;
					}
				}
				if (IsFound == true){
					break;
				}
				for (unordered_set<string>::iterator it = cur.begin(); it != cur.end(); it++){
					string newword = *it;
					string oldword = newword;
					for (int i = 0; i<n; i++){
						for (char ch = 'a'; ch <= 'z'; ch++){
							if (ch == newword[i]){
								continue;
							}
							swap(ch, newword[i]);
							ittemp = dict.find(newword);
							if ((ittemp != dict.end()) && visited.find(newword) == visited.end()){
								father[newword].push_back(oldword);
								next.insert(newword);
							}
							swap(ch, newword[i]);
						}
	
					}
				}
				cur = next;
				next.erase(next.begin(), next.end());
			}
	
	
			if (IsFound == true){
				vector<string> oneresult;
				oneresult.push_back(end);
				getPath(start, end, father, ret, oneresult);
	
			}
	
			return ret;
		}
		//递归求解路径
		void getPath(string &start, string &end, unordered_map<string, vector<string> > &father, vector<vector<string> > &ret, vector<string> &oneresult){
			string curstr = oneresult.back();
			if (curstr == start){
				reverse(oneresult.begin(), oneresult.end());
				ret.push_back(oneresult);
				reverse(oneresult.begin(), oneresult.end());
				return;
			}
			for (int i = 0; i<father[curstr].size(); i++){
				oneresult.push_back(father[curstr][i]);
				getPath(start, end, father, ret, oneresult);
				oneresult.pop_back();
			}
		}
	
		bool check(string s1, string s2){
			if (s1.size() != s2.size()){
				return false;
			}
			int counter = 0;
			for (int i = 0; i<s1.size(); i++){
				if (s1[i] != s2[i]){
					counter++;
				}
			}
			return counter == 1;
	
		}
	};


## 链表
###2.[Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/) 
    Given a linked list, return the node where the cycle begins. If there is no cycle, return null.
    
    Note: Do not modify the linked list.
    
    Follow up:
    Can you solve it without using extra space?

####solution
快慢指针

	class Solution {
	public:
	    ListNode *detectCycle(ListNode *head) {
	        if(head == NULL){
	            return NULL;
	        }
	        ListNode *slow=head,*fast=head;
	        
	        while(fast->next!=NULL && fast->next->next !=NULL){
	            fast = fast->next->next;
	            slow = slow->next;
	            if(fast == slow){
	                fast = head;
	                while(fast != slow){
	                    fast= fast->next;
	                    slow = slow->next;
	                }
	                return slow;
	            }
	        }
	        return NULL;
	    }
	};


## 树 #

###3.判断一棵树是否为另一棵树的子树
####solution
	bool IsPart(TreeNode *root1, TreeNode *root2)
	{
	    if (root2 == NULL)
	        return true;
	    if (root1 == NULL)
	        return false;
	    if (root1->val != root2->val)
	        return false;
	    return IsPart(root1->left, root2->left) &&
	        IsPart(root1->right, root2->right);
	}
	bool IsPartTree(TreeNode *root1, TreeNode *root2)
	{
	    bool result = false;
	    if (root1 != NULL && root2 != NULL)
	    {
	        if (root1->val == root2->val)
	            result = IsPart(root1, root2);
	        if (!result)
	            result = IsPartTree(root1->left, root2);
	        if (!result)
	            result = IsPartTree(root1->right, root2);
	    }
	    return result;
	}


	
###4.[Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/) 
    Follow up for problem "Populating Next Right Pointers in Each Node".
    
    What if the given tree could be any binary tree? Would your previous solution still work?
    
    Note:
    
    You may only use constant extra space.
    For example,
    Given the following binary tree,
        1
       / \
      2   3
     / \   \
    4   5   7
    After calling your function, the tree should look like:
        1 -> NULL
       / \
      2->3 -> NULL
     / \   \
    4-> 5-> 7 -> NULL

####solution
discuss中答案，记录pre指针和每层开始指针，代码的优点在于思路清晰，每次找到不为空的作为下一个prev，没有使用while循环，代码清晰

	/**
	 * Definition for binary tree with next pointer.
	 * struct TreeLinkNode {
	 *  int val;
	 *  TreeLinkNode *left, *right, *next;
	 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
	 * };
	 */
	class Solution {
	public:
	    void connect(TreeLinkNode *root) {
	        if(root == NULL){
	            return ;
	        }
	        root->next = NULL;
	        TreeLinkNode *head = root;
	        TreeLinkNode *cur = NULL;
	        TreeLinkNode *pre = NULL;
	        while(head != NULL){
	            cur = head;
	            while(cur != NULL){
	                if(cur->left != NULL){
	                    if(pre == NULL){
	                        pre = cur->left;
	                        head = cur->left;
	                    }
	                    else{
	                        pre->next = cur->left;
	                        pre = pre->next;
	                    }
	                }
	                if(cur->right != NULL){
	                    if(pre == NULL){
	                        pre = cur->right;
	                        head = cur->right;
	                    }
	                    else{
	                        pre->next = cur->right;
	                        pre = pre->next;
	                    }
	                }
	                cur = cur->next;
	            }
	            if(pre == NULL){
	                return ;
	            }
	            pre = NULL;
	        }
	    }
	};

## 图
###5.[Course Schedule II ](https://leetcode.com/problems/course-schedule-ii/)
    There are a total of n courses you have to take, labeled from 0 to n - 1.
    
    Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]
    
    Given the total number of courses and a list of prerequisite pairs, return the ordering of courses you should take to finish all courses.
    
    There may be multiple correct orders, you just need to return one of them. If it is impossible to finish all courses, return an empty array.
    
    For example:
    
    2, [[1,0]]
    There are a total of 2 courses to take. To take course 1 you should have finished course 0. So the correct course order is [0,1]
    
    4, [[1,0],[2,0],[3,1],[3,2]]
    There are a total of 4 courses to take. To take course 3 you should have finished both courses 1 and 2. Both courses 1 and 2 should be taken after you finished course 0. So one correct course order is [0,1,2,3]. Another correct ordering is[0,2,1,3].
    
    Note:
    The input prerequisites is a graph represented by a list of edges, not adjacency matrices. Read more about how a graph is represented.
    
    click to show more hints.

两种方法：拓扑排序，DFS
###Kahn算法

	L← Empty list that will contain the sorted elements
	S ← Set of all nodes with no incoming edges
	while S is non-empty do
	    remove a node n from S
	    insert n into L
	    foreach node m with an edge e from n to m do
	        remove edge e from the graph
	        if m has no other incoming edges then
	            insert m into S
	if graph has edges then
	    return error (graph has at least onecycle)
	else 
	    return L (a topologically sortedorder)

###DFS方法
	L ← Empty list that will contain the sorted nodes
	while there are unmarked nodes do
		select an unmarked node n
		visit(n) 

	function visit(node n)
		if n has a temporary mark then stop (not a DAG)
		if n is not marked (i.e. has not been visited yet) then
			mark n temporarily
			for each node m with an edge from n to m do
				visit(m)
			mark n permanently
			unmark n temporarily
			add n to head of L

**code 1 (Kahn算法):**

	class Solution {
	public:
		vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
			vector<int> ret;
			if(prerequisites.size() <= 0){
				for(int i=0 ; i<numCourses ; i++){
					ret.push_back(i);
				}
				return ret;
			}
			queue<int> qu;
			vector<int> degree(numCourses,0);
			vector<vector<int> > nums(numCourses);
		
			for(vector<pair<int,int>>::iterator it=prerequisites.begin() ; it!=prerequisites.end() ; it++){
				nums[it->second].push_back(it->first);
				degree[it->first]++;
			}
			for(int i=0 ; i<degree.size() ; i++){
				if(degree[i] == 0){
					qu.push(i);
				}
			}
			int finishnum = 0;
			while(qu.empty() == false){
				int p = qu.front();
				qu.pop();
				ret.push_back(p);
				for(int i=0 ; i<nums[p].size() ; i++){
					degree[nums[p][i]]--;
					if(degree[nums[p][i]] == 0){
						qu.push(nums[p][i]);
					}
				}
			}
			if(ret.size() == numCourses){
				return ret;
			}
			return vector<int>();
		}
	};

难点:数据结构的选择

    1.用vector L、queue S分别存储已经访问过的节点和入度为0待访问的节点
    邻接表： vector<set<int> > table，存储节点i的入节点，如边1-->2，则2中添加1，这样便于求解入度为0的节点。连接链表为空
    首先遍历一次table，找到入度为0的节点加入到S中，遍历S，每次遍历一次table找到入节点有S.front()的节点，删除该节点，如果度为0则加入到S
    每次遍历一次table，耗时；之所以用set而不是list，因此输入可能有重复边，同时也便于快速查找。


    2.换用另一个数据结构
    L和S不变
    用vector<vector<int>>存储节点的出节点真正的邻接表 如边1-->2，则1中添加2
    用时用vector<int> degree 保存每个点的入度，这样遍历时直接访问table[i]，将table[i]中每个元素的degree减一，如果degree[i]为0，则将i加入到S
    更快，更方便
    没有删除操作
    考虑重复边，这时候table中保存两次，但在删除时节点的度也会减两次，不影响结果。

**code 2 (DFS方法):**

	class Solution {
	public:
	    vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
	        vector<int> ret ;
	        if(prerequisites.size() <= 0){
	            for(int i=0 ; i<numCourses ; i++){
	                ret.push_back(i);
	            }
	            return ret;
	        }
	        
	        vector<vector<int> > nums(numCourses);
	        vector<int> degree(numCourses,0);
	        for(vector<pair<int, int>>::iterator it=prerequisites.begin() ; it!=prerequisites.end() ; it++){
	            nums[it->second].push_back(it->first);
	            degree[it->first]++;
	        }
	        vector<int> nodes;
	        for(int i=0 ; i<degree.size() ; i++){
	            if(degree[i] == 0){
	                nodes.push_back(i);
	            }
	        }
	        
	        vector<int> visit(numCourses,0);
	        for(int i=0 ; i<nodes.size() ; i++){
	            if(DFS(nodes[i],nums,ret,visit) == false){
	                return vector<int>();
	            }
	        }
	        if(ret.size()  != numCourses){
	            return vector<int>();
	        }
	        reverse(ret.begin(),ret.end());
	        return ret;
	    }
	    
	    bool DFS(int noden,vector<vector<int> > &nums,vector<int> &finishvec,vector<int> &visit){
	        visit[noden] = -1;
	        for(int i=0 ; i<nums[noden].size() ; i++){
	            if(visit[nums[noden][i]] == -1){
	                return false;
	            }
	            else if(visit[nums[noden][i]]==0 &&  DFS(nums[noden][i],nums,finishvec,visit) == false){
	                return false;
	            }
	        }
	        visit[noden] = 1;
	        finishvec.push_back(noden);
	        return true;
	    }
	    
	};



## 贪心
###6.[Jump Game II](http://https://leetcode.com/problems/jump-game-ii/)

    Given an array of non-negative integers, you are initially positioned at the first index of the array.
    
    Each element in the array represents your maximum jump length at that position.
    
    Your goal is to reach the last index in the minimum number of jumps.
    
    For example:
    Given array A = [2,3,1,1,4]
    
    The minimum number of jumps to reach the last index is 2. (Jump 1 step from index 0 to 1, then 3 steps to the last index.)

题目一：仅判断能够到达


1. 贪心向上，遍历从0开始到能到达的最大高度，若此高度到n-1，符合要求

代码：

	class Solution {
	public:
	    bool canJump(vector<int>& nums) {
	        if(nums.size() <= 1){
	            return true;
	        }
	        int maxreach = nums[0];
	        int n = nums.size();
	        
	        for(int i=0 ; i<=maxreach ; i++){
	            maxreach = max(nums[i]+i,maxreach);
	            if(maxreach >= n-1){
	                return true;
	            }
	        }
	        return false;
	    }
	};

2. 从后向前，一个变量标记，到某一步能到达，其后所有步必能到达

代码：

	class Solution {
	public:
	    bool canJump(vector<int>& nums) {
	        if(nums.size() <= 1){
	            return true;
	        }
	        int n = nums.size();
	        int minnum = n-1;
	
	        for(int i = n-2 ; i>=0 ; i--){
	            if(i+nums[i] >= minnum){
	                minnum = i;
	            }
	        }
	        return minnum == 0;
	        
	    }
	};
题目二：能到达时返回最短步数

修改上述方法一：
	
	class Solution {
	public:
	    int jump(vector<int>& nums) {
	        if(nums.size() == 0){
	            return INT_MAX;
	        }
	        if(nums.size() == 1){
	            return 0;
	        }
	        int n = nums.size();
	        
	        int last = 0;
	        int cur = nums[0];
	        int step = 0;
	        
	        for(int i=0 ; i<=cur ; i++){
	            if(i > last){
	                step++;
	                last = cur;
	                if(last >= n-1){
	                    return step;
	                }
	            }
	            cur = max(cur,i+nums[i]);
	            
	        }
	        return INT_MAX;
	    }
	};



##Trie Tree
###7.[Word Search II](https://leetcode.com/problems/word-search-ii/)
    Given a 2D board and a list of words from the dictionary, find all words in the board.
    
    Each word must be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.
    
    For example,
    Given words = ["oath","pea","eat","rain"] and board =
    
    [
      ['o','a','a','n'],
      ['e','t','a','e'],
      ['i','h','k','r'],
      ['i','f','l','v']
    ]
    Return ["eat","oath"].
    Note:
    You may assume that all inputs are consist of lowercase letters a-z.


Answer in discuss

	 class Solution {
	        class Trie{
	        public:
	            Trie *children[26]; // pointers to its substrings starting with 'a' to 'z'
	            bool leaf; // if the node is a leaf, or if there is a word stopping at here
	            int idx; // if it is a leaf, the string index of the array words
	            Trie()
	            {
	                this->leaf = false;
	                this->idx = 0;
	                fill_n(this->children, 26, nullptr);            
	            }
	        };
	
	    public:
	        void insertWords(Trie *root, vector<string>& words, int idx)
	        {
	            int pos = 0, len = words[idx].size();
	            while(pos<len)
	            {
	                if(nullptr == root->children[words[idx][pos]-'a']) root->children[words[idx][pos]-'a'] = new Trie();
	                root = root->children[words[idx][pos++]-'a'];
	            }
	            root->leaf = true;
	            root->idx = idx;
	        }
	
	        Trie *buildTrie(vector<string>& words)
	        {
	            Trie *root = new Trie(); 
	            int i;
	            for(i=0; i<words.size();i++) insertWords(root, words, i);
	            return root;
	        }
	
	        void checkWords(vector<vector<char>>& board, int i, int j, int row, int col, Trie *root, vector<string> &res, vector<string>& words)
	        {
	            char temp;
	            if(board[i][j]=='X') return; // visited before;
	            if(nullptr == root->children[board[i][j]-'a']) return ; // no string with such prefix
	            else
	            {
	                temp = board[i][j];
	                if(root->children[temp-'a']->leaf)  // if it is a leaf
	                {
	                    res.push_back(words[root->children[temp-'a']->idx]);
	                    root->children[temp-'a']->leaf = false; // set to false to indicate that we found it already
	                }
	                board[i][j]='X'; //mark the current position as visited
	// check all the possible neighbors
	                if(i>0) checkWords(board, i-1, j, row, col, root->children[temp-'a'], res, words);
	                if((i+1)<row) checkWords(board, i+1, j, row, col,  root->children[temp-'a'], res, words);
	                if(j>0) checkWords(board, i, j-1,  row, col, root->children[temp-'a'], res, words);
	                if((j+1)<col)  checkWords(board, i, j+1,  row, col, root->children[temp-'a'], res, words);
	                board[i][j] = temp; // recover the current position
	            }
	        }
	
	        vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
	           vector<string> res;
	           int row = board.size();
	           if(0==row) return res;
	           int col = board[0].size();
	           if(0==col) return res;
	           int wordCount = words.size();
	           if(0==wordCount) return res;
	
	           Trie *root = buildTrie(words);
	
	           int i,j;
	           for(i =0 ; i<row; i++)
	           {
	               for(j=0; j<col && wordCount > res.size(); j++)
	               {
	                   checkWords(board, i, j, row, col, root, res, words);
	               }
	           }
	           return res;
	        }
	 };
