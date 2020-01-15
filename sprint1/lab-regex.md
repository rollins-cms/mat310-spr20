# Lab - Regular Expressions

"Regular expressions" in a programming environment means something slightly different (and more powerful) than "regular expressions" as a concept in formal language theory.  Programmers usually mean the former, while mathematicians usually mean the latter.  This short lab will expose you to (the programmer version of) regular expressions with `grep`, an indispensable Unix programming tool (for decades!) for searching the contents of files. 

It's amazingly useful to be able to search for a pattern within a huge data file without having to pre-process the data (e.g. load it into an Excel spreadsheet or write a program to manipulate into a manageable format).  Take these examples:

### Example 1 (from genomics):
A nucleic acid is represented by the letters `a`, `c`, `g`, or `t`.  A genome is a string of nucleic acids.  Fragile X Syndrome is a genome having an occurrence of `gcg` followed by any number `cgg` or `agg` triplets followed by `ctg`.  Does a large file containing many genome's worth of data have a genome with Fragile X Syndrome? 

### Example 2 (from commercial computing):
An email address is a sequence of characters consisting of:
1. a sequence of letters followed by
2. the character `@` followed by
3. a non-empty sequence of lowercase letters followed by
4. a `.` followed by
5. `com` or `edu`  
Given a large file of email addresses, can we determine which were malformed (perhaps by data entry errors)?

Again, tools which have existed for decades can help us easily answer problems of these sorts.

## Getting Started
This is a hands-on activity which is meant to be done at a computer.  The easiest way to do this is on a Mac (because Macs are built on BSD which is a variation of the Unix operating system).  I recommend that your team make time to do this activity in the Mac lab (Bush 308 or in Olin Library).  I have tested this activity in the Bush Mac lab.  Your personal Mac laptop (should you own one) will probably also work, but no guarantees.

* On a Mac, open the Spotlight search window by clicking on the small magnifying glass (upper right hand corner).
* Type in "terminal" and click to open the Terminal application -- the terminal is a powerful window to the inner workings of your computer.  Usually, we interact with computers via Graphical User Interfaces (GUIs) consisting of windows, icons, a mouse, etc.  But you don't have to use the GUI.  The Terminal gives us access to another way of interacting with our computer.  Instead of pointing-and-clicking, we will type powerful, (seemingly) arcane commands to instruct the computer to do things.  Protip: think of it like casting computer spells and start referring to yourself as "Harry" or "Hermione".  

* In the terminal, you will see some information, followed by something that looks like:
```
bushimac11:~ vsummet$
```
This is called the *prompt*.  It indicates that the computer is ready for you to type a command to interact with it.  Yours will include a different computer name (I was on `bushimac11` when writing this activity) and a different user ID (you are not `vsummet` afterall; you are Harry/Hermione).  In this lab, we'll use the generic:
```
prompt$
```
to indicate that a command should be typed at a prompt.  You should follow along with this activity and type the commands and observe the output for yourself.  If something goes wrong, you can almost always get back to a prompt by typing `Ctrl-c` -- that is, hold down the control key and type a `c` at the same time.

## Now You Have Two Problems

There's a famous programming joke:

```
You need to solve a problem. 
You decide to use regular expressions. 
Now you have two problems.
```

`grep` is short for *globally search a regular expression and print*. Its basic use case takes two arguments:

- A regular expression
    
- A file name
    
`grep` searches the target file for any strings matching the pattern described by the regular expression and prints them to the screen.

To run the `grep` utility, we'll type `grep` at the prompt.

### Wordlist

There is a tradition of including a plain text dictionary of words as part of a Unix installation, for use with spellchecking and password programs. On Macs, this dictionary is preinstalled in `/usr/share/dict/words`.  There are other dictionaries too including ones with proper names.  You can install many more as well.

## Searching for a Substring
The simplest `grep` case is to search target file for a specified substring:

```
prompt$ grep "cat" /usr/share/dict/words
```

The first argument is the search string enclosed in quotes. It isn't strictly required to use quotes with every regex string, but some complex patterns require it, so I like to use it as a default. You could also enclose the string in single quotes.

The second argument is the complete path to the dictionary file. Remember that Unix file paths always begin at the root directory, which is denoted by `/`. Pro tip: press TAB to autocomplete the part of the path that you're currently typing.

The command prints all words in the file containing the string `cat`. This is a lot of words!

Other substring choices may yield fewer results:

```
grep "platypus" /usr/share/dict/words
```

* **Question 1:** Write a command which finds all the words with 2 consecutive s's.
* **Question 2:** List all the words that have 3 consecutive s's (or specify that there are none if that's what your results indicated). 

## Matching Beginning and Endings
Use the `^` symbol to denote the beginning of a line. Patterns that start with `^` will only be matched if they occur at the beginning of the line. To find all words that begin with `x`:

```
prompt$ grep "^x" /usr/share/dict/words
```

* **Question 3:** Write a command to all the words that begin with `q`.

Use `$` to match the end of a line. To find all of the words that end with `esses`:

```
prompt$ grep "esses$" /usr/share/dict/words
```

* **Question 4:** Write a command which find all the words that end with `ingly`.

