# The Y Programming Language -- Version 0.1

In this project, we'll be writing a parser given the rules of a grammar defining a real programming language.  In this case, the language is based Per Brinch Hansen's *On Pascal Compilers* which describes the TinyBasic language, and Bob Nystrom's Lox language described in the free book *Crafting Interpreters*.

As we discussed, the first step is taking a string of characters (the source code) and turning it into a series of symbols or **tokens**.  This process is called **lexical analysis** or just "lexing."  After we have a sequences of tokens, we have to parse those tokens to see if we have a syntactially correct program.  You'll be implementing parts of a parser.  By the end, we won't have a full-fledged language like you're used to programming in, but hopefully you'll see how you could extend this project to get there.

## Getting Started
Download the source code zip file and extract it.  You can compile and run this program any way you choose: the command line, an IDE like Eclipse or IntelliJ, or an online IDE like Mimir or Codio (if you have access to one).

For this project, you should compile the `Driver.java` file and this file kicks off the process.  If you look at the code, you'll see it first calls the `Lexer` class to lex the source code and turn it into a stream of tokens (the definition of which is in `Token.java`).  After that, it calls the `Parser` class to try to turn that sequence of tokens into a `Program`.

So let's take a look at the grammar that the code currently implements.

#### EBNF Grammar
This is the first version of the language grammar. It allows for
printing addition expressions using only integer values.

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

You can currently compile and run `Driver`.  In the source code directory is a directory named `tests`.  This `tests` directory has the `PrintTest.y` source code in it which is a syntactically correct program which can be parsed.  `Driver` takes a command line argument with the name of a source code file:

```
prompt$  javac Driver.java
prompt$  java Driver PrintTest.y
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

# The Y Programming Language -- v 0.2 Adding Multiplication and Division

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

