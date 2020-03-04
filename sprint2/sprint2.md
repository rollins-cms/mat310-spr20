# Sprint 2: Nondeterministic Finite Automata and Regular Languages

# Roadmap:
* Teams
* Sprint Duration: Feb. 12 - Feb. 26
* Quiz date/time: Feb. 26 at the start of class
* Requirements due: Feb. 24 at the end of class 

# Rationale: 
In Sprint 2, we will continue our exploration of regular languages.  Now that we've constructed **deterministic** finite automata, we'll move on to **nondeterministic** finite automata.  Nondeterminisim means that we can have more than one outcome even when the input is fixed.  At first pass, there seems to be no relation between DFAs and NFAs, but as we will see, they are equivalent!  We'll see what makes a finite state machine diagram nondeterministric and then learn how to convert NDFAs to DFAs and see that they are equivalent.  We'll see that NDFAs can actually be more concise and simple compared to DFAs.  Moreover, because of several key closure theorems they can be combined easily to make even more complex machines using union, concatenation, and Kleene star operations very easily!  By the end of this sprint, we'll have 3 ways to express regular langauges: DFAs, NFAs, and regular expressions.  At the end of this sprint, we'll show that are are language (called non-regular languages) which can't be recognized by either DFAs or NFAs (and also can't be expressed using regular expressions).

# Responsibilities (What you need to know):
1. Non-deterministic finite automata
   * Formal defintion
   * Differences between DFA and NFA
   * Conversion to DFA (equivalence to DFA!)
   * Building more complex NFA from smaller DFA/NFA (Related to relationship between regular expressions and NFAs)
2. The pumping lemma
   * Proof by contradiction technique
   * Decomposition of string into xy^iz
   * pumping length p and restrictions on it

# Requirements (What you need to do):
Your deliverables grade for this sprint is the problem set.  The Scrumage grade is determined separately.

## Individual Requirements:
* Understand the responsibilities on the list.
* Contribute to your team deliverables.

## Team Requirements:
* Track tasks and generate a burndown chart -- You can use technologies other than Trello in this sprint, but you must have some common list of tasks which you can track (and I can view) and a representation of the burndown chart.
* [The problem set](./sprint2_prob_set.pdf)
   
# Resources:  
I may add to these as we go through the sprint.  Use the [Expert Request](https://rollins.co1.qualtrics.com/jfe/form/SV_0jNfbBpN1clDJfn?course=mat310s20&sprint=2) for more resources. 

* Textbook readings:
   * IToC: 1.2, 1.4
   * FoC: 3.5, 3.6, 3.7
* [Overview of NFAs](https://www.youtube.com/watch?v=f-EUv9LHi0k) (YouTube, 10min)- includes how to trace a string using a NFA, the formal definition of NFAs, and how to identify an NFA
* There are two videos posted on Canvas (in the Pages section) that I recorded showing the NFA to DFA conversion algorithm.  This is a crucial step to understanding the equivalence of NFAs and DFAs.
* [Pumping Lemma Video](https://www.youtube.com/watch?v=g4e2RElzCSQ) (YouTube, 30min) Quite possibly the most accessible explanation of the pumping lemma I've seen.
* [Practice quiz](./sprint2_practice_quiz.pdf) and [answer key](./sprint2_practice_quiz_ans.pdf)
* [Quiz](./sprint2_quiz.pdf) and [answer key](./sprint2_quiz_ans.pdf)
   
# Reality Check:
  * On Wednesday, after the Sprint 1 quiz, do the Sprint 1 retrospective.  Do sprint planning meeting (generate sprint backlog and time estimations).  Skim the problem set.
  * TODO by next class:  Skim readings, making notes of page/section numbers for responsibilities on the list or problems on the problem set.
  * Monday class: Go over problem set with teammates and identify areas of general confusion.  Ask questions or file expert requests.
  * TODO by next class: Focus on any areas your group is still struggling with and file expert requests to check problems or ask questions.
  * Wednesday: Make sure everyone understands problem areas.
  * TODO by next class: Complete rough draft of all answers for problem set (except 1-2 which might need group discussion).
  * Monday: Do final check of problem set.  Make sure problem set is neat, tidy, and meets guidelines.  Turn in your problem set at the end of class.
  * TODO before next class: Take the practice quiz and check your answers.  Study as a group.  
  * Wednesday: Quiz (30 minutes), Sprint 2 retrospective, and Sprint 3 planning.

# Retrospective:
  * Complete this [sprint retrospective survey](https://rollins.co1.qualtrics.com/jfe/form/SV_3rAIzhpHFYbIixf?course=mat310s20&sprint=2).  Each team member should complete a survey.
