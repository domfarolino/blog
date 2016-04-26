---
layout: post
title: Binary Tree - Right Side View Algorithm
---

$$\sum_{n=1}^\infty 1/n^2 = \frac{\pi^2}{6}$$

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
queue we get to them after we get to all of the nodes at the current level. This gives us
a level order traversal.

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
of the front-most. This is important because our modifications must let us store the largest node of a single level into our
`returnVector` before moving onto the first node of the next level. This means we'll need to know exactly how many nodes are
at each level so we can break appropriately before pushing nodes from another level to the queue. We can do this by
maintaining a variable `count` to hold the number of nodes in the current level. The key is getting `count`
correct from the beginning. This will act as a base all of the other level counts will be built off of. Observation
will reveal that `count` is equal to the number of nodes in the queue (namely `queue.size()`) when we enter the while loop
for the first time. Note how `q.size()` will wax and wane as we traverse through a single level filling the queue with nodes
of the next level. This is why we'll need to set `count = q.size()` before we iterate throug the queue and do anything.

Our next step is to perform a number of steps that when finished, the queue only contains nodes of the next level so `count` will always
have the correct level count. We know how many nodes are in the current level (`1`). We now need to grab the largest node at this level.
At this point this is the only node in the queue, but looking at how we push nodes into the queue (`parent->leftChild` before `parent->rightChild`)
we can see that the largest node will always exist at `q.back()` (also `q.at(count-1)`). Now that we have the largest node at the
current level stored, we just need to loop through all of the `count` nodes in the queue and for each one push its children to the
queue and pop the current node (standard procedure). This will fill the queue will all of the nodes at the next level and purge the
queue of nodes from the current. Notice during this operation `q.size()` will most likely change, which is why we set `count = q.size()`
before doing any of this, to allow us to keep an accurate number of nodes at our current level. We must also remember to decrement
`count` every time we pop from the queue. After this operation the queue will ONLY contain nodes from the new leve. This means
`q.size()` will always be equal to the level count, meaning `count` will always correctly be set to the number of nodes at the current level.