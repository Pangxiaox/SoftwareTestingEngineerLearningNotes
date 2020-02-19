# Python测试框架

## unittest

**一些概念**

Test Case：一个testcase的实例就是一个测试用例。测试前准备环境的搭建（setUp），执行测试代码（run），以及测试后环境的还原（tearDown）

Test Suite：多个测试用例集合在一起

TestLoader：用来加载TestCase到TestSuite中

TextTestRunner：执行测试用例，run(test)会执行TestSuite/TestCase中的run(result)方法

TestFixture：测试用例环境的搭建和销毁

**unittest静态类图**  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/Python_tests/unittest.png)  

⭐整体流程：首先写好TestCase，然后由TestLoader加载TestCase到TestSuite，然后由TextTestRunner来运行TestSuite，运行的结果保存在TextTestResult中，整个过程集成在unittest.main模块中

### unittest初级使用

**步骤**

- 导入unittest模块、被测文件或者其中的类
- 创建一个测试类，被继承unittest.TestCase
- 重写setUp和tearDown方法（如有初始化条件和结束条件）
- 定义测试函数，函数名以test_开头，测试用例
- 在函数体内使用断言来判断测试结果是否符合预期结果
- 调用unittest.main()方法运行测试用例，无此方法也可以运行

**设置setUp和tearDown，每次用例执行前都会执行初始化条件和结束条件**

```python
import unittest


class Test_Math(unittest.TestCase):
    def setUp(self):
        print("测试用例执行前的初始化操作========")

    def tearDown(self):
        print("测试用例执行完之后的收尾操作=====")

    def test_addTwoNum_01(self):
        sum = 5 + 7
        print(sum)

    def test_subTwoNum_02(self):
        sub = 13 - 2
        print(sub)
```

执行结果：

```
测试用例执行前的初始化操作========
12
测试用例执行完之后的收尾操作=====
测试用例执行前的初始化操作========
11
测试用例执行完之后的收尾操作=====
```

**设置setUpClass和tearDownClass，执行所有测试用例，仅执行一次初始化条件和结束条件**

```python
import unittest


class Test_Math(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        print("测试用例执行前的初始化操作========")

    @classmethod
    def tearDownClass(cls):
        print("测试用例执行完之后的收尾操作=====")

    def test_addTwoNum_01(self):
        sum = 5 + 7
        print(sum)

    def test_subTwoNum_02(self):
        sub = 13 - 2
        print(sub)
```

执行结果：

```
测试用例执行前的初始化操作========
12
11
测试用例执行完之后的收尾操作=====
```

**断言Assert——结果对比**

| Method                   | Checks that                           |
| ------------------------ | ------------------------------------- |
| assertEqual(a,b)         | a == b                                |
| assertNotEqual(a,b)      | a != b                                |
| assertTrue(x)            | bool(x) is True                       |
| assertFalse(x)           | bool(x) is false                      |
| assertIs(a,b)            | a is b：判断是否是同一对象（id（a）） |
| assertNotIs(a,b)         | a is not b                            |
| assertIsNone(x)          | x is None                             |
| assertIsNotNone(x)       | x is not None                         |
| assertIn(a,b)            | a in b                                |
| assertNotIn(a,b)         | a not in b                            |
| assertIsInstance(a,b)    | isInstance(a,b)                       |
| assertNotIsInstance(a,b) | not isInstance(a,b)                   |

**设置断言，当一条测试用例执行失败，不会影响其他测试用例执行**

```python
import unittest


class Test_Math(unittest.TestCase):
    def setUp(self):
        print("测试用例执行前的初始化操作========")

    def tearDown(self):
        print("测试用例执行完之后的收尾操作=====")

    def test_addTwoNum_01(self):
        sum = 5 + 7
        print(sum)
        self.assertEqual(12, sum)

    def test_subTwoNum_02(self):
        sub = 13 - 2
        print(sub)
        self.assertEqual(1, sub)
```

执行结果：

```
测试用例执行前的初始化操作========
12
测试用例执行完之后的收尾操作=====
测试用例执行前的初始化操作========
11
测试用例执行完之后的收尾操作=====

FAILED (failures=1)


11 != 1

Expected :1
Actual   :11
```

