# Setup unitary test on Python
Purpose of this file : base for making unitary test.

## First the test, then the application !

New approch : Test Driven Development, you start by writing the test, then the code. It allow you to stay focus on a
purpose, to simplify as much as you can your test... And be sure that they are done !

## Chose your weapon

Depending of the usage, you can use the basic and simple unittest (standard python library, at least for 3.6). Or you
can use dedicated package, such as Pytest. Note that test written with unittest can be run by pytest. Pytest add
functionality such as Junit xml report with more informations.

### Writing tests

With unittest syntaxe, the skeletton is simple : you define a class for a testsuite, a functon for a testcase.

```python
import unittest

class My_suite(unittest.TestCase):
    def test_case(self):
        assert 2 == 1 + 1
   
if __name__=="__main__":
    unittest.main()
```
***Note check why it's a testcase...***

## Be the master of time and space

In order to test properly, I want to have a determined environment. What if I need to test something linked to the time,
or a response to an API ?

### Working with datetime and timestamp

A simply library : Freezgun. It will give you a simple decorator to fix the time.

```python
import unittest
import datetime
from freezegun import freeze_time

class MyTestSuite(unittest.TestCase):
    @freeze_time("2077-01-01T23:59:59.999")
    def test_01_test_freeze_time(self):
        """ Simple test to check that freezegun is working """
        assert datetime.datetime.now().isoformat() == "2077-01-01T23:59:59.999"
        
if __name__ == '__main__':
    unittest.main()
```

Then you can run your tests:
```
python3 -m pytest . --junitxml=REPORT.xml -o junit_suite_name=Test_suite_01
============================= test session starts =============================
platform win32 -- Python 3.7.1, pytest-4.5.0, py-1.8.0, pluggy-0.11.0
rootdir: C:\Users\...\examples_garden\Python
collected 1 item

application\test_watch.py .                                              [100%]

- generated xml file: C:\...\Python\TEST_REPORT.xml -
========================== 1 passed in 0.20 seconds ===========================
```

### Redirect outputs

Just a nice trick: redirect standard output inside a file. I use contextlib. You can also redirect standard errors.

```python
from contextlib import redirect_stdout

def dummy():
    print("Hello there")

def redirected_dummy():
    with open("standard_output.txt", 'w', encoding="utf-8") as stdout_file:
        with redirect_stdout(stdout_file):
            return dummy()
```

### Mock an API