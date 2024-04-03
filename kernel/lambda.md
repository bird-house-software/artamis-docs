# Lambda


## Parser Kernel

### Math Parser

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