**断言也可以直接写>=  <=  ==来判断**

```python
    def test_addTwoNum_01(self):
        sum = 5 + 7
        print(sum)
        assert sum == 12
```

**写一个方法文件和单元测试文件：**

```python
#  方法文件
class MyClass:
    def __init__(self, a, b):
        self.a = a
        self.b = b

    def add(self):
        sum = self.a+self.b
        return sum

    def sub(self):
        sub = self.a-self.b
        return sub
        
#  单元测试文件
import unittest
from MyClass import MyClass


class Test_MyClass(unittest.TestCase):
    def setUp(self):
        self.myclass = MyClass(3,3)

    def test_addTwoNum(self):
        sum = self.myclass.add()
        assert sum == 6

    def test_subTwoNum(self):
        sub = self.myclass.sub()
        self.assertEqual(0, sub)
```

### unittest进阶使用

**testsuite：addTest()：添加一个测试用例、addTest([,,])：添加多个测试用例**

```python
        s = unittest.TestSuite()
        s.addTest(Test_MyClass("test_addTwoNum"))
        s.addTests([Test_MyClass("test_addTwoNum"), Test_MyClass("test_subTwoNum")])
        runner = unittest.TextTestRunner()
        runner.run(s)
```

**addTest参数：是一个测试用例的列表**

- 方式一：类名('方法名')的集合

- 方式二：unittest.TestLoader.discover方法匹配目录下的用例

  🔺discover方法的默认匹配模式是从当前目录下找以test开头的py文件，测试用例的顺序执行是根据字母a-z，0-9顺序执行

- 方式三：unittest.TestLoader.loadTestsFromModule匹配模块中的测试用例

⭐TestLoader类、TestSuite类、TestRunner类需要先实例化再使用

**采用unittest.TestLoader.discover方式加载测试用例**

```python
#  方法文件
class MyClass:
    def __init__(self, a, b):
        self.a = a
        self.b = b

    def add(self):
        sum = self.a+self.b
        return sum

    def sub(self):
        sub = self.a-self.b
        return sub
        
#  单元测试文件1
import unittest
from MyClass import MyClass


class Test_MyClass(unittest.TestCase):
    def setUp(self):
        self.myclass = MyClass(3,3)

    def test_addTwoNum(self):
        sum = self.myclass.add()
        assert sum == 6

    def test_subTwoNum(self):
        sub = self.myclass.sub()
        self.assertEqual(0, sub)

#  单元测试文件2
import unittest
from MyClass import MyClass


class Test_MyClass(unittest.TestCase):
    def setUp(self):
        self.myclass = MyClass(4, 4)

    def test_addTwoNum(self):
        sum = self.myclass.add()
        assert sum == 8

    def test_subTwoNum(self):
        sub = self.myclass.sub()
        self.assertEqual(0, sub)
        
#  运行测试主文件
import unittest
import os

s = unittest.TestSuite()
loader = unittest.TestLoader()
s.addTests(loader.discover(os.getcwd()))
run = unittest.TextTestRunner()
run.run(s)
```

**测试报告生成**

- text文件——写到文件中

```python
        s= unittest.TestSuite()
        s.addTest(Test_MyClass("test_addTwoNum"))
        s.addTests([Test_MyClass("test_addTwoNum"),Test_MyClass("test_subTwoNum")])
        fs = open("test_run_report.txt", "w")
        run = unittest.TextTestRunner(fs)
        run.run(s)
```

输出文件内容：

```
...
----------------------------------------------------------------------
Ran 3 tests in 0.000s

OK
```

- 用HTMLTestRunner

**跳过某个case：skip装饰器**

```python
    @unittest.skip("don't")
    def test_sub(self):
        self.assertEqual(1, sub(7, 7))
```

- unittest.skip(reason)：skip无条件跳过
- unittest.skipIf(conditon,reason)：当condition为True时跳过
- unittest.skipUnless(condition,reason)：当condition为False时跳过

**跳过某个case：TestCase.skipTest()方法**

```python
    def test_sub(self):
        self.skipTest("don't")
        self.assertEqual(1, sub(7, 7))
```

