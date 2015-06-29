# Review

有关树的题目可能考察的知识点：
	
*	遍历（前序，中序，后序）
*	构建	
*	性质（求深度，求和，平衡二叉树，对称...)
*	二叉搜索树

一般要求：

*	递归实现
*	迭代实现

相关题目涉及的算法：

*	DFS
*	BFS
*	divide and conquer
*	Binary Search

# Examples

## Traversal
### 1. preorder
**Problem**

	Binary Tree Preorder Traversal
	Given a binary tree, return the preorder traversal of its nodes' values.
	For example:
	Given binary tree {1,#,2,3},
   	1
    \
     2
    /
   	3
	return [1,2,3].
	Note: Recursive solution is trivial, could you do it iteratively?


**Solution**

	典型的DFS问题
	1. 递归 ： time O(n), space O(n) ==> recursive stack size, average is O(logn)
	2. 迭代 ： 维护一个stack，time O(n), sapce O(n)
	3. Morris traversal（线索二叉树）：time O(n), space O(1)
**code**

	/**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        /*******************************recursion*********************************************/
        // time : O(n), space : O(logn) recursive stack size
        void preorderRecursive(TreeNode* root, vector<int>& res) {
            if (!root) return;
            res.push_back(root -> val);
            preorderRecursive(root -> left, res);
            preorderRecursive(root -> right, res);
        }
        vector<int> preorderTraversal_1(TreeNode* root) {
            vector<int> res;
            preorderRecursive(root, res);
            return res;
        }
        /*********************************iteration*******************************************/
        //  time : O(n), space : O(logn) stack size
        vector<int> preorderTraversal_2(TreeNode* root) {
            stack<TreeNode* > s;
            vector<int> res;
            while (root || !s.empty()) {
                if (root) {
                    res.push_back(root -> val);
                    s.push(root);
                    root = root -> left; // visit left subtree
                } else {
                    root = s.top() -> right; // visit right subtree
                    s.pop(); // remove root
                }
            }
            return res;
        }
        /***********************************morris ***********************************************/
        /*
        Morris Traversal的方法。 
        Morris遍历方法用了线索二叉树，这个方法利用叶子节点中的右空指针指向中序遍历下的后继节点就可以了。 
        这样就节省了需要用栈来记录前驱或者后继结点的额外空间， 所以可以达到O（1）的空间复杂度。
        不过这种方法有一个问题就是会暂时性的改动树的结构， 
        */
        // time : O(n), space O(1)
        vector<int> preorderTraversal(TreeNode* root) {
            vector<int> res;
            while (root) {
                if (root -> left) {
                    TreeNode* next = root -> left;
                    while (next -> right && next -> right != root) {
                        next = next -> right; // find morris node(left subtree's last right node)
                    }
                    // left subtree visited, recover tree, visit right subtree
                    if (next -> right == root) {
                        root = root -> right; // visit right subtree
                        next -> right = NULL; // recover tree
                    } else {
                        res.push_back(root -> val);
                        next -> right = root; // morris
                        root = root -> left; // visit left subtree
                    }
                } else {
                    res.push_back(root -> val);
                    root = root -> right;
                }
            }
            return res;
        }
    };
 
 **Similar problem**
 
 	inorder traversal
 	postorder traversal
 	
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
    //--------------------------recursive-----------------------------------------
        void postorderRecursive(TreeNode* root, vector<int>& res) {
            if (!root) return;
            postorderRecursive(root -> left, res);
            postorderRecursive(root -> right, res);
            res.push_back(root -> val);
        }
        vector<int> postorderTraversal_1(TreeNode* root) {
            vector<int> res;
            postorderRecursive(root, res);
            return res;
        }
    //----------------------------iteration-----------------------------------------
        vector<int> postorderTraversal(TreeNode* root) {
            vector<int> res;
            stack<TreeNode* > s;
            TreeNode* prev = NULL;
            while (root || !s.empty()) {
                if (root) {
                    s.push(root);
                    root = root -> left;
                } else {
                    root = s.top();
                    // right subtree is NULL or visited, than visit current node
                    if (!root -> right || root -> right == prev) {
                        res.push_back(root -> val);
                        s.pop();
                        prev = root;
                    // visit right subtree first
                    } else {
                        root = root -> right;
                    }
                }
            }
            return res;
        }
    };
### 2. level order

