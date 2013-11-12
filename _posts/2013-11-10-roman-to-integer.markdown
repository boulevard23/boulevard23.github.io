---
layout: post
title:  "Roman to Integer"
date:   2013-11-10 12:19:20
categories: algorithm
---

> Question: Given a roman numeral, convert it to an integer.
> Input is guaranteed to be within the range from 1 to 3999.

I am not familiar with the Roman Number. After doing some Google, it is clear that Roman Number has following characters:

	I = 1
	V = 5
	X = 10
	L = 50
	C = 100
	D = 500
	M = 1000

The following are some examples:

	II = 2
	III = 3
	IV = 4
	VI = 6
	IX = 9
	XI = 11
	XIX = 19

Maybe you've already found the rules: if this letter is bigger than the next one, simply add them together. Otherwise, the result will be the latter one minus the previous one.

Now comes with my solution:

{% highlight java %}
public int romanToInt(String s) {
    // IMPORTANT: Please reset any member data you declared, as
    // the same Solution instance will be reused for each test case.
    int prev = 0;
    int cur = 0;
    int result = 0;
    for (int i = 0; i < s.length(); i++) {
        cur = getNum(s, i);
        if (prev < cur) result += cur - 2 * prev;
        else result += cur;
        prev = cur;
    }
    return result;
}
    
private int getNum(String s, int pos) {
    switch (s.charAt(pos)) {
        case 'I': return 1;
        case 'V': return 5;          
        case 'X': return 10;
        case 'L': return 50;
        case 'C': return 100;
        case 'D': return 500;
        case 'M': return 1000;
    }
    return -1;
}

{% endhighlight %}