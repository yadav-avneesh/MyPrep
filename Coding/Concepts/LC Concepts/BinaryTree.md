# Introduction

A tree is a frequently-used data structure to simulate a hierarchical tree
structure.

Each node of the tree will have a root value and a list of references to other
nodes which are called child nodes. From graph view, a tree can also be defined
as a directed acyclic graph which has N nodes and N-1 edges.

A Binary Tree is one of the most typical tree structure. As the name suggests,
a binary tree is a tree data structure in which each node has at most two
children, which are referred to as the left child and the right child.

By completing this card, you will be able to:
- Understand the concept of a tree and a binary tree;
- Be familiar with different traversal methods;
- Use recursion to solve binary-tree-related problems;


## Traverse A Tree

Pre-order Traversal
In-order Traversal
Post-order Traversal
Recursive or Iterative

- Pre-order traversal is to visit the root first. Then traverse the left
  subtree. Finally, traverse the right subtree.
- In-order traversal is to traverse the left subtree first. Then visit the root.
  Finally, traverse the right subtree.
  - Typically, for binary search tree, we can retrieve all the data in sorted
    order using in-order traversal. We will mention that again in another card
    (Introduction to Data Structure - Binary Search Tree).
- Post-order traversal is to traverse the left subtree first. Then traverse the
  right subtree. Finally, visit the root.
  - It is worth noting that when you delete nodes in a tree, deletion process
    will be in post-order. That is to say, when you delete a node, you will
    delete its left child and its right child before you delete the node itself.
  - Also, post-order is widely used in mathematical expressions. It is easier
    to write a program to parse a post-order expression.
  - Here is an example:
                +
            *       5
        4       -
            7       2
  - You can easily figure out the original expression using the inorder
    traversal. However, it is not easy for a program to handle this expression
    since you have to check the priorities of operations.
  - If you handle this tree in postorder, you can easily handle the expression
    using a stack. Each time when you meet a operator, you can just pop 2
    elements from the stack, calculate the result and push the result back into
    the stack.
- Recursive or Iterative, Try to practice the three different traversal methods
  in our after-article exercise. You might want to implement the methods
  recursively or iteratively. Implement both recursion and iteration solutions
  and compare the differences between them.

### How to traverse the tree
There are two general strategies to traverse a tree:

Depth First Search (DFS)

In this strategy, we adopt the depth as the priority, so that one
would start from a root and reach all the way down to certain leaf,
and then back to root to reach another branch.

The DFS strategy can further be distinguished as
preorder, inorder, and postorder depending on the relative order
among the root node, left node and right node.

Breadth First Search (BFS)

We scan through the tree level by level, following the order of height,
from top to bottom. The nodes on higher level would be visited before
the ones with lower levels.

### BT Pre Order

