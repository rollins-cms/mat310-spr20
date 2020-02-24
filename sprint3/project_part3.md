In this lab, you'll be adding support for more language features to the Y Programming Language we began in class last week.  In this lab, we'll study or code the following features:

1. Support for variables
2. Negation statements (ie the unary negation operator) For example: `-6` or `-x`
3. Assignment statements.  For example `x=4`
4. The modulo operator
5. String values

Begin by opening the `Parser`, `Interpreter`, `Expr`, and `Stmt` source files.

Now let's take a look at the updated EBNF grammar.

## Updated EBNF Grammar

Here is the EBNF grammar for the language including assignments.

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

Notice that `Statement` now has two choices: `PrintStatement` and `AssignStatement`. An `AssignStatement` is required to begin with a `Name` token.

`Name` has also been added as a option for `Atom`: this corresponds to using a variable in an expression.

Another new element is `NegExpr` which allows us to negate a value or variable.

We also have two new production rules for dealing with the `String` datatype.

## Negation Expression

The final new element of our grammar is `NegExpr`, which now sits between `MultExpr`, which we added last time, and `Atom`. A `NegExpr` implements the
unary negation operator. `NegExpr` can be chained, so it's possible to negate a negation. The following example is valid:

```
program NegationExample:
    print ---(2 + 2)   { Prints -4 } 
end
```

Implementing `NegExpr` required a few changes. Look at each of the files:

- Adding a `NegExpr` class to `Expr.java`.

- Adding a `negExpr` method to the parser. The method has two cases, one where the `NegExpr` begins with a minus symbol and one where it's simply an atom.  Remember that this goes back to the fact that the `NegExpr` production rule in our EBNF grammar has 2 possible expansions.

- Adding an `evalNegExpr` method to the interpreter, which also has two cases.

Take a moment and reflect on how the structure of these changes follows directly from the grammar.

Before you go any further, run the following test and verify your code now supports negation statements.

{Check It!|assessment}(code-output-compare-3332155969)

## Towards Variables

The key to supporting variables is a **symbol table**.  A symbol tables is a data structure created and maintained by the compiler/interpreter which stores a mapping between a variable's name and its value:

- Assigning to a variable is done by updating the value associated with its name in the symbol table.

- Getting the value of a variable is done by looking up the value for a given name.

- Creating a new variable is done by simply adding a new `name->value` mapping to the table.

Your symbol table will be a **Java `HashMap`** object.

To add support for variables, we will do the following steps individually over the next few lessons: 

1. Create an `AssignStmt` class in `Stmt.java` and a `VarAccess` class in `Expr.java`.

2. Modify the parser to recognize assignments and variable accesses.

3. Add the symbol table to `Interpreter.java`.

4. Update the interpreter to process assignments and look up variable values.


## Adding Classes for Assignment Statements
Question: **What is the purpose of the classes defined in `Stmt.java` and `Expr.java`?**

Answer: They are blobs of state that represent nodes in the parse tree. Each class has a set of state variables that represent that 
important children of that node, as defined in the language grammar.

An assignment statement has a variable name on the left hand side and an expression on the right-hand side. Add the following to `Stmt.java`:

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
There are two places in the parser that need to change. 

First, adding code to recognize assignment statements. Modify the `stmt` method to recognize each kind of statement. If a statement begins with a `NAME` token, it must be an assignment:

```
public Stmt stmt() {
    if (check(Tokens.PRINT)) {
        return printStmt();
    } else if (check(Tokens.NAME)) {
        return assignStmt();
    } else {
        Driver.error(currentToken().type + " cannot begin a statement.",
                     currentToken().line);
        return null;
    }
}
```

Next, add the `assignStmt` method:

```
//*** Name := Expr ***//
public Stmt.AssignStmt assignStmt() {
    String name = (String) currentToken().value;
    consume(Tokens.NAME);
    consume(Tokens.ASSIGN);
    Expr e = expr();
    return new Stmt.AssignStmt(name, e);
}
```

The second set of changes need to be made to the `atom` method, to recognize variables used in expressions:

```
//*** An atom is a single value ***//
public Expr atom() {

    // Integer literal
    if (check(Tokens.INTEGER)) {
        return integer();
    }

    // Nested expression
    else if (check(Tokens.LEFT_PAREN)) {
        consume(Tokens.LEFT_PAREN);
        Expr e = expr();
        consume(Tokens.RIGHT_PAREN);
        return e;
    }
		
    // Var name
    else if (check(Tokens.NAME)) {
        String name = (String) currentToken().value;
        consume(Tokens.NAME);
        return new Expr.VarAccess(name);
    }

    // Anything else is an error
    else {
        Driver.error("Expected atom, found " + currentToken().type, currentToken().line);
        return null;
    }
}
```

