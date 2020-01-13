# Lab - Regular Expressions

This short lab will let you practice using Codio and expose you to regular expressions with `grep`, a standard Unix program for searching the contents of files.

## Getting Started
Play around with some features of Codio.
* Open a terminal by clicking on the small computer browser icon at the top of the file tree (between the padlock and the play icons).
* In the `View>Panels` and `View>Layouts` menu, you can configure your screen however you like.  Some assignments will start with a default view, but you can change it to your liking.
* There are also options in the `View` menu for changing font size and color scheme.  There is also a useful `Wrap` option which wraps lines of files to keep them in your viewable screen.
* You can navigate the sections of this lab by clicking on the "hamburger menu" (circle with 3 horizontal lines in it) on the right hand side of this panel and selecting the section you want.  You can get rid of the section navigation by clicking the same menu again.

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

### Wordlist

There is a tradition of including a plain text dictionary of words as part of a Unix installation, for use with spellchecking and password programs. The list we used for the password cracking project in CMS230 was a medium sized example.

To begin, type `sudo apt-get install wamerican-large`.  This installs a large list of American words into the directory `/usr/share/dict`. Other less patriotic wordlists are also available. You can run `sudo apt-get install wordlist` to see a list of all the options.

Remind yourself: what does the `sudo` command do?

## Searching for a Substring
The simplest `grep` case is to search target file for a specified substring:

```
prompt$ grep "cat" /usr/share/dict/american-english-large
```

The first argument is the search string enclosed in quotes. It isn't strictly required to use quotes with every regex string, but some complex patterns require it, so I like to use it as a default. You could also enclose the string in single quotes.

The second argument is the complete path to the dictionary file. Remember that Unix file paths always begin at the root directory, which is denoted by `/`. Pro tip: press TAB to autocomplete the part of the path that you're currently typing.

The command prints all words in the file containing the string `cat`. This is a lot of words!

Other substring choices may yield fewer results:

```
grep "platypus" /usr/share/dict/american-english-large
```

* **Question 1:** Write a command which finds all the words with 2 consecutive a's.
* **Question 2:** List all the words that have 3 consecutive a's. Separate them by commas or specify that there are no words with three consecutive a's.

## Matching Beginning and Endings
Use the `^` symbol to denote the beginning of a line. Patterns that start with `^` will only be matched if they occur at the beginning of the line. To find all words that begin with `x`:

```
prompt$ grep "^x" /usr/share/dict/american-english-large
```

* **Question 3:** Write a command to all the words that begin with `q`.

Use `$` to match the end of a line. To find all of the words that end with `esses`:

```
prompt$ grep "esses$" /usr/share/dict/american-english-large
```

Write a command which find all the words that end with `ingly`.

If you combine both `^` and `$`, you can specify a line that must be matched exactly:

```
prompt$ grep "^platypus$" /usr/share/dict/american-english-large
```

## Character Sets
`grep` becomes more powerful when you allow it to match patterns that contain multiple options.

Sets of characters are enclosed in square brackets. For example,

- `[aeiou]` is the set of lowercase vowels
    
- `[aeiouAEIOU]` is the set of lowercase and uppercase vowel characters

- `[a-zA-Z0-9]` is the compact way or writing the set of all lowercase letters, uppercase letters, and digits
    
When you give `grep` a character set, it's allowed to match any of the options that occur within the set. For example, find all the words in the wordlist that start with `q` followed by a vowel but not `qu`:

```
prompt$ grep "^q[aeio]" /usr/share/dict/american-english-large
```

Questions like the previous one can be made easier by using `^` as the first symbol in a set, which matches everything *except* the characters in the set:

- `[^aeiou]` is the set of all characters *except* the lowercase vowels
    
- `[^a-z]` is the set of all characters *except* the lowercase letters
    
Notice: the `^` character is **inside** the brackets forming our character set.  This is different than a `^` that appears at the start of an expression and matches the beginning of a string.    
    
