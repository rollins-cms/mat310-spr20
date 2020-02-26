# Parsing The Y Programming Language

In this project, we'll be writing a parser given the rules of a grammar defining a real programming language.  In this case, the language is based Per Brinch Hansen's *On Pascal Compilers* which describes the TinyBasic language, and Bob Nystrom's Lox language described in the free book *Crafting Interpreters*.  This language uses a Pascal-like syntax.  The following program is written in the `y` language:

```
program Print:
    print 1 + 2 + 3
end
```


As we discussed, the first step is taking a string of characters (the source code) and turning it into a series of symbols or **tokens**.  This process is called **lexical analysis** or just "lexing."  After we have a sequences of tokens, we have to parse those tokens to see if we have a syntactially correct program.  Once we know the **parse tree**, we can do a **tree-walk** to execute the program in an **interpreter**.  You'll be implementing parts of a parser. (The lexer and interpreter are already written for you, but you'll have to uncomment some sections of the interpreter.) By the end, we won't have a full-fledged programming language, but hopefully you'll see how you could extend this project to get there.

## Getting Started
Download the source code zip file and extract it.  You can compile and run this program any way you choose: the command line, an IDE like Eclipse or IntelliJ, or an online IDE like Mimir or Codio (if you have access to one).

For this project, you should compile the `Driver.java` file which includes all the other necessary files.  If you look at the code, you'll see it first calls the `Lexer` class to lex the source code and turn it into a stream of tokens (the definition of tokens are in `Token.java`).  After that, it calls the `Parser` class to try to turn that sequence of tokens into a `Program`.  Finally, the `Program` (the parse tree) is passed to the interpreter which executes the statements.

So let's take a look at the grammar that the code currently implements.

#### EBNF Grammar
This is the first version of the language grammar. It allows for printing addition expressions using only integer values.

Some notes about the EBNF format:
* `' '` denotes a literal token
* `[ ]` denotes 0 or 1 instance of its contents
* `{ }` denotes 0 or more instances of its contents

```
Program --> 'program' NAME ':' Block 'end'

Block --> {Statement}

Statement --> PrintStatement

PrintStatement --> 'print' Expression

Expression --> AddExpr

AddExpr --> Atom [('+' | '-') AddExpr]
            
Atom --> IntegerLiteral | '(' Expr ')'
```

#### Example Program
```
program Print:
    print 1 + 2 + 3
end
```

Take a moment and draw the parse tree for the above program, following the grammar rules given.  The `Parser.java` code currently implements this grammar. So let's investigate it.

You can currently compile and run `Driver`.  In the source code directory is a directory named `tests`.  This `tests` directory has the `Print.y` source code in it which is the syntactically correct program above which can be parsed.  `Driver` takes a command line argument with the name of a source code file:

```
prompt$  javac Driver.java
prompt$  java Driver tests/Print.y
```
After you do this, you should see output which indicates the three stages (lexing, parsing, and interpreting) your source code (`Print.y`) has taken place.  You should also see the output from the program: `6` because the statement `print 1+2+3` should cause the value 6 to be displayed.

Now, you need to understand how the Parser arrived at that conclusion.  Your first job is to understand how the `Parser.java` code implements the above grammar.  Notice that `Driver.java` calls the parser, starting with the `Program` production rule (remember that the first rule in our grammar indicates the starting point).  
```
	// Step 3: Parsing
    	// Returns the root of the parse tree
    	Parser parser = new Parser(tokens);
    	Program program = parser.parse();
```
As you can see in the comments, the `Program` rule is the **root of our parse tree**!

Now, open the `Parser.java` code and find the `parse` method:
```
public Program parse() {
	consume(Tokens.PROGRAM);
	consume(Tokens.NAME);
	consume(Tokens.COLON);
	Stmt.Block body = block(); 
	...
```
This code "consumes" tokens as we parse them and moves us through the series of tokens (from the lexer).  In this case, the tokens correspond to the `Program --> 'program' NAME ':' Block 'end'` grammar rule.  We then call the `block` method once we reach the point where the sequence of tokens must contain a "block" of statements.  

Continue tracing the code into the `block` method where we see that a block of code must be a series of statements (ie, the production rule `Block --> {Statement}`. Continue tracing the small sample program above, reading the method comments carefully to understand how we continue to call more and more methods (which are the **production rules** of our grammar) until we come to the **terminals** which cannot be expanded any more.  Remember that methods do not return/finish until their either 1) encounter an explicit `return` statement or finish all the statements in the method body.  Compare the series of method calls to your hand-generated parse tree for the program.

Now, let's kick it up a notch and add a rule to our grammar for multiplication/division.