**调整输出执行结果的详细程度：unittest.main()方法中加verbosity参数**

- verbosity = 0：不输出每一用例的执行结果
- verbosity = 1：默认
- verbosity = 2：输出详细执行结果

```python
import unittest
from test import Test_MyClass

s = unittest.TestSuite()
s.addTests([Test_MyClass("test_add"), Test_MyClass("test_sub")])
runner = unittest.TextTestRunner(verbosity=2)
runner.run(s)
```

输出结果：

```
初始化
test_add (test.Test_MyClass) ... ok
结束
test_sub (test.Test_MyClass) ... skipped "don't"

----------------------------------------------------------------------
Ran 2 tests in 0.000s

OK (skipped=1)
```

当verbosity改为1：

```
.s
初始化
----------------------------------------------------------------------
结束
Ran 2 tests in 0.000s

OK (skipped=1)

```

当verbosity改为0：

```
初始化
----------------------------------------------------------------------
Ran 2 tests in 0.000s

结束
OK (skipped=1)
```



## pytest

默认执行当前目录下的所有以 `test_` 为前缀或以 `_test` 为后缀的文件中以 `test_`为前缀的函数

**简单例子**

```python
import pytest

def fun(x):
    return x+2


def test_ans():
    assert fun(5) == 7


if __name__ == '__main__':
    pytest.main()
```

断言正确时输出结果：

```
============================= test session starts =============================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: html-2.0.1, metadata-1.8.0
collected 1 item

test_class.py .                                                          [100%]

============================== 1 passed in 0.06s ==============================
```

断言错误时输出结果：（即 `assert fun(5) == 6`）

```
============================= test session starts =============================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: html-2.0.1, metadata-1.8.0
collected 1 item

test_class.py F                                                          [100%]

================================== FAILURES ===================================
__________________________________ test_ans ___________________________________

    def test_ans():
>       assert fun(5) == 6
E       assert 7 == 6
E        +  where 7 = fun(5)

test_class.py:8: AssertionError
============================== 1 failed in 0.11s ==============================
```

**使用类来组成多个用例**

```python
import pytest


class TestClass:
    def test_one(self):
        x = "this"
        assert 'h' in x

    def test_two(self):
        x = "hello"
        assert hasattr(x, 'check')


if __name__ == "__main__":
    pytest.main()
```

输出结果：

```
============================= test session starts =============================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: html-2.0.1, metadata-1.8.0
collected 2 items

test_class.py .F                                                         [100%]

================================== FAILURES ===================================
_____________________________ TestClass.test_two ______________________________

self = <test_class.TestClass object at 0x0000020877D9DB70>

    def test_two(self):
        x = "hello"
>       assert hasattr(x, 'check')
E       AssertionError: assert False
E        +  where False = hasattr('hello', 'check')

test_class.py:11: AssertionError
========================= 1 failed, 1 passed in 0.14s =========================
```

**支持参数化：使用pytest.mark.parametrize的参数，第一个为变量的元组、第二个为变量赋值的元组列表**

```python
# content of test_time.py
import pytest
from datetime import datetime, timedelta

testdata = [
    (datetime(2001, 12, 12), datetime(2001, 12, 11), timedelta(1)),
    (datetime(2001, 12, 11), datetime(2001, 12, 12), timedelta(-1)),
]


@pytest.mark.parametrize("a,b,expected", testdata)
def test_timedistance_v0(a, b, expected):
    diff = a - b
    assert diff == expected


@pytest.mark.parametrize("a,b,expected", testdata, ids=["forward", "backward"])
def test_timedistance_v1(a, b, expected):
    diff = a - b
    assert diff == expected


def idfn(val):
    if isinstance(val, (datetime,)):
        # note this wouldn't show any hours/minutes/seconds
        return val.strftime('%Y%m%d')


@pytest.mark.parametrize("a,b,expected", testdata, ids=idfn)
def test_timedistance_v2(a, b, expected):
    diff = a - b
    assert diff == expected


if __name__ == "__main__":
    pytest.main()
```

输出结果：

```
============================= test session starts =============================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: html-2.0.1, metadata-1.8.0
collected 6 items

test_class.py ......                                                     [100%]

============================== 6 passed in 0.15s ==============================
```

