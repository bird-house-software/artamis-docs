# Lexer

```
Note: This is in alpha and is likely to change!
```

[Index](/)

For matching tokens.

## Examples

### Keyword Token

For tokens that are keywords or symbols

```
kw := loop while if else
op := + - * / ( )
cmp := <= >= < >
```

### Regex Token

For tokens that match a regular expression

```
float := /[0-9]+\.[0-9]+/
int := /[0-9]+/
str := /"(!")+"/
ident := /[a-zA-Z_]+/
line:comment := /\/\/*+?(?:\n)/
```

## Parser Kernel

### Math Parser

```
// Tokens
float := /[0-9]+\.[0-9]+/
int := /[0-9]+/
op := ( ) + - * /
```