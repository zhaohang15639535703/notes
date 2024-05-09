## 2.The Interpreter and Its Environment

To declare an encoding other than the default one, a special comment line should be added as the *first* line of the file. The syntax is as follows:

```
# -*- coding: encoding -*-
```

## 3.Using Python as a Calculator

### 3.1 Numbers

The interpreter acts as a simple calculator: you can type an expression at it and it will write the value. Expression syntax is straightforward: the operators `+`, `-`, `*` and `/` can be used to perform arithmetic; parentheses (`()`) can be used for grouping. For example:

```python
>>> 2 + 2
4
>>> 50 - 5*6
20
>>> (50 - 5*6) / 4
5.0
>>> 8 / 5  # division always returns a floating point number
1.6
```

Division (`/`) always returns a float. To do [floor division](https://docs.python.org/3/glossary.html#term-floor-division) and get an integer result you can use the `//` operator; to calculate the remainder you can use `%`:



With Python, it is possible to use the `**` operator to calculate powers

```
>>> 5 ** 2  # 5 squared
25
>>> 2 ** 7  # 2 to the power of 7
128
```

The equal sign (`=`) is used to assign a value to a variable.



**In interactive mode, the last printed expression is assigned to the variable `_`.** This means that when you are using Python as a desk calculator, it is somewhat easier to continue calculations, for example:

```python
>>> tax = 12.5 / 100
>>> price = 100.50
>>> price * tax
12.5625
>>> price + _
113.0625
>>> round(_, 2)
113.06
```



### 3.2 Text

In the Python shell, the string definition and output string can look different. The [`print()`](https://docs.python.org/3/library/functions.html#print) function produces a more readable output, by omitting the enclosing quotes and by printing escaped and special characters:

```python
>>> s = 'First line.\nSecond line.'  # \n means newline
>>> s  # without print(), special characters are included in the string
'First line.\nSecond line.'
>>> print(s)  # with print(), special characters are interpreted, so \n produces new line
First line.
Second line.
```





If you don’t want characters prefaced by `\` to be interpreted as special characters, you can use *raw strings* by adding an `r` before the first quote:

```python
>>> print('C:\some\name')  # here \n means newline!
C:\some
ame
>>> print(r'C:\some\name')  # note the r before the quote
C:\some\name
```



String literals can span multiple lines. One way is using triple-quotes: `"""..."""` or `'''...'''`. End of lines are automatically included in the string, but it’s possible to prevent this by adding a `\` at the end of the line. The following example:

```python
print("""\
Usage: thingy [OPTIONS]
     -h                        Display this usage message
     -H hostname               Hostname to connect to
""")
```

produces the following output (note that the initial newline is not included):

```
Usage: thingy [OPTIONS]
     -h                        Display this usage message
     -H hostname               Hostname to connect to
```

Strings can be concatenated (glued together) with the `+` operator, and **repeated with `*`**:

```python
>>> # 3 times 'un', followed by 'ium'
>>> 3 * 'un' + 'ium'
'unununium'
```



Two or more *string literals* (i.e. the ones enclosed between quotes) next to each other are automatically concatenated.

```python
>>> 'Py' 'thon'
'Python'
```

This feature is particularly useful when you want to break long strings:

```python
>>> text = ('Put several strings within parentheses '
...         'to have them joined together.')
>>> text
'Put several strings within parentheses to have them joined together.'
```

but this only works with two literals though, not with variables or expressions.



Strings can be *indexed* (subscripted), with the first character having index 0. There is no separate character type; a character is simply a string of size one.

Indices may also be **negative numbers**, to start counting from the right.

Attempting to use an index that is too large will result in an error.



In addition to indexing, ***slicing*** is also supported. While indexing is used to obtain individual characters, *slicing* allows you to obtain a substring:

```python
>>> word[0:2]  # characters from position 0 (included) to 2 (excluded)
'Py'
>>> word[2:5]  # characters from position 2 (included) to 5 (excluded)
'tho'
```

Slice indices have useful defaults; an omitted first index defaults to zero, an omitted second index defaults to the size of the string being sliced.

```python
word[:2]   # character from the beginning to position 2 (excluded)
'Py'
>>> word[4:]   # characters from position 4 (included) to the end
'on'
>>> word[-2:]  # characters from the second-last (included) to the end
'on'
```

**the start is always included, and the end always excluded.**



different from index, out of range slice indexes are handled gracefully when used for slicing:

```python
>>> word[4:42]
'on'
>>> word[42:]
''
```



Python strings cannot be changed, it is **immutable**

The built-in function [`len()`](https://docs.python.org/3/library/functions.html#len) returns the length of a string:



### 3.3 Lists

**lists are a mutable type**

Like strings (and all other built-in [sequence](https://docs.python.org/3/glossary.html#term-sequence) types), lists can be indexed and sliced

You can also add new items at the end of the list, by using the `list.append()` *method* (we will see more about methods later):

\>>>

```python
>>> cubes.append(216)  # add the cube of 6
>>> cubes.append(7 ** 3)  # and the cube of 7
>>> cubes
[1, 8, 27, 64, 125, 216, 343]
```



**Simple assignment in Python never copies data.** When you assign a list to a variable, the variable refers to the *existing list*. 

```python
rgb = ["Red", "Green", "Blue"]
>>> rgba = rgb
>>> id(rgb) == id(rgba)  # they reference the same object
True
>>> rgba.append("Alph")
>>> rgb
["Red", "Green", "Blue", "Alph"]
```

**All slice operations return a new list containing the requested elements**. This means that the following slice returns a [**shallow copy**](https://docs.python.org/3/library/copy.html#shallow-vs-deep-copy) of the list:

```python
correct_rgba = rgba[:]
>>> correct_rgba[-1] = "Alpha"
>>> correct_rgba
["Red", "Green", "Blue", "Alpha"]
>>> rgba
["Red", "Green", "Blue", "Alph"]
```



Assignment to slices is also possible, and this can even change the size of the list or clear it entirely:

```python
letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> letters
['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> # replace some values
>>> letters[2:5] = ['C', 'D', 'E']
>>> letters
['a', 'b', 'C', 'D', 'E', 'f', 'g']
>>> # now remove them
>>> letters[2:5] = []
>>> letters
['a', 'b', 'f', 'g']
>>> # clear the list by replacing all the elements with an empty list
>>> letters[:] = []
>>> letters
[]
```

The built-in function [`len()`](https://docs.python.org/3/library/functions.html#len) also applies to lists:



Assignment to slices is also possible, and this can even change the size of the list or clear it entirely:

```python
letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> letters
['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> # replace some values
>>> letters[2:5] = ['C', 'D', 'E']
>>> letters
['a', 'b', 'C', 'D', 'E', 'f', 'g']
>>> # now remove them
>>> letters[2:5] = []
>>> letters
['a', 'b', 'f', 'g']
>>> # clear the list by replacing all the elements with an empty list
>>> letters[:] = []
>>> letters
[]
```

The built-in function [`len()`](https://docs.python.org/3/library/functions.html#len) also applies to lists:





 In Python, like in C, **any non-zero integer value is true; zero is false**. 