Find all the words that contain characters other than a normal lowercase or uppercase letter:

```
prompt$ grep "[^a-zA-Z]" /usr/share/dict/american-english-large
```
    
* **Question 4:** Write a command to find all the words that start with `q` but not `qu` using the exclusionary character set approach. Hint: notice that this problem doesn't state that `q` must be followed by a vowel! It can be followed by any letter except a `u`.

## Kleene Closure
Use `*` to match 0 or more instances of a pattern. This is often combined with `.` as a generic placeholder that can match any character. `.*` is therefore a wildcard that matches any number (including zero) of arbitrary characters.

Find the words of any length that start with `q` and end with `ing`:

```
prompt$ grep "^q.*ing$" /usr/share/dict/american-english-large
```

* **Question 5:** I'm thinking of a word that starts with he and ends with he. The word is either ____ or ____ 
* **Question 6:** Write a command which could be used to find all the words that contain no vowels. Hint: use `^` and `$` to specify that the entire line must have no vowels.

## Repetition
The syntax `\{n\}` lets you repeat a pattern `n` times. This is a little weird. The `\` functions as an escape character to indicate
that you're using `{` and `}` as control characters, rather than the literal left and right brace characters. This is one of the
frustrating things about regular expressions: some control characters, like `*`, are used as-is, but others need the special 
backslash escape prefix.

Find all the words with 4 vowels:

```
prompt$ grep "[aeiou]\{4\}" /usr/share/dict/american-english-large
```

* **Question 7:** Write a command to find all the words with 5 consecutive consonants.

**For fun: find the words with the most consecutive consonants by trying larger values of `n`.**

## Union
Use `\|` to match one of a set of options. Again, the use of the `\` is obnoxious. Sorry, not sorry.

Find all the words that start or end with `x`:

```
prompt$ grep "^x\|x$" /usr/share/dict/american-english-large
```

Parentheses `\(` and `\)` can be used to group expressions. For example, to match all words starting with either `aa` or `ee`:

```
prompt$ grep "^\(aa\|ee\)" /usr/share/dict/american-english-large
```

## Advanced Regular Expressions (a.k.a Things that Make Your Brain Hurt)
Here's one last advanced regex feature. If you enclose a pattern in parentheses, the string that matches that pattern is said to be "captured". The evaluator will keep track of the captured pattern in case you want to refer to it later, which you can do using `\1`.

For example, match any words having double letters:

```
prompt$ grep "\(.\)\1" /usr/share/dict/american-english-large
```

The parentheses grouping `\(.\)` matches any character. The `\1` then requires the analyzer to match whatever character was captured during the evaluation of the parenthesis expression.

Note that this could not be achieved by using `[a-z]\{2\}`. Try it out if you're unsure why.

## Wrap-Up
This is really just a cursory overview of the features of `grep` and regular expressions in Unix. A few final notes:

- I do not expect you to memorize the syntax for regular expressions.  Everyone just looks up the syntax when they need it.
    
- There are actually a few different syntaxes in circulation; the Perl language has its own built-in regex evaluator with its own syntax.

- Virtually all programming languages have the capability (or the capability is added via a library) to utilize regular expressions. 
    
- The general answer to questions about pattern matching, substring replacement, etc. is "Use `grep` and regular expressions."


What if we wanted to match words with consecutive pairs of double letters? Use a second pair of parentheses to capture a second letter, then match it a second time with `\2`:

```
prompt$ grep "\(.\)\1\(.\)\2" /usr/share/dict/american-english-large
```

I'm thinking of a word that has three consecutive pairs of double letters:

```
prompt$ grep "\(.\)\1\(.\)\2\(.\)\3" /usr/share/dict/american-english-large
```

This example also illustrates how the regex evaluators contained in real programming environments are more powerful than the default theoretical model of regular expressions, which assumed that we had no available memory for elements of the string and therefore couldn't keep track of anything.







