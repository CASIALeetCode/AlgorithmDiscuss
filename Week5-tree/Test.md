# Problem 

### Binary Tree Maximum Path Sum

    Binary Tree Maximum Path Sum
    Given a binary tree, find the maximum path sum.
    
    The path may start and end at any node in the tree.
    
    For example:
    Given the below binary tree,
    
           1
          / \
         2   3
    Return 6.
    
    
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
        int maxPathSumRe(TreeNode* root, int& maxSum) {
            if (!root) return 0;
            int leftPathSum = maxPathSumRe(root -> left, maxSum);
            int rightPathSum = maxPathSumRe(root -> right, maxSum);
            // bug point: 
            maxSum = max(maxSum, cur);
            */
            int cur = root -> val + max(max(leftPathSum, rightPathSum), 0);
            maxSum = max(maxSum, max(cur, root -> val + leftPathSum + rightPathSum));
            return cur;
        }
        int maxPathSum(TreeNode* root) {
            if (!root) return 0;
            int maxSum = numeric_limits<int>::min();
            maxPathSumRe(root, maxSum);
            return maxSum;
        }
    };

    

### Flatten Binary Tree to Linked List

    Flatten Binary Tree to Linked List
    Given a binary tree, flatten it to a linked list in-place.
    
    For example,
    Given
    
             1
            / \
           2   5
          / \   \
         3   4   6
    The flattened tree should look like:
       1
        \
         2
          \
           3
            \
             4
              \
               5
                \
                 6
                 
                 
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
        /***************************method 1*****************************/
		// time : O(2 * n)
		// space : O(n)
        void preorderTraversal(TreeNode* root, stack<TreeNode* >& cache) {
            if (!root) return;
            cache.push(root);
            preorderTraversal(root -> left, cache);
            preorderTraversal(root -> right, cache);
        }
        void flatten(TreeNode* root) {
            stack<TreeNode* > cache;
            preorderTraversal(root, cache);
            TreeNode* prev = NULL;
            while (!cache.empty()) {
                TreeNode* cur = cache.top();
                cache.pop();
                cur -> left = NULL;
                cur -> right = prev;
                prev = cur;
            }
        }
        
        /***********************method 2 **************************/
        // time : O(n)
		// space : O(n)
		if (!root) {
            return;
        }
        stack<TreeNode *> s;
        s.push(root);
        TreeNode *dummy = new TreeNode(0);
        while (!s.empty()) {
            TreeNode *node = s.top();
            s.pop();
            if (node -> right) {
                s.push(node -> right);
            }
            if (node -> left) {
                s.push(node -> left);
            }
            dummy -> right = node; // dummy -> root -> left -> right
            dummy -> left = NULL;
            dummy = node;
        }

		/********************** method 3 **************************/
		// time : O(n)
		// space : O(h)
        void flattenRe(TreeNode* root, TreeNode* & prev) {
            if (!root) return;
            flattenRe(root -> right, prev);
            flattenRe(root -> left, prev);
            root -> right = prev -> right;
            prev -> right = root;
            root -> left = NULL;
        }
        void flatten(TreeNode* root) {
            TreeNode* prev = new TreeNode(0);
            flattenRe(root, prev);
        }

		/*******************method 4 : morris traversal***********/
    	void flatten(TreeNode* root) {
            TreeNode* dummy = new TreeNode(0);
            while (root) {
                if (root -> left) {
                    TreeNode* next = root -> left;
                    while (next -> right && next -> right != root) {
                        next = next -> right;
                    }
                    if (next -> right == NULL) {
                        next -> right = root;
                        root = root -> left;
                    } else {
                        TreeNode* tmp = root -> right;
                        next -> right = root -> right;
                        root -> right = root -> left;
                        root -> left = NULL;
                        root = tmp;
                    }
                } else {
                    root = root -> right;
                }
            }
        }
    };
