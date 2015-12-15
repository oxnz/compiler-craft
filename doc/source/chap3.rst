Parser
======

The parser consumes the token stream produced by lexer and produces a syntax
tree according the grammer. There are several algorithms used in that process,
such as recursive decent, LALR(1), LR(k).


Performance
-----------

As said above, parsers traditionally use lexers to convert the character stream
into a token stream. But for parsers which are very simple or not use backtracking
a lot, lexer stage is just an extra layer of complexity that increse the
per-character overhead. in these cases, the parser could just use the raw character
stream directly instead of a token stream.

There are several inefficiencies in the typical implementation of a parser. One of
them is copying string date to the heap. This involves allocating many blocks of
varying sizes, from bytes to megabytes, and requires us to copy all strings from the
original stream to the heap. Avoiding the copy operation would eliminate both
sources of overhead. Using a technique known as in-place (or in situ) parsing, the
parser can use data from the stream directly.

For parsers operate directly on a character stream, memory mapped file I/O could be
used to improve performance. If there's some transformations need to be done against
the character stream, there's one constraints: the transformation should not increase
the length of the string. Supporting null-termination and text transformation requires
a special memory mapping mode known as *copy-on-write* to avoid modifying the file on
disk. Using memory mapped file I/O with in-place parsing has the following benefits:

* The kernel can usually map cache pages directly into the process address space,
  thus eliminating a memor copy that would have happened with standard file I/O.
* If the file is not already in the cache, the kernel can prefetch sections of the
  file from disk, effectively making I/O and parsing parallel.
* Since only modified pages need to allocate physical memory, memory comsumption can
  be greatly decreased on documents with large text sections.

Eliminating string copies is not the only thing we can do to optimize parser
performance. When comparing parser performance, a useful metric is the average
number of processor cycles spent for each character. While the varies among documents
and processor architectures, it is reasonably stable for documents of similar structure.
Thus it makes sense to optimize for this metric, and an obvious place to start is in
the operations performed for each character.

The most important operation is detecting character set memebership: given a character
from the input stream, does it belong to a certain charcaters?

A useful approach is to create a boolean flag table, where for each character value a
true/false value is stored depending on whether the character belongs to the set or not.
Depending on the encoding, different table data structures and sizes make sense as
followings:

* For encodings where each character occupies no more than 8 bits, a table of size 256
  is sufficient.
* For UTF-8, we would like to use a byte-indexed table to avoid code point decoding;
  this works only if all characters with code points (i.e. numeric values) above 127
  belong to the set or no characters with code points above 127 belong to the set. If
  either of these are true, then a table of size 256 is sufficient. The first 128 entries
  of the table are filled with true or false (depending on whether the character is in the
  target set) and the last 128 entries of the table all share the same value. Because of
  the way UTF-8 encodes data, all code points above 127 will be represented as sequenes of
  bytes with values above 127. Furthermore, the first character of the sequence will also
  be above 127.
* For UTF-16 or UTF-32, tables of large size are usually impratical. Given the same
  constraints as the one for optimized UTF-8, we can leave the table to be 128 or 256
  entries large, and add an additional comparison to deal with values outside the range.


If the tested range includes all characters in a certain interval, it might make sense
to use a comparison instead of a table lookup. With careful use of unsigned arithmetics
just one comparison is needed. For example, a test for a character being a digit:

.. code:: c

    bool isdigit(char ch) { return (ch >= '0' && ch <= '9'); }

can be rewritten using just one comparison:

.. code:: c

    bool isdigit(char ch) { return (unsigned)(ch - '0') < 10; }

Although a recursive-descent parser is quick, however, it would be much quick if the
recursive could be transformed into a loop to improve performance by avoiding expensive
function calls.