**Problem**
	Binary Tree Level Order Traversal
	Given a binary tree, 
	return the level order traversal of its nodes' values. (ie, from left to right, level by 	level).

	For example:
	Given binary tree {3,9,20,#,#,15,7},
    	3
  	   / \
      9  20
      /  \
     15   7
	return its level order traversal as:
	[
  		[3],
  		[9,20],
  		[15,7]
	]

**Solution**

	典型的BFS问题，也可以用DFS
	1. dfs : recursion, 用level来标记root -> val的位置，res[level].push_back(root -> val)
	2. bfs : iteration, 维护一个队列（有两种方式来标记一层的结束：lastNode记录；NULL标记）

**Code**

    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        /********************************BFS, iteration**************************/
        vector<vector<int>> levelOrder(TreeNode* root) {
            if(!root) return vector<vector<int> >();
            queue<TreeNode* > q;
            q.push(root);
            q.push(NULL);
            vector<vector<int> > res;
            res.push_back(vector<int>());
            while (!q.empty()) {
                TreeNode* node = q.front();
                q.pop();
                if (!node) {
                    if (q.empty()) break;
                    res.push_back(vector<int>());
                    q.push(NULL);
                } else {
                    res[res.size() - 1].push_back(node -> val);
                    if (node -> left) q.push(node -> left);
                    if (node -> right) q.push(node -> right);
                }
            }
            return res;
        }
        /********************************DFS, recursion****************************/
        void levelOrderRe(TreeNode* root, int level, vector<vector<int> >& res) {
            if (!root) return;
            if (res.size() == level) res.push_back(vector<int>());
            res[level].push_back(root -> val);
            levelOrderRe(root -> left, level + 1, res);
            levelOrderRe(root -> right, level + 1, res);
        }
        vector<vector<int> > levelOrder(TreeNode* root) {
            vector<vector<int> > res;
            levelOrderRe(root, 0, res);
            return res;
        }
    };
    