**5种常用运行模式**

- 运行后生成测试报告（htmlReport）

对于上面这个python代码，在命令行中输入 `pytest --html=a.html`

打开a.html后页面显示如图：  


![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/Python_tests/cmd.PNG)  

- 运行指定的case

直接运行test_class.py文件中所有cases：

`pytest test_class.py`

运行test_class.py文件中的TestClassOne这个class下的两个cases：

`pytest test_class.py::TestClassOne`

运行test_class.py文件中的TestClassTwo这个class下的test_one：

`pytest test_class.py::TestClassTwo::test_one`

⭐定义class时，需要以T开头，不然pytest不会运行这个class

- 多进程运行cases：

缩短脚本运行的时长，用多进程运行：`pytest test_class.py -n NUM`

(NUM：并发的进程数)

- 重试运行cases：

在接口测试时，有时遇到503或短时的网络波动，导致case运行失败，需要重试运行cases

`pytest test_class.py --reruns NUM`

(NUM：重试的次数)

- 显示print内容

运行pytest时print内容不会显示出来，需要运行：`pytest test_class.py -s`

⭐pytest多种运行模式可以叠加执行，比如同时运行4个进程，又想打印print内容：

`pytest test_class.py -s -n 4`

**⭐编写pytest测试样例的一些规则：**

- 测试文件以`test_`开头或以`_test`结尾
- 测试类以Test开头，并且不能带有init方法
- 测试函数以`test_`开头
- 断言使用基本的assert

**skip、skipif、xfail**

```python
import pytest


@pytest.mark.skip(reason="skip1")
def test_func_slow_1():
    assert 1 == 5


@pytest.mark.xfail(reason="skip2")
def test_func_slow_2():
    assert 1 == 1


if __name__ == "__main__":
    pytest.main()
```

输出结果：

```
============================= test session starts =============================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: forked-1.1.3, html-2.0.1, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 2 items

test_class.py sX                                                         [100%]

======================== 1 skipped, 1 xpassed in 0.14s ========================
```

另外一例子：

```python
import pytest


@pytest.mark.skipif(1+1 == 3, reason="skip1")
def test_func_slow_1():
    assert 1 == 5


@pytest.mark.xfail(reason="skip2")
def test_func_slow_2():
    assert 1 == 2


if __name__ == "__main__":
    pytest.main()
```

输出结果：

```
============================= test session starts =============================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: forked-1.1.3, html-2.0.1, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 2 items

test_class.py Fx                                                         [100%]

================================== FAILURES ===================================
______________________________ test_func_slow_1 _______________________________

    @pytest.mark.skipif(1+1 == 3, reason="skip1")
    def test_func_slow_1():
>       assert 1 == 5
E       assert 1 == 5

test_class.py:6: AssertionError
======================== 1 failed, 1 xfailed in 0.22s =========================
```

总结：

- pytest.mark.skip可标记无法在某些平台上运行的测试功能或者希望失败的测试功能
- xfail意味着您希望测试由于某种原因而失败，当测试没有通过时，就是xfail；当测试通过时尽管预计会失败，但它是一个xpass

**断言使用**

①正常断言

```python
assert 2 == 3
```

②异常断言

```python
import pytest


def test_zero_division():
    with pytest.raises(ZeroDivisionError):
        1 / 0


if __name__ == "__main__":
    pytest.main()
```

输出结果：

```
============================= test session starts =============================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 1 item

test_class.py .                                                          [100%]

============================== 1 passed in 0.06s ==============================
```

**pytest 命令行参数**

测试文件test_class.py:

```python
import pytest


class TestClass():
    def test_1(self):
        assert 1 == 2

    def test_2(self):
        assert 1 == 1

    def test_3(self):
        assert 2 > 5


if __name__ == "__main__":
    pytest.main()
```

- -k EXPRESSION：执行某个关键字的用例，用例匹配给出的表达式，匹配范围是文件名、类名、函数名，用and区分

`pytest -k "test_class and TestClass and not test_3"`

输出结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 3 items / 1 deselected / 2 selected