## Part 1: The Y Programming Language -- v 0.2 -- Adding Multiplication, Division and Modulo

Here's an updated grammar for adding multiplication/division statements and expressions:

#### Updated EBNF Grammar

```
Program --> 'program' NAME ':' Block 'end'

Block --> {Statement}

Statement --> PrintStatement

PrintStatement --> 'print' Expression

Expression --> AddExpr

AddExpr --> MultExpr [('+' | '-') AddExpr]
            
MultExpr --> Atom [('*' | '/') MultExpr]

Atom --> IntegerLiteral | '(' Expr ')'
```

Note changes from v.1 to v.2!
* `AddExpr` production changed
* `MultExpr` production added (very similar to old `AddExpr`)

Steps to make our Parser support new `MultExpr`:
1. Add an `MultExpr` class in the `Expr.java` file.  You can closely match the existing `AddExpr` class.
2. Add a `multExpr()` method to the parser.  Look at the existing `addExpr()` for reference and change the function calls to match the modified grammar. 
3. Modify the `addExpr` method to make the left part a `MultExpr` instead of an `Atom` as it was in v.1.

Question: **What is the purpose of the classes defined in `Stmt.java` and `Expr.java`?**

Answer: They are blobs of state that represent nodes in the parse tree. Each class has a set of state variables that represent that 
important children of that node, as defined in the language grammar.



Once you get the above steps finished, open `Driver.java` and change the line which says:
```
InterpreterInitial interpreter = new InterpreterInitial();
```
to
```
InterpreterPart1 interpreter = new InterpreterPart1();
```
This gives us a new interpreter which matches our new grammar.

Now try your parser with the program `tests/Mult.y` as it can be parsed with no errors.  Don't proceed until you have `PrintMult.y` parsing and giving the correct output.

#### Mod
Now, let's add support for a mod operator. We can easily do this in our `MultExpr` rule, and the updated production would be:

```
MultExpr --> Atom [('*' | '/' | '%') MultExpr]
```

Take a look at the `multExpr` method in the parser.  How would you change it to correctly parse the modulo operator?  This change should be very straightforward.

After you've added this functionality, make sure the `Mod.y` program compiles/parses correctly and gives the expected output.


## Part 2: The Y Programming Language -- v. 0.3 -- Negations, Variables, and Assignments, OhMy!

Now we're going add more things: negation statements, variables, Strings, and assignment statements.

### Updated EBNF Grammar:
```
Program --> 'program' NAME ':' Block 'end'

Block --> {Statement}

Statement --> PrintStatement
              | AssignStatement

PrintStatement --> 'print' Expression
              
AssignStatement --> NAME ':=' Expression

Expression --> AddExpr | StringExpr

StringExpr --> StringLiteral

AddExpr --> MultExpr [('+' | '-') AddExpr]

MultExpr --> NegExpr [('*' | '/' | '%') MultExpr]

NegExpr --> '-' NegExpr | Atom
                    
Atom --> IntegerLiteral
         | '(' Expression ')'
         | NAME
```
A few things to notice about this version of the grammar:
1. `Statement` now has two choices: `PrintStatement` or `AssignStatement`. After this rule, we'll be able to have statements like `x := 4`.  An `AssignStatement` is required to begin with a `NAME` token and use the assignment operator, `:=`.  (The `NAME` token is an identifier -- e.g. program name, method name, or variable name.)  After this rule, we'll be able to have statements like `x := 4`
2. `NAME` has also been added as a option for `Atom` -- this corresponds to using a variable in an expression like `x+4` instead of having only literal integer values like `3+4`
3. Another new element is `NegExpr` which allows us to negate a value or variable.
4. We also have a new production rule for dealing with the `String` datatype, and the `Expr` production rule has also changed to accommodate this new datatype.


### Negation Expression
A new element of our grammar is `NegExpr`, which now sits between `MultExpr`, which we added last time, and `Atom`. A `NegExpr` implements the unary negation operator. `NegExpr` can be chained, so it's possible to negate a negation. The following example is valid:

```
program Negation:
  print ---(2+5)
end
```

Implementing `NegExpr` requires a few changes:
1. Adding a `NegExpr` class to `Expr.java`:
```
    static class NegExpr extends Expr {
        Expr expr;
        boolean hasNegative;

        public NegExpr(Expr expr, boolean hasNegative) {
            this.expr = expr;
            this.hasNegative = hasNegative;
        }
    }
 ```

