---
title: leetcode 刷题笔记（二）
date: 2020-05-10 14:55:20
tags: programming
categories: [Learning Notes]
---

33-40题的部分节选。

## 先说两句
这一次的笔记加入了题目的链接，省的翻阅题目麻烦的一匹：）

如果以后还能想到更好的改进还会陆续加进去~

 <!-- more -->
 
## leet 33 搜索旋转排序数组

[题目链接](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

基于二分查找，对于不满足排序的部分则对两边都进行查找。

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        if(nums.size() == 0)
            return -1;
        return bisearch(nums,0,nums.size()-1,target);
    }

    int bisearch(vector<int>& nums,int head,int tail,int target){
        cout << head << " " << tail << endl;
        if(tail - head <= 1){
            if(nums[head] == target)
                return head;
            else if(nums[tail] == target)
                return tail;
            else
                return -1; 
        }

        if(nums[head] > nums[tail]){
            int res1 = bisearch(nums,head,(head+tail)/2,target);
            int res2 = bisearch(nums,(head+tail)/2,tail,target);
            if(res1 != -1)
                return res1;
            if(res2 != -1)
                return res2;
            return -1;
        }

        if(nums[(head+tail)/2] >= target)
            return bisearch(nums,head,(head+tail)/2,target);
        else
            return bisearch(nums,(head+tail)/2,tail,target);
    }

};
```
## leet 34 在排序数组中查找元素的第一个和最后一个位置

[题目链接](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

二分查找边界，关键在于边界条件的判断。对于左边界，右侧等于target时要继续查找。右边界亦然。最后两个边界临界值判断时，左边界优先取左侧，右边界优先取右侧。

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> res;
        if(nums.size() == 0){
            res.push_back(-1);
            res.push_back(-1);
            return res;
        }
            

        int left = searchleft(nums,0,nums.size() - 1, target);
        int right = searchright(nums,0,nums.size() - 1, target);

        res.push_back(left);
        res.push_back(right);

        return res;
        
    }

    int searchleft(vector<int>& nums, int left, int right, int target){
        if(right - left <= 1){
            if(nums[left] == target)
                return left;
            else if(nums[right] == target)
                return right;
            else 
                return -1;
        }
            
        int mid = (left + right)/2;
        if(nums[mid] >= target)
            return searchleft(nums,left,mid,target);
        else
            return searchleft(nums,mid,right,target);
    }

    int searchright(vector<int> nums, int left, int right, int target){
        if(right - left <= 1){
            if(nums[right] == target)
                return right;
            else if(nums[left] == target)
                return left;
            else 
                return -1;
        }
        int mid = (left + right)/2;
        if(nums[mid] <= target)
            return searchright(nums,mid,right,target);
        else
            return searchright(nums,left,mid,target);
    }
};
```

## leet 39. 组合总和

[题目链接](https://leetcode-cn.com/problems/combination-sum/)

深度优先搜索，为了避免重复需要记录深度进行剪枝。

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> res;
        if(candidates.size() == 0)
            return res;
        vector<int> temp;
        dfs(candidates,target,temp,res,0);
        return res;
    }

    void dfs(vector<int>& candidates, int target, vector<int>& temp, vector<vector<int>>& res,int deep){
        if(target == 0){
            res.push_back(temp);
            return;
        }
        if(target < 0)
            return;

        for(int i = deep;i < candidates.size();i++){
            if(candidates[i] <= target){
                temp.push_back(candidates[i]);
                dfs(candidates,target-candidates[i],temp,res,i);
                temp.pop_back();
            }
        }
        return;
    }

};
```

## leet 40. 组合总和 II

[题目链接](https://leetcode-cn.com/problems/combination-sum-ii/)

相比39题去重步骤更为繁琐，首先对candidates进行排序以简化查重步骤。而后需保证同一深度不能使用同一数字，否则会导致结果重复，可以设置一个flag变量达到目的。

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> res;
        if(candidates.size() == 0)
            return res;
        vector<int> temp;
        sort(candidates.begin(),candidates.end());
        dfs(candidates,target,temp,res,0);
        return res;
    }

    void dfs(vector<int>& candidates, int target, vector<int>& temp, vector<vector<int>>& res,int deep){
        cout << target << endl;
        if(target == 0){
            res.push_back(temp);
            return;
        }
        if(target < 0)
            return;

        int flag = 0;
        for(int i = deep;i < candidates.size();i++){
            if(candidates[i] <= target && candidates[i] != flag){
                flag = candidates[i];
                temp.push_back(candidates[i]);
                dfs(candidates,target-candidates[i],temp,res,i+1);
                temp.pop_back();
            }
        }
        return;
    }
};
```