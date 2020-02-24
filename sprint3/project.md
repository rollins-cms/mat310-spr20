# Parsing The Y Programming Language

In this project, we'll be writing a parser given the rules of a grammar defining a real programming language.  In this case, the language is based Per Brinch Hansen's *On Pascal Compilers* which describes the TinyBasic language, and Bob Nystrom's Lox language described in the free book *Crafting Interpreters*.

As we discussed, the first step is taking a string of characters (the source code) and turning it into a series of symbols or **tokens**.  This process is called **lexical analysis** or just "lexing."  After we have a sequences of tokens, we have to parse those tokens to see if we have a syntactially correct program.  You'll be implementing parts of a parser.  By the end, we won't have a full-fledged language like you're used to programming in, but hopefully you'll see how you could extend this project to get there.

## Getting Started
Download the source code zip file and extract it.  You can compile and run this program any way you choose: the command line, an IDE like Eclipse or IntelliJ, or an online IDE like Mimir or Codio (if you have access to one).

For this project, you should compile the `Driver.java` file and this file kicks off the process.  If you look at the code, you'll see it first calls the `Lexer` class to lex the source code and turn it into a stream of tokens (the definition of which is in `Token.java`).  After that, it calls the `Parser` class to try to turn that sequence of tokens into a `Program`.

So let's take a look at the grammar that the code currently implements.

#### EBNF Grammar
This is the first version of the language grammar. It allows for printing addition expressions using only integer values.

`' '` denotes a literal token
`[ ]` denotes 0 or 1 instance of its contents
`{ }` denotes 0 or more instances of its contents

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
program PrintTest:
    print 1 + 2 + 3
end
```

You can currently compile and run `Driver`.  In the source code directory is a directory named `tests`.  This `tests` directory has the `PrintTest.y` source code in it which is the syntactically correct program above which can be parsed.  `Driver` takes a command line argument with the name of a source code file:

```
prompt$  javac Driver.java
prompt$  java Driver tests/PrintTest.y
```
After you do this, you should see output which indicates your source code (`PrintTest.y`) was free from syntax errors.

Now, you need to understand how the Parser arrived at that conclusion.  Your first job is to understand how the `Parser.java` code implements the above grammar.  Notice that `Driver.java` calls the parser, starting with the `Program` production rule.  
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
```
This code "consumes" tokens as we parse them and moves us through the `Program --> 'program' NAME ':' Block 'end'` grammar rule.  We then call the `block` function once we reach the point where the sequence of tokens must contain a "block" of statements.  Continue tracing the code into the `block` method where we see that a block of code must be a series of statements enclosed in curly braces (ie, the production rule `Block --> {Statement}`. Continue tracing the small sample program above, reading the method comments carefully to understand how we continue to call more and more methods (which are the **production rules** of our grammar) until we come to the **terminals** which cannot be expanded any more.  Remember that methods do not return/finish until their either 1) encounter an explicit `return` statement or finish all the statements in the method body.

Now, let's kick it up a notch and add a rule to our grammar for multiplication/division.

# Part 1: The Y Programming Language -- v 0.2 -- Adding Multiplication and Division

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
* `MultExpr` added (very similar to old `AddExpr`)

Steps to make our Parser support new Expr
1. Add an `MultExpr` class in the `Expr.java` file.  You can closely match the existing `AddExpr` class.
2. Add a `multExpr()` method to the parser.
3. Modify the `addExpr` method to make the left part a `MultExpr` instead of an `Atom` as it was in v.1.
4. Compile and test with the `PrintMult.y` test in the `tests` directory.

Question: **What is the purpose of the classes defined in `Stmt.java` and `Expr.java`?**
Answer: They are blobs of state that represent nodes in the parse tree. Each class has a set of state variables that represent that 
important children of that node, as defined in the language grammar.

Once you get the above steps finished, the programs `tests/PrintMult.y` can be parsed with no errors.  Don't proceed until you have `PrintMult.y` passing.

# Part 2: The Y Programming Language -- v. 0.3 -- Negations, Variables, and Assignments, OhMy!

#### Updated EBNF Grammar:
```
Program --> 'program' NAME ':' Block 'end'

Block --> {Statement}

Statement --> PrintStatement
              | AssignStatement

PrintStatement --> 'print' Expression
              
AssignStatement --> Name ':=' Expression

Expression --> AddExpr | StringExpr

StringExpr --> StringLiteral

AddExpr --> MultExpr [('+' | '-') AddExpr]

MultExpr --> NegExpr [('*' | '/' | '%') MultExpr]

NegExpr --> '-' NegExpr | Atom
                    
Atom --> IntegerLiteral
         | '(' Expression ')'
         | Name
```
A few things to notice about this version of the grammar:
* `Statement` now has two choices: `PrintStatement` or `AssignStatement`. An `AssignStatement` is required to begin with a `Name` token.
* `Name` has also been added as a option for `Atom`: this corresponds to using a variable in an expression.
* Another new element is `NegExpr` which allows us to negate a value or variable.
* We also have two new production rules for dealing with the `String` datatype.

## Mod
Add support for a mod operator. Notice that the updated grammar is:

`MultExpr --> NegExpr [('*' | '/' | '%') MultExpr]`

Take a look at the `multExpr` method in the parser.  This change should be very straightforward.

{Check It!|assessment}(code-output-compare-1708272676)

