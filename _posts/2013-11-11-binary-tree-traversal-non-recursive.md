---
layout: post
title:  "Binary Tree Traversal - Non Recursive"
date:   2013-11-11 17:58:20
categories: algorithm
---

## Binary Tree Traversal - Non Recursive

> Question: Implement the algorithms to traverse a binary tree in pre-order, in-order, post-order without using recursion.

It is pretty easy to write the recursive version. To write the non-recursive version, you need to use stack. 

The pre-order is the easiest one. Pre-order means: _root, left, right_. So you can simply put the root into a stack, pop it up, then push the right child, then the left child. The keys for the other 2 traversal are the same, using stack to change the sequence.

{% highlight java %}
public ArrayList<Integer> preorderTraversal(TreeNode root) {
    // IMPORTANT: Please reset any member data you declared, as
    // the same Solution instance will be reused for each test case.
    Stack<TreeNode> s = new Stack<TreeNode>();
    ArrayList<Integer> result = new ArrayList<Integer>();
    if (root == null) return result;
    s.push(root);
    while (!s.isEmpty()) {
        TreeNode tmp = s.pop();
        result.add(tmp.val);
        if (tmp.right != null) s.push(tmp.right);
        if (tmp.left != null) s.push(tmp.left);
    }
    return result;
}
{% endhighlight %}

Now it comes with the in-order: _left, root, right_. This time, you cannot push the root and then immediately pop it out, because root will be in the middle of the final sequence. What you should do is keep pushing the left child, until it is null. You pop out one and push it's right child. The solution is not hard to come up with, but the code is a little bit tricky:

{% highlight java %}
public ArrayList<Integer> inorderTraversal(TreeNode root) {
    // Note: The Solution object is instantiated only once and is reused by each test case.
    ArrayList<Integer> result = new ArrayList<Integer>();
    Stack<TreeNode> stack = new Stack<TreeNode>();
    TreeNode node = root;
    while (true) {
        if (node != null) {
            stack.push(node);
            node = node.left;
        } else {
            if (stack.isEmpty()) break;
            node = stack.pop();
            result.add(node.val);
            node = node.right;
        }
    }
    return result;
}
{% endhighlight %}

You need to use a variable to store the current step. The same structure of code like pre-order one will cause a dead loop, try it and you will know what I mean.

The most difficult one is the post-order: _left, right, root_. Now the root goes to the very right, so you can do the same thing as the pre-order one, you push the root, then pop it, push the left, then right. Now you got the sequence of _root, right, left_, actually, put them into another stack and get the reverse order, it is exactly the post-order.

{% highlight java %}
public ArrayList<Integer> postorderTraversal(TreeNode root) {
    // IMPORTANT: Please reset any member data you declared, as
    // the same Solution instance will be reused for each test case.
    Stack<TreeNode> s = new Stack<TreeNode>();
    Stack<Integer> aux = new Stack<Integer>();
    ArrayList<Integer> result = new ArrayList<Integer>();
    if (root == null) return result;
    s.push(root);
    while (!s.isEmpty()) {
        TreeNode tmp = s.pop();
        aux.push(tmp.val);
        if (tmp.left != null) s.push(tmp.left);
        if (tmp.right != null) s.push(tmp.right);
    }
    while (!aux.isEmpty()) {
        result.add(aux.pop());
    }
    return result;
}
{% endhighlight %}
