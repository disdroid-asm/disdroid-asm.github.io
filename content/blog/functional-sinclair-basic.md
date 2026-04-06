+++
date = '2026-04-06T09:18:08+01:00'
title = 'Functional programming in Sinclair BASIC for FN and Profit - Part one'
tags = ['zx-spectrum','sinclair','basic','programming','fn and profit']
categories = ['ZX Spectrum','BASIC']
+++

This article explains how to create dynamic numeric functions in Sinclair BASIC, using recursion instead of `FOR..NEXT` loops.  
The next part will deal with string parsing and tokenisation.
Firstly however, we need to be able to perform reduction on our data.

## Creating funtional algorithms in Sinclair BASiC

Combining VAL with inline conditionals via AND and OR allows us to construct and evaluate complex expressions 
on the fly using strings. These can be encapsulated and re-used via `DEF FN`.  
This enables us, for example, to avoid subscript errors by forming the strings conditionally. 
We can't evaluate `a(n)` if `n` is equal to zero, therefore we make use of `AND` inline, like so:  
`VAL ("0" + ("+a(n)" AND n>0) )`  
If the `AND` part evaluates to false then `VAL("0")` gets evaluated,
otherwise it's `VAL("0+a(n)")`. Therefore we can apply our formula even if `n` is zero, without a subscript error.    
So we can build and evaluate complex conditionals using `VAL/VAL$` string expressions combined with `AND/OR` inline, and structure 
these using `FN`.

## How to define recursive functions

Here's a function that evaluates the factorial of it's parameter, using recursion:

```
DEF FN a(n) = VAL (("n+FN a(n-1)" AND n>1) + ("n" AND n<=1))
```
So if the parameter `n` is less than or equal to one, the function returns `n`. Otherwise, it returns `n` plus the result 
of the recursion applied to `n` minus one.  

The FN inside the quotes is a keyword token, so if you're not using the tokenised editor you'll need to insert the character code (168 in this case):

```
DEF FN a(n) = VAL (("n+"+CHR$(168)+"a(n-1)" AND n>1) + ("n" AND n<=1))
```

## How to buld an array filter

In modern programming, collections can have a `filter` function which takes an expression as a parameter and returns a collection 
by applying the expression to each element and removing elements where the expression evaluates to false.  

For example, `c.filter[(i)->i>2]` returns a collection containing all the elements of `c` that are greater than 2.  

A function in Sinclair BASIC can't return a collection, so what we'll do instead is return the index of the last matching element,
or zero if none are found, and our routine can loop until we reach the end of the array. 
So it's a `find` method rather than a filter (`findLast` to be precise).  

The functions take a parameter representing the index to begin the search, and in this case they work downhill through the array until the 
beginning of the array is reached. We could work uphill starting at 1, however we would need an extra parameter representing the 
length of the array.  

This program defines `FN b(n)` where `n` is the starting index to a pre-defined one dimensional array called `a()`.  
The function returns the index of the last even number in the array counting downwards from n.  
It's easiest to walk backwards and make sure we stop at n=1 to avoid a subscript error.  

Lines 10-30 print all the even numbers from an array initialised with values from -50 to 49, starting at the end of the array.

```
  10 DIM a(100): LET j=-50: FOR i=1 TO 100: LET a(i)=j: LET j=j+1: NEXT i
  20 LET x=100
  30 IF x>0 THEN LET y=FN b(x): IF y>0 THEN PRINT a(y): LET x=y-1: GO TO 30
9000 DEF FN b(n)=VAL ("0"+("+n" AND a(n)/2=INT (a(n)/2))+("+"+CHR$ (168)+"b(n-1)" AND n>1 AND a(n)/2<>INT (a(n)/2)))
```
## A general purpose "choose" function

Here's a general purpose `choose` function that we can use as the basis of our array filters.  

This statement defines `FN d(a$,b$,c$)` where each parameter represents an expression. If `a$` evaluates to non-zero (true) then `b$` is
evaluated and returned, otherwise it evaluates `c$` and returns the result.  

```
DEF FN d(a$,b$,c$)=VAL ((b$ AND VAL (a$))+(c$ AND NOT VAL (a$)))
```

The caveat is that `VAL(a$)` gets evaluated twice. We could replace it with a numeric parameter and pass in `VAL(a$)` instead of
`a$` as the first parameter.  

## Leveraging our choose function to perform find and filter over a collection

Now we can create our filter over `a(n)` in terms of `FN d`.  
We use three strings expressions: the condition and the trua and false cases.  
The test for even numbers is `"a(n)/2=INT (a(n)/2)"`. The expression for the `true` choice is `"n"`, and for 
`false` is `"0"+("+FN e(n-1)" AND n>1)`.  

The expression for the `false` choice generates a recursion to evaluate the preceding array element if the index is greater than
one, otherwise it evaluates to zero.  

We'll need to use `STR$` to convert `n` into it's numerical form, becase `FN e` doesn't have access to the parameters of the 
calling function whilst evaluating the string epressions.  

```
DEF FN e(n)=FN d( "a("+STR$ (n)+")/2=INT (a("+STR$ (n)+")/2)", STR$ (n), "0"+("+FN e("+STR$ (n-1)+")" AND n>1) )
```

This program filters the even numbers from an array of random integers.
```
  10 DIM a(100): FOR i=1 TO 100: LET a(i)=INT (RND*1000)-500: NEXT i
  20 LET x=100
  30 IF x>0 THEN LET y=FN e(x): IF y>0 THEN PRINT y,a(y): LET x=y-1: GO TO 30
9000 DEF FN e(n)=FN d( "a("+STR$ (n)+")/2="+CHR$ (186)+"(a("+STR$ (n)+")/2)", STR$ (n), "0"+("+"+CHR$ (168)+"e("+STR$ (n-1)+")" AND n>1) )
9010 DEF FN d(a$,b$,c$)=VAL ((b$ AND VAL (a$))+(c$ AND NOT VAL (a$)))
```
The advantages of using string expressions in this way is that they can be created programatically, loaded from storage or from user 
input.

Incidentally, the problem I've had loading and saving arrays (`LOAD/SAVE "..." DATA a$()`) is that there's no way to determing the 
dimenstions of the arrays.  This makes it necessary to save another array containing the dimensions of the arrays containing the data!  

__Thank's for reading - make sure to check back for part two which will deal with string conversion, parsing, and tokenisation...__
