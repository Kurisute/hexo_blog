---
title: leetcode 刷题笔记（四）
date: 2020-05-17 19:47:36
tags: programming
categories: [Learning Notes]
---

精选100题的53-75题。

<!--more-->

## 53. 最大子序和
[Link](https://leetcode-cn.com/problems/maximum-subarray/)

分治算法实现，利用了 __线段树__ 的思想。

```cpp

class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        return partition(nums,0,nums.size()-1);
    }

    int partition(vector<int>& nums,int l,int r){
        if(l == r)
            return nums[l];

        //Divide into 2 parts, recursion
        int m = (l+r)/2;
        int leftmax = partition(nums,l,m);
        int rightmax = partition(nums,m+1,r);

        //Find the max-array-sum which started from the left of the mid
        int mleftmax = INT_MIN;
        int sum = 0;
        for(int i = m;i >= l;i--){
            sum += nums[i];
            mleftmax = max(sum,mleftmax);
        }

        //Find the max-array-sum which started from the right of the mid
        int mrightmax = INT_MIN;
        sum = 0;
        for(int i = m+1;i <= r;i++){
            sum += nums[i];
            mrightmax = max(sum,mrightmax);
        }

        //Compare the 3 results, select the max
        int res = max(leftmax,rightmax);
        res = max(mleftmax+mrightmax,res);

        return res;

    }

};

```

## 56. 合并区间
[Link](https://leetcode-cn.com/problems/merge-intervals/)

对区间先进行排序。可以证明，可以被合并的区间必然连续排列，然后利用贪心法进行合并即可。

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> res;
        
        if(intervals.size() == 0)
            return res;

        sort(intervals.begin(),intervals.end());
        int n = intervals.size();
        
        int L = intervals[0][0];
        int R = intervals[0][1];
        for(int i = 1;i < n;i++){
            if(intervals[i][0] > R){
                res.push_back({L,R});
                L = intervals[i][0];
                R = intervals[i][1];
                continue;
            }

            R = max(R,intervals[i][1]);
        }
        res.push_back({L,R});
        return res;

    }
};
```

## 62. 不同路径
[Link](https://leetcode-cn.com/problems/unique-paths/)

简单的动态规划。（一开始用递归做超时了，汗，bksw）

```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        
        int dp[m][n];
        for(int i = 0;i < m;i++)
            dp[i][0] = 1;
        for(int i = 0;i < n;i++)
            dp[0][i] = 1;

        for(int i = 1;i < m;i++)
            for(int j = 1;j < n;j++){
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }

        return dp[m-1][n-1];
    }
};

```
## 64. 最小路径和

[Link](https://leetcode-cn.com/problems/minimum-path-sum/)

和62题极为相似的动态规划。

```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if(grid.size() == 0)
            return 0;
        int m = grid.size();
        int n = grid[0].size();

        int dp[m][n];
        dp[0][0] = grid[0][0];
        for(int i = 1;i < m;i++)
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        for(int i = 1;i < n;i++)
            dp[0][i] = dp[0][i-1] + grid[0][i];

        for(int i = 1;i < m;i++)
            for(int j = 1;j < n;j++)
                dp[i][j] = min(dp[i-1][j],dp[i][j-1]) + grid[i][j];
                
        return dp[m-1][n-1];
    }
};
```

## 72. 编辑距离
[Link](https://leetcode-cn.com/problems/edit-distance/)

动态规划求解，原题中三种变换相当于word1添加字符&&word
2添加字符&&word1中一个字符变换。

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        //Initialization
        int m = word1.size();
        int n = word2.size();
        int dp[m+1][n+1];
        for(int i = 0;i <= m;i++)
            dp[i][0] = i;
        for(int i = 0;i <= n;i++)
            dp[0][i] = i;

        //DP,dp[i][j]代表word1的前i个字符和word2的前j个字符的编辑距离。
        for(int i = 1;i <= m;i++)
            for(int j = 1;j <= n;j++)
                dp[i][j] = (word1[i-1] == word2[j-1])?(1+min(dp[i-1][j],min(dp[i][j-1],dp[i-1][j-1]-1))):(1+min(dp[i-1][j],min(dp[i][j-1],dp[i-1][j-1])));
    
        return dp[m][n];
    }
};
```
## 75. 颜色分类
[Link](https://leetcode-cn.com/problems/sort-colors/)

又被称为荷兰国旗问题，又是老dijkstra的脑洞...可以用双指针快速解决，因为只有三种元素需要排序。

```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int left = 0;
        int right = nums.size() - 1;
        int curr = 0;
        while(curr <= right){
            if(nums[curr] == 0){
                swap(nums[curr],nums[left]);
                curr++;
                left++;
            }
            else if(nums[curr] == 2){
                swap(nums[curr],nums[right]);
                right--;
            }
            else
                curr++;
        }
        return;
    }
};
```