If you combine both `^` and `$`, you can specify a line that must be matched exactly:

```
prompt$ grep "^ship$" /usr/share/dict/words
```

Compare that output to that generated by the command:
```
prompt$ grep "ship$" /usr/share/dict/words
```


## Character Sets
`grep` becomes more powerful when you allow it to match patterns that contain multiple options.

Sets of characters are enclosed in square brackets. For example,

- `[aeiou]` is the set of lowercase vowels
    
- `[aeiouAEIOU]` is the set of lowercase and uppercase vowel characters

- `[a-zA-Z0-9]` is the compact way or writing the set of all lowercase letters, uppercase letters, and digits
    
When you give `grep` a character set, it's allowed to match any of the options that occur within the set. For example, find all the words in the wordlist that start with `q` followed by a vowel but not `qu`:

```
prompt$ grep "^q[aeio]" /usr/share/dict/words
```

Questions like the previous one can be made easier by using `^` as the first symbol in a set, which matches everything *except* the characters in the set:

- `[^aeiou]` is the set of all characters *except* the lowercase vowels
    
- `[^a-z]` is the set of all characters *except* the lowercase letters
    
Notice: the `^` character is **inside** the brackets forming our character set.  This is different than a `^` that appears at the start of an expression and matches the beginning of a string.    
    
Find all the words that contain characters other than a normal lowercase or uppercase letter:

```
prompt$ grep "[^a-zA-Z]" /usr/share/dict/words
```
    
* **Question 5:** Write a command to find all the words that start with `q` but not `qu` using the exclusionary character set approach. Hint: notice that this problem doesn't state that `q` must be followed by a vowel! It can be followed by any letter except a `u`.

## Kleene Closure
Use `*` to match 0 or more instances of a pattern. This is often combined with `.` as a generic placeholder that can match any character. `.*` is therefore a wildcard that matches any number (including zero) of arbitrary characters.

Find the words of any length that start with `q` and end with `ing`:

```
prompt$ grep "^q.*ing$" /usr/share/dict/words
```

* **Question 6:** I'm thinking of a word that starts with `he` and ends with `he`. The word is either ____ , ___, or ____ 
* **Question 7:** Write a command which could be used to find all the words that contain no vowels. Hint: use `^` and `$` to specify that the entire line must have no vowels.

## Repetition
The syntax `\{n\}` lets you repeat a pattern `n` times. This is a little weird. The `\` functions as an escape character to indicate
that you're using `{` and `}` as control characters, rather than the literal left and right brace characters. This is one of the
frustrating things about regular expressions: some control characters, like `*`, are used as-is, but others need the special 
backslash escape prefix.

Find all the words with 4 consecutive vowels:

```
prompt$ grep "[aeiou]\{4\}" /usr/share/dict/words
```

* **Question 8:** Write a command to find all the words with 5 consecutive consonants.

* **Question 9:** What word(s) in the dictionary contain the most consecutive consonants? The most consecutive vowels?  Hint: just trying larger values of `n`.**

## Union
Use `\|` to match one of a set of options. Again, the use of the `\` is obnoxious. Sorry, not sorry.

Find all the words that start or end with `x`:

```
prompt$ grep "^x\|x$" /usr/share/dict/words
```

Parentheses `\(` and `\)` can be used to group expressions. For example, to match all words starting with either `aa` or `ee`:

```
prompt$ grep "^\(aa\|ee\)" /usr/share/dict/words
```

## Advanced Regular Expressions (a.k.a Things that Make Your Brain Hurt)
Here's one last advanced regex feature. If you enclose a pattern in parentheses, the string that matches that pattern is said to be "captured". The evaluator will keep track of the captured pattern in case you want to refer to it later, which you can do using `\1`.

For example, match any words having double letters:

```
prompt$ grep "\(.\)\1" /usr/share/dict/words
```

The parentheses grouping `\(.\)` matches any character. The `\1` then requires the analyzer to match whatever character was captured during the evaluation of the parenthesis expression.

Note that this could not be achieved by using `[a-z]\{2\}`. Try it out if you're unsure why.
* **Question 10:** What does the regular expression `[a-z]\{2\}` actually match?

## More Pain!
What if we wanted to match words with consecutive pairs of double letters? Use a second pair of parentheses to capture a second letter, then match it a second time with `\2`:

```
prompt$ grep "\(.\)\1\(.\)\2" /usr/share/dict/words
```

* **Question 11:** I'm thinking of a word that has *four* consecutive pairs of double letters.  What is it?

This example also illustrates how the regex evaluators contained in real programming environments are more powerful than the default theoretical model of regular expressions, which assumed that we had no available memory for elements of the string and therefore couldn't keep track of anything.


## Wrap-Up
This is really just a cursory overview of the features of `grep` and regular expressions in Unix. A few final notes:

- I do not expect you to memorize the syntax for regular expressions.  Everyone just looks up the syntax when they need it.
    
- There are actually a few different syntaxes in circulation; the Perl language has its own built-in regex evaluator with its own syntax.

- Virtually all programming languages have the capability (or the capability is added via a library) to utilize regular expressions. 
    
- The general answer to questions about pattern matching, substring replacement, etc. is "Use `grep` and regular expressions."