**Similar Problem**

	Binary Tree Level Order Traversal II
	Binary Tree Zigzag Level Order Traversal

	/**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        // -----------------------two stack------------------------------------
        vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
            vector<vector<int> > res;
            if (!root) return res;
            vector<stack<TreeNode* > > s(2);
            int cur = 0;
            int last = 1;
            bool leftToRight = true;
            s[cur].push(root);
            res.push_back(vector<int>());
            while (!s[cur].empty() || !s[last].empty()) {
                if (!s[cur].empty()) {
                    TreeNode* node = s[cur].top();
                    s[cur].pop();
                    res[res.size() - 1].push_back(node -> val);
                    if (!leftToRight) {
                        if (node -> right) s[last].push(node -> right);
                        if (node -> left) s[last].push(node -> left);
                    } else {
                        if (node -> left) s[last].push(node -> left);
                        if (node -> right) s[last].push(node -> right);
                    }
                } else {
                    cur = !cur;
                    last = !last;
                    leftToRight = !leftToRight;
                    res.push_back(vector<int>());
                }
            }
            return res;
        }
        // -----------------------a vector + begin + end ----------------------------
        vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
            vector<vector<int> > res;
            if (!root) return res;
            int begin = 0;
            int end = 0;
            vector<TreeNode* > q;
            q.push_back(root);
            bool leftToRight = true;
            while (begin <= end) {
                res.push_back(vector<int>());
                for (int i = end; i >= begin; --i) {
                    res[res.size() - 1].push_back(q[i] -> val);
                    if (leftToRight) {
                        if (q[i] -> left) q.push_back(q[i] -> left);
                        if (q[i] -> right) q.push_back(q[i] -> right);
                    } else {
                        if (q[i] -> right) q.push_back(q[i] -> right);
                        if (q[i] -> left) q.push_back(q[i] -> left);
                    }
                }
                begin = end + 1;
                end = q.size() - 1;
                leftToRight = !leftToRight;
            }
            return res;
        }
    };

## Construct Tree
### Construct Binary Tree from Inorder and Postorder Traversal

**Problem**

	Construct Binary Tree from Inorder and Postorder Traversal 
	Given inorder and postorder traversal of a tree, construct the binary tree.

	Note:
	You may assume that duplicates do not exist in the tree.

**Solution**
	
	典型的分治问题:
		root = ...;
		root -> left = ...;
		root -> right = ...;
		return root;

**Code**

	/**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        int findInorderRoot(vector<int>& inorder, int s, int e, int target) {
            for (int i = s; i <= e; ++i) {
                if (inorder[i] == target) return i;
            }
        }
        TreeNode* buildTreeRe(vector<int>& inorder, int iStart, int iEnd, 
        					   vector<int>& postorder, int pStart, int pEnd) {
            if (iStart > iEnd) {
                return NULL;
            }
            TreeNode* root = new TreeNode(postorder[pEnd]);
            int index = findInorderRoot(inorder, iStart, iEnd, postorder[pEnd]);
            root -> left = buildTreeRe(inorder, iStart, index - 1, 
            							 postorder, pStart, pStart + index - iStart - 1);
            root -> right = buildTreeRe(inorder, index + 1, iEnd, 
            							 postorder, pStart + index - iStart, pEnd - 1);
            return root;
        }
        TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
            if (inorder.empty() || postorder.size() != inorder.size()) return NULL;
            int n = inorder.size();
            return buildTreeRe(inorder, 0, n - 1, postorder, 0, n - 1);
        }
    };

**Similar**

	Construct Binary Tree from Preorder and Inorder Traversal
	Convert Sorted Array to Binary Search Tree : 高度最小BST ？
	Convert Sorted List to Binary Search Tree: 
		很多小trick, e.g 找链表的中点的前一个；先构建右子树，再构建左子树

## Tree Property

### 1. Depth
	
	Maximum depth of binary tree
	
	/**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
    //----------------------recursive : DFS----------------------------------------
        int maxDepth(TreeNode* root) {
            if (!root) return 0;
            return 1 + max(maxDepth(root -> left), maxDepth(root -> right));
        }
    //-------------------------iterative : BFS----------------------------------
        int maxDepth(TreeNode* root) {
            if (!root) return 0;
            queue<TreeNode* > q;
            q.push(root);
            q.push(NULL);
            int depth = 0;
            while (!q.empty()) {
                TreeNode* node = q.front();
                q.pop();
                if (!node) {
                    depth++;
                    if (q.empty()) return depth;
                    q.push(NULL);
                } else {
                    if (node -> left) q.push(node -> left);
                    if (node -> right) q.push(node -> right);
                }
            }
            return depth;
        }
    };
    
	Minimum depth of binary tree
	
    //----------------------recursion: dfs----------------------------
    int minDepth(TreeNode* root) {
        if (!root) return 0;
        if (!root -> left) {
            return minDepth(root -> right) + 1;
        }
        if (!root -> right) {
            return minDepth(root -> left) + 1;
        }
        return min(minDepth(root -> left), minDepth(root -> right)) + 1;
    }
    //--------------------iteration: bfs----------------------------------
    // use NULL mark level's end, then depth++
    int minDepth_3(TreeNode* root) {
        if (!root) return 0;
        queue<TreeNode* > q;
        q.push(root);
        q.push(NULL);
        int level = 0;
        while (!q.empty()) {
            TreeNode* node = q.front();
            q.pop();
            if (!node) {
                level++;
                if (q.empty()) return level;
                q.push(NULL);
            } else if (!node -> left && !node -> right) {
                return level + 1;
            } else {
                if (node -> left) q.push(node -> left);
                if (node -> right) q.push(node -> right);
            }
        }
    }
 


### Path Sum 	
 	Path sum I,II : dfs + backtracking
 
## Binary Search Tree

	validate binary search tree
	Insert, Remove, Search

### binary search tree iterator

**Problem**

    Binary Search Tree Iterator
    Implement an iterator over a binary search tree (BST). 
    Your iterator will be initialized with the root node of a BST.
    
    Calling next() will return the next smallest number in the BST.
    
    Note: next() and hasNext() should run in average O(1) time and uses O(h) memory, 
    where h is the height of the tree.

**Solution**

	use a stack, average time : O(n), space O(n)

**Code**
 
 	/**
     * Definition for binary tree
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class BSTIterator {
    public:
        BSTIterator(TreeNode *root) {
            this -> root = root;
            updateNodes(root);
        }
        
        /** @return whether we have a next smallest number */
        bool hasNext() {
            return !nodes.empty();
        }
    
        /** @return the next smallest number */
        int next() {
            TreeNode* cur = nodes.top();
            nodes.pop();
            updateNodes(cur -> right);
            return cur -> val;
        }
    private:
        void updateNodes(TreeNode* cur) {
            while (cur) {
                nodes.push(cur);
                cur = cur -> left;
            }
        }
        TreeNode* root = NULL;
        stack<TreeNode* > nodes;
    };
 
