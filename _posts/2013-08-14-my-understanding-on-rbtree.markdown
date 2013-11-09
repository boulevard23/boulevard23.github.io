---
layout: post
title:  "My Understanding on Red-Black Tree"
date:   2013-08-14 09:09:12
categories: algorithm
---

The only thing worth to mention last week is how I learn the Red-Black tree (RB tree). There ara plenty of tutorials teaching you this data structure if you Google it. Obviously, I am not trying to teach you again, what I want is just write down how I understand it gradually. Recording the whole procedure of my understanding helps me improve my thinking method, and it is easy to backtrace if I forget the details someday (_someday_ means in the near future …)

The very first time I encountered the Red-Black tree is in [Data Structures & Algorithms in Java, 2nd edition][DSA]. This book doesn't mention 2-3 tree before getting into RB tree, and I don't even know 2-3 tree at that time. The consequence is I cannot believe how smart those who invent RB tree are! Just like a pear fell on the inventer's head and he suddenly came up with an idea to draw the tree in red and black and do some ridiculous rotations, jobs done! The only thing I still remember is there are some magical ways to keep the tree balance. 'Why not using red black tree?', I often use this to show off when I find someone is using a plain binary tree…

I started learning it again in [Algorithms][algorithms] these days. It is not too bad this time. The slides introduced 2-3 tree first. Though 2-3 tree is not often used in practical situation, it is pretty easy to understand. And I've learn an very important rule: **Insert at the leaf level, and then move it upwards if necessary. The tree actually grows up, not down, this is how to keep a tree balance when inserting.**

Now the RB tree is easy now. Using red and black nodes is just another way to represent 2-3 tree, there are 2 rules in the left-leaning RB tree:

1. No path from the root to the bottom contains two consecutive red links.
2. The number of black links on every such path is the same.

The first rule restricts the tree to a 2-3 tree, since one red link means a 3-node in 2-3 tree, if there are two consecutive red links, that means the 2-3 tree contains a 4-node.

And if you convert the RB tree to the corresponding 2-3 tree, the second rule is clear now. It simply indicates the 2-3 tree is a balanced tree.

Then the insertion is simple to understand now. Why all new nodes inserted are red? Because in 2-3 tree, we don't want the tree to grow when inserting, so we insert the new node to a leaf, which is a 2-node or 3-node. After inserting, 2-node becomes 3-node and 3-node becomes 4-node, 4-node is a temporary status and need some operations to correct it. The corresponding operations in RB tree is rotation left, rotation right and flip color. They all serve one purpose: eliminate the 2 consective red links situation (means 4-node in 2-3 tree).

I was so excited after I understand this. And I tried to find some tutorials online see if they also introduce 2-3 tree before talking about RB tree. Then I found they are actually using some other rules:

1. A node is either red or black.
2. The root is always black.
3. All leaves are black.
4. Every red node must have two black child nodes.
5. Every simple path from a given node to any of its descendant leaves contains the same number of black nodes.

These rules are quite similar to the rules I learnt, but a little difference in 3rd and 4th. These are also the rules mentioned in [Data Structures & Algorithms in Java, 2nd edition][DSA]. Now I am confused...

After doing some Google, I found [this paper][paper]. Robert Sedgewick actually invent a new way to implement the RB tree. He makes all the red links lean on the left subtree, and call it left-leaning RB tree (LLRB tree). The normal RB tree is actually an isometry of 2-3-4 tree, and LLRB tree is an isometry of 2-3 tree. Normal RB tree allows the parent is black and 2 children are red. This corresponds to the 4-node in 2-3-4 tree, which is not allowed in LLRB tree. LLRB tree would simply flips all the colors of these nodes.

Finally, let's talk about the deletion. It is much more difficult than I think. Since I only studied the detailed implementation in LLRB tree, I would only talke about deletion in LLRB tree.

In the paper, Sedgewick mentioned that **the invariant for deletion is that the current node or its left child is red.** Let's think about it step by step. Because we are using LLRB tree, it is actually a 2-3 tree. In order to make it balance even after deletion, we should not delete those 2-node. So what we gonna do is to make the node you want to delete locate in a 3-node. You can use 3 basic operation: rotation left, rotation right and flip color do get the job done. We keep doing this until we find the node want to delete, then if the current node is red, this means the node you want to delete is the bigger node in this 3-node. And if the current node's left child is red, this means the node you want to delete is the smaller node in this 3-node.

Now, how to use 3 basic operation to keep this invariant. Sedgewick said: **we can do by moving to the left unless the current node is red and its left child and left grandchild are both black. In that case, we can do a color flip, which restores the invariant but may introduce successive reds on the right.**

The following image illustrate how to use rotation and flip color to keep the invariant.

![moveRedLeft](/assets/img/moveRedLeft.png)

And since the whole procedure may introduce successive reds on the right. That is why we need `fixUp()` when finish the deletion.

All right, I think I finished all the stuff. And there is one more paper here: [Left-Leaning Red-Black Trees Considered Harmful][paper2], I think it is quite right, but anyway, the LLRB's slides help me understand RB tree better. I want to end by citing one sentence in this paper:
> _The simplest code is the code you didn't write. So use someone else's Red-Black tree if you can!!!_

[DSA]:http://www.amazon.com/Data-Structures-Algorithms-Java-Edition/dp/0672324539

[algorithms]: http://www.amazon.com/Algorithms-4th-Edition-Robert-Sedgewick/dp/032157351X/ref=sr_1_1?ie=UTF8&qid=1375665091&sr=8-1&keywords=algorithms

[paper]: http://www.cs.princeton.edu/~rs/talks/LLRB/LLRB.pdf

[paper2]: http://www.read.seas.harvard.edu/~kohler/notes/llrb.html
