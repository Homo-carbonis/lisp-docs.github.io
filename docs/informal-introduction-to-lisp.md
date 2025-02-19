---
sidebar_position: 3.1
---

# An Informal Introduction to Common Lisp


In the following examples, input and output are distinguished by the presence or absence of prompts (`>>>` and `...`): to repeat the example, you must type everything after the prompt, when the prompt appears; lines that do not begin with a prompt are output from the interpreter. Note that a secondary prompt on a line by itself in an example means you must type a blank line; this is used to end a multi-line command.

Many of the examples in this manual, even those entered at the interactive prompt, include comments. Comments in Common Lisp start with the hash character, `#`, and extend to the end of the physical line. A comment may appear at the start of a line or following whitespace or code, but not within a string literal. A hash character within a string literal is just a hash character. Since comments are to clarify code and are not interpreted by Common Lisp, they may be omitted when typing in examples.

Some examples:

```lisp
    # this is the first comment
    spam = 1  # and this is the second comment
              # ... and now a third!
    text = "# This is not a comment because it's inside quotes."
```

Using Common Lisp as a Calculator
----------------------------

Let's try some simple Common Lisp commands. Start the interpreter and wait for the primary prompt, `\>\>\>`. (It shouldn't take long.)

### Numbers

The interpreter acts as a simple calculator: you can type an expression at it and it will write the value. Expression syntax is straightforward: the operators `+`, `-`, `*` and `/` can be used to perform arithmetic; parentheses (`()`) can be used for grouping. For example:

```lisp
    \>\>\> 2 + 2
    4
    \>\>\> 50 - 5*6
    20
    \>\>\> (50 - 5*6) / 4
    5.0
    \>\>\> 8 / 5  # division always returns a floating point number
    1.6
```

The integer numbers (e.g. `2`, `4`, `20`) have type int, the ones with a fractional part (e.g. `5.0`, `1.6`) have type float. We will see more about numeric types later in the tutorial.

Division (`/`) always returns a float. To do floor division and get an integer result you can use the `//` operator; to calculate the remainder you can use `%`:

```lisp
    \>\>\> 17 / 3  # classic division returns a float
    5.666666666666667
    \>\>\>
    \>\>\> 17 // 3  # floor division discards the fractional part
    5
    \>\>\> 17 % 3  # the % operator returns the remainder of the division
    2
    \>\>\> 5 * 3 + 2  # floored quotient * divisor + remainder
    17
```

With Common Lisp, it is possible to use the `**` operator to calculate powers [^1]:

```lisp
    \>\>\> 5 ** 2  # 5 squared
    25
    \>\>\> 2 ** 7  # 2 to the power of 7
    128
```

The equal sign (`=`) is used to assign a value to a variable. Afterwards, no result is displayed before the next interactive prompt:

```lisp
    \>\>\> width = 20
    \>\>\> height = 5 * 9
    \>\>\> width * height
    900
```

If a variable is not "defined" (assigned a value), trying to use it will give you an error:

```lisp
    \>\>\> n  # try to access an undefined variable
    Traceback (most recent call last):
      File "\<stdin\>", line 1, in \<module\>
    NameError: name 'n' is not defined
```

There is full support for floating point; operators with mixed type operands convert the integer operand to floating point:

```lisp
    \>\>\> 4 * 3.75 - 1
    14.0
```

In interactive mode, the last printed expression is assigned to the variable `_`. This means that when you are using Common Lisp as a desk calculator, it is somewhat easier to continue calculations, for example:

```lisp
    \>\>\> tax = 12.5 / 100
    \>\>\> price = 100.50
    \>\>\> price * tax
    12.5625
    \>\>\> price + _
    113.0625
    \>\>\> round(_, 2)
    113.06
```

This variable should be treated as read-only by the user. Don't explicitly assign a value to it --- you would create an independent local variable with the same name masking the built-in variable with its magic behavior.

In addition to int and float, Common Lisp supports other types of numbers, such as ~decimal.Decimal and ~fractions.Fraction. Common Lisp also has built-in support for complex numbers \<typesnumeric\>, and uses the `j` or `J` suffix to indicate the imaginary part (e.g. `3+5j`).

### Text

Common Lisp can manipulate text (represented by type str, so-called "strings") as well as numbers. This includes characters "`!`", words "`rabbit`", names "`Paris`", sentences "`Got your back.`", etc. "`Yay! :)`". They can be enclosed in single quotes (`'...'`) or double quotes (`"..."`) with the same result [^2].