### Inorder Successor

**Problem**

	Write an algorithm to find the ‘next’ node (i.e., in-order successor) of a given node 
    in a binary search tree where each node has a link to its parent.
    
    (
    In Binary Tree, Inorder successor of a node is the next node in Inorder traversal 
    of the Binary Tree. Inorder Successor is NULL for the last node in Inoorder traversal.
    In Binary Search Tree, Inorder Successor of an input node can also be defined as 
    the node with the smallest key greater than the key of input node. 
    So, it is sometimes important to find next node in sorted order.
    )

**Solution**

    1. with parent pointer:
    1) If right subtree of node is not NULL, then succ lies in right subtree. Do following.
    Go to right subtree and return the node with minimum key value in right subtree.
    2) If right sbtree of node is NULL, then succ is one of the ancestors. Do following.
    Travel up using the parent pointer until you see a node which is left child of it’s parent. 
    The parent of such a node is the succ.
    
    2. without parent pointer: for BST
    1) If right subtree of node is not NULL, then succ lies in right subtree. Do following.
    Go to right subtree and return the node with minimum key value in right subtree.
    2) If right sbtree of node is NULL, then start from root and us search like technique. Do following.
    Travel down the tree, 
    if a node’s data is greater than root’s data then go right side, otherwise go to left side.

**Code**

	#include <iostream>
    using namespace std;
    
    struct TreeNode {
        int val;
        TreeNode* left;
        TreeNode* right;
        TreeNode* parent;
        TreeNode(int v) : val(v), left(NULL), right(NULL), parent(NULL) {}
    };
    
    TreeNode* mostLeftOfSubtree(TreeNode* root) {
        if (!root) return NULL;
        while (root -> left) {
            root = root -> left;
        }
        return root;
    }
    
    /**********************method 1******************************/
    TreeNode* findInorderSuccessor(TreeNode* root, TreeNode* node) {
        if (!root || !node) return NULL;
        // step 1
        if (node -> right) {
            return mostLeftOfSubtree(node -> right);
        }
        // step 2
        TreeNode* p = node;
        while (p -> parent && p -> parent -> left != p) {
            p = p -> parent;
        }
        return p -> parent;
    }
    /*************************method 1*****************************/
    TreeNode* findInorderSuccessor(TreeNode* root, TreeNode* node) {
        if (!root || !node) return NULL;
        // step 1
        if (node -> right) {
            return mostLeftOfSubtree(node -> right);
        }
        // step 2 : binary search, find the first parent which is greater than node
        TreeNode* succ = NULL;
        while (root) {
            if (root -> val > node -> val) {
                succ = root;
                root = root -> left;
            } else if (root -> val < node -> val) {
                root = root -> right;
            } else {
                break;
            }
            return succ;
        }
        
    }
    
    void connectNodes(TreeNode* root, TreeNode* left, TreeNode* right, TreeNode* parent) {
        root -> left = left;
        root -> right = right;
        root -> parent = parent;
    }
    
    int main()
    {
    /*
    				1
    			  /	  \
    			2       3
              /   \      \
    		 4	   5      8
    			 /  
    			6
    */
        TreeNode* root = new TreeNode(1);
        TreeNode* node1 = new TreeNode(2);
        TreeNode* node2 = new TreeNode(3);
        TreeNode* node3 = new TreeNode(4);
        TreeNode* node4 = new TreeNode(5);
        TreeNode* node5 = new TreeNode(6);
        TreeNode* node6 = new TreeNode(7);
        TreeNode* node7 = new TreeNode(8);
        connectNodes(root, node1, node2, NULL);
        connectNodes(node1, node3, node4, root);
        connectNodes(node4, node6, NULL, node1);
        connectNodes(node2, node7, NULL, root);
        //TreeNode* succ = findInorderSuccessor(root, node4); // 1
        TreeNode* succ = findInorderSuccessor(root, node7); // 1
        if (succ) {
            cout << succ -> val << endl;
        } else {
            cout << "NULL" << endl;
        }
        return 0;
    }
