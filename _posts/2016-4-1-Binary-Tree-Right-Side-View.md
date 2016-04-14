---
layout: post
title: Binary Tree - Right Side View Algorithm
---

I came across a cool algorithm on [leetcode](https://leetcode.com/problems/binary-tree-right-side-view/) not too long ago and to practice my
writing, I figured I'd try to explain my thought process behind it.

The problem statment is as follows:

> Given a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.

> For example:
> Given the following binary tree,

>         1            <---
>        / \
>       2   3         <---
>        \   \
>         5   4       <---
>
> You should return [1, 3, 4].

As per the norm with OJ problems, the most accessible example given does not closely cover a lot of the edge cases necessary to consider
when coming up with a sturdy implementation. Let's try and think about what it means to get the right-most node as we
go down the tree. The key realization here is that the right-most node at each level is really just the largest
value at each level. This boils down to implementing a modified breadth first search algorithm. 

Let's start with taking a look at a regular BFS algorithm, and then we'll take a look at how we'll need to modify it.
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
    returnVector.push_back(q.front()->val);
    
    if (q.front()->left) q.push(q.front()->left);
    if (q.front()->right) q.push(q.front()->right);
    q.pop();
  }

  return returnVector;
}

```

If you haven't seen this before here's a quick and dirty explanation of
the code. The idea is to enqueue the first node (root of the tree), and while the
queue's size is > 0, enqueue any of the node's children and and remove the current node
from the queue.

Given the following tree structure:

```
      1
     / \
    2   3
   /   / \
  4   5   6
```

A visual of the queue would look like this:

```
___       _______       _____       _______       _____       ________
 1    =>   1 2 3    =>   2 3    =>   2 3 4    =>   3 4    =>   3 4 5 6  ...
---       -------       -----       -------       -----       --------
```

From this we can see at any given moment every node in the queue will be at a level equivalent to or deeper than that
of the front-most. This is important because our modifications must let us store the largest node in a single level into our
`returnVector` before we move onto the first node of the next level. This means we'll need to know exactly how many nodes are
at each level so we can break appropriately before filling up the queue with nodes from another level. We can maintain a variable
`count` to hold the number of nodes in the current level. Since we'll be starting at the root of the tree, our `count` will be equal
to `queue.size()` when we enter the while loop for the first time.

We now know how many nodes our in the current level (`1`). We need to grab the largest node in this level.
This will always be at `q.back()` because we are always pushing the smaller of the children to the queue first (see visual above).
Now we just need to loop through all of the nodes in the queue (there are `count` of them), and for each one push its children to
the queue and pop the current node. We must also decrement our `count` to correctly maintain the number of nodes at each level. Notice
how this process will cause our `queue.size()` to expand as expected (given the current node has children to push), but our
`count` always maintains the number of nodes in the current level. Now we can tell when their are no more nodes in a level to push.
The queue will now contain all the nodes in the next level with the largest at the very end. Simple rinse and repeat the above process.