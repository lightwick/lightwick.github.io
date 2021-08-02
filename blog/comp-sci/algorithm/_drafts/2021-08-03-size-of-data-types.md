---
layout: post
---

> <h4>ISO/IEC 9899:TC3<br>6.5.3.4 The sizeof operator</h4>
> 
> 1. ...
> 2. The sizeof operator yields the size (in bytes) of its operand, which may be an expression or the parenthesized name of a type. [...]
> 3. When applied to an operand that has type char, unsigned char, or signed char, (or a qualiﬁed version thereof) the result is 1. [...]

A char is always 1 byte, but a byte isn't always 8 bits; it mostly is though, by convention.