2. Adding a `negExpr()` method to `Parser.java`. The method has two cases, one where the `NegExpr` begins with a minus symbol and one where it's simply an atom.  We have to check our stream of tokens to see if the next token is `MINUS`.  If it is, then we use the `NegExpr --> '-' NegExpr` rule.  Otherwise, we use the `NegExpr --> Atom` rule.  Don't forget to "consume" the `MINUS` token and return a new `Expr.NegExpr` object.  Remember that all this goes back to the fact that the `NegExpr` production rule in our EBNF grammar has 2 possible expansions.
3. Modifying our `multExpr()` method in Parser.java since the `MultExpr` production rule has changed.  This method will now need to call the `negExpr()` method you wrote in step 2 for the left hand side expression.

Take a moment and reflect on how the structure of these changes follows directly from the grammar.

Make sure your code compiles:
```
javac Driver.java
```
before moving on.

### Towards Variables

From the perspective of parsing, adding support for variables is pretty straightforward.  Remember that our lexer already determined whether or not the variable's identifier (ie name) is valid.  The parser just has to determine if the sequence where a variable's name is used is syntactically valid.  After the parser is finished, the compiler/interpretter is actually responsible for updating mappings in a **symbol table** which stores (name, value) pairs for each variable in the program.

### Adding Classes for Assignment Statements
An assignment statement has a variable name on the left hand side and an expression on the right-hand side. Like `x = 4;` in Java or `x := 4` in our language.  Add the following to `Stmt.java`:

```
static class AssignStmt extends Stmt {
	String name;
    	Expr expr;
    	
    	public AssignStmt (String name, Expr expr) {
      		this.name = name;
      		this.expr = expr;
    	}
}
```

A variable access is described by the name of the variable. Add this to `Expr.java`:

```
static class VarAccess extends Expr {
	String name;
		
	public VarAccess(String name) {
      		this.name = name;
    	}
}
```

Before going on, compile your program and fix any errors using the command `javac Driver.java`

### Modify Parser to Recognize Assignment Statements
1. Add the `assignStmt` method to `Parser.java` which handles the production rule:
```
AssignStatement --> Name ':=' Expression
```
It should return an `Stmt.AssignStmt` object you defined previously.

2. Now, add code to recognize assignment statements. Modify the `stmt` method in `Parser.java` to recognize each kind of statement. Currently, if the code recognizes a `PRINT` token, it knows that the statement is a print statement.  We now add the functionality that if a statement begins with a `NAME` token, it must be an assignment.  It's a good idea to do a quick compile here to make sure you have correctly matched the types before proceeding.

3. The next set of changes need to be made to the `atom` method, to recognize variables used in expressions. We previously checked if an atom was an integer literal or something nested inside parentheses.  Now we need to check for the existence of a `NAME` token which would indicate a variable being used.  This method can now return a `Expr.VarAccess` object made with the `NAME` token's `value` field.  Don't forget to consume the `NAME` token **after** you've extracted the value.

Take another break to compile your program (`javac Driver.java`) and fix any errors that have shown up. Remember to always start with the first error produced by the compiler.

### Strings
Now we want to add a new data type to our langauge.  So far, we've just had integers, but now we want to have Strings.

1. We need to add a `StringExpr` class to the `Expr.java` file.  Look at the `IntegerExpr` class and copy/paste it.  Change it's name to `StringExpr` and then figure out how to change it so that it stores a String rather than an Integer value.
2. Now we have to take into account that the `Expression` production rule now has two possible expansions: `Expression --> AddExpr | StringExpr`.  Modify the `expr()` method in `Parser.java` to check to see if the next token is a `STRING` token.  If it is, we need to get the value of that token, consume the `STRING` token, and create a new `StringExpr` from the value:
```
if (check(Tokens.STRING)) {
   String s = (String) currentToken().value;
   consume(Tokens.STRING);
   return new Expr.StringExpr(s);
}
```
Incorporate the above code into the `expr` method.  

### Testing Part 2
Let's check our work:

First, we need to update our interpreter to match are new grammar.  Change the interpreter in `Driver.java` to be:
```
InterpreterPart2 interpreter = new InterpreterPart2(program);
```
Then recompile and:
* Run `Negation.y` test and verify your code now supports negation statements.
* Run `Assignment.y and Reassignment.y` to check variable support as well as assignment statements
* Run the test `Strings.y` to test string datatypes

You can (and should) change some of these `.y` programs to test your implementation.

## Part 3 -- Y Programming Language v.0.4 -- While Loops
The last feature we'll add to our language is the idea of conditional execution.  This also requires some rules for relational operators: `=, >, >=, <, <=, <>` (remember, these are Pascal operators, not Java).