[Reference](https://leetcode.com/problems/binary-tree-preorder-traversal/editorial/)

```python
# Recursive
class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        answer = []

        def dfs(node):
            if not node:
                return
            # Visit root first, then the left subtree, then the right subtree.
            answer.append(node.val)
            dfs(node.left)
            dfs(node.right)

        dfs(root)
        return answer

'''
Complexity Analysis
- Let n be the number of nodes in the tree.
- Time complexity: O(n)
  - We visit each node once and perform a constant amount of work at each node.
- Space complexity: O(n)
  - The space is taken up by the recursive call stack. Ideally, we are not using
    any extra space for variables. But the recursion internally uses a call
    stack that takes up space equivalent to the depth of the tree. The max depth
    of the tree could be O(n)O(n)O(n) in the worst-case scenario when the tree
    is skewed.
'''

# Iterative
class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        answer = []
        stack = [root]
        # Note that we add curr_node's right child to the stack first.
        while stack:
            curr_node = stack.pop()
            if curr_node:
                answer.append(curr_node.val)
                stack.append(curr_node.right)
                stack.append(curr_node.left)
        return answer

# TC = SC = O(N)
```

#### Morris Traversal

- This approach is more of an extension that you wouldn't be expected to come up
with in an interview!
- Algorithm
  - Initialize an answer array. We also need two pointers, curr and last.
    Initialize curr as the root node.
  - While curr is not NULL, check if it has a left child or not:
    - If it has a left child, let last be the rightmost node of curr's left
      subtree. Add curr to the answer and modify last's right child as curr.
    - Otherwise, add curr to the answer and move on to curr's right child.
  - During the iteration, if we visit a node whose right child is curr, it
    implies that we are currently at the last node of this curr node. We reset
    last's right child to NULL and move on to curr's right child.
  - Return answer after the iteration stops.

```python
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        answer = []
        curr = root

        while curr:
            # If there is no left child, go for the right child.
            # Otherwise, find the last node in the left subtree.
            if not curr.left:
                answer.append(curr.val)
                curr = curr.right
            else:
                last = curr.left
                while last.right and last.right != curr:
                    last = last.right

                # If the last node is not modified, we let
                # 'curr' be its right child. Otherwise, it means we
                # have finished visiting the entire left subtree.
                if not last.right:
                    answer.append(curr.val)
                    last.right = curr
                    curr = curr.left
                else:
                    last.right = None
                    curr = curr.right
        return answer
```

### BT Inorder

#### Approach 1: Recursive Approach
- The first method to solve this problem is using recursion.
- This is the classical method and is straightforward. We can define a helper
function to implement recursion.
- Complexity Analysis
  - Time complexity: O(n)
    - T(n) = 2⋅T(n/2)+1T(n) =2⋅T(n/2)+1.
  - Space complexity: O(n)
    - The worst case space required is O(n), and in the average case it's
      O(log⁡n), where n is number of nodes.

#### Approach 2: Iterating method using Stack
- The strategy is very similiar to the first method, the different is using
  stack.
- Complexity Analysis
  - Time complexity: O(n)
  - Space complexity: O(n)


#### Approach 3: Morris Traversal
- In this method, we have to use a new data structure-Threaded Binary Tree,
  and the strategy is as follows:
- Step 1: Initialize current as root
- Step 2: While current is not NULL,
    ```{r, eval=FALSE}
    If current does not have left child
        a. Add current’s value
        b. Go to the right, i.e., current = current.right
    Else
        a. In current's left subtree, make current the right child of the rightmost node
        b. Go to this left child, i.e., current = current.left

    For example:


            1
            /   \
        2     3
        / \   /
        4   5 6
    First, 1 is the root, so initialize 1 as current, 1 has left child which is 2, the current's left subtree is

            2
            / \
        4   5
    So in this subtree, the rightmost node is 5, then make the current(1) as the right child of 5. Set current = cuurent.left (current = 2).
    The tree now looks like:

            2
            / \
        4   5
                \
                1
                \
                3
                /
                6
    For current 2, which has left child 4, we can continue with thesame process as we did above

            4
            \
            2
            \
                5
                \
                1
                \
                    3
                /
                6
    then add 4 because it has no left child, then add 2, 5, 1, 3 one by one, for node 3 which has left child 6, do the same as above.
    Finally, the inorder taversal is [4,2,5,1,6,3].

    For more details, please check
    Threaded binary tree and
    Explaination of Morris Method
    ```
- Complexity Analysis
  - Time complexity: O(n)
  - SC: O(1)


### BT Postorder


### Level Order

Level-order traversal is to traverse the tree level by level.

Breadth-First Search is an algorithm to traverse or search in data structures
like a tree or a graph. The algorithm starts with a root node and visit the node
itself first. Then traverse its neighbors, traverse its second level neighbors,
traverse its third level neighbors, so on and so forth.

When we do breadth-first search in a tree, the order of the nodes we visited is
in level order.

Typically, we use a queue to help us to do BFS.

```python

# Recursive

class Solution:
    def levelOrder(self, root):
        """
        :type root: TreeNode
        :rtype: List[List[int]]
        """
        levels = []
        if not root:
            return levels

        def helper(node, level):
            # start the current level
            if len(levels) == level:
                levels.append([])

            # append the current node value
            levels[level].append(node.val)

            # process child nodes for the next level
            if node.left:
                helper(node.left, level + 1)
            if node.right:
                helper(node.right, level + 1)

        helper(root, 0)
        return levels

# Iterative
from collections import deque
class Solution:
    def levelOrder(self, root):
        """
        :type root: TreeNode
        :rtype: List[List[int]]
        """
        levels = []
        if not root:
            return levels

        level = 0
        queue = deque([root,])
        while queue:
            # start the current level
            levels.append([])
            # number of elements in the current level
            level_length = len(queue)

            for i in range(level_length):
                node = queue.popleft()
                # fulfill the current level
                levels[level].append(node.val)

                # add child nodes of the current level
                # in the queue for the next level
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

            # go to next level
            level += 1

        return levels

```