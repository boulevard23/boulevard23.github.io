---
layout: post
title:  "Longest Increasing Subsequence"
date:   2013-11-05 20:53:20
categories: algorithm
---

> Question: Find the longest increasing subsequence. For example, input: `5, 2, 8, 6, 3, 6, 9, 2`, output: `2, 3, 6, 9`

This is actually a classic Dynamic Programming. From the **_start_** to the **_end_**, the longest increasing subsequence is the longest subsequence from the **_start_** to the **_previous to end_** + 1.

{% highlight java %}

public void getLongestSubsequences(int[] input) {
    int size = input.length;

    // This is the dp array
    int[] longestSubsequences = new int[size];
		
    // prev is used to construct the subsequence
    int[] prev = new int[size];
	
    // initial the prev array
    for (int i = 0; i < size; i++) {
        prev[i] = -1;
    }
	
    for (int w = 1; w < size; w++) {
        // Key is v, value is the longest subsequence's num from v to w
        HashMap<Integer, Integer> hm = new HashMap<Integer, Integer>();
        for (int v = 0; v < w; v++) {
            // input[v] < input[w] simply means there is a path from v to w
            // you can plus 1 to the previous longest subsequence
            if (input[v] < input[w]) {
                hm.put(v, longestSubsequences[v] + 1);
            } else {
                hm.put(v, longestSubsequences[v]);
            }
        }
        // The main equation
        longestSubsequences[w] = max(hm, prev, w);
    }

    return constructSubsequence(input, prev, size);
}

// Loop and find the max longest subsequence num from all the previous index
private int max(HashMap<Integer, Integer> hm, int[] prev, int w) {
    int max = 0;
    
    Iterator it = hm.entrySet().iterator();
    while (it.hasNext()) {
        Map.Entry me = (Map.Entry) it.next();
        if ((Integer) me.getValue() > max) {
            max = (Integer) me.getValue();
            prev[w] = (Integer) me.getKey();
        }
    }
    return max;
}
	
private ArrayList<Integer> constructSubsequence(int[] input, int[] path, int size) {
    ArrayList<Integer> result = new ArrayList<Integer>();
    for (int i = size - 1; path[i] != -1; i = path[i]) {
        result.add(0, input[path[i]]);
    }
    return result;
}
	
{% endhighlight %}

Actually, did you find that this problem can also be converted to a graph problem. From left to right, add a directed edge from a smaller number to a bigger number. Now you will find that this is a topology sorted graph. The longest increasing subsequence is the longest path in this graph.