## Negation Expression
Another new element of our grammar is `NegExpr`, which now sits between `MultExpr`, which we added last time, and `Atom`. A `NegExpr` implements the unary negation operator. `NegExpr` can be chained, so it's possible to negate a negation. The following example is valid:

```
program NegationExample:
    print ---(2 + 2)   { Prints -4 } 
end
```

Implementing `NegExpr` required a few changes:
- Adding a `NegExpr` class to `Expr.java`.
- Adding a `negExpr` method to the parser. The method has two cases, one where the `NegExpr` begins with a minus symbol and one where it's simply an atom.  Remember that this goes back to the fact that the `NegExpr` production rule in our EBNF grammar has 2 possible expansions.

Take a moment and reflect on how the structure of these changes follows directly from the grammar.

Before you go any further, run the following test and verify your code now supports negation statements.

{Check It!|assessment}(code-output-compare-3332155969)

## Towards Variables

From the perspective of parsing, adding support for variables is pretty straightforward.  Remember that our lexer already determined whether or not the variable's identifier (ie name) is valid.  The parser just has to determined if the sequence where a variable is used is syntactically valid.  After the parser is finished, the compiler/interpretter is actually responsible for updating mappings in a **symbol table** which stores (name, value) pairs for each variable in the program.

To add support for variables:
1. Create an `AssignStmt` class in `Stmt.java` and a `VarAccess` class in `Expr.java`.
2. Modify the parser to recognize assignments and variable accesses.

## Adding Classes for Assignment Statements
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

## Modify Parser to Recognize Assignment Statements


1. First, add code to recognize assignment statements. Modify the `stmt` method to recognize each kind of statement. Currently, if the code recognizes a `PRINT` token, it knows that the statement is a print statement.  We now add the functionality that if a statement begins with a `NAME` token, it must be an assignment.

2. Next, add the `assignStmt` method which handles the production rule:
```
AssignStatement --> Name ':=' Expression
```
It should return an `AssignStmt` object you defined previously.

3. The next set of changes need to be made to the `atom` method, to recognize variables used in expressions. We previously checked if an atom was an integer literal or something nested inside parentheses.  Now we need to check for the existence of a `NAME` token which would indicate a variable being used.  This method can now return a `Expr.VarAccess` object made with the `NAME` token's `value` field.

**Compile break!**

Take another break to compile your program (`javac Driver.java`) and fix any errors that have shown up. Remember to always start with the first error produced by the compiler.

#### Y Programming Language v.0.4 -- While Loops
## While loops
The last feature we'll add to our language is the idea of conditional execution.  This also requires some rules for relational operators: `=, >, >=, <, <=, <>` (remember, these are Pascal operators, not Java).

```
```
Program --> 'program' NAME ':' Block 'end'

Block --> {Statement}

Statement --> PrintStatement
              | AssignStatement
	      | WhileLoop

WhileLoop --> 'while' CondExpr ':' Block 'end'

PrintStatement --> 'print' Expression
              
AssignStatement --> Name ':=' Expression

Expression --> AddExpr | StringExpr

StringExpr --> StringLiteral

CondExpr --> AddExpr [('=' | '<' | '<=' | '>' | '>=' | '<>') AddExpr]

AddExpr --> MultExpr [('+' | '-') AddExpr]

MultExpr --> NegExpr [('*' | '/' | '%') MultExpr]

NegExpr --> '-' NegExpr | Atom
                    
Atom --> IntegerLiteral
         | '(' Expression ')'
         | Name
```

```
Note that there's a new kind of expression, `CondExpr`.  Stated simply, this sort of expression would result in a true/false result (although, like C, our language above doesn't have booleans as an `Atom`).  To add support for `CondExpr` we need to do two things:
1. Add the `CondExpr` class to `Expr.java` (This object will be very similar to `AddExpr` or `MultExpr`.)
2. Add the `condExpr()` rule method to `Parser.java`.  This will be very similar to the `addExpr()` method but will use different operators.  See `Token.java` for the operator names.

Your parser should now be able to handle simple programs with condition expressions like:
```
program CondTest:
	print 9 < 4
end
```

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
5. We're now ready to add `whileLoop()` method to `Parser.java` to implement the `WhileLoop` production from our grammar.  Take a look at the `parse()` method which implements the `Program --> 'program' NAME ':' Block 'end'` production rule.  Our `whileLoop()` method will be similar, but will need to return a `CondBlock` instead of just a `Block`.

### Reflect
Take a minute to pat yourself on the back and reflect on what you've built.  You've implemented a good chunk of what you learned in CMS167, but you're now able to see exactly how we use foundational concepts from formal languages to understand programming languages better.  We have a really good understanding of why programming languages need to be **unambiguous** and how that relates to our grammar being **unambiguous**.  

Obviously, we'd need to add a few more things before we had a full fledged programming language.  Chief among them are subroutines/functions and if statements.  But after seeing while loops, you probably have a good idea what those if statements might look like (but consider, what does an `else` part of an if statement look like?)  Fun for a future day.

### Submission
At the beginning of `Parser.java` add four things:
1. The names of everyone who worked on the project
2. Your honor code statement
3. Your collaboration statement
4. An estimate of how many hours you worked on this project.

Then, zip up your `Parser.java`, `Expr.java`, and `Stmt.java` files. Submit you zip file to the assignment on Canvas for this project. 
