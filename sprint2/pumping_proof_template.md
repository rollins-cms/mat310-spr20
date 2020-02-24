# Pumping Lemma Proof Template*
Pumping Lemma proofs have a very standard outline. It's almost always better to follow the standard outline, rather than trying to improvise directly from the statement of the theorem.

## Review of Pumping Lemma for Regular Languages
Recall that the pumping-lemma states that for any regular language L, there exists a number p such that for any string w in L of length at least p there are strings x,y,z such that
1. w = xyz
2. |xy| <= p
3. |y| > 0
4. For all i, xy<sup>i</sup>z is in L

To prove that a language L is not regular, we show that the pumping lemma fails. That is, we show that

For any number p, there exists a string s in L of length at least p such that if we choose any strings x,y,and z satisfying conditions (1)-(3), then there is some number i such that xy<sup>i</sup>z is not in L.


## Template
So, a pumping lemma proof normally looks something like this (just fill in the blanks)

Suppose L were regular. Let p be the pumping length given by the pumping lemma.
Consider the string s = (insert here some description of a string, parametrized by p)

Clearly, |s| >= p and s is in L. So, by the pumping lemma, there must be some choice of x,y,z satisfying the conditions of the pumping lemma.

But, consider any choice of x,y,z for which s = xyz, |xy| <= p, and |y| >= 1.

[Insert argument as to why for some particular i, xy<sup>i</sup>z is not in L; usually i=0 or i=2 works. Using the fact that |xy| <= p, you know that y must be in the first p characters of the string s. Using this, you can usually restrict the cases for what y can be, and argue for each of the cases that xy<sup>i</sup>z is not in L.] 

Since xy<sup>i</sup>z is not in L, we get a contraditiction; hence the assumption that L is regular is false. Hence L is not regular. QED

## Points to Note

* Note that your proof shows for an arbitrary p, there is a particular string s in L (long enough) such that for *any* split of s=xyz (satisfying the conditions), there is an i such that xy<sup>i</sup>z is not in L. So you must assume a general p, construct a concrete s, consider a general split x,y,z, and show a particular i for which the pumped word is not in L. You cannot choose a particular p (like p=2)! You cannot choose a particular split x,y,z!
* The string s must change as p changes, e.g. portions of it should get longer and shorter.
* The string s does not need to be a random, representative member of L. It may come from a very specific subset of L. For example, if your language is all strings with equal numbers of 0's and 1's, your s might be O<sup>p</sup>1<sup>p</sup>.
* Make sure your string wp is long enough, so that the first p characters have a very limited form (e.g. all zeros).
* It's important to check that |s| >= p and that s is in L. And your proof should mention these facts. However, they don't normally need any justification, e.g. the use of "Clearly" in the above template.
* When you assert that x,y, and z must exist, it would normally be good mathematical style to quote the specific conditions they must satisfy. But everyone knows what these conditions are, so it's common to just say they satisfy "the conditions from the pumping lemma."
You should, however, know clearly in your own mind what those numbered conditions were.
* In the variable part at the end of the proof, be sure that you explicitly use the facts that |xy| <= p and |y| > 0, when you argue that xz or xyyz (or xy<sup>i</sup>z for some other value of i) does not have the right form to be in L.
   * Exception: when the alphabet has only one character (e.g. the language contains all strings of zeros that have prime length), you may not need to use the fact that |xy| < p.
* The vast majority of proofs use i = 0 or i = 2, but there are exceptions.

**Based on Profs. Sariel Har-Peled and Madhusudan Parthasarathy class at University of Illinois*
