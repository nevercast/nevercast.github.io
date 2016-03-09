---
title: Unit testing in Python 3
tags: [uni, programming, python]
---

## Unit testing

Unit testing is the process by which a program tests itself or another program by running small units of code
and checking the output against an expected result. In the context of Python, these 'units of code' might be a single method
or function. 

A single test might run 1 function with known arguments and compare the returned result against what is to be expected. During
development of a program you would typically document rules or contracts that describe what each function/method/unit should or
shouldn't do. It's from this that your unit tests are derived.

Here's an example:
```python
def square(x):
  return x * x
```

This function is an implementation of squaring a number. For this function we might have a collection of inputs and their expected
outputs. We know that `3` should square to `9`, `4` should square to `16`. This becomes our tests.

Here is a table of inputs and their expected outputs:

| *Input* | *Output* |
|---------|----------|
|-2       | 4        |
|-1       | 1        |
|0        | 0        |
|1        | 1        |
|2        | 4        |
|3        | 9        |

This is the table we will write our tests around later. Unit testing is a great way to ensure that each small part of your program
works as expected. If every little piece works together, you can be more certain that your program as a whole works too.

## Unit testing in Python

Python comes out-of-the-box with a unit testing module called [unittest](https://docs.python.org/3/library/unittest.html).
This built in support makes unit testing easy and portable. You do not have to install 3rd party modules in Python to be
able to test your code from anywhere Python is installed.

Each test 'case' in the `unittest` module is defined by a class that extends `unittest.TestCase`. From within that class you can
create each method that will do a single test on your program. The test methods in your test case class *MUST* be prefixed with `test_`.
This is how `unittest` finds them.

Here is a basic test class in Python 3:

```python
# Firstly, we need the unittest module. So import it.
import unittest

# Next we define our class that will test our program
class MyProgramTests(unittest.TestCase):

  # Add a method prefixed with test_ that will be run by unittest.
  def test_square(self):
    pass
```

Currently our square test (`test_square(self)`) doesn't do any checks. We will write those shortly. For the moment it is empty.

In effectively 3 lines we are ready to start writing tests for our code. Let's drop our `square` function in there from
earlier and write our first test.

```python
import unittest

def square(x):
  """Squares x and returns the result."""
  return x * x

class MyProgramTests(unittest.TestCase):
  def test_square(self):
    self.assertEqual(16, square(4))
```
