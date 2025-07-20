---
title: leetcode 刷题笔记（五）
date: 2020-05-26 11:39:49
tags: programming
categories: [Learning Notes]
---

精选100题的76-96题。

<!--more-->

## 76. 最小覆盖子串
[Link](https://leetcode-cn.com/problems/minimum-window-substring/)
双指针滑动窗口。难点在于用cnt+map记录已经匹配的字母。

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        int m = s.size();
        int n = t.size();
        int left = 0;
        int cnt = 0;
        int minlen = m + 1;
        int start = 0;
        map<char,int> busket;
        for(int i = 0;i < n;i++){
            busket[t[i]]++;
        }
        for(int i = 0;i < m;i++){
            if(--busket[s[i]] >= 0)
                cnt++;
            while(cnt == n){
                if(minlen > i - left + 1){
                    minlen = i - left + 1;
                    start = left;
                }
                if(++busket[s[left]] > 0)
                    cnt--;
                left++;
            }
        }

        if(minlen == m + 1)
            return "";

        return s.substr(start,minlen);
    }
};
```

##79. 单词搜索
[Link](https://leetcode-cn.com/problems/word-search/)

可能这就是DFS+剪枝回溯吧，每次都思路简单但是超臭超长（摊手）。要点在于：

 1. 找到一个符合正确的答案后就直接返回true，实现剪枝。
 2. c++传递引用的速度要比形参快非常多，能用引用尽量使用引用。

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if(board.size() == 0)
            return false;
        if(word.size() == 0)
            return true;

        int m = board.size();
        int n = board[0].size();
        bool res = false;
        vector<vector<bool>> flags(m,vector<bool>(n));

        for(int i = 0;i < m;i++)
            for(int j = 0;j < n;j++)
                flags[i][j] = true;

        for(int i = 0;i < m;i++){
            for(int j = 0;j < n;j++){
                if(board[i][j] == word[0]){
                    if(word.size() == 1)
                        return true;
                    else{
                        flags[i][j] = false;
                        res = dfs(board,flags,i,j,word,1);
                        if(res)
                            return true;
                        flags[i][j] = true;
                    }
                }
            }
        }
        return res;
    }

    bool dfs(vector<vector<char>>& board,vector<vector<bool>>& flags, int y,int x,string& word,int deep){
        if(deep == word.size())
            return true;
        
        //cout << y << " " << x << endl;

        int m = board.size();
        int n = board[0].size();
        bool up = false,down = false,left = false,right = false;
        if(y < m - 1 && board[y+1][x] == word[deep] && flags[y+1][x]){
            flags[y+1][x] = false;
            down = dfs(board,flags,y+1,x,word,deep+1);
            flags[y+1][x] = true;
            if(down)
                return true;
        }
        if(y > 0 && board[y-1][x] == word[deep] && flags[y-1][x]){
            flags[y-1][x] = false;
            up = dfs(board,flags,y-1,x,word,deep+1);
            flags[y-1][x] = true;
            if(up)
                return true;
        }

        if(x < n - 1 && board[y][x+1] == word[deep] && flags[y][x+1]){
            flags[y][x+1] = false;
            right = dfs(board,flags,y,x+1,word,deep+1);
            flags[y][x+1] = true;
            if(right)
                return true;
        }

        if(x > 0 && board[y][x-1] == word[deep] && flags[y][x-1]){
            flags[y][x-1] = false;
            left = dfs(board,flags,y,x-1,word,deep+1);
            flags[y][x-1] = true;
            if(left)
                return true;
        }

        //cout << up << down << left << right << endl;

        return false;
    }


};
```

## 84. 柱状图中最大的矩形
[Link](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

这道题虽然通过单调栈完成了，但是对于栈方法理解还有缺陷，需要以后复习。

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        stack<int> stk;
        stk.push(-1);
        heights.push_back(-1);
        int largest = 0;
        int n = heights.size();
        for(int i = 0;i < heights.size();i++){
            while(stk.top() != -1 && heights[stk.top()] > heights[i]){
                int h = heights[stk.top()];
                stk.pop();
                largest = max(largest,(i - stk.top() - 1) * h);
            }
            stk.push(i);
        }
    return largest;
    }
};
```


## 85. 最大矩形
[Link](https://leetcode-cn.com/problems/maximal-rectangle/)

84题的二维进阶版，只需要对每行的每个位置计算矩形可能的最大宽度，然后再纵向选取最大宽度即可。为了方便使用单调栈会增加头尾两个0行。

```cpp
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        if(matrix.empty())
            return 0;
        int m = matrix.size();
        int n = matrix[0].size();
        vector<vector<int>> maxrow(m+2,vector<int>(n,0));
        for(int i = 0;i < m;i++){
            for(int j = 0;j < n;j++){
                if(matrix[i][j] == '1'){
                    if(j == 0)
                        maxrow[i+1][j] = 1;
                    else
                        maxrow[i+1][j] = maxrow[i+1][j-1] + 1;
                }
            }
        }
        /*测试代码
        for(int i = 0;i < m+2;i++){
            for(int j = 0;j < n;j++)
                cout << maxrow[i][j] << " ";
            cout << endl;
        }
        */
        int maxsize = 0;
        for(int j = 0;j < n;j++){
            stack<int> stk;
            int tempmax = 0;

            for(int i = 0;i < m+2;i++){
                while(!stk.empty() && maxrow[i][j] < maxrow[stk.top()][j]){
                    int h = maxrow[stk.top()][j];
                    stk.pop();
                    int left = stk.top() + 1;
                    int right = i - 1;
                    tempmax = max(tempmax,(right - left + 1)*h);
                    //cout << i << " " << j << " " << tempmax << endl;
                }
                stk.push(i);
                //cout << "push" << i << endl;    
            }
            maxsize = max(maxsize,tempmax);
        }

        return maxsize;


    }
};
```

## 94. 二叉树的中序遍历
[Link](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

这题简直就是模板题，需要好好记忆和练习。中序遍历利用栈，对于每个节点将子节点入栈，如果到达叶节点则输出，然后弹出栈顶元素作为根元素重复执行前一过程。

关于前中后序遍历，下面这个帖子会有很大帮助（图解）：[Link](https://www.cnblogs.com/bigsai/p/11393609.html)

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
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> stk;
        
        while(!stk.empty() || root){
            if(root){
                stk.push(root);
                root = root -> left;
            }
            else{
                root = stk.top();
                stk.pop();
                res.push_back(root -> val);
                root = root -> right;
            } 
        }
        return res;
    }
};
```
## 96. 不同的二叉搜索树
[Link](https://leetcode-cn.com/problems/unique-binary-search-trees/)

Catalan数的来源。采用分治算法，直接递归会TLE，所以建立一个静态数组字典用来存储计算过的结果以便之后复用。

```cpp
class Solution {
public:
    int numTrees(int n) {
        static vector<int> dict(50,-1);
        if(n == 0)
            return 1;
        if(n == 1)
            return 1;
        if(n == 2)
            return 2;
        int total = 0;
        for(int i = 1;i <= n;i++){
            int leftnum,rightnum;
            if(dict[i-1] == -1)
                dict[i-1] = numTrees(i-1);
            if(dict[n-i] == -1)
                dict[n-1] = numTrees(n-i);
            
            leftnum = dict[i-1];
            rightnum = dict[n-i];
            //cout << i << " " << leftnum << " " << rightnum << endl;
            total += (leftnum*rightnum);
        }
        return total;
    }
};
```