test_class.py F.                                                                                           [100%]

=================================================== FAILURES ====================================================
_______________________________________________ TestClass.test_1 ________________________________________________

self = <test_class.TestClass object at 0x00000220430D2BA8>

    def test_1(self):
>       assert 1 == 2
E       assert 1 == 2

test_class.py:6: AssertionError
=================================== 1 failed, 1 passed, 1 deselected in 0.12s ===================================
```

- --maxfail=num：当错误个数到达给定数时，退出测试

`pytest --maxfail=1`

输出结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 3 items

test_class.py F

=================================================== FAILURES ====================================================
_______________________________________________ TestClass.test_1 ________________________________________________

self = <test_class.TestClass object at 0x000001F9465A2B70>

    def test_1(self):
>       assert 1 == 2
E       assert 1 == 2

test_class.py:6: AssertionError
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! stopping after 1 failures !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
=============================================== 1 failed in 0.08s ===============================================

```

- -m MARKEXPR：只能运行由相应标识的测试用例。使用该参数，测试用例要用@pytest.mark.marker修饰

`pytest -m t`

在测试文件的test_2函数上加上 `@pytest.mark.t`

测试结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 3 items / 2 deselected / 1 selected

test_class.py .                                                                                            [100%]

=============================================== warnings summary ================================================
d:\python3.6\lib\site-packages\_pytest\mark\structures.py:327
  d:\python3.6\lib\site-packages\_pytest\mark\structures.py:327: PytestUnknownMarkWarning: Unknown pytest.mark.t -
 is this a typo?  You can register custom marks to avoid this warning - for details, see https://docs.pytest.org/e
n/latest/mark.html
    PytestUnknownMarkWarning,

-- Docs: https://docs.pytest.org/en/latest/warnings.html
================================== 1 passed, 2 deselected, 1 warning in 0.17s ===================================
```

⭐ -m后面不能带单引号，只能带双引号

用表达式运行多个标识：

```
pytest -m "slow or faster"  运行有slow标识或faster标识的用例
pytest -m "slow and faster"  运行有slow和faster标识的用例
pytest -m "slow and not faster"  运行有slow和没有faster标识的用例
```

- -v，--verbose：详细结果

`pytest -v` 或者 `pytest --verbose`

输出结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1 -- d:\python3.6\python.exe
cachedir: .pytest_cache
metadata: {'Python': '3.6.6', 'Platform': 'Windows-10-10.0.18362-SP0', 'Packages': {'pytest': '5.3.5', 'py': '1.8.
1', 'pluggy': '0.13.1'}, 'Plugins': {'cov': '2.8.1', 'forked': '1.1.3', 'html': '2.0.1', 'incremental': '0.5.0', '
metadata': '1.8.0', 'rerunfailures': '8.0', 'xdist': '1.31.0'}, 'JAVA_HOME': 'C:\\Program Files\\Java\\jdk1.8.0_18
1'}
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 3 items

test_class.py::TestClass::test_1 FAILED                                                                    [ 33%]
test_class.py::TestClass::test_2 PASSED                                                                    [ 66%]
test_class.py::TestClass::test_3 FAILED                                                                    [100%]

=================================================== FAILURES ====================================================
_______________________________________________ TestClass.test_1 ________________________________________________

self = <test_class.TestClass object at 0x0000016E30D68320>

    def test_1(self):
>       assert 1 == 2
E       assert 1 == 2
E         -1
E         +2

test_class.py:6: AssertionError
_______________________________________________ TestClass.test_3 ________________________________________________

self = <test_class.TestClass object at 0x0000016E30D6BBA8>

    def test_3(self):
>       assert 2 > 5
E       assert 2 > 5

test_class.py:12: AssertionError
========================================== 2 failed, 1 passed in 0.24s ==========================================
```

- -q，--quiet：简化控制台输出

`pytest -q` 或者 `pytest --quiet`

输出结果：

