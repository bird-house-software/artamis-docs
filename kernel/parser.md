# Parser

```
Note: This is in alpha and is likely to change!
```

[Index](/)

Use the Lexer to read Tokens and return an Abstract Syntax Tree. 

## Examples

### Basics

A basic add or subtract expression.

```
num := /[0-9]+/
op := + -

sum ::= num op:+ sum { ADD $1 $3 }
      | num op:- sum { SUB $1 $3 }
      | num

num ::= num: { INTEGER }
```

### Required Expressions

For when an expression is required once parsed. in the example below the `sum!` expression is required or an automated Runtime Error will be raised instead.

```
sum ::= num op:+ sum! { ADD $1 $3 }
      | num op:- sum! { SUB $1 $3 }
      | num
```

This gives the following result if the `sum` expression is missing

```
@> 2+3-4+
Error: (line:# col:#) 2+3-4+
                            ^^ expected value or expression.
```

### Custom Error Messages

For a custom Error message you can follow the lambda call with one or more `!!` error expressions wich start with the double exclaimition `!!` then `^#` where the number represents the expression node that failed for this error message to appear. the `^#` part can be ommited if the error message should apply to all  

```
sum ::= num op:+ sum! { ADD $1 $3 } !! ^3 Expected Operand!
      | num op:- sum! { SUB $1 $3 } !! Expected Operand!
      | num
```

This gives the following result if the `sum` expression is missing

```
@> 2+3-4+
Error: (line:# col:#) 2+3-4+
                            ^^ Expected Operand!
```

## Parser Kernel

### Math Parser

A simple math parser that can recognise basic math equations `(+-*/)` below is the ARTAMIS code to build it.

```
// Tokens
float := /[0-9]+\.[0-9]+/
int := /[0-9]+/
op := ( ) + - * /

// Syntax
arith ::= term op:+ arith! { ADD $1 $3 } !! Expected Operand!
        | term op:- arith! { SUB $1 $3 } !! Expected Operand!
        | term

term  ::= factor op:* term! { MUL $1 $3 } !! Expected Operand!
        | factor op:/ term! { DIV $1 $3 } !! Expected Operand!
        | factor

factor ::= op:( arith! op:)! { $2 EVAL } !! ^2 Expected Arith Expression!
                                         !! ^3 Expected Closing Bracket /~)~/
         | value

value ::= float: { FLOAT } 
        | int:   { INTEGER }

```