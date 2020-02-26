# Sprint 3: Context Free Grammars, Lexical Analysis, and Parsing

# Roadmap:
* Teams
* Sprint Duration: Feb. 26 - Mar. 11
* Quiz date/time: Mar. 11 at the start of class
* Requirements due: Program set due Mar. 11 at the end of class.

# Rationale: 
We saw in Sprint 2 that there were some languages which were non-regular and thus couldn't be modeled with DFAs, NFAs, or regular expressions.  In this sprint, we'll look at a class of languages called **context-free languages** which can be generated with **context-free grammars**.  Grammars are the rules by which we generate a language -- including programming languages and natural languages (sorta).  

It turns out that context-free grammars are highly relevant to the process of turning high-level source code (such as Java programs you might have written in CMS167) into machine code which the computer can actually execute.  You probably know this process as **compiling** some source code into a program.  Checking to see if a program is syntactically correct is an example use of a context-free grammar, and you'll begin to work on implementing this process during this sprint.

# Responsibilities (What you need to know):
1. Context free grammars
   * Identify elements: production rules, terminals, non-terminals, starting symbol
   * The BNF format for expressing a grammar (use of |, [], etc)
   * Derivation of specific strings given a CFG or BNF grammar
2. Lexical analysis
   * transition diagrams to model symbol formation process
   * concept of "pushback" to determine longest possible symbol pattern
3. Parsing
   * Use the left-derivation technique to derive a given string for a grammar
   * Generate multiple derivations for ambiguous grammars
   * Draw parse trees

# Requirements (What you need to do):
## Individual Requirements:
   * Participate in team and partner meetings.  Actively contribute to your group.

## Team Requirements:
   * Maintain a Trello board and track your sprint backlog along with time estimates.
   * There is a significant [coding project](./project.md) for this sprint.  You may work in either paris or triads.  Thus, if you're currently in a group of 4, you can split into 2 pairs.  If you are currently in a team of 3, you can work as a triad.  It will be useful to arrange your pairings in such a way that there are strong coders and strong mathematicians working on the project together; a diverse team is a strength!
   
# Resources:  
I may add to these as we go through the sprint.  Use the [Expert Request](https://rollins.co1.qualtrics.com/jfe/form/SV_0jNfbBpN1clDJfn?course=mat310s20&sprint=3) for more resources. 
   * Textbook reading: For this sprint, you should read both as FoC contains significantly more information on CFGs, but doesn't contain anything on ambiguous grammars. 
      * FoC: Chapter 4.1-4.3 -- 4.2 will be most relevant to the project
      * IToC: 2.1 (especially subsection on ambiguity, pgs. 105-106 in 2nd ed.)
  * [Concise Overview of CFGs and Parse Trees](https://brilliant.org/wiki/context-free-grammars/)
  * [Practice parsing problems](./cfg-problems.pdf)
  * [Practice lexical analysis problems](./la-problem.pdf)
  * [Practice quiz](./practice_quiz.pdf)
  
# Reality Check:
  * On Wednesday, after quiz: Do Sprint 2 retrospective. Do basic team setup.  Do sprint planning meeting.  Decide on project groupings (pair or triad)
  * TODO by next class:  Watch the video about grammars and the overview of the projects.  Download starter code and get it compiling.  Read about parse trees and try one or two of the practice parse tree problems.
  * Monday class: Mini-lecture on coding the parsing process using left-derivation.  Start working on understanding code and perhaps start Part 1.
  * TODO by next class: Finish Part 1 of project.  Watch video on lexical analysis and try one of the practice problems.
  * Wednesday: Continue working on project.
  * TODO by Monday: Continue working on project.  Finish Part 2 and at least read over Part 3.
  * Monday: Continue working on project.  Hopefully be finished or close to it.
  * TODO before next class: Take the practice quiz and check your answers.  Study as a group.  
  * Wednesday: Quiz (30 minutes), Sprint 3 retrospective, and Sprint 4 planning.

# Retrospective:
  * Complete this [sprint retrospective survey](https://rollins.co1.qualtrics.com/jfe/form/SV_3rAIzhpHFYbIixf?course=mat310s20&sprint=3).  Each team member should complete a survey.
