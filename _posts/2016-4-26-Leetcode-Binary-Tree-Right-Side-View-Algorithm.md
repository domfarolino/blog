---
layout: post
title: Leetcode Binary Tree Right Side View Algorithm
category: Algorithms and Data Structures
tags: [binary tree, recursion, leetcode, algorithm]
---

I came across a simple but cool algorithm on [leetcode](https://leetcode.com/problems/binary-tree-right-side-view/) not too
long ago and to practice my writing I figured I'd try to explain my thought process behind it.

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

Let's start with taking a look at a regular BFS algorithm, and then we'll take a look at how we can modify it.
Your typical C++ BFS algorithm might look like this:

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
from the queue. This will cause the queue to expand until remaining nodes have no children
to add (leaf nodes), and since we are pushing the children to the back of the
queue, we only get to them after we get to all of the nodes at the current level. This gives
us a level order traversal.

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
of the front-most. This is important because our modifications must let us store the largest node in a single level in our
`returnVector` before moving onto the first node of the next level. This means we'll need to know exactly how many nodes are
at each level so we can break appropriately before filling the queue with nodes from another level.

We can do this by maintaining a variable `count` to hold the number of nodes in the current level. The key is getting `count`
correct from the beginning. This will act as a base that all of the other level counts will be built off of. Observation
will reveal that `count` is equal to the number of nodes in the queue (namely `queue.size()`) when we enter the while loop
for the first time. This provides us a good place to set/reset `count` every time we perform operations on the current level.

> Note how as we iterate through the queue adding nodes from the next level, the queue will contain some nodes from the current
> level, and some from the next. This is why it is necessary to set `count` when the queue only contains nodes from the current
> level to give us an accurate number of nodes we still have to process.

We now need to grab the largest node in the current level. Looking at how we push nodes into the queue (`parent->leftChild` before
`parent->rightChild`) we can tell that the largest node will always exist at `q.back()` (also `q.at(count-1)`).

Now that we have the largest node in the current level we must store it in our `returnVector`. With that taken care of we simply
perform a number of steps that when finished, leave the queue filled only with nodes of the next level. Let's loop through the
`count` nodes in the queue and for each one push its children and pop the current node (standard BFS procedure). Remember to decrement
`count` every time we pop from the queue to maintain an accurate count of nodes we need to process. Once this is completed the queue will ONLY
contain nodes from the new level which gives us a great place to rinse and repeat.

After all is said and done you might end up with something like the following:

```cpp

vector<int> rightSideView(TreeNode *root) {
    vector<int> ret;
    queue<TreeNode*> q;

    if(!root) return ret;
    q.push(root);
    
    int count = q.size();
    while(q.size()) {

        ret.push_back(q.back()->val);
        count = q.size();

        while(count) {
            if(q.front()->left)
                q.push(q.front()->left);
            if(q.front()->right)
                q.push(q.front()->right);
            q.pop();
            count--;
        }

    }
    return ret;
}

```

Of course this is not the only way to do it, and if you have any improvements to this method
or other ideas you'd like to submit please leave a comment below.