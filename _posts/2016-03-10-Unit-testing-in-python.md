---
title: Unit testing in Python 3
tags: [uni, programming, python, unit testing]
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

We have added our square function from earlier and told Python to assert (check) that it's result is equal to `16` when it's called with `4`. Save this file as `square_test.py`. We can now run this test from the terminal (or command line in Windows):

`python -m unittest square_test`

You should get a result similar to this
```
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

Running from the terminal is a convienent way to run unit tests if you're using some sort of build process and wanted to check the tests first. There are many reasons to use this method of running tests. There is however another way. We can run the tests when the test file itself is executed. To do this we call `unittest.main()`.

```python
import unittest

def square(x):
  """Squares x and returns the result."""
  return x * x

class MyProgramTests(unittest.TestCase):
  def test_square(self):
    self.assertEqual(16, square(4))
    
if __name__ == '__main__':
    unittest.main()
```

We check if the current file is the __main__ program, meaning we are executing it directly (Running it from the terminal, or running it from an IDE such as PyCharm or Wing). In this case it calls `unittest.main()` which runs our tests.

Now we can run the tests with `python square_test.py` typed in to the terminal. This may be more convienent for some users. Especially from inside an IDE. Observe that `python -m unittest square_test` still works.

Now let's finish up our tests from the table we had earlier. Here is the source for all 6 tests.

```python
import unittest

def square(x):
  """Squares x and returns the result."""
  return x * x

class MyProgramTests(unittest.TestCase):
  def test_square(self):
    self.assertEqual(4, square(-2))
    self.assertEqual(1, square(-1))
    self.assertEqual(0, square(0))
    self.assertEqual(1, square(1))
    self.assertEqual(4, square(2))
    self.assertEqual(9, square(3))
    
if __name__ == '__main__':
    unittest.main()
```

By running the test again you should see an `OK` status. The tests worked. We know our square function is good.

*__Note:__ for those of you running your tests from inside an IDE. If you get a Traceback message about SystemExit. You can prevent this by changing `unittest.main()` to `unittest.main(exit=False)`. This tells `unittest` not to call `sys.exit()`. If you rely on the exit code of your tests when using the unittest from inside a build script. Calling the test file directly (`python square_test.py`) will no longer return a failure exit code.*

## Unit testing an external module

In our current example we have our square function defined in the same file as our tests. If you are writing a program for someone else, or are completing a challenge at University for example (UC represent), writing the tests in the same file that you want to submit might not be a practical solution, since it could manipulate the output of your program. For this we should write our tests separately from our program in two distinct files. One that we can keep, and one we can send to others (or send both if they want to run the tests too).

To use functions from another file you need to import it. Let's quickly change our program to be done over two files. I suggest creating a folder for these two files also.

#### tests.py

```python
import unittest
import main

class MyProgramTests(unittest.TestCase):
  def test_square(self):
    self.assertEqual(4, main.square(-2))
    self.assertEqual(1, main.square(-1))
    self.assertEqual(0, main.square(0))
    self.assertEqual(1, main.square(1))
    self.assertEqual(4, main.square(2))
    self.assertEqual(9, main.square(3))
    
if __name__ == '__main__':
    unittest.main()
```

#### main.py

```python
def square(x):
  """Squares x and returns the result."""
  return x * x
```

Our actual program is in `main.py`, while our tests are in `tests.py`. This is good [Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns).

## They are almost of equal

So far we have only tested integers and asserted that they were equal. If you're using floats, due to their [inaccuracy](https://docs.python.org/2/tutorial/floatingpoint.html) ([wiki](https://en.wikipedia.org/wiki/Floating_point)) you should use approximate equality. Here is an example:

First we write our tests, and then we write our code to satisfy those tests. Let's make a multiplier function that will multiply two numbers.

#### tests.py

```python
import unittest
import main

class MyProgramTests(unittest.TestCase):
  def test_square(self):
    self.assertEqual(4, main.square(-2))
    self.assertEqual(1, main.square(-1))
    self.assertEqual(0, main.square(0))
    self.assertEqual(1, main.square(1))
    self.assertEqual(4, main.square(2))
    self.assertEqual(9, main.square(3))
  
  def test_mul(self):
    self.assertEqual(-4, main.mul(-2, 2))
    self.assertEqual(0, main.mul(-1, 0))
    self.assertEqual(40, main.mul(5, 8))
    self.assertEqual(36, main.mul(12, 3))
    self.assertEqual(17.5, main.mul(2.5, 7))
    self.assertEqual(7.02, main.mul(2.6, 2.7))    