```
F.F                                                                                                        [100%]
=================================================== FAILURES ====================================================
_______________________________________________ TestClass.test_1 ________________________________________________

self = <test_class.TestClass object at 0x0000028751273F60>

    def test_1(self):
>       assert 1 == 2
E       assert 1 == 2

test_class.py:6: AssertionError
_______________________________________________ TestClass.test_3 ________________________________________________

self = <test_class.TestClass object at 0x00000287512DE080>

    def test_3(self):
>       assert 2 > 5
E       assert 2 > 5

test_class.py:12: AssertionError
2 failed, 1 passed in 0.10s
```

- -s：输入我们用例中的调试信息，比如print的打印信息等

在测试文件中的test_2函数当中加入 `print("---test_2")`代码：

`pytest -s`执行结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 3 items

test_class.py F---test_2
.F

=================================================== FAILURES ====================================================
_______________________________________________ TestClass.test_1 ________________________________________________

self = <test_class.TestClass object at 0x0000023DE5093F60>

    def test_1(self):
>       assert 1 == 2
E       assert 1 == 2

test_class.py:6: AssertionError
_______________________________________________ TestClass.test_3 ________________________________________________

self = <test_class.TestClass object at 0x0000023DE5079C18>

    def test_3(self):
>       assert 2 > 5
E       assert 2 > 5

test_class.py:13: AssertionError
========================================== 2 failed, 1 passed in 0.25s ==========================================
```

- -v：输出用例更加详细的执行信息，比如用例所在的文件及用例名等

`pytest -v`

测试结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1 -- d:\python3.6\python.exe
cachedir: .pytest_cache
metadata: {'Python': '3.6.6', 'Platform': 'Windows-10-10.0.18362-SP0', 'Packages': {'pytest': '5.3.5', 'py': '1.8.
1', 'pluggy': '0.13.1'}, 'Plugins': {'cov': '2.8.1', 'forked': '1.1.3', 'html': '2.0.1', 'incremental': '0.5.0', '
metadata': '1.8.0', 'rerunfailures': '8.0', 'xdist': '1.31.0'}, 'JAVA_HOME': 'C:\\Program Files\\Java\\jdk1.8.0_18
1'}
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 3 items

test_class.py::TestClass::test_1 FAILED                                                                    [ 33%]
test_class.py::TestClass::test_2 PASSED                                                                    [ 66%]
test_class.py::TestClass::test_3 FAILED                                                                    [100%]

=================================================== FAILURES ====================================================
_______________________________________________ TestClass.test_1 ________________________________________________

self = <test_class.TestClass object at 0x0000026F273E27F0>

    def test_1(self):
>       assert 1 == 2
E       assert 1 == 2
E         -1
E         +2

test_class.py:6: AssertionError
_______________________________________________ TestClass.test_3 ________________________________________________

self = <test_class.TestClass object at 0x0000026F27C42080>

    def test_3(self):
>       assert 2 > 5
E       assert 2 > 5

test_class.py:13: AssertionError
========================================== 2 failed, 1 passed in 0.12s ==========================================
```

- --junit-xml=path：输出xml文件格式，与jenkins集成时使用
- --result-log=path：将最后结果保存到本地文件中

**pytest fixture**

pytest支持以xUnit格式型的测试模型（setUp/tearDown），但与unittest有一点差别：

- 模块形式：setup_module/teardown_module
- 函数形式：setup_function/teardown_function
- 类形式：setup_class/teardown_class
- 方法形式：setup_method/teardown_method

🔺pytest可以直接运行unittest模式的测试用例

🔺如在pytest模式中使用setUpClass()函数是不行的，但如果用例类继承自unittest.TestCase，还是可以识别

**1. fixture scope范围参数**

@pytest.fixture(scope='xxx')

- scope='function'：每一个用例都执行
- scope='class'：每个类都执行
- scope='module'：每个模块执行（函数形式的用例）
- scope='session'：每个session只运行一次，在自动化测试时，登陆步骤可以使用该session

**2. 调用fixture的三种方法**

**函数或类里面方法直接传fixture的函数参数名称**

```python
from __future__ import print_function
import pytest


@pytest.fixture(scope='module')
def resource_a_setup(request):
    print('\nresources_a_setup()')

    def resource_a_teardown():
        print('\nresources_a_teardown()')

    request.addfinalizer(resource_a_teardown)


def test_1(resource_a_setup):
    print('test_1()')


def test_2():
    print('\ntest_2()')


def test_3(resource_a_setup):
    print('\ntest_3()')


if __name__ == "__main__":
    pytest.main()
```