**Compile break!**

Take another break to compile your program (`javac Driver.java`) and fix any errors that have shown up. Remember to always start with the first error produced by the compiler.

## Modify the Interpretter
The symbol table will be a `HashMap` named `environment`, which is the traditional name for this sort of thing. Add it as as class member (eg. instance variable) of `Interpreter.java`. Also add a constructor that initializes an empty `HashMap` (note that the `Interpreter` class has been using the default constructor up until now).

```
// Environment is the current local scope
HashMap<String, Object> environment;
	
	
public Interpreter() {
    this.environment = new HashMap<String, Object>();
}
```

If it isn't already there, you must add a brief incantation to get `HashMap`.

```
import java.util.HashMap;
```

The remaining changes are mirrors of their counterparts in the parser. First, update the `evalStmt` method to detect `AssignStmt`:

```
//*** Evaluate a single statement ***//
public void evalStmt(Stmt stmt) {
    if (stmt instanceof Stmt.PrintStmt) {
        evalPrintStmt((Stmt.PrintStmt) stmt);
    } else if (stmt instanceof Stmt.AssignStmt) {
        evalAssignStmt((Stmt.AssignStmt) stmt);
    }

    // Add cases for other kinds of statements
}
```

Then the `evalAssignStmt` method:

```
//*** Assign statement: update mapping in environment table ***//
public void evalAssignStmt(Stmt.AssignStmt stmt) {
    String name = stmt.name;
    Object value = evalExpr(stmt.expr);
    this.environment.put(name, value);
}
```

The method is straighforward: it evaluates the right-hand side expression, then inserts the `name-->value` mapping into `environment`.
Note that this will create a mapping for `name` if it doesn't already exist, so we're able to declare new variables by simply assigning to them, like in Python.

The second set of changes are to incorporate variables into expressions:

```
//*** Evaluate a single atom value ***//
public Object evalAtom(Expr e) {

    // Integer
    if (e instanceof Expr.IntegerValue) {
        return evalInteger((Expr.IntegerValue) e);
    }
		
    // Var name
    else if (e instanceof Expr.VarAccess) {
        String name = ((Expr.VarAccess) e).name;

        if (!this.environment.containsKey(name)) {
            Driver.error("Unknown variable name " + name + ".");
        }

        return this.environment.get(name);
    }

    // Nested expression
    else {
        return evalExpr(e);
    }
}
```

Now compile everything one final time.

## Tests Here

## Mod
Add support for a mod operator. Let the updated grammar be:

```
MultExpr --> NegExpr [('*' | '/' | '%') MultExpr]
```

Take a look at the `multExpr` method in the parser, then modify `evalMultExpr` in the interpreter to implement the mod operator.

{Check It!|assessment}(code-output-compare-1708272676)

## Support for Strings
Now suppose you want to add support for string expressions. Let the grammar change to:

```
Expression --> AddExpr | StringExpr

StringExpr --> StringLiteral
```

A `StringLiteral` is the `STRING` token returned by the Lexer. Recall that it has a `value`, which is the text of the string.

To implement this feature:

- Add a new `StringExpr` class with a `String` value to `Expr.java`.

- Add code to `expr` in the parser to distinguish between `AddExpr` and `StringExpr` (hint: you can check the type of the next token with the `check` method).  Remember that you need to consume the `STRING` token at some point to move past it in your stream of Tokens.

{Check It!|assessment}(code-output-compare-4256923871)


## While loops
One of the last features we need in our language is the idea of conditional execution.  This also requires the idea of relational operators.

```
```
Program --> 'program' NAME ':' Block 'end'

WhileLoop --> 'while' AddExpr ('<' | '<=' | '>' | '>=' | '==' | '<>') AddExpr ':' Block 'end'

Block --> {Statement}

Statement --> PrintStatement
              | AssignStatement
	      | WhileLoop

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

```

1. Add CondExpr to Expr (very similar to AddExpr)
2. Add condExpr() rule method to parser

test w/ simple print x > 4 program

3. Add CondBlock to Stmt (similar to Block but stores condition with it)
4. Add whileLoop production rule method.









