# Kernel

```
Note: This is in alpha and is likely to change!
```

Back to [Index](/)

---

This is for building a simple language kernel using raw ARTAMIS code. It should explain how to build a simplified language kernel - some examples being a [Math Parser](#math-parser), [Other Parser](#), etc.

### Components
- [Token](./token)
- [Lexer](./lexer)
- [Parser](./parser)

---

### Math Parser

A simple math parser that can recognise basic math equations `(+-*/)` below is the ARTAMIS code to build it.

```
// Tokens
float := /[0-9]+\.[0-9]+/
int := /[0-9]+/
op := ( ) + - * /

ident := /[a-zA-Z_]+/
assign := := =

// Syntax
EXPR ||= math

math ::= assign
       | arith

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
        | ident: { GET_IDENT }

assign ::= ident: assign: arith! { SET_IDENT } !! ^3 Expected Arith Expression!

// Lambda
ADD $a $b => { a + b }
SUB $a $b => { a - b }
MUL => { $1 * $2 }
DIV => { $1 / $2 }

FLOAT =>   { $$.eval_as<f32> }
INTEGER => { $$.eval_as<i32> }

GET_IDENT => { $$.get_ident }
SET_IDENT => { $$.set_ident }
```

Here is some example interpreter input and output

```
@> 5+4*3-2
15
--
@> a=10/2
5
--
@> b=5-2
3
--
@> a+4*b-2
15
--
@> 5+4*(3-2)
9
--
```