`pytest -s -v` 输出结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1 -- d:\python3.6\python.exe
cachedir: .pytest_cache
metadata: {'Python': '3.6.6', 'Platform': 'Windows-10-10.0.18362-SP0', 'Packages': {'pytest': '5.3.5', 'py': '1.8.
1', 'pluggy': '0.13.1'}, 'Plugins': {'cov': '2.8.1', 'forked': '1.1.3', 'html': '2.0.1', 'incremental': '0.5.0', '
metadata': '1.8.0', 'rerunfailures': '8.0', 'xdist': '1.31.0'}, 'JAVA_HOME': 'C:\\Program Files\\Java\\jdk1.8.0_18
1'}
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 3 items

test_class.py::test_1
resources_a_setup()
test_1()
PASSED
test_class.py::test_2
test_2()
PASSED
test_class.py::test_3
test_3()
PASSED
resources_a_teardown()


=============================================== 3 passed in 0.10s ===============================================
```

**使用装饰器@pytest.mark.usefixtures()修饰需要运行的用例**

```python
from __future__ import print_function
import pytest


@pytest.fixture()
def test1():
    print('\n开始执行func')


@pytest.mark.usefixtures('test1')
def test_a():
    print('---用例a执行---')


@pytest.mark.usefixtures("test1")
class TestCase:
    def test_b(self):
        print("---用例b执行---")

    def test_c(self):
        print("---用例c执行---")


if __name__ == "__main__":
    pytest.main()
```

`pytest -s` 测试结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 3 items

test_class.py
开始执行func
---用例a执行---
.
开始执行func
---用例b执行---
.
开始执行func
---用例c执行---
.

=============================================== 3 passed in 0.03s ===============================================
```

**叠加usefixtures**

如果一个方法或者一个class用例想要同时调用多个fixture，可以是用@pytest.mark.usefixture()叠加。叠加顺序是：先执行的放底层，后执行的放上层

```python
from __future__ import print_function
import pytest


@pytest.fixture()
def test1():
    print('\n开始执行func')


@pytest.fixture()
def test2():
    print('\n开始执行func2')


@pytest.mark.usefixtures("test1")
@pytest.mark.usefixtures('test2')
def test_a():
    print('---用例a执行---')


@pytest.mark.usefixtures('test2')
@pytest.mark.usefixtures("test1")
class TestCase:
    def test_b(self):
        print("---用例b执行---")

    def test_c(self):
        print("---用例c执行---")


if __name__ == "__main__":
    pytest.main()
```

`pytest -s`测试结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 3 items

test_class.py
开始执行func2

开始执行func
---用例a执行---
.
开始执行func

开始执行func2
---用例b执行---
.
开始执行func

开始执行func2
---用例c执行---
.

=============================================== 3 passed in 0.11s ===============================================
```

**usefixture与传fixture区别**

- 如果fixture有返回值，那么usefixture就无法获取到返回值
- 当fixture需要用到return出来的参数时，只能将参数名称直接当参数传入。不需要用到return出来的参数时，两种方式都可以

**fixture自动使用autouse=True**

```python
from __future__ import print_function
import pytest


@pytest.fixture(scope='module',autouse=True)
def test1():
    print('\n开始执行module')


@pytest.fixture(scope='class',autouse=True)
def test2():
    print('\n开始执行class')


@pytest.fixture(scope='function',autouse=True)
def test3():
    print('\n开始执行function')


def test_a():
    print('---用例a执行---')


def test_d():
    print('---用例d执行---')


class TestCase:
    def test_b(self):
        print("---用例b执行---")

    def test_c(self):
        print("---用例c执行---")


if __name__ == "__main__":
    pytest.main()
```

`pytest -s`测试结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 4 items

test_class.py
开始执行module

开始执行class

开始执行function
---用例a执行---
.
开始执行class

开始执行function
---用例d执行---
.
开始执行class

开始执行function
---用例b执行---
.
开始执行function
---用例c执行---
.

=============================================== 4 passed in 0.16s ===============================================
```

**conftest.py作用范围**

一般工程根目录下的conftest.py文件起到全局作用，在不同子目录下也可以放conftest.py的文件，作用范围只在该层级以及以下目录生效

**conftest在不同层级间作用域不一样**

```python
# conftest.py
import pytest


@pytest.fixture(scope='session', autouse=True)
def login():
    print('----准备登录----')
    
# login/conftest.py
import pytest


@pytest.fixture(scope='session', autouse=True)
def bai_du():
    print('-----登录百度页面-----')
    
#  login/login.py
import pytest


class TestCase:

    def test_login(self):
        print('hhh，成功登录百度')


if __name__ == '__main__':
    pytest.main(['-s', 'login.py'])
```

测试结果：

```
============================= test session starts =============================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY\login
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 1 item

login.py ----准备登录----
-----登录百度页面-----
hhh，成功登录百度
.

============================== 1 passed in 0.06s ==============================
```

**conftest不能跨模块调用**

```python
#  log/conftest.py
import pytest


@pytest.fixture(scope='function', autouse=True)
def log_web():
    print('打印页面日志成功')
    
#  log/log.py
import pytest


def test_web():
    print('hhh,成功一次打印日志')


def test_web1():
    print('hhh,成功两次打印日志')


if __name__ == '__main__':
    pytest.main(['-s', 'log.py'])
```

测试结果：

```
============================= test session starts =============================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY\log
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 2 items

log.py ----准备登录----
打印页面日志成功
hhh,成功一次打印日志
.打印页面日志成功
hhh,成功两次打印日志
.

============================== 2 passed in 0.09s ==============================
```

**参数化**

**单个参数**

```python
import pytest


class TestClass():
    def setup_class(self):
        print("------->setup_class")

    def teardown_class(self):
        print("------->teardown_class")

    @pytest.mark.parametrize("a", [3, 6])  # a参数被赋予两个值，函数会运行两遍
    def test_a(self, a):  # 参数必须和parametrize里面的参数一致
        print("test data:a=%d" % a)
        assert a % 3 == 0


if __name__ == '__main__':
    pytest.main()
```

输出结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 2 items

test_class.py ------->setup_class
test data:a=3
.test data:a=6
.------->teardown_class


=============================================== 2 passed in 0.04s ===============================================
```

**多个参数**

```python
import pytest


class TestClass():
    def setup_class(self):
        print("------->setup_class")

    def teardown_class(self):
        print("------->teardown_class")

    @pytest.mark.parametrize("a,b", [(1, 2), (0, 3)])  # 参数a,b均被赋予两个值，函数会运行两遍
    def test_a(self, a, b):  # 参数必须和parametrize里面的参数一致
        print("test data:a=%d,b=%d" % (a, b))
        assert a + b == 3


if __name__ == '__main__':
    pytest.main()
```

测试结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 2 items

test_class.py ------->setup_class
test data:a=1,b=2
.test data:a=0,b=3
.------->teardown_class


=============================================== 2 passed in 0.18s ===============================================
```

**函数返回值类型入参**

```python
import pytest


def return_test_data():
    return [(1,2),(0,3)]


class TestClass():
    def setup_class(self):
        print("------->setup_class")

    def teardown_class(self):
        print("------->teardown_class")

    @pytest.mark.parametrize("a,b",return_test_data())
    # 使用函数返回值的形式传入参数值
    def test_a(self,a,b):
        print("test data:a=%d,b=%d"%(a,b))
        assert a+b == 3


if __name__ == '__main__':
    pytest.main()
```

测试结果：

```
============================================== test session starts ==============================================
platform win32 -- Python 3.6.6, pytest-5.3.5, py-1.8.1, pluggy-0.13.1
rootdir: D:\untitled21_PY
plugins: cov-2.8.1, forked-1.1.3, html-2.0.1, incremental-0.5.0, metadata-1.8.0, rerunfailures-8.0, xdist-1.31.0
collected 2 items

test_class.py ------->setup_class
test data:a=1,b=2
.test data:a=0,b=3
.------->teardown_class


=============================================== 2 passed in 0.18s ===============================================
```

