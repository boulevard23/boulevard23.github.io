---
layout: post
title:  "Swap Nodes in Pairs"
date:   2013-11-09 15:23:20
categories: algorithm
---

> Question:
> Given a linked list, swap every two adjacent nodes and return its head.
> For example, Given `1 -> 2 -> 3 -> 4`, you should return the list as `2 -> 1 -> 4 -> 3`.
> Your algorithm should use only constant space. You may not modify the values in the list, only nodes itself can be changed.

For the linked list question, there is a very useful technique which might make your code pretty easy: **_using the dummy head_**. Sometimes you need to write code to deal with different cases for different head. Under this circumstance, you can add a new dummy head before, and put the logical business into a while loop.

{% highlight java %}
	public ListNode swapPairs(ListNode head) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        if (head == null) return head;
        ListNode dump = new ListNode(0);
        dump.next = head;
        ListNode cur = head;
        ListNode prev = dump;
        ListNode next = cur.next;
        ListNode nextNext = null;
        while (next != null) {
            nextNext = next.next;
            prev.next = next;
            next.next = cur;
            cur.next = nextNext;
            
            prev = prev.next.next;
            cur = prev.next;
            if (cur == null) break;
            next = cur.next;
            if (next == null) break;
            nextNext = next.next;
        }
        return dump.next;
    }
{% endhighlight %}
    
This is my first version. It still a little more complicated. I simply think of using prev, cur, next and next to the next variable to swap the node (swap cur and next node). Then I skip one node, assign prev with prev.next.next. This step is not necessary. cur node now is alreay the next next to prev, simply assign cur.next to prev is OK. I found the following version online, the same idea, but more concise than my first version.

	public ListNode swapPairs(ListNode head) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        ListNode dumpHead = new ListNode(0);
        dumpHead.next = head;
        ListNode prev = dumpHead, cur = head;
        
        while (cur != null && cur.next != null){
            ListNode temp = cur.next.next;
            cur.next.next = prev.next;
            prev.next = cur.next;
            cur.next = temp;
            prev = cur;
            cur = prev.next;
        }
        
        return dumpHead.next;
    }