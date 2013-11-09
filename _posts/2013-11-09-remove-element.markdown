---
layout: post
title:  "Remove Element"
date:   2013-11-09 12:37:20
categories: algorithm
---

> Question: 
> Given an array and a value, remove all instances of that value in place and return the new length.
> The order of elements can be changed. It doesn't matter what you leave beyond the new length.

This problem is not difficult at all, but very interesting actually. The key point here is you have no need to care about the sequence of the original array. For a problem about deleting elements in array, you should keep in mind that if you do it from index 0 to the end, every time you delete an element, you have to move all the rest elements one step forward. In most cases, it would cost much less if you iterate the process from the end to the start.

Solution:

{% highlight java %}

public int removeElement(int[] A, int elem) {
    // IMPORTANT: Please reset any member data you declared, as
    // the same Solution instance will be reused for each test case.
    int notElemPos = A.length - 1;
    for (int i = A.length - 1; i >= 0; i--) {
       if (A[i] == elem) A[i] = A[notElemPos--];
    }
    return notElemPos + 1;
}
{% endhighlight %}
