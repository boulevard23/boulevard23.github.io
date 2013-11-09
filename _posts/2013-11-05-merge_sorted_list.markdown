---
layout: post
title:  "Merge Sorted List"
date:   2013-11-05 20:48:20
categories: algorithm
---

## Merge Sorted List

> Question: Assume that 2 lists are already sorted. Merge them into one list.

{% highlight java %}
public ArrayList<Integer> mergeList(ArrayList<Integer> a, ArrayList<Integer> b) {
    int index = 0;
    while (!b.isEmpty()) {
        int tmp = b.remove(0);
        // There is one tricky part here,
        // you must write index < a.size() first
        // otherwise tmp >= a.get(index) will be executed first
        // which will cause the index out of bound error
        for (; index < a.size() && tmp >= a.get(index); index++);
        if (index == a.size()) {
            a.add(tmp);
        } else {
            a.add(index, tmp);
        }
    }
    return a;
}
{% endhighlight %}
	
At first I thought getting the index of an ArrayList and add element into it at the same time will cause concurrent modification error. Seems it is ok for ArrayList.

This problem is pretty easy, I just want to achive the algorithms I've written.

-------
Update:

In the LeetCode, the problem is a little bit different. The input param are not ArrayLists, but two ListNode.

My first attempt is ugly, need to deal with many error cases.

{% highlight java %}

public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    // IMPORTANT: Please reset any member data you declared, as
    // the same Solution instance will be reused for each test case.
    if (l1 == null) return l2;
    if (l2 == null) return l1;
    ListNode head, cur, other;
    if (l1.val > l2.val) {
        head = l2;
        cur = l2;
        other = l1;
    } else {
        head = l1;
        cur = l1;
        other = l2;
    }
    while (other != null) {
        if (cur.next == null) {
            cur.next = other;
            break;
        }
        if (cur.next.val > other.val) {
            ListNode tmp1 = other.next;
            ListNode tmp2 = cur.next;
            cur.next = other;
            other.next = tmp2;
            other = tmp1;
        } else {
            cur = cur.next;
        }
    }
    return head;
}
{% endhighlight %}
    
So I searched for a better solution:

{% highlight java %}

public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
	// IMPORTANT: Please reset any member data you declared, as
    // the same Solution instance will be reused for each test case.
        
    // This is a dummy head
    ListNode head = new ListNode(0);
    ListNode cur = head;
    while (l1 != null && l2 != null) {
        if (l1.val < l2. val) {
            cur.next = l1;
            l1 = l1.next;
        } else {
    		cur.next = l2;
            l2 = l2.next;
        }
        cur = cur.next;
    }
    if (l1 != null) {  
        cur.next = l1;  
    } else if (l2 != null) {  
        cur.next = l2;  
    }  
    return head.next;  
}
{% endhighlight %}

The key point here is the dummy head, it takes care of all the situations and make the code pretty simple. (Without it I need to choose a node with small value to be the new head).

Setting up a dummy variable sometimes will make your life easier.