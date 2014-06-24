Green
=====

Clean, colorful test runner for Python
--------------------------------------
[![Version](https://img.shields.io/pypi/v/green.svg?style=flat)](https://pypi.python.org/pypi/green)
[![Build Status](https://img.shields.io/travis/CleanCut/green.svg?style=flat)](https://travis-ci.org/CleanCut/green)
[![Coverage Status](https://img.shields.io/coveralls/CleanCut/green.svg?style=flat)](https://coveralls.io/r/CleanCut/green?branch=master)

Green is a clean, colorful test runner for Python unit tests.  Compare it to
[nose](https://nose.readthedocs.org/en/latest/) or
[trial](http://twistedmatrix.com/trac/wiki/TwistedTrial).

Green grew out of a desire to see pretty colors.  Really!  A big part of the
whole the **Red/Green/Refactor** process in test-driven-development is
_actually getting to see red and green output_.  Most python unit testing
actually goes **Gray/Gray/Refactor** (at least on my terminal, which is gray
text on black background).  That's a shame.  Even TV is in color these days.
Why not terminal output?  Even worse, the default output for most test runners
is cluttered, hard-to-read, redundant, and the dang status indicators are not lined
up in a vertical column!  Green fixes all that.

### Features ###

- **Colorful** - Terminal output makes good use of color when the terminal supports it.
- **Clean** - Low redundancy in output. Result stats for each test is lined up in a vertical column.
- **Fast** - Can run tests in independent subprocesses.
- **Powerful** - Multi-target + auto-discovery.
- **Traditional** - Use the normal `unittest` classes and methods for your unit tests.
- **Descriptive** - Four verbosity levels, from just dots to full docstring output.
- **Thorough** - Built-in, optional integration with
  [coverage](http://nedbatchelder.com/code/coverage/).
- **Modern** - Supports Python 2.7, 3.3, 3.4, and [PyPy](http://pypy.org)
- **Portable** - Completely supports OS X, Linux, and BSDs (and maybe Windows).
- **Flexible** - Optional HTML output.


Basic Usage
-----------

You may need to prepend the installation command with `sudo` or run it as root
if your normal user cannot write to the local Python package directory.

```bash
pip3 install green    # To upgrade: "pip3 install --upgrade green"
```

Now run green...

```bash
# From inside your code directory
green

# From outside your code directory
green code_directory

# A specific file
green test_stuff.py

# A specific test inside a large package.
#
# Assuming you want to run TestClass.test_function inside
# package/test/test_module.py ...
green package.test.test_module.TestClass.test_function
```

Basic Troubleshooting
---------------------

One easy way to avoid common importing problems is to navigate to the *parent*
directory of the directory your python code is in.  Then pass green the
directory your code is in and let it autodiscover the tests (see the Tutorial below
for tips on making your tests discoverable).

    cd /parent/directory
    green code_directory

Another way to address importing problems is to carefully set up your
`PYTHONPATH` environment variable to include the parent path of your code
directory.  Then you should be able to just run `green` from _inside_ your code
directory directory.

```bash
export PYTHONPATH=/parent/directory
cd /parent/directory/code_directory
green
```

Verbosity Levels
----------------

By default, Green mimics the verbosity levels of vanilla unittest, meaning that
output is mostly just dots.  For Green we recommend adding more verbosity by
using the `-v` or `-vv` options.

Levels:
- `default` - Dots, tracebacks, and summary line.
- `-v` - Add module/class heirarchy and function names
- `-vv` - Version information added. Function names are replaced with first line of docstring.
- `-vvv` - Highest verbosity level recognized.  Details coming soon.

See the section entitled [Running Green](#running-green) for example output.

Canned Examples
---------------

To see all examples of all the failures, errors, etc. that could occur:

    green green.examples


To run Green's own internal unit tests:

    green green


Advanced Usage
--------------

Please see `green --help`


Unit Test Structure Tutorial
----------------------------

This tutorial *does* cover:

- External structure of your project (directory and file layout)
- Skeleton of a real test module
- How to import stuff from from your project into your test module
- Gotchas about naming...everything.
- Where to run green from and what the output could look like.

This tutorial *does not* cover:

- [Why you should write unit tests at
  all](http://stackoverflow.com/questions/67299/is-unit-testing-worth-the-effort)
- The benefits of [Test-Driven
  Development](http://en.wikipedia.org/wiki/Test-driven_development)
- How to use the [unittest](https://docs.python.org/library/unittest.html) module.
- How to write
  [good](http://stackoverflow.com/questions/61400/what-makes-a-good-unit-test)
  unit tests.

### External Structure ###

This is what your project layout should look like with just one module in your
package:


    proj                  # 'proj' is the package
    ├── __init__.py
    ├── foo.py            # 'foo' (or proj.foo) is the only "real" module
    └── test              # 'test' is a sub-package
        ├── __init__.py
        └── test_foo.py   # 'test_foo' is the only "test" module

Notes:

1. There is an `__init__.py` in every directory.  Don't forget it.  It can be
   an empty file, but it needs to exist.

2. `proj` itself is a directory that you will be storing somewhere.  We'll
   pretend it's in `/home/user`

3. The `test` directory needs to start with `test`.

4. The test modules need to start with `test`.


When your project starts adding code in sub-packages, you will need to make a
choice on where you put their tests.  I prefer to create a `test` subdirectory
in each sub-package.

    proj
    ├── __init__.py
    ├── foo.py
    ├── subpkg
    │   ├── __init__.py
    │   ├── bar.py
    │   └── test              # test subdirectory in every sub-package
    │       ├── __init__.py
    │       └── test_bar.py
    └── test
        ├── __init__.py
        └── test_foo.py


The other option is to start mirroring your subpackage layout from within a single test directory.

    proj
    ├── __init__.py
    ├── foo.py
    ├── subpkg
    │   ├── __init__.py
    │   ├── bar.py
    └── test
        ├── __init__.py
        ├── subpkg            # mirror sub-package layout inside test dir
        │   ├── __init__.py
        │   └── test_bar.py
        └── test_foo.py


### Skeleton of Test Module ###

Assume `foo.py` contains the following contents:

```python
def answer():
    return 42

class School():

    def food(self):
        return 'awful'

    def age(self):
        return 300
```

Here's a possible version of `test_foo.py` you could have.

```python
# Import stuff you need for the unit tests themselves to work
import unittest

# Import stuff that you want to test.  Don't import extra stuff if you don't
# have to.
from proj.foo import answer, School

# If you need the whole module, you can do this:
#     from proj import foo
#
# Here's another reasonable way to import the whole module:
#     import proj.foo as foo
#
# In either case, you would obviously need to access objects like this:
#     foo.answer()
#     foo.School()

# Then write your tests

class TestAnswer(unittest.TestCase):

    def test_type(self):
        "answer() returns an integer"
        self.assertEqual(type(answer()), int)

    def test_expected(self):
        "answer() returns 42"
        self.assertEqual(answer(), 42)

class TestSchool(unittest.TestCase):

    def test_food(self):
        school = School()
        self.assertEqual(school.food(), 'awful')

    def test_age(self):
        school = School()
        self.assertEqual(school.age(), 300)
```

Notes:

1. Start all your test class names with `Test`, and always subclass
   `unittest.TestCase`

2. Start all your test method names with `test`.

3. What a test class and/or its methods _actually test_ is entirely up to you.
   In some sense it is an artform.  Just use the test classes to group a bunch
   of methods that seem logical to go together.

4. The methods of `TestAnswer` have docstrings, while the methods on
   `TestSchool` do not.  For more verbose output modes, green will use the
   method docstring to describe the test if it is present, and the name of the
   method if it is not.  Notice the difference in the output below.

### Running Green ###

To run the unittests, we would change to the parent directory of the project
(`/home/user` in this example) and then run `green proj`.

**In a real terminal, this output is syntax highlighted**

    $ green proj
    ....
    Ran 4 tests in 0.000s

    OK (passes=4)

Okay, so that's the classic short-form output for unit tests.  Green really
shines when you start getting more verbose:

**In a real terminal, this output is syntax highlighted**

    $ green -v proj
    test.test_foo
      TestAnswer
    .   answer() returns 42
    .   answer() returns an integer
      TestSchool
    .   test_age
    .   test_food

    Ran 4 tests in 0.001s

    OK (passes=4)

Notes:

1. Green outputs clean, heirarchical output.

2. Test status is aligned on the _left_

3. Method names are replaced with docstrings when present.

4. Green always outputs a summary of statuses that will add up to the total
   number of tests that were run.  For some reason, many test runners forget
   about statuses other than Error and Fail, and even the built-in unittest runner
   forgets about passing ones.

5. Possible values for test status (these match the `unittest` short status characters exactly)
 - `.` Pass
 - `F` Failure
 - `E` Error
 - `s` Skipped
 - `x` Expected Failure
 - `u` Unexpected pass
