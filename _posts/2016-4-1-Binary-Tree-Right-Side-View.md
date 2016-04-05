---
layout: post
title: Binary Tree - Right Side View Algorithm
---

I came across what I thought was an interesting algorithm on [leetcode](https://leetcode.com/problems/binary-tree-right-side-view/) not too long ago.
The problem statment is as follows:

> Given a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.

> For example:
> Given the following binary tree,

>         1            <---
>       /   \
>      2     3         <---
>       \     \
>        5     4       <---
>
> You should return [1, 3, 4].

As per the norm with OJ problems, the most accessible example given does not closely cover a lot of the edge cases necessary to consider
when coming up with a sturdy implementation. Let's try and think about what it means to get the right-most node as we
go down the tree. The key realization here, is that getting the right-most node from each level really just means getting the largest
value at each level. This boils down to a modified breadth first search algorithm. 

So let's start with just taking a look at a regular BFS algorithm, and then we'll take a look at how we'll need to modify it.
Your typical C++ BFS algorithm may look like this:

```cpp

struct TreeNode {
  int val;
  TreeNode* left;
  TreeNode* right;
  TreeNode(int val) : val(val), left(NULL), right(NULL) { }
};

vector<int> breadthFirstSearch(TreeNode* root) {
  vector<int> returnVector;
  queue<TreeNode*> q;
  if (!root) return returnVector;
  
  // Fill q with root
  q.push(root);

  while(q.size()) {
    if (q.front()->left) q.push(q.front()->left);
    if (q.front()->right) q.push(q.front()->right);

    returnVector.push_back(q.front()->val);
    q.pop();
  }

  return returnVector;
}

```