if __name__ == '__main__':
    unittest.main()
```

Looks good yes? We have a nice mix of big numbers and small numbers, negatives and floats. Let's run this test.

`python tests.py`

You should get an Attribute Error. We haven't written a function called `mul` that will do multiplication for us yet. So Python can't find it. Let's fix that by writing the beginning of it. 

#### main.py
```python
def square(x):
  """Squares x and returns the result."""
  return x * x

def mul(num1, num2):
  """Multiplies two numbers"""
```

Here we have the skeleton of the multiplication function. If you run the tests again you will see something similar to this `AssertionError: -4 != None`. On the first line of the `test_mul()` function in our tests we have this: `self.assertEqual(-4, main.mul(-2, 2))`. This tests that `-2*2 == -4`. We however didn't get -4 from our `mul` function. We got `None`. This is shown with the AssertionError message. `AssertionError: -4 != None` means "I expected to get `-4`, but instead I was given `None`".
e
Our `mul` function is not returning anything, because we have not added anything for it to return. Let's do that.

#### main.py
```python
def square(x):
  """Squares x and returns the result."""
  return x * x

def mul(num1, num2):
  """Multiplies two numbers"""
  return num1 * num2
```

There we go! Now we can expect this and all the other tests to pass. Because our multiplication takes place and is returned. Run the tests!

__Ouch__. What happened there? A test failed! But our math seems just fine. This is called floating precision. Computers represent numbers as 1s and 0s, to represent a number that has a decimal place we have to somehow do that with the limitation of binary. Floating point numbers use a formula to do that, by multiplying by exponents and shifting the decimal place we can represent almost any number with the limit of losing some accuracy.

The message you should have receieved is

```
AssertionError: 7.02 != 7.0200000000000005
```

We have seen an AssertionError before, so we know that the test expected to get `7.02` but instead was given `7.0200000000000005`. Although these numbers (when rounded) are almost the same, they are not exactly the same. When testing floating numbers, you cannot be sure that the result will be equal, it will just be very very close.

We have a way around this! There are many ways to assert something with `unittest`. We have only used one, the `assertEqual`. There is another way that is perfect for our situation. That is `assertAlmostEqual`. `assertAlmostEqual` will check that the numbers are equal when rounded to 7 decimal places. Let's replace the last test with an almost equal test.

#### tests.py
```python
import unittest
import main

class MyProgramTests(unittest.TestCase):
  def test_square(self):
    self.assertEqual(4, main.square(-2))
    self.assertEqual(1, main.square(-1))
    self.assertEqual(0, main.square(0))
    self.assertEqual(1, main.square(1))
    self.assertEqual(4, main.square(2))
    self.assertEqual(9, main.square(3))
  
  def test_mul(self):
    self.assertEqual(-4, main.mul(-2, 2))
    self.assertEqual(0, main.mul(-1, 0))
    self.assertEqual(40, main.mul(5, 8))
    self.assertEqual(36, main.mul(12, 3))
    self.assertEqual(17.5, main.mul(2.5, 7))
    self.assertAlmostEqual(7.02, main.mul(2.6, 2.7))    

if __name__ == '__main__':
    unittest.main()
```

The test now checks if the result, and 7.02 are the same, when rounded to `7` decimal places. `7` is a pretty good amount of accuracy, but if you wish to use less or more accuracy you can change it. This is an example of comparing two numbers while including only the first 3 decimal places. `self.assertAlmostEqual(5.1, 5.10029, 3)`. The 3 on the end (the last argument) tells `assertAlmostEqual` how many decimal places to round to. You can read more about [assertAlmostEqual here.](https://docs.python.org/3/library/unittest.html#unittest.TestCase.assertAlmostEqual)

When comparing floating point numbers, you should always use an approximate test with a sufficient amount of accuracy instead of just 'equals'. 

## Tell me more

There are many methods exposed by `unittest` that allow all sorts of checks. A full list is available on the [unittest documentation](https://docs.python.org/3/library/unittest.html). 

This should be enough information to get you started writing unit tests for all your Python programs. In my follow up article I will cover other data types including strings, lists and dictionaries. As well as simulating input from a user and checking the output of your program from unit tests, without a user being present. 

As always, comments are below. Use them and let me know what you'd like to see next, in a follow up to unit tests or about anything. Happy testing!
