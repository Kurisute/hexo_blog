---
title: leetcode 刷题笔记
date: 2020-05-10 09:21:38
tags: programming
categories: [Learning Notes]
---

24-32题的部分节选。

leetcode刷题的简易笔记及代码（后续仍会更新和修改）

<!--more-->

## leet 24 两两交换链表节点

只处理头部两个的交换，对后面进行递归。

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if(head == NULL)
            return NULL;
        if(head -> next == NULL)
            return head;
        ListNode* node1 = head;
        ListNode* node2 = head -> next;

        node1 -> next = swapPairs(node2 -> next);
        node2 -> next = node1;
        
        return node2;

    }
};
```

## leet 25 K个一组翻转链表


```cpp
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* ptr = head;
        for(int i = 0;i < k;i++){
            if(ptr == NULL)
                return head;
            ptr = ptr -> next;
        }
        ListNode* newHead = reverse(head,ptr);
        head -> next = reverseKGroup(ptr,k);

        return newHead;
    }

    ListNode* reverse(ListNode* head,ListNode* tail){
        ListNode* cur,*pre,*nxt;
        pre = NULL; cur = head; nxt = head;

        while(cur != tail){
            nxt = cur -> next;
            cur -> next = pre;
            pre = cur;
            cur = nxt;
        }
        return pre;
    }
};
```

## KMP算法

```cpp
int KMP(char* t, char* p) 
{
    int i = 0; 
    int j = 0;

    while (i < strlen(t) && j < strlen(p))
    {
        if (j == -1 || t[i] == p[j]) 
        {
            i++;
            j++;
        }
        else 
            j = next[j];
    }

    if (j == strlen(p))
       return i - j;
    else 
       return -1;
}

void getNext(char * p, int * next)
{
    next[0] = -1;
    int i = 0, j = -1;

    while (i < strlen(p))
    {
        if (j == -1 || p[i] == p[j])
        {
            ++i;
            ++j;
            next[i] = j;
        }   
        else
            j = next[j];
    }
}
```

![KMP算法示意图](KMP.png)


## leet 30 串联所有单词的子串

设计给出单词的数量字典（因为不考虑拼接顺序，故数量成为唯一标准），然后遍历子串并制作词典与原词典进行比较即可看出是否符合条件。

```cpp
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        vector<int> res;
        if(s.size() == 0 || words.size() == 0)  //输入边界条件判断
            return res;

        map<string,int> dict;  
        int wordlen = words[0].size();
        int wordsnum = words.size();

        if(wordlen*wordsnum > s.size())     //拼接子串长度大于原串的情况
            return res;

        for(int i = 0;i < wordsnum;i++){    //制作单词数目字典
            dict[words[i]]++;
        }


        for(int i =  0;i <= s.size() - wordsnum*wordlen;i++){
            map<string,int> tempdict;
            int num = 0;
            while(num < wordsnum){
                string w = s.substr(i+num*wordlen,wordlen);
                if(dict.find(w) == dict.end())      //字典中不含有w
                    break;
                tempdict[w]++;
                if(tempdict[w] > dict[w])           //字典中含有的w不足
                    break;
                num++;
            }
            if(num == wordsnum)                     //恰好数量合适，添加位置
                res.push_back(i);
        }
        return res;
        
    }
};
```

## leet 32 最长有效括号

最值动态规划

```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        int len = s.size();
        if(len == 0)
            return 0;
        int dp[len];
        for(int i = 0;i < len;i++){
            dp[i] = 0;
        }
        for(int i = 1;i < len;i++){
            if(s[i] == '(')
                continue;
            if(i >= 1 && s[i - 1] == '('){
                if(i >= 2)
                    dp[i] = dp[i - 2] + 2;
                else
                    dp[i] = 2;
            }
            else{
                if(i - dp[i-1] - 1 >= 0 && s[i - dp[i-1] - 1] == '(')
                    if(i - dp[i-1] - 2 >= 0)
                        dp[i] = dp[i-1] + dp[i - dp[i-1] - 2] + 2;
                    else
                        dp[i] = dp[i-1] + 2;
            }
        }
        int max = 0;
        for(int i = 0;i < len;i++){
            if(dp[i] > max)
                max = dp[i];
        }
        return max;

    }
};
```