# Implementation of a Recursive Descent Parser

## Example Grammar

Consider the following grammar for arithmetic statements:

```
E -> T E'
E' -> + T E' | empty
T -> F T'
T' -> * F T' | empty
F -> (E) | digit
digit -> 0 | 1 | 2 | ... | 9
```
empty is the empty string

## Pseudocode Implementation
```
/*** Global variable for the current input token ***/
currentToken;

// getNextToken() calls the lexer and returns the next input token
currentToken = getNextToken();

/*** Function to test if the current token in the input is the same
     as the parameter token, t ***/
check(t) {

  // If there is a match, advance to the next token
  if (currentToken == t) {
    currentToken = getNextToken();
    return true;
  }
  
  // If not, return false
  else {
    return false;
  }
}


/*** Function to require a match the given token or error ***/
require(t) {
  if (!match(t)) {
    error(t)
  }
}


/*** Function to print a message and quit when an error occurs ***/
error(t) {
    print "ERROR: Expected " + t + " Found " + currentToken "."
    print "I give up :("
    quit()
}


/*** Write one function for each nonterminal ***/
/*** Each rule expands the leftmost non-terminal in the rule first and then
works to the right ***/

E() {
  // Try to expand T
  T()
   
  // Try to expand E'
  E'()
}


E'() {

  // If the current character of the input is +, match it, then
  // try to expand the other nonterminals
  if (match(<PLUS>)) {
    T()
    E'()
  }
   
  // If the current character is not '+', try the empty string
  else {
    return;
  }
}


T() {
  F()
  T'()
}


T'() {
  // If the current character of the input is *, match it, then
  // try to expand the other nonterminals
  if (match(<TIMES>)) {
    F()
    T'()
  }
   
  // If the current character is not '*', try the empty string
  else {
    return;
  }
}


F() {
  // Case for an expression in parentheses
  if (match(<LEFT_PAREN>) {
    E()
    require(<RIGHT_PAREN>)
  }
   
  // Else, the only other choice is to turn the factor into a digit
  else {
    digit()
  }
}


digit() {
  for (i = 0 to 9) {
  
    // Try to match a number token with value i
    if (match(<NUMBER, i>)) {
      
      // Return on matching any number value
      return
    }
  }
  
  // If we didn't match any numbers something is wrong
  error(“digit”)
}


main() {

  // Begin by expanding the start symbol
  E()
  
  // If everything is matched, we're done
  if (currentToken == <END_OF_INPUT>) {
    print "PARSED."
  }
}
```