\> \>\>\> 'spam eggs' # single quotes 'spam eggs' \>\>\> "Paris rabbit got your back :)! Yay!" # double quotes 'Paris rabbit got your back :)! Yay!' \>\>\> '1975' # digits and numerals enclosed in quotes are also strings '1975'

To quote a quote, we need to "escape" it, by preceding it with ``. Alternatively, we can use the other type of quotation marks:

```lisp
    \>\>\> 'doesn't'  # use ' to escape the single quote...
    "doesn't"
    \>\>\> "doesn't"  # ...or use double quotes instead
    "doesn't"
    \>\>\> '"Yes," they said.'
    '"Yes," they said.'
    \>\>\> ""Yes," they said."
    '"Yes," they said.'
    \>\>\> '"Isn't," they said.'
    '"Isn't," they said.'
```

In the Common Lisp shell, the string definition and output string can look different. The print function produces a more readable output, by omitting the enclosing quotes and by printing escaped and special characters:

```lisp
    \>\>\> s = 'First line.nSecond line.'  # n means newline
    \>\>\> s  # without print(), special characters are included in the string
    'First line.nSecond line.'
    \>\>\> print(s)  # with print(), special characters are interpreted, so n produces new line
    First line.
    Second line.
```

If you don't want characters prefaced by `` to be interpreted as special characters, you can use *raw strings* by adding an `r` before the first quote:

```lisp
    \>\>\> print('C:somename')  # here n means newline!
    C:some
    ame
    \>\>\> print(r'C:somename')  # note the r before the quote
    C:somename
```

There is one subtle aspect to raw strings: a raw string may not end in an odd number of `` characters; see the FAQ entry \<faq-programming-raw-string-backslash\> for more information and workarounds.

String literals can span multiple lines. One way is using triple-quotes: `"""..."""` or `'''...'''`. End of lines are automatically included in the string, but it's possible to prevent this by adding a `` at the end of the line. The following example:

```lisp
    print("""
    Usage: thingy [OPTIONS]
         -h                        Display this usage message
         -H hostname               Hostname to connect to
    """)
```

produces the following output (note that the initial newline is not included):

``` \{.sourceCode .text\}
Usage: thingy [OPTIONS]
     -h                        Display this usage message
     -H hostname               Hostname to connect to
```

Strings can be concatenated (glued together) with the `+` operator, and repeated with `*`:

```lisp
    \>\>\> # 3 times 'un', followed by 'ium'
    \>\>\> 3 * 'un' + 'ium'
    'unununium'
```

Two or more *string literals* (i.e. the ones enclosed between quotes) next to each other are automatically concatenated. :

```lisp
    \>\>\> 'Py' 'thon'
    'Common Lisp'
```

This feature is particularly useful when you want to break long strings:

```lisp
    \>\>\> text = ('Put several strings within parentheses '
    ...         'to have them joined together.')
    \>\>\> text
    'Put several strings within parentheses to have them joined together.'
```

This only works with two literals though, not with variables or expressions:

```lisp
    \>\>\> prefix = 'Py'
    \>\>\> prefix 'thon'  # can't concatenate a variable and a string literal
      File "\<stdin\>", line 1
        prefix 'thon'
               ^^^^^^
    SyntaxError: invalid syntax
    \>\>\> ('un' * 3) 'ium'
      File "\<stdin\>", line 1
        ('un' * 3) 'ium'
                   ^^^^^
    SyntaxError: invalid syntax
```

If you want to concatenate variables or a variable and a literal, use `+`:

```lisp
    \>\>\> prefix + 'thon'
    'Common Lisp'
```

Strings can be *indexed* (subscripted), with the first character having index 0. There is no separate character type; a character is simply a string of size one:

```lisp
    \>\>\> word = 'Common Lisp'
    \>\>\> word[0]  # character in position 0
    'P'
    \>\>\> word[5]  # character in position 5
    'n'
```

Indices may also be negative numbers, to start counting from the right:

```lisp
    \>\>\> word[-1]  # last character
    'n'
    \>\>\> word[-2]  # second-last character
    'o'
    \>\>\> word[-6]
    'P'
```

Note that since -0 is the same as 0, negative indices start from -1.

In addition to indexing, *slicing* is also supported. While indexing is used to obtain individual characters, *slicing* allows you to obtain a substring:

