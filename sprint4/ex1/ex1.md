# Exercise 1
This exercise begins with a lab-like portion which walks you through a TM in JFLAP.  Then you have to answer some questions about it on your own.

## Introduction
A Turing Machine (TM) is a powerful model which represents a general purpose computer.
The Church-Turing thesis states that our intuitive notion of algorithms is equivalent to TM
algorithms. In this exercise, we discuss the concept of a TM through an example and use
JFLAP to simulate and test the TM.

A TM can be defined in terms of its components in a manner similar to DFA, NFA, etc. Informally, a one-tape TM consists of a single infinite tape that is divided into cells. Each cell can contain a symbol. A read-write head can scan the tape one cell at a
time by moving to the right or left (or staying on a cell). There are also variations of TMs with several tapes and heads. In this class we restrict our discussion to a TM with a single tape and single head. Initially the cells are all blanks and an input string is placed
on contiguous cells. The tape head then scans the input and moves according to the TM's transition function. If the TM reaches an accept state the input is accepted. Otherwise a TM may reject an input string or may never halt.

## An example TM
A TM can also be described by a state-transition diagram. Load the file exercise1.jff into JFLAP. We will use JFLAP to test this TM with several inputs. We observe that this TM
has nine states q0 through q8, with q0 as the initial state and q8 as the final state. Initially
the tape head is at the leftmost symbol of the input. When not using a graphical representation of a TM, a transition is often written like:
```
(q0,a) --> (q1, x, R)
```
which can be interpreted as follows: If the TM is in state q0 and the tape head reads a in the current
cell, then it writes x in that cell and moves one cell to the right (R) and the machine is now in state q1. 

Note how the transitions are represented in JFLAP.  While the states are represented graphically (by circles as we've seen many times before), the other symbols are presented on a transition arrow.  The symbols are (in order): the symbol read from the tape, the symbol written to the tape, and the direction the head moves. The little square symbols
in the transition from q7 to q8 denote the special blank symbol.


For the alphabet Σ = {a, b, $}, let us now consider the language L = {w$w|w ∈ {a, b}
∗}.
Thus L is the set of words that have any string of a’s and b’s followed by one $ sign followed
by the same string. For example, ab$ab, aaa$aaa, and $ are words in L, while ab$aa, aba
and $$ are not in L. We want to construct a TM that accepts precisely the words in L.

A standard method of building a TM is to use the Church-Turing thesis. Thus, we first
describe an intuitive algorithm for recognizing words in L, and then we build a TM for this
algorithm. Our intuitive algorithm is stated in terms of reading an input on a tape. So to
recognize that an input is of the form w$w, we look at the leftmost character of the first w
and try to match it with the first symbol to the right $. We then repeat this process for each
symbol in w. To keep track of what symbols have already been read, we replace a symbol
(a or b) with an x as soon as it is read for the next match. Thus the tape head zig-zags
between the symbols to the left of the $ sign and to the right of the $ sign, matching them
one at a time. If a match fails at any step, the input is rejected.

Let's trace the behavior in our TM.  For example, starting at the initial state q0, if an a is read, it is replaced by an x and the TM moves to
the state q1. Now we are looking to match the a just read to an a to the right of the $
sign. We stay in q1 as we read through the other a's and b's. Once we reach the $ sign,
the TM moves to the state q2. We move though the x's and look for an a. If at any
time this match does not occur, the TM halts in a non-accept state and the input is not
accepted. If an a is found, the TM moves left through all the symbols until it finds the $
sign and then keeps moving left until it is back to q0, at which point the TM repeats the
process, taking a path toward q3 if a b is read. If all the a's and b's are matched and the TM
returns to q0, it then moves right through the q7 and reads only x’s to reach the accept state.
You will now simulate this TM in JFLAP and test it for several inputs.

## Part 1
Load the TM in the file exercise1.jff if you haven't already done so. 

1A. Study the path `q0 --> q1 --> q2 --> q5 --> q6 --> q0` and the transitions along this path.
Explain the actions of the TM along this path.

1B. Repeat the above step for the path `q0 --> q3 --> q4 --> q5 --> q6 --> q0`.  Again, explain the actions of the TM along this path.

1C. Choose Input from the menu and click on Step... Enter the input `abb$abb` and click
OK. At this stage the initial
state q0 is highlighted. In the lower left corner, you see the current state, the tape and
the current cell highlighted in orange.

Click on the button Step in the lower left corner. Describe the changes you see on the
tape and the current state. Explain those changes. Continue to step through until the
state in again at q0. Compare this with you answer in Question 1A. above. 

1D. Continue to step through until you see the state q8 highlighted and the lower left
portion of the window is green. Is the input accepted?
 
1E. Repeat the above steps with a new input: `abb$aba`. Is this
input accepted? Why or why not? What happens after the TM reaches q4?

## Part 2 - Simulating multiple inputs
Close the simulation you ran in Part 1.  If you accidentally close the entire TM, just reload it from the exercise1.jff file.

2A. Choose Input in the menu and click on Multiple Run. Enter
inputs as shown in the table below and run the multiple inputs. Explain why each input is accepted or rejected.

| Input |
|---|
| aa$aa  |
| ab$ab  |
| a$a |
| ab$abb |
| bbbbb$bbbbb |
| $$$$ |
| $ |

2B. Enter four more different inputs, two of which should be accepted and two rejected.

2C. If q7 is also made an accept state, how would it change the language of words that are
accepted by the modified TM? Explain your answer. Test your answer with several
inputs in JFLAP.








