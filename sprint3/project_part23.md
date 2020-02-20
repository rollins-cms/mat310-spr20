# The Y Programming Language -- Version 0.1

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
program Test:
    print 1 + 2 + 3
end
```

# The Y Programming Language -- v 0.2 Adding Multiplication and Division


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

Steps to make our Parser & Interpretter support new Expr
1. Add an Expr.MultExpr class
2. Add a multExpr() method to the parser
3. Modify the addExpr method to make the left part a MultExpr:
4. Add multiplication to the Interpreter
5. Modify evalAddExpr in the Interpreter
6. Compile and test

