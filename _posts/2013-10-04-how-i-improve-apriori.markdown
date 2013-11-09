---
layout: post
title:  "How I Improve My Own Implementation of Apriori"
date:   2013-10-04 16:20:20
categories: algorithm
---

It is actually a homework from one course: data mining. Apriori is a very famous algorithm which is commonly used in data mining. It can generate the association rules from the big data. The well-known example: [Beer and Diapers][walmart] is actually one application of this algorithm. I'm gonna skip the principle of this algorithm and go to my implementation, you can check the details [here][apriori].

The data set I use is the gene info. You can find it in my [repo][gene-data]. Don't worry if you don't understand, I don't either. What you will get is just a combination of genes indicates another combination of genes like: `G1_UP, G2_UP -> G3_UP`. This simply means if `G1_UP` and `G2_UP` both show up in the transaction, `G3_UP` might also show up in this transaction.

I use Python to implement this algorithm. My first attempt uses a list structure. The thorough idea is to use tuple store all the combination of candidates, and store all the tuples in a list. The first round would generate all the size 1 candidates tuple like `(G1_UP,)`, the next round would generate the size 2 candidates like `(G1_UP, G2_UP,)` based on the previous round. It only takes me 2 hours to get the stuff done. I used a very small data set (6 transactions, each has 3 elements), and it works pretty good. Then the nightmare comes, I apply the original data to my program, the terminal is full of the intermidate print strings. After 10 minutes, it is still running… I was so naive and stupid at that time, since I know the implementation is correct, I ran the program whole night and hope to get the result when I get up next day. You probably guess the answer, the program is still running!

Now it's time to improve. I found a Java implementation online. The input file for that program contains actually only numbers. But look at me, I was comparing the string like `G1_UP`, `G1_DOWN`. So I convert all the strings to numbers following the rule I defined. After this procedure, I can get the result in 110s for the min support = 30%. Great!

I know using 110s is not an efficient result. I used my converted data and ran that on the Java implementation. The same min support, it only takes 3.5s. God damn it! Now I realized how bad I was.

After ran the program several times, I found that every time you generate new candidates, you need to delete the ones which the appear times don't go beyond the threshold. The threshold here means the min support. You need to count the appear times of the candidates to reach this goal. In my implementation, each transaction is a list, and each candidate item is also a list, I simply compared each element in the candidate item with each element in the transaction. What I choose to do here is using dictionary, since it will hash the element, the cost time to check if an element exists in a list will certainly be constant. Now, the time goes down to 92s. Not too bad. And just after the improvement, I suddenly realized that I need to fill a value to use dictionary because it is a key value pair. But I only need to check whether one element appears in the list, the set structure in Python is actually born for this. Now it only needs 74s.

The third attempt is a huge improvement. There is an anti-monotone property here: any subset of frequent itemset must be also frequent, and any superset of infrequent itemset must also be infrequent. That means, if I generate candidates: `(abcd,)`, then all the combination of this item must appear in the previous candidates. `abc`, `bcd`, `abd`, `acd` must show up in the previous candidates, otherwise `abcd` should not be a candidate. This is what the professor told us to do. And actually, even you've done this step, you still need to count the appear times of `abcd` in all the transactions to make sure the times exceed the threshold. So why don't we just count the times, since generating all the combinations is time consuming. I deleted the corresponding code, it costs 18s now!

18s is a huge step forward compare to 110s. But it is still not acceptable. This time I started improving the performance of generating new candidates. The rule here is: if you want to generate from size 3 candidates to size 4 candidates, you need to find all the size 3 candidates with the first 2 are the same. For example: `abc` and `abd` can be merged to `abcd`. I used 2 nested for loops to check if 2 element in the list have the same prefix part. It's actually a brute force way, stupid implementation… What I modified is sorting the list first, in this way you can skip a lot of comparisons. This time I decreased the time to 11s.

I think these improvements are what I can get by myself. The Java version's 3.5s seems like impossible for me. I digged into the Java code trying to find if there are some secret ways. It actually do have one. When counting how many times the candidate show up in all the transactions, I used the set, because it performs a hash lookup. What the Java code doing is using the sieve way. We have 200 different elements total, so we create a 200 boolean list contains all `False`, for each transaction, we turn the show up elements to `True`. It is similar to the prime number generator. After switching to this implementation, I got 8.5s!

That's the best I can get at that time. I think Python itself limit the performance. I was always thinking to replace list to numpy's ndarray. Until last night I went to the _PythonBuffalo_ meetup. Some guy mentioned pypy, and I happened to figure out what pypy is serveral days ago (from [this aritical][pypy]). So, why not give it a try? I just simply download and run the code through pypy. The magic happens, 1.2s!!! Even faster than the Java's implementation.

It's a long story, but really worth me to dig into. Improving the performance is hard, but interesting. Now I sort of understanding how amazing algorithm is :)

[walmart]:http://higherhighslowerlows.com/2010/10/19/beer-and-diapers-a-love-story/
[apriori]:http://nikhilvithlani.blogspot.com/2012/03/apriori-algorithm-for-data-mining-made.html
[gene-data]:https://github.com/boulevard23/Apriori/blob/master/association-rule-test-data.txt
[pypy]:http://www.toptal.com/python/why-are-there-so-many-pythons