### Final Grammar
```
Program --> 'program' NAME ':' Block 'end'

Block --> {Statement}

Statement --> PrintStatement
              | AssignStatement
	      | WhileLoop

WhileLoop --> 'while' CondExpr ':' Block 'end'

PrintStatement --> 'print' Expression
              
AssignStatement --> Name ':=' Expression

Expression --> CondExpr | StringExpr

StringExpr --> StringLiteral

CondExpr --> AddExpr [('=' | '<' | '<=' | '>' | '>=' | '<>') AddExpr]

AddExpr --> MultExpr [('+' | '-') AddExpr]

MultExpr --> NegExpr [('*' | '/' | '%') MultExpr]

NegExpr --> '-' NegExpr | Atom
                    
Atom --> IntegerLiteral
         | '(' Expression ')'
         | Name
```

A few things to notice:
* We now have a conditional expression production rule which uses relational operators to compare a left and (optional) right side.  This allows us to have expressions like `x <= 4` for example.
* We have a new production rule for while loops which begin with teh keyword "while" followed by a conditional expression, a colon, and a block of statements which ends with the keyword "end".
* We've added the while loop to our statement production rule (since an entire while loop is a statements).
* The expression prodcution rule has changed to be either a conditional statement or an atom (the previous version was an addition expression or an atom)


### Conditional Expression
First, we need to modify our language to account for expressions like `x < 6` or `x + 4 < 10`.

Note that there's a new kind of expression, `CondExpr`.  Stated simply, this sort of expression would result in a true/false result (although, like C, our language above doesn't have a formal booleans datatype).  To add support for `CondExpr` we need to do two things:
1. Add the `CondExpr` class to `Expr.java` (This object will be very similar to `AddExpr` or `MultExpr` so copy one of those and modify it.)
2. Add the `condExpr()` rule method to `Parser.java`.  This will be very similar to the `addExpr()` method but will use different operators.  See `Token.java` for the operator names.
3. Modify the `expr()` method to use a `CondExpr` instead of an `AddExpr` as in our last version of the grammar.

Your parser will now be able to handle simple programs with condition expressions like:
```
program Conditional:
	print 9 < 4
end
```

### Combining a conditional expression with a block of code
So we now have the `CondExpr` which can be used to determine if the body of the while loop executes or not.  We're now ready to try out the rules for `WhileLoop` in our grammar.  However, we need a special kind of `Block` object in our code: one which stores a condition with it.  This would allow us to test the condition once (in the case of an if statement) or multiple times (in the case of a while loop) before we execute the body of the statement.
1. Add a `CondBlock` object to `Stmt.java` (similar to `Block` but stores condition with it):
```
    static class CondBlock extends Stmt {
        Block body = null;
        Expr condition = null;
        
        public CondBlock(Expr condition, Block body) {  
            this.condition = condition;
    	    this.body = body;
    	}
    }
```
2. We're now ready to add `whileLoop()` method to `Parser.java` to implement the `WhileLoop` production from our grammar.  Take a look at the `parse()` method which implements the `Program --> 'program' NAME ':' Block 'end'` production rule.  Our `whileLoop()` method will be similar, but will need to return a `CondBlock` instead of just a `Block`.
3. Modify the `stmt()` method to call the `whileLoop()` method if it sees a `WHILE` token.  `stmt` should now account for the three different types of statements: print, assignment, and while loop.

### Testing Part 3
Just as in previous parts, we need to make our interpreter match our new Grammar, so update the Driver to use `InterpreterPart3`.

Then:
* Run the test `Conditional.y`.  You may want to change this test a few times to check that all 6 relational operators are working
* Run the test `While.y`.  You can modify this program to change the execution (or lack thereof) of the body of the while loop.

## Reflect
Take a minute to pat yourself on the back and reflect on what you've built.  Using a formal grammar, you've implemented a good chunk of a compiler!  Specifically, a **top-down, recursive** parser.  You're now able to see exactly how we use foundational concepts from formal languages to implement programming languages and useful tools (like compilers!) better.  You have a really good understanding of why programming languages need to be **unambiguous** and how that relates to our grammar.  You might also have a better appreciation of [Admiral Grace Hopper's](https://history-computer.com/ModernComputer/Software/FirstCompiler.html) work on the very first compiler and why we consider that development such a watershed moment in computer science!  It's worth noting that she was a formally trained mathematician.

Obviously, we'd need to add a few more things before we had a full fledged (Turing complete) programming language.  Chief among them are subroutines/functions and if statements.  But after seeing while loops, you probably have a good idea what those if statements might look like.  But consider, what does an `else` part of an if statement look like? Fun for a future day.

## Submission
At the beginning of `Parser.java` add four things:
1. The names of everyone who worked on the project
2. Your honor code statement
3. Your collaboration statement
4. An estimate of how many hours you worked on this project.

Then, zip up your `Parser.java`, `Expr.java`, and `Stmt.java` files. Submit you zip file to the assignment on Canvas for this project. 
