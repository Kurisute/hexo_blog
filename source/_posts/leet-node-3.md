---
title: leetcode 刷题笔记（三）
date: 2020-05-17 12:21:54
tags: programming
categories: [Learning Notes]
---

41-51题的部分节选。
 <!-- more -->

## 41. 缺失的第一个正数
[题目链接](https://leetcode-cn.com/problems/first-missing-positive/)

改题目要求使用O(N)算法及O(1)空间，因此使用原数组作为bitmap记录出现过的正整数。

先将负数全部转化为INT_MAX避免对bitmap造成干扰，然后根据出现过的正整数将对应下标所在数据调整为负数，正负符号即成为一组bitflag，然后对bitmap按顺序查找即可发现第一个没出现的正整数。

题目第二个难点在于数组下标从0开始计数，因此需要进行下标上的-1调整。

如果全部为负数，那么则说明数组大小内的正整数都出现过，那么就输出size+1。

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        if(nums.size() == 0)
            return 1;
                
        for(int i = 0;i < nums.size();i++){
            if(nums[i] <= 0)
                nums[i] = INT_MAX;
        }
        for(int i = 0;i < nums.size();i++){
            
            if(abs(nums[i]) <= nums.size()){
                nums[abs(nums[i]) - 1] = abs(nums[abs(nums[i]) - 1])*-1;
            }
        }
        for(int i = 0;i < nums.size();i++){
            cout << nums[i] << endl;
            if(nums[i] > 0)
                return i + 1;
        }
        return nums.size() + 1;
    }
};
```

## 42. 接雨水

[题目链接](https://leetcode-cn.com/problems/trapping-rain-water/)
双指针问题，也可以用动态规划解决。

```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int ans = 0;
        int left = 0;
        int right = height.size()-1;
        int leftmax = 0;
        int rightmax = 0;
        while(left < right){
            if(height[left] < height[right]){
                if(height[left] >= leftmax)
                    leftmax = height[left];
                else
                    ans += (leftmax - height[left]);
                left++;
            }
            else{
                if(height[right] >= rightmax)
                    rightmax = height[right];
                else
                    ans += (rightmax - height[right]);
                right--;
            }
        }
        return ans;
    }
};
```

## 43. 字符串相乘
[题目链接](https://leetcode-cn.com/problems/multiply-strings/)
大整数乘法。
```cpp
class Solution {
public:
    string multiply(string num1, string num2) {
        reverse(num1.begin(),num1.end());
        reverse(num2.begin(),num2.end());
        vector<int> tmpres(num1.size()+num2.size(),0);
        string res;
        for(int i = 0;i < num1.size() ;i++)
            for(int j = 0;j < num2.size();j++){
                int prod = (num1[i] - '0')*(num2[j] - '0');
                tmpres[i+j] += prod;
            }

        for(int i = 0;i < tmpres.size() - 1;i++){
            if(tmpres[i] >= 10){
                tmpres[i+1] += (tmpres[i] / 10);
                tmpres[i] %= 10;
            }
        }
        
        int tail = tmpres.size() - 1;
        while(tmpres[tail] == 0 && tail > 0)
            tail--;
        
        while(tail >= 0){
            res.push_back('0' + tmpres[tail]);
            tail--;
        }

        return res;
    }
};
```
## 44. 通配符匹配
[题目链接](https://leetcode-cn.com/problems/wildcard-matching/)

动态规划。
```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int slen = s.size();
        int plen = p.size();
        bool dp[slen + 1][plen + 1];
        for(int i = 0;i <= slen;i++)
            for(int j = 0;j <= plen;j++){
                if(i == 0 && j == 0)
                    dp[i][j] = true;
                else
                    dp[i][j] = false;
            }
        
        for(int i = 1;i <= plen;i++){
            if(p[i - 1] == '*')
                dp[0][i] = dp[0][i-1];        
        }
        
        
        for(int i = 1;i <= slen;i++)
            for(int j = 1;j <= plen;j++){
                if(p[j-1] == '?' || s[i-1] == p[j-1])
                    dp[i][j] = dp[i-1][j-1];
                if(p[j-1] == '*')
                    dp[i][j] = dp[i-1][j] || dp[i][j-1];
            }
        return dp[slen][plen];
    }
};
```
## 45. 跳跃游戏 II
[Link](https://leetcode-cn.com/problems/jump-game-ii/)

贪心问题

```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        if(nums.empty())
            return 0;
        int len = nums.size();
        int now = 0,end = 0,maxpos = 0;
        int step = 0;
        for(int i = 0;i < len - 1;i++){
            maxpos = max(maxpos,i + nums[i]);
            if(i == end){
                end = maxpos;
                step++;
            }
        }
        return step;
    }
};
```



## 51. N皇后
[Link](https://leetcode-cn.com/problems/n-queens/)
回溯法+剪枝，个人感觉难点在于主副对角线flag坐标的计算。

```cpp
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        //Flags Initialization: "left" means Leading Diagonal, while "right" means Sub Diagonal
        vector<string> temp;
        vector<bool> row_flag(n,true);
        vector<bool> col_flag(n,true);
        vector<bool> left_flag(2*n-1,true);
        vector<bool> right_flag(2*n-1,true);

        string s;
        for(int i = 0;i < n;i++)
            s.push_back('.');
        for(int i = 0;i < n;i++){
            temp.push_back(s);
        }

        vector<vector<string>> res;

        //deep-first-search
        dfs(temp,0,res,n,row_flag,col_flag,left_flag,right_flag);

        
        return res;
            
    }

    void dfs(vector<string>& temp,int d,vector<vector<string>>& res,int n,vector<bool>& row_flag,vector<bool>& col_flag,
                vector<bool>& left_flag,vector<bool>& right_flag){
        if(d == n){
            res.push_back(temp);
            return;
        }

        for(int i = 0;i < n;i++){
            if(row_flag[d] && col_flag[i] && left_flag[i+d] && right_flag[n+i-d]){
                temp[d][i] = 'Q';
                row_flag[d] = false;
                col_flag[i] = false;
                left_flag[i+d] = false;
                right_flag[n+i-d] = false;
            
                dfs(temp,d+1,res,n,row_flag,col_flag,left_flag,right_flag);
                
                //Back Tracking
                temp[d][i] = '.';
                row_flag[d] = true;
                col_flag[i] = true;
                left_flag[i+d] = true;
                right_flag[n+i-d] = true;
            }
        }
    }
};

```
