# Lambda

```
Note: This is in alpha and is likely to change!
```

Back to [Index](/)

---

The Runtime environment will have many static functions that manipulate the state of the program. For now we call those Lambdas. They are small code bites that usiually perform a single operation. This could be to `ADD` two values together or to `GET_IDENT` to retrieve a variable. Lambdas are how you make the Syntax perform work.

## Examples

### Arithmatic

Example of simple Arithmatic lambdas for simple math Binary Operations.

```
ADD $a $b => { a + b }
SUB $a $b => { a - b }
MUL => { $1 * $2 }
DIV => { $1 / $2 }
```
### Raw Values

Example of simple value extraction for converting a token to a value type.

```
FLOAT =>   { $$.eval_as<f32> }
INTEGER => { $$.eval_as<i32> }
STRING =>  { $$.eval_as<String> }
```
### Variables

Example of storing or retrieving a value for a simple variable.

```
GET_IDENT => { $$.get_ident }
SET_IDENT => { $$.set_ident }
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
arith ::= term op:+ arith! { ADD $1 $3 } !! ^3 Expected Operand!
        | term op:- arith! { SUB $1 $3 } !! ^3 Expected Operand!
        | term

term  ::= factor op:* term! { MUL $1 $3 } !! ^3 Expected Operand!
        | factor op:/ term! { DIV $1 $3 } !! ^3 Expected Operand!
        | factor

factor ::= op:( arith! op:)! { $2 EVAL } !! ^2 Expected Arith Expression!
                                         !! ^3 Expected Closing Bracket /~)~/
         | value

value ::= float: { FLOAT }
        | int:   { INTEGER }

// Lambda
ADD $a $b => { a + b }
SUB $a $b => { a - b }
MUL => { $1 * $2 }
DIV => { $1 / $2 }
FLOAT =>   { $$.eval_as<f32> }
INTEGER => { $$.eval_as<i32> }
```