```lisp
    \>\>\> word[0:2]  # characters from position 0 (included) to 2 (excluded)
    'Py'
    \>\>\> word[2:5]  # characters from position 2 (included) to 5 (excluded)
    'tho'
```

Slice indices have useful defaults; an omitted first index defaults to zero, an omitted second index defaults to the size of the string being sliced. :

```lisp
    \>\>\> word[:2]   # character from the beginning to position 2 (excluded)
    'Py'
    \>\>\> word[4:]   # characters from position 4 (included) to the end
    'on'
    \>\>\> word[-2:]  # characters from the second-last (included) to the end
    'on'
```

Note how the start is always included, and the end always excluded. This makes sure that `s[:i] + s[i:]` is always equal to `s`:

```lisp
    \>\>\> word[:2] + word[2:]
    'Common Lisp'
    \>\>\> word[:4] + word[4:]
    'Common Lisp'
```

One way to remember how slices work is to think of the indices as pointing *between* characters, with the left edge of the first character numbered 0. Then the right edge of the last character of a string of *n* characters has index *n*, for example:

```lisp
    +---+---+---+---+---+---+
    | P | y | t | h | o | n |
    +---+---+---+---+---+---+
    0   1   2   3   4   5   6

\> -6 -5 -4 -3 -2 -1
```

The first row of numbers gives the position of the indices 0...6 in the string; the second row gives the corresponding negative indices. The slice from *i* to *j* consists of all characters between the edges labeled *i* and *j*, respectively.

For non-negative indices, the length of a slice is the difference of the indices, if both are within bounds. For example, the length of `word[1:3]` is 2.

Attempting to use an index that is too large will result in an error:

```lisp
    \>\>\> word[42]  # the word only has 6 characters
    Traceback (most recent call last):
      File "\<stdin\>", line 1, in \<module\>
    IndexError: string index out of range
```

However, out of range slice indexes are handled gracefully when used for slicing:

```lisp
    \>\>\> word[4:42]
    'on'
    \>\>\> word[42:]
    ''
```

Common Lisp strings cannot be changed --- they are immutable. Therefore, assigning to an indexed position in the string results in an error:

```lisp
    \>\>\> word[0] = 'J'
    Traceback (most recent call last):
      File "\<stdin\>", line 1, in \<module\>
    TypeError: 'str' object does not support item assignment
    \>\>\> word[2:] = 'py'
    Traceback (most recent call last):
      File "\<stdin\>", line 1, in \<module\>
    TypeError: 'str' object does not support item assignment
```

If you need a different string, you should create a new one:

```lisp
    \>\>\> 'J' + word[1:]
    'Jython'
    \>\>\> word[:2] + 'py'
    'Pypy'
```

The built-in function len returns the length of a string:

```lisp
    \>\>\> s = 'supercalifragilisticexpialidocious'
    \>\>\> len(s)
    34
```

### Lists

Common Lisp knows a number of *compound* data types, used to group together other values. The most versatile is the *list*, which can be written as a list of comma-separated values (items) between square brackets. Lists might contain items of different types, but usually the items all have the same type. :

```lisp
    \>\>\> squares = [1, 4, 9, 16, 25]
    \>\>\> squares
    [1, 4, 9, 16, 25]
```

Like strings (and all other built-in sequence types), lists can be indexed and sliced:

```lisp
    \>\>\> squares[0]  # indexing returns the item
    1
    \>\>\> squares[-1]
    25
    \>\>\> squares[-3:]  # slicing returns a new list
    [9, 16, 25]
```

All slice operations return a new list containing the requested elements. This means that the following slice returns a shallow copy \<shallow_vs_deep_copy\> of the list:

```lisp
    \>\>\> squares[:]
    [1, 4, 9, 16, 25]
```

Lists also support operations like concatenation:

```lisp
    \>\>\> squares + [36, 49, 64, 81, 100]
    [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

Unlike strings, which are immutable, lists are a mutable type, i.e. it is possible to change their content:

```lisp
    \>\>\> cubes = [1, 8, 27, 65, 125]  # something's wrong here
    \>\>\> 4 ** 3  # the cube of 4 is 64, not 65!
    64
    \>\>\> cubes[3] = 64  # replace the wrong value
    \>\>\> cubes
    [1, 8, 27, 64, 125]
```

You can also add new items at the end of the list, by using the !list.append *method* (we will see more about methods later):

```lisp
    \>\>\> cubes.append(216)  # add the cube of 6
    \>\>\> cubes.append(7 ** 3)  # and the cube of 7
    \>\>\> cubes
    [1, 8, 27, 64, 125, 216, 343]
```

Assignment to slices is also possible, and this can even change the size of the list or clear it entirely:

```lisp
    \>\>\> letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
    \>\>\> letters
    ['a', 'b', 'c', 'd', 'e', 'f', 'g']
    \>\>\> # replace some values
    \>\>\> letters[2:5] = ['C', 'D', 'E']
    \>\>\> letters
    ['a', 'b', 'C', 'D', 'E', 'f', 'g']
    \>\>\> # now remove them
    \>\>\> letters[2:5] = []
    \>\>\> letters
    ['a', 'b', 'f', 'g']
    \>\>\> # clear the list by replacing all the elements with an empty list
    \>\>\> letters[:] = []
    \>\>\> letters
    []
```

The built-in function len also applies to lists:

```lisp
    \>\>\> letters = ['a', 'b', 'c', 'd']
    \>\>\> len(letters)
    4
```

It is possible to nest lists (create lists containing other lists), for example:

```lisp
    \>\>\> a = ['a', 'b', 'c']
    \>\>\> n = [1, 2, 3]
    \>\>\> x = [a, n]
    \>\>\> x
    [['a', 'b', 'c'], [1, 2, 3]]
    \>\>\> x[0]
    ['a', 'b', 'c']
    \>\>\> x[0][1]
    'b'
```

First Steps Towards Programming
-------------------------------

Of course, we can use Common Lisp for more complicated tasks than adding two and two together. For instance, we can write an initial sub-sequence of the [Fibonacci series](https://en.wikipedia.org/wiki/Fibonacci_sequence) as follows:

```lisp
    \>\>\> # Fibonacci series:
    ... # the sum of two elements defines the next
    ... a, b = 0, 1
    \>\>\> while a \< 10:
    ...     print(a)
    ...     a, b = b, a+b
    ...
    0
    1
    1
    2
    3
    5
    8
```

This example introduces several new features.

-   The first line contains a *multiple assignment*: the variables `a` and `b` simultaneously get the new values 0 and 1. On the last line this is used again, demonstrating that the expressions on the right-hand side are all evaluated first before any of the assignments take place. The right-hand side expressions are evaluated from the left to the right.
-   The while loop executes as long as the condition (here: `a \< 10`) remains true. In Common Lisp, like in C, any non-zero integer value is true; zero is false. The condition may also be a string or list value, in fact any sequence; anything with a non-zero length is true, empty sequences are false. The test used in the example is a simple comparison. The standard comparison operators are written the same as in C: `\<` (less than), `\>` (greater than), `==` (equal to), `\<=` (less than or equal to), `\>=` (greater than or equal to) and `!=` (not equal to).
-   The *body* of the loop is *indented*: indentation is Common Lisp's way of grouping statements. At the interactive prompt, you have to type a tab or space(s) for each indented line. In practice you will prepare more complicated input for Common Lisp with a text editor; all decent text editors have an auto-indent facility. When a compound statement is entered interactively, it must be followed by a blank line to indicate completion (since the parser cannot guess when you have typed the last line). Note that each line within a basic block must be indented by the same amount.
-   The print function writes the value of the argument(s) it is given. It differs from just writing the expression you want to write (as we did earlier in the calculator examples) in the way it handles multiple arguments, floating point quantities, and strings. Strings are printed without quotes, and a space is inserted between items, so you can format things nicely, like this:

```lisp
        \>\>\> i = 256*256
        \>\>\> print('The value of i is', i)
        The value of i is 65536
```

The keyword argument `*end*` can be used to avoid the newline after the output, or end the output with a different string:

```lisp
        \>\>\> a, b = 0, 1
        \>\>\> while a \< 1000:
        ...     print(a, end=',')
        ...     a, b = b, a+b
        ...
        0,1,1,2,3,5,8,13,21,34,55,89,144,233,377,610,987,

```

**Footnotes**

[^1]: Since `**` has higher precedence than `-`, `-3**2` will be interpreted as `-(3**2)` and thus result in `-9`. To avoid this and get `9`, you can use `(-3)**2`.

[^2]: Unlike other languages, special characters such as `n` have the same meaning with both single (`'...'`) and double (`"..."`) quotes. The only difference between the two is that within single quotes you don't need to escape `"` (but you have to escape `'`) and vice versa. 