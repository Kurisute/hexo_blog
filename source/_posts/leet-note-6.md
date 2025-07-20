---
title: leetcode 刷题笔记（六）
date: 2020-06-17 10:33:15
tags: programming
categories: [Learning Notes]
---


精选100题的98-136题。

<!--more-->


## 98. 验证二叉搜索树
[Link](https://leetcode-cn.com/problems/validate-binary-search-tree/)

BST的中序遍历一定是有序数组，利用这一性质可以检查是否是BST。这题非常弱智的出现了INT_MIN做根节点的数据，所以利用long long类型来规避该问题。

```cpp
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
    bool isValidBST(TreeNode* root) {
        stack<TreeNode*> stk;
        long long inorder = (long long)INT_MIN - 1; 
        while(!stk.empty() || root){
            while(root){
                stk.push(root);
                root = root -> left;
            }
            root = stk.top();
            stk.pop();
            if(root -> val <= inorder)
                return false;
            inorder = root -> val;
            root = root -> right;
        }
        return true;
    }
};
```

## 105. 从前序与中序遍历序列构造二叉树
[Link](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

根据前序遍历确定根节点，然后在中序遍历中寻找根节点，确定左右子树大小，然后回到前序遍历中找到下一个根节点，迭代。

为节省时间开销，利用unordered_map制作中序遍历的hash表。

```cpp
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
private:
    unordered_map<int,int> index;
public:
    TreeNode* mybuild(vector<int>& preorder,vector<int>& inorder,int preleft,int preright,int inleft,int inright){
        if(preleft > preright)
            return nullptr;
        int preroot = preleft;
        int inroot = index[preorder[preroot]];
        TreeNode* root = new TreeNode(preorder[preroot]);
        int leftsize = inroot - inleft;

        root -> left = mybuild(preorder,inorder,preleft+1,preleft+leftsize,inleft,inroot-1);
        root -> right = mybuild(preorder,inorder,preleft+leftsize+1,preright,inroot+1,inright);
        return root;
    }

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = inorder.size();
        for(int i = 0;i < n;i++){
            index[inorder[i]] = i;
        }

        return mybuild(preorder,inorder,0,n-1,0,n-1);
    }
};
```

## 114. 二叉树展开为链表
[Link](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

当存在左节点时，将左子树插入根和右节点之间，递归（或递推）。

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void flatten(TreeNode* root) {
        TreeNode* pre;
        while(root){
            if(root -> left){
                pre = root -> left;
                while(pre -> right) pre = pre -> right;
                pre -> right = root -> right;
                root -> right = root -> left;
                root -> left = nullptr;
            }
            root = root -> right;
        }
        return;
    }
};
```

## 121. 买卖股票的最佳时机
[Link](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

贪心法。

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int minptr = 0;
        int curptr = 0;
        int n = prices.size();
        int maxprofit = 0;
        while(curptr < n){
            if(prices[curptr] < prices[minptr])
                minptr = curptr;
            else{
                if(prices[curptr] - prices[minptr] > maxprofit)
                    maxprofit = prices[curptr] - prices[minptr];
            }
            curptr++;
        }
        return maxprofit;
    }
};
```

## 124. 二叉树中的最大路径和
[Link](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

```cpp
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
    int maxPathSum(TreeNode* root) {
        if(!root)
            return 0;
        int maxval = INT_MIN;
        dfs(root,maxval);
        return maxval;
    }

    int dfs(TreeNode* node,int& maxval){
        if(node == NULL)
            return 0;
        int leftval = max(0,dfs(node -> left,maxval));
        int rightval = max(0,dfs(node -> right,maxval));
        maxval = max(maxval,leftval + rightval + node -> val);
        return node -> val + max(leftval,rightval);
    }
};
```

## 128. 最长连续序列
[Link](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        //dp[i]表示当数字i在连续序列的左端或右端时的最大长度
        unordered_map<int,int> dp;
        int res = 0;
        for(int i = 0;i < nums.size();i++){
            int x = nums[i];
            if(!dp[x]) dp[x+dp[x+1]] = dp[x-dp[x-1]] = dp[x] = dp[x+1] + dp[x-1] + 1;
            res = max(res,dp[x]);
        }
        return res;
    }
};
```

## 136. 只出现一次的数字
[Link](https://leetcode-cn.com/problems/single-number/)

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        //异或运算的应用
        int res = 0;
        for(int i = 0;i < nums.size();i++)
            res ^= nums[i];
        return res;
    }
};
```