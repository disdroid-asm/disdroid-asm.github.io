+++
date = '2026-04-07T11:28:34+01:00'
draft = true
title = 'Functional programming in Sinclair BASIC for FN and Profit - Part two'
tags = ['zx-spectrum','sinclair','basic','programming','fn and profit']
categories = ['ZX Spectrum','BASIC']
+++



Functional style programming is entirely possible in Sinclair BASIC using DEF FN because
functions can be recursive. We can also roll custom expressions on the fly using VAL and VAL$.
These are perfect for string parsing. The only caveat is that we have to watch our subscripts,
especially if we want to be prepared for empty strings.
For this reason we're going to define some helpers. Because we're using plain text, BASIC tokens
are encoded using CHR$.


FN e$(a$,b$,c$)
This one takes a string parameter A$, an expression B$, and the name of a function in C$ without 
the $ sign. If VAL b$ returns non-zero we apply the function in C$ to A$ and return the result, 
otherwise we return A$.

DEF FN e$(a$,b$,c$)=VAL$ (("a$" AND VAL (b$)=0)+(CHR$ 168+c$+"$(a$)" AND VAL (b$)<>0))


FN f$(a$,c$)
This one applies C$ if LEN A$ is non-zero.

DEF FN f$(a$,c$)=FN e$(a$,CHR$ 177+"(a$)",c$)


FN s$(a$)
Trims leading whitespace from A$. A$ must not be empty.

DEF FN s$(a$)=VAL$ ((CHR$ 168+"f$(a$(2"+CHR$ 204+"),""s"")" AND a$(1)=" ")+("a$" AND a$(1)<>" "))


FN t$(a$)
Trims trailing whitespace from the non-empty string in A$.

DEF FN t$(a$)=VAL$ ((CHR$ 168+"f$(a$("+CHR$ 204+CHR$ 177+"a$-1),""t"")" AND a$(LEN a$)=" ")+("a$" AND a$(LEN a$)<>" "))


FN x$(a$)
Extracts the first token from A$ up to the first whitespace. There must be at least one ws in A$
so it's usually necessary to append one: FN x$(a$+" ")

DEF FN x$(a$)=VAL$ (("a$(1)+"+CHR$ 168+"f$(a$(2"+CHR$ 204+"),""x"")" AND a$(2)<>" ")+("a$(1)" AND a$(2)=" "))



To use these on potentially empty strings we create three wrapper functions:

DEF FN u$(a$)=FN f$(a$,"s")
DEF FN v$(a$)=FN f$(a$,"t")
DEF FN w$(a$)=FN v$(FN u$(a$))

And another wrapper for the token extraction to append a trailing ws, after trim.

DEF FN y$(a$)=FN x$(FN w$(a$)+" ")

To use it on a potentially empty string:

DEF FN z$(a$)=FN f$(FN w$(a$),"y")