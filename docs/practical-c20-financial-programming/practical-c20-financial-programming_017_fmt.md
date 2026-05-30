# 15. 扩展金融库

C++ 是一种富有表现力的语言，可用于开发一些最复杂的软件，包括银行和其他金融机构中常使用的高性能应用程序。然而，有时使用解释型语言来组合和扩展 C++ 库是有益的，这可以简化原型和其他非关键应用程序的创建。许多此类解释型语言被用于连接预编译库。其中，Python 和 Lua 是金融行业中最常用的解释型语言之一。

在本章中，我将展示如何使用流行的脚本和扩展语言（如 Lua 和 Python）与 C++ 库交互。接下来几节中讨论的解决方案和算法，允许你在用其他语言开发的应用程序中重用前面章节中介绍的许多 C++ 组件。在某些情况下，你还可以在自己的 C++ 应用程序中使用从外部语言创建的代码。

以下是本章讨论的一些主题：

*   **用 Python 扩展 C++**：Python 语言为开发服务器端应用程序提供了出色的特性。如果你想将 C++ 库用作其他服务的一部分，Python 可能是集成不同库的最佳方式。

## 用 Lua 扩展 C++

Lua 是一种相对较新的语言，以其动态特性的简洁实现而著称。它也被用作一种可嵌入到您自己的大型 C++ 应用程序中的扩展语言。

### 将 C++ 股票处理代码导出到 Python

生成提供将 C++ 股票处理类导出到 Python 能力的代码。

### 解决方案

Python 是一种流行的语言，已被用于多个领域，包括 Web 应用、科学数据探索和金融。Python 最大的优势之一是其能够干净地整合大型编程库集合。这种互操作性的一个非常重要的原因是 Python 用于与不同语言（尤其是 C 和 C++）交互的简单机制。

在本节中，您将学习 Python 的扩展机制以及如何从您的 C++ 代码中访问它。我为此过程提供了一个金融示例，您将能够访问一个最初使用 C++ 设计和实现的 `Stock` 类。

Python 在大多数操作系统上作为开源软件提供。您可以在 [`http://python.org`](http://python.org) 找到大多数操作系统的源代码和二进制文件。您还会发现 Python 预装在许多类 UNIX 操作系统上，例如 Linux 和 Mac OS X。Python 中库扩展的主要机制是模块。模块用于将数据和代码导出到 Python 源文件或其他模块。关键字 `import` 可用于将现有模块加载到内存中。例如：

```
import sys
```

这是一个用于加载 `sys` 模块的命令，该模块提供对系统相关函数的访问。虽然模块本身可以用 Python 创建，但作为 C++ 程序员，我们的主要兴趣在于使用 C++ 创建模块。这可以通过 Python 模块创建 API（应用程序编程接口）实现，该 API 可用于 C 和 C++，并且包含在大多数 Python 安装中。

由于扩展机制是用 C 语言编写的（为了与现有的适用于 Python 的 C 库兼容），因此有必要创建一些 C 函数来封装您的原始 C++ 类，以实现与 Python 环境的互操作性。

作为这个过程如何工作的示例，请考虑 `Stock` 类，它可以用于对单个股票进行建模。清单 15-1 展示了该类的公共接口。

```
class Stock {
public:
Stock(const std::string &ticker, double price, double div);
Stock(const Stock &p);
~Stock();
Stock &operator=(const Stock &p);
std::string ticker();
double price();
void setPrice(double price);
double dividend();
void setDividend(double div);
double dividendYield();
}
Listing 15-1
Interface for Stock Class
```

Python 的扩展 API 是一组头文件和库，它们将 Python 环境暴露给用 C 或 C++ 编写的其他应用程序。要使用扩展 API 将诸如 `Stock` 之类的类导出到 Python，我们需要创建一些将接收并返回 Python 解释器发送的请求的函数。这是在 `Stock_Py.cpp` 文件中完成的，该文件包含一个处理 `Stock` 类每个成员的函数列表。

第一个值得关注的函数是 `stock_create` 函数，其定义如下：

```
PyObject *stock_create(PyObject *self, PyObject *args)
```

直接从 Python 调用的函数通常具有这样的签名：接收两个 Python 对象并返回一个 Python 对象。每当使用 `object.function()` 语法进行调用时，此类函数的第一个参数是作为调用目标的 Python 对象（类似于 C++ 中的 `this` 指针）。第二个参数是一个 Python 列表，存储传递给该函数的所有参数。

这个函数做的第一件事是检索作为参数传递的参数。这可以在 Python API 中通过调用 `PyArg_ParseTuple` 函数来完成，该函数负责检查参数并将其值复制到数据对象中。此函数的第一个参数是表示参数列表的对象。第二个参数是一个字符串，定义了参数列表中每个数据元素的类型。最后，剩余的参数是指向数据应存储位置的指针。

```
if (!PyArg_ParseTuple(args, "sdd", &ticker, &price, &dividend))
return NULL;
```

如果此函数不成功，它将返回 false，这将导致 `stock_create` 返回 `NULL`。向解释器返回 `NULL` 表示被调用的函数出于某种原因失败。

一旦输入数据被验证，下一步是创建一个 `Stock` 类的对象并正确初始化它。结果存储在一个使用 `PyCapsule_New` 函数创建的 Python 胶囊对象中。此函数调用将析构函数的名称作为最后一个参数，在本例中只是 `stock_destructor`，一个调用 `Stock` 对象析构函数的函数。创建 Python 胶囊后，新的 Python 对象将作为函数的结果返回。

对于仅返回单个数据对象的函数示例，请考虑 `stock_ticker` 函数，它提供对 `Stock::ticker()` 成员函数的访问：

```
PyObject *stock_ticker(PyObject *self, PyObject *args)
{
PyObject *obj;
if (!PyArg_ParseTuple(args, "O!", &PyCapsule_Type, &obj))
return NULL;
Stock *stock = getStock(obj);
return Py_BuildValue("s", stock->ticker().c_str());
}
```

在此函数中，第一步是验证输入参数，这使用了 `PyArg_ParseTuple` 函数。`PyArg_ParseTuple` 函数接收数据容器和一个确定每个参数类型的字符串作为参数，后跟指向将存储数据的变量的指针。在这种情况下要检索的对象是 `PyCapsule` 类型，如剩余参数（它们提供了对象类型和指向对象变量的指针）所定义。一旦参数被检索出来，您就可以使用 `getStock` 函数获取 `Stock` 指针。最后，调用 `ticker()` 成员函数。要将数据返回给 Python 解释器，您需要将结果转换为 Python 对象。这是通过 `Py_BuildValue` 函数完成的，该函数使用格式字符串来确定其剩余参数的类型。其他函数类似，它们的主要工作是从参数列表中检索数据并将结果转换为 Python 对象。

注意

即使一个暴露给 Python 的函数不返回任何结果，您仍然需要返回一个有效的 Python 对象。在这种情况下，您可以使用 `Py_None`，它表示一个标准的 Python 对象，意思是“没有数据”。

最后，我们有 `initstock` 函数。它调用 Python API 中的 `Py_InitModule` 函数来确定此模块中暴露的函数。`Py_InitModule` 函数接收模块名称和一个数组作为参数，该数组包含所有函数（称为 `stockMethods`）的列表、它们的名称和描述。当这些信息传递给 Python 解释器时，每当导入 `stock` 模块时，它就对 Python 开发人员可用。

### 完整代码

清单 15-2 展示了 `Stock` 类及其关联的 Python 粘合代码的完整代码。

```cpp
//
//  Stock.h
#ifndef __FinancialSamples__Stock__
#define __FinancialSamples__Stock__
#include
class Stock {
public:
Stock(const std::string &ticker, double price, double div);
Stock(const Stock &p);
~Stock();
Stock &operator=(const Stock &p);
std::string ticker();
double price();
void setPrice(double price);
double dividend();
void setDividend(double div);
double dividendYield();
private:
std::string m_ticker;
double m_currentPrice;
double m_dividend;
};
#endif /* defined(__FinancialSamples__Stock__) */
//
//  Stock.cpp
#include "Stock.h"
Stock::Stock(const std::string &ticker, double price, double div)
: m_ticker(ticker),
m_currentPrice(price),
m_dividend(div)
{
}
Stock::Stock(const Stock &p)
: m_ticker(p.m_ticker),
m_currentPrice(p.m_currentPrice),
m_dividend(p.m_dividend)
{
}
Stock::~Stock()
{
}
Stock &Stock::operator=(const Stock &p)
{
if (this != &p)
{
m_ticker = p.m_ticker;
m_currentPrice = p.m_currentPrice;
m_dividend = p.m_dividend;
}
return *this;
}
double Stock::price()
{
return m_currentPrice;
}
void Stock::setPrice(double price)
{
m_currentPrice = price;
}
double Stock::dividend()
{
return m_dividend;
}
void Stock::setDividend(double div)
{
m_dividend = div;
}
double Stock::dividendYield()
{
return  m_dividend / m_currentPrice;
}
std::string Stock::ticker()
{
return m_ticker;
}
//
//  Stock_Py.cpp
#include "Stock_Py.h"
#include "Stock.h"
#include
#include
#include
namespace {
void stock_destructor(PyObject *capsule)
{
printf("调用析构函数\n");
Stock *stock = reinterpret_cast(PyCapsule_GetPointer(capsule, NULL));
delete stock;
}
PyObject *stock_create(PyObject *self, PyObject *args)
{
char *ticker;
double price;
double dividend;
if (!PyArg_ParseTuple(args, "sdd", &ticker, &price, &dividend))
return NULL;
printf("股票代码: %s, 价格: %lf, 股息: %lf\n", ticker, price, dividend);
Stock *stock = new Stock(ticker, price, dividend);
PyObject* stockObj = PyCapsule_New(stock, NULL,  stock_destructor);
return stockObj;
}
Stock *getStock(PyObject *obj)
{
if (!PyCapsule_CheckExact(obj))
printf("错误: 不是股票对象\n");
fflush(stdout);
return reinterpret_cast(PyCapsule_GetPointer(obj, NULL));
}
PyObject *returnNone()
{
Py_INCREF(Py_None);
return Py_None;
}
PyObject *stock_ticker(PyObject *self, PyObject *args)
{
PyObject *obj;
if (!PyArg_ParseTuple(args, "O!", &PyCapsule_Type, &obj))
return NULL;
Stock *stock = getStock(obj);
return Py_BuildValue("s", stock->ticker().c_str());
}
PyObject *stock_price(PyObject *self, PyObject *args)
{
PyObject *obj;
if (!PyArg_ParseTuple(args, "O!", &PyCapsule_Type, &obj))
return NULL;
Stock *stock = getStock(obj);
return Py_BuildValue("d", stock->price());
}
PyObject *stock_setPrice(PyObject *self, PyObject *args)
{
double price;
PyObject *obj;
if (!PyArg_ParseTuple(args, "O!d", &PyCapsule_Type, &obj, &price))
return NULL;
Stock *stock = getStock(obj);
if (!stock)
return NULL;
stock->setPrice(price);
return returnNone();
}
PyObject *stock_dividend(PyObject *self, PyObject *args)
{
PyObject *obj;
if (!PyArg_ParseTuple(args, "O!", &PyCapsule_Type, &obj))
return NULL;
Stock *stock = getStock(obj);
if (!stock)
return NULL;
return Py_BuildValue("d", stock->dividend());
}
PyObject *stock_setDividend(PyObject *self, PyObject *args)
{
double dividend;
PyObject *obj;
if (!PyArg_ParseTuple(args, "O!d", &PyCapsule_Type, &obj, &dividend))
return NULL;
Stock *stock = getStock(obj);
stock->setDividend(dividend);
return returnNone();
}
PyObject *stock_dividendYield(PyObject *self, PyObject *args)
{
PyObject *obj;
if (!PyArg_ParseTuple(args, "O!", &PyCapsule_Type, &obj))
return NULL;
Stock *stock = getStock(obj);
return Py_BuildValue("d", stock->dividendYield());
}
PyMethodDef stockMethods[] = {
{"new",  stock_create, METH_VARARGS, "创建一个新的股票对象。"},
{"ticker",  stock_ticker, METH_VARARGS, "获取股票对象的股票代码。"},
{"price",  stock_price, METH_VARARGS, "获取股票的价格。"},
{"setPrice",  stock_setPrice, METH_VARARGS, "设置股票对象的价格。"},
{"dividend",  stock_dividend, METH_VARARGS, "获取股票的股息。"},
{"setDividend",  stock_setDividend, METH_VARARGS, "设置股票对象的股息。"},
{"dividendYield",  stock_dividendYield, METH_VARARGS, "获取股票的股息收益率。"},
{NULL, NULL, 0, NULL}
};
}
PyMODINIT_FUNC initstock()
{
Py_InitModule("stock", stockMethods);
}
#
```

### `stock-setup.py`

```python
from distutils.core import setup, Extension
setup(name="stock", version="1.0",
ext_modules=[Extension("stock", ["Stock.cpp", "Stock_Py.cpp"])])
```

**代码清单 15-2** `Stock` 类的接口与实现

### 运行代码

构建 Python 扩展模块的过程与本书中介绍的其它 C++ 应用程序略有不同。该过程需要创建一个可加载模块，该模块在不同平台上形式各异，例如在 Windows 上是 `dll`，在众多 UNIX 系统上则是共享对象文件。为了简化这一过程，Python 开发者创建了一个工具，它利用 `setup.py` 文件自动执行构建。使用 `setup.py`，你无需查明特定的构建信息，例如包含目录和链接库。如代码清单 15-1 所示，文件 `stock-setup.py` 描述了扩展以及构建模块所需的源文件。构建过程（在 Mac OS X 系统上执行）如下所示。如清单所示，C++ 构建过程由 Python 通过 `build_ext` 选项自动生成。

```bash
$ python stock-setup.py build_ext -i
running build_ext
building 'stock' extension
cc -fno-strict-aliasing -fno-common -dynamic -arch x86_64 -arch i386 -g -Os -pipe
-fno-common -fno-strict-aliasing -fwrapv -DENABLE_DTRACE -DMACOSX -DNDEBUG -Wall
-Wstrict-prototypes -Wshorten-64-to-32 -DNDEBUG -g -fwrapv -Os -Wall -Wstrict-prototypes -DENABLE_DTRACE -arch x86_64 -arch i386 -pipe -I/System/Library/Frameworks/Python.framework/Versions/2.7/include/python2.7 -c Stock.cpp -o build/temp.macosx-10.9-intel-2.7/Stock.o
cc -fno-strict-aliasing -fno-common -dynamic -arch x86_64 -arch i386 -g -Os -pipe
-fno-common -fno-strict-aliasing -fwrapv -DENABLE_DTRACE -DMACOSX -DNDEBUG -Wall
-Wstrict-prototypes -Wshorten-64-to-32 -DNDEBUG -g -fwrapv -Os -Wall -Wstrict-prototypes -DENABLE_DTRACE -arch x86_64 -arch i386 -pipe -I/System/Library/Frameworks/Python.framework/Versions/2.7/include/python2.7 -c Stock_Py.cpp -o build/temp.macosx-10.9-intel-2.7/Stock_Py.o
c++ -bundle -undefined dynamic_lookup -arch x86_64 -arch i386 -Wl,-F. build/temp.macosx-10.9-intel-2.7/Stock.o build/temp.macosx-10.9-intel-2.7/Stock_Py.o -o /Users/carlosoliveira/code/FinancialSamples/FinancialSamples/stock.so
```

模块编译完成后，可以轻松加载到 Python 脚本或交互式会话中。以下是 `stock` 模块使用示例的记录：

```python
$ python
Python 2.7.5 (default, Mar 9 2014, 22:15:05)
[GCC 4.2.1 Compatible Apple LLVM 5.0 (clang-500.0.68)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import stock
>>> a = stock.new('IBM',1,1)
ticker: IBM, price: 1.000000, dividend: 1.000000
>>> stock.setPrice(a, 105)
>>> stock.price(a)
105.0
>>> stock.setDividend(a, 2.2)
>>> stock.dividend(a)
2.2
>>> stock.dividendYield(a)
0.020952380952380955
```

## 将 C++ 类直接导出到 Python

编写 C++ 代码将现有类导出到 Python 应用程序中。

### 解决方案

在“将 C++ 股票处理代码导出到 Python”一节中，你已经了解了如何使用模块机制将 C++ 代码导出到 Python。通过外部 Python API，可以暴露先前使用 C++ 创建的函数和类。然而，外部 API 也强制要求创建胶水代码，这不仅是一项枯燥的任务，而且容易出错，这项工作交给计算机来处理会更好。

为了简化外部 Python API 引发的一些问题，开发了一个新的 boost 库。`boost::python` 库使用基于模板的机制来自动创建 Python 所需的集成代码。通过这种方式，开发者可以更轻松地使用一组 C++ 模板来暴露类、变量和函数，同时避免了诸如在 Python 对象之间转换数据等重复性任务。以下是使用 boost 将 C++ 代码导出到 Python 的几个优点：

- **避免样板代码**：将 C++ 类导出到 Python 模块所需的大量代码既简单又重复。使用模板解决方案可以轻松减少或完全消除 Python 外部 API 所需的大量样板代码。

- **提供类型安全性**：在 Python 中，所有对象都具有相同的类型 `PyObject`。虽然 Python 在运行时会对正确类型进行检查，但你应该尽可能利用 C++ 的编译时检查。使用 `boost::python`，将使用 C++ 类型，并且仅在需要时自动转换为 Python 类型。

- **减少编程工作量**：使用 boost 库，你可以利用大量已开发的、用于解决将 C++ 类导出到 Python 这一特定问题的代码。如果直接使用 Python API，你可能会遇到 `boost::python` 中已经解决过的问题。如同 C++ 编程的其他领域一样，理想的做法是重用优秀的库和设计，而不是重新发明现有的解决方案。

尽管 `boost::python` 有这些优点，但也有一些原因可能会让你希望避免使用它，而直接使用 Python API：

- **项目规模**：有时，你只需要为特定用途将单个类或函数导出到 Python。在这种情况下，坚持使用 Python API 并跳过 `boost::python` 可能同样简单。

- **Boost 集成**：如果你的项目不使用 boost，或者不允许将其他 boost 库集成到代码中，那么很难使用此解决方案进行 Python 集成。

- **项目的特殊需求**：虽然 `boost::python` 中的模板非常灵活，但你可能对你正在导出的类型有额外的要求。在这种情况下，只有底层的 Python 扩展 API 才能提供项目所需的灵活性。

使用 `boost::python` 库非常简单，你只需要看一些示例及其参考文档，就能了解如何快速导出类。在代码清单 15-3 所示的代码中，我将展示如何对一个示例（来自第 5 章的 `Matrix` 类）执行此操作。`boost::python` 库与其他 boost 库一起安装，因此如果 boost 已经安装在你的系统中，你就可以直接使用它。该库的主要头文件是 `<boost/python.hpp>`，它提供了导出 C++ 类所需的所有宏和模板。

该库中的主要宏是 `BOOST_PYTHON_MODULE`。这个宏用于生成 Python 运行时期望的样板代码。在该宏之后的范围内，你可以声明将从 Python 可见的类、函数和其他类型。

要导出 C++ 类，主要提供的工具是 `class_` 模板。正如你所料，这个模板用于执行定义类型及其属性的繁重工作，底层使用 Python API。`class_` 的模板参数是类名。构造函数需要一个供 Python 使用的名称和一个默认构造函数。构造函数使用 `init` 模板定义，参数作为模板参数添加。

在主要的`class_`模板中，你会看到对`def`成员函数的调用，该函数用于向类定义新成员。每次调用`def`时，`class_`模板都会生成额外的代码来处理从Python代码对给定成员函数的调用。例如，你有以下定义：

```
def("subtract", &MatrixP::subtract)
```

这里，`subtract`成员函数被定义，第一个参数是名称，第二个参数是调用的目标。

### 完整代码

你可以在代码清单15-3中看到`Matrix`模块的完整代码。清单末尾的`setup.py`文件可用于构建模块。

```
//
//  Matrix_Py.h
#ifndef __FinancialSamples__Matrix_Py__
#define __FinancialSamples__Matrix_Py__
#include 
#include "Matrix.h"
class MatrixP : public Matrix {
public:
MatrixP(int a);
MatrixP(int a, int b);
MatrixP(const MatrixP &p);
~MatrixP();
void set(int a, int b, double v);
double get(int a, int b);
};
#endif /* defined(__FinancialSamples__Matrix_Py__) */
//
//  Matrix_Py.cpp
#include "Matrix_Py.h"
// include this header file for access to boost::python templates and macros
#include 
// add the using clause to reduce namespace clutter
using namespace boost::python;
MatrixP::MatrixP(int a)
: Matrix(a)
{
}
MatrixP::MatrixP(int a, int b)
: Matrix(a, b)
{
}
MatrixP::MatrixP(const MatrixP &p)
: Matrix(p)
{
}
MatrixP::~MatrixP()
{
}
void MatrixP::set(int a, int b, double v)
{
(*this)[a][b] = v;
}
double MatrixP::get(int a, int b)
{
return (*this)[a][b];
}
// this macro generates all the boilerplate required by the Python API
BOOST_PYTHON_MODULE(matrix)
{
// defines a new class to be exported
class_("Matrix",
init())    // the init form defines a constructor
// another constructor with two int parameters
.def(init())
// here are some standard functions (name first, member function second)
.def("add", &MatrixP::add)
.def("subtract", &MatrixP::subtract)
.def("multiply", &MatrixP::multiply)
.def("numRows", &MatrixP::numRows)
.def("trace", &MatrixP::trace)
.def("transpose", &MatrixP::transpose)
.def("set", &MatrixP::set)
.def("get", &MatrixP::get)
;
}
#
```

### `matrix-setup.py`

构建矩阵模块的Python代码：

```python
from distutils.core import setup, Extension

### 需要包含 boost::python 库的包含路径和库路径
setup(name="matrix", version="1.0",
ext_modules=[Extension("matrix", ["Matrix.cpp", "matrix_Py.cpp"],
include_dirs=["/opt/local/include/"],
library_dirs=["/opt/local/lib/"],
libraries=["boost_python-mt"])])
```

**代码清单15-3** 矩阵模块及关联的`setup.py`文件

---

### 运行代码

要编译代码，可以遵循与上一节所述类似的步骤。这意味着可以使用Python构建系统来编译扩展（通常编译为`.so`或`.dll`格式）。为此，需要创建一个设置文件，这里我们将其命名为`matrix-setup.py`。请注意，`matrix-setup.py`文件中也列出了`libraries`键。该键告诉构建系统链接到`boost_python-mt`库。您可能还需要将`include_dirs`和`library_dirs`键更改为系统中boost的安装位置。以下是通过Python运行设置文件的结果：

```
$ python matrix-setup.py build_ext -i
running build_ext
building 'matrix' extension
cc -fno-strict-aliasing -fno-common -dynamic -arch x86_64 -arch i386 -g -Os -pipe
-fno-common -fno-strict-aliasing -fwrapv -DENABLE_DTRACE -DMACOSX -DNDEBUG -Wall
-Wstrict-prototypes -Wshorten-64-to-32 -DNDEBUG -g -fwrapv -Os -Wall -Wstrict-prototypes -DENABLE_DTRACE -arch x86_64 -arch i386 -pipe -I/opt/local/include/ -I/System/Library/Frameworks/Python.framework/Versions/2.7/include/python2.7 -c Matrix.cpp -o build/temp.macosx-10.9-intel-2.7/Matrix.o
c++ -bundle -undefined dynamic_lookup -arch x86_64 -arch i386 -Wl,-F. build/temp.macosx-10.9-intel-2.7/Matrix.o build/temp.macosx-10.9-intel-2.7/matrix_Py.o -L/opt/local/lib/ -lboost_python-mt -o /Users/carlosoliveira/code/FinancialSamples/FinancialSamples/matrix.so
```

这个过程完成后，您应该会得到一个名为`matrix.so`（如果是在Windows系统上构建，则为`matrix.dll`）的文件。您可以通过如下导入语句加载它：

```
$ python
Python 2.7.5 (default, Mar 9 2014, 22:15:05)
[GCC 4.2.1 Compatible Apple LLVM 5.0 (clang-500.0.68)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import matrix
>>> m = matrix.Matrix(5,5)
>>> m.set(2,2,4)
>>> m.get(2,2)
4.0
```

---

## 使用Lua作为扩展语言

将Lua用作针对C++编写类的扩展库。

### 解决方案

Lua是一种脚本语言，旨在为C和C++代码提供扩展机制，并作为其他应用程序的嵌入式语言。在这方面，它非常成功，目前有大量软件产品使用Lua来基于现有的C和C++类库实现扩展模块。这类应用的例子可以在计算机游戏、图像处理软件包和金融行业软件中找到。

Lua的成功与其简洁的系统有关，该系统力求尽可能紧密地与C和C++环境融合。基于此目标，Lua仅提供了构建动态垃圾回收运行时系统所需的基本机制。这些语言特性使其易于被选用于为大规模应用程序代码库创建可编程扩展。

您可以从主网站 [`http://lua.org`](http://lua.org) 下载 Lua 的源代码形式。将 Lua 集成到 C++ 项目中最简单的方法是直接添加源文件。您也可以决定创建一个包含 Lua 解释器的独立库，并使用 Lua 文档中提供的信息将其链接到您的应用程序。要将 Lua 用作扩展语言，第一步是导入并初始化 Lua 运行时引擎。这可以使用 Lua C API 来完成，该 API 是 Lua 语言标准安装的一部分。主头文件 `lua.h` 使开发者能够访问运行时引擎的主要功能以及 Lua 的标准库。

以下是示例应用程序的主函数，您可以看到将 Lua 加载到程序中所需操作的顺序：

```c
int main (void) {
char buff[256];
lua_State *L = luaL_newstate();
int error;
// 加载 Lua 附带的某些 (C) 库
luaopen_base(L);
luaopen_table(L);
luaopen_io(L);
luaopen_string(L);
luaopen_math(L);
// 加载 LuaOption 对象
LuaWrapper::Register(L);
while (fgets(buff, sizeof(buff), stdin) != NULL) {
error = luaL_loadbuffer(L, buff, strlen(buff), "line") ||
lua_pcall(L, 0, 0, 0);
if (error) {
cerr << lua_tostring(L, -1) << endl;
lua_pop(L, 1);  // 从 Lua 栈中移除错误
}
lua_close(L);
return 0;
}
```

此 API 中的大多数函数都使用 `lua_State` 结构作为参数。如上述示例所示，您可以使用 `luaL_newstate` 函数创建一个新的 `lua_State` 对象。完成此初始化步骤后，您可以加载 Lua 运行时附带的某些库。可以使用 `luaopen_string` 和 `luaopen_math` 等函数选择库的子集，它们分别用于加载处理字符串和数学运算的 Lua 函数。

下一步是将您可能需要的任何用户定义库加载到 Lua 数据表中。Lua 语言的架构是将其动态信息存储在一个栈中，这是一种先进先出的数据结构。存在一个全局栈，用于存储相当于全局变量的信息。您可以根据需要使用这个栈来存储新的表。要存储单个值，您可以使用 `lua_pushstring`、`lua_pushnumber` 或 `lua_pushclosure`（用于函数）等函数将它们压入栈中。您可以使用 `lua_tonumber` 等函数从栈顶读取数据。

使用 Lua 运行时还可以做另一件事：直接调用某个 Lua 函数。要调用一个函数，您需要将想要调用的函数名称压入栈中，紧接着是所需的参数。然后，您需要调用 `lua_pcall` 函数来执行调用。您可以在前面展示的 `main` 函数末尾看到一个关于 `lua_pcall` 的例子。最后，您可以访问函数的结果，这些结果在返回时存储在栈顶。

虽然这起初看起来工作量很大，但由于 Lua 扩展 API 的通用性，实现起来轻而易举。我提供了一个示例，演示如何从 Lua 代码中使用 `Option` 类访问 C++ 类，该类仅包含两个数据成员：一个代码（字符串）和行权价格（双精度浮点数）。原始类通过 `LuaOption` 类从 Lua 访问。之所以需要这样做，是由于 Lua 只能与接收 `lua_State` 类型参数的函数交互。`LuaOption` 的每个方法都从栈中检索数据，调用 `Option` 类中的相应方法，并将结果返回到栈中。最后，`LuaOption` 类借助模板 `LuaWrap` 进行注册。

### 完整代码

代码清单 15-4 中的示例展示了如何使用 Lua API 将扩展语言嵌入到您的应用程序中。此示例中唯一暴露的类是 `Option` 类。`LuaOption` 类是 `Option` 的一个简单封装，负责在 Lua 类型与 C++ 类型之间进行参数转换。主函数具有加载 Lua 文件并调用其中任何函数的能力。

```cpp
//
//  Option.h
#ifndef __FinancialSamples__Option__
#define __FinancialSamples__Option__
#include 
class Option {
public:
Option(const std::string &ticker, double strike);
Option(const Option &p);
~Option();
Option &operator=(const Option &p);
std::string ticker();
double strike();
void setTicker(const std::string &);
void setStrike(double);
private:
std::string m_ticker;
double m_strike;
};
#endif /* defined(__FinancialSamples__Option__) */

//
//  Option.cpp
#include "Option.h"
Option::Option(const std::string &ticker, double strike)
: m_ticker(ticker),
m_strike(strike)
{
}
Option::Option(const Option &p)
: m_ticker(p.m_ticker),
m_strike(p.m_strike)
{
}
Option::~Option()
{
}

Option &Option::operator=(const Option &p)
{
if (this != &p)
{
m_ticker = p.m_ticker;
m_strike = p.m_strike;
}
return *this;
}

std::string Option::ticker()
{
return m_ticker;
}

double Option::strike()
{
return m_strike;
}

void Option::setTicker(const std::string &s)
{
m_ticker = s;
}

void Option::setStrike(double val)
{
m_strike = val;
}

//
//  LuaOption.h
#ifndef __FinancialSamples__LuaOption__
#define __FinancialSamples__LuaOption__
#include "LuaWrap.h"
class Option;
#include 
class LuaOption {
public:
LuaOption(lua_State *l);
void setObject(lua_State *l);

static const char className[];
static LuaWrapper::RegType methods[];
// Lua functions should receive lua_State and return int
int ticker(lua_State *l);
int strike(lua_State *l);
int setTicker(lua_State *l);
int setStrike(lua_State *l);
private:
Option *m_option;
};
#endif /* defined(__FinancialSamples__LuaOption__) */

//
//  LuaOption.cpp
#include "LuaOption.h"
#include "Option.h"
#include 
const char LuaOption::className[] = "Option";

LuaOption::LuaOption(lua_State *L)
{
m_option = (Option*)lua_touserdata(L, 1);
}

void LuaOption::setObject(lua_State *L)
{
m_option = (Option*)lua_touserdata(L, 1);
}

int LuaOption::ticker(lua_State *L)
{
lua_pushstring(L, m_option->ticker().c_str());
return 1;
}

int LuaOption::strike(lua_State *L)
{
lua_pushnumber(L, m_option->strike());
return 1;
}

int LuaOption::setTicker(lua_State *L)
{
m_option->setTicker((const char*)luaL_checkstring(L, 1));
return 0;
}

int LuaOption::setStrike(lua_State *L)
{
m_option->setStrike((double)luaL_checknumber(L, 1));
return 0;
}

#define method(class, name) {#name, &class::name}
LuaWrapper::RegType LuaOption::methods[] = {
method(LuaOption, ticker),
method(LuaOption, strike),
method(LuaOption, setTicker),
method(LuaOption, setStrike),
{0,0}
};

//
//  LuaWrapper.h
// original code from luna wrapper example (from http://lua-users.org/wiki/LunaWrapper)
#ifndef __FinancialSamples__Luna__
#define __FinancialSamples__Luna__
#include 
template class LuaWrapper {
public:
static void Register(lua_State *L) {
lua_pushcfunction(L, &LuaWrapper::constructor);
lua_setglobal(L, T::className);
luaL_newmetatable(L, T::className);
lua_pushstring(L, "__gc");
lua_pushcfunction(L, &LuaWrapper::gc_obj);
lua_settable(L, -3);
}
static int constructor(lua_State *L) {
T* obj = new T(L);
lua_newtable(L);
lua_pushnumber(L, 0);
T** a = (T**)lua_newuserdata(L, sizeof(T*));
*a = obj;
luaL_getmetatable(L, T::className);
lua_setmetatable(L, -2);
lua_settable(L, -3); // table[0] = obj;
for (int i = 0; T::methods[i].name; i++) {
lua_pushstring(L, T::methods[i].name);
lua_pushnumber(L, i);
lua_pushcclosure(L, &LuaWrapper::thunk, 1);
lua_settable(L, -3);
}
return 1;
}

static int thunk(lua_State *L) {
int i = (int)lua_tonumber(L, lua_upvalueindex(1));
lua_pushnumber(L, 0);
lua_gettable(L, 1);
T** obj = static_cast(luaL_checkudata(L, -1, T::className));
lua_remove(L, -1);
return ((*obj)->*(T::methods[i].mfunc))(L);
}
static int gc_obj(lua_State *L) {
T** obj = static_cast(luaL_checkudata(L, -1, T::className));
delete (*obj);
return 0;
}
struct RegType {
const char *name;
int(T::*mfunc)(lua_State*);
};
};
#endif /* defined(__FinancialSamples__LuaWrapper__) */

//
//  LuaMain.cpp
#include "LuaMain.h"
#include 
#include 
#include 
#include 
#include 
using std::cout;
using std::cerr;
using std::endl;

int main (void) {
char buff[256];
lua_State *L = luaL_newstate();
int error;
// load some of the (C) libraries included with Lua
luaopen_base(L);
luaopen_table(L);
luaopen_io(L);
luaopen_string(L);
luaopen_math(L);
// load LuaOption object
LuaWrapper::Register(L);
while (fgets(buff, sizeof(buff), stdin) != NULL) {
error = luaL_loadbuffer(L, buff, strlen(buff), "line") ||
lua_pcall(L, 0, 0, 0);
if (error) {
cerr << lua_tostring(L, -1) << endl;
lua_pop(L, 1);  // remove error from Lua stack
}
}
lua_close(L);
return 0;
}
```

清单 15-4 `Option` 类及其 Lua 封装 `LuaOption`

### 运行代码

你可以使用任何符合标准的 C++ 编译器来构建清单 15-4 中的代码。唯一的额外步骤是，你必须在编译器配置中添加 Lua 包含文件和库的路径。例如，在我的系统中，我将 Lua 文件编译到了目录 `/code/lua-5.2.3/src/` 下，因此该代码可以按如下方式构建：

```
$ c++ -o luatest Option.cpp LuaOption.cpp  LuaMain.cpp -I/Users/code/lua-5.2.3/src \
-L/Users /code/lua-5.2.3/src/ -llua
```

### 结论

诸如 Python 和 Lua 这样的扩展语言在过去几年中变得非常流行。它们提供了快速开发由现有组件构成的应用程序的能力。然而，得益于 C++ 的灵活性，正如你所见，我们可以创建易于与这些语言集成的 C++ 库。

首先，你学习了如何使用 Python 作为 C++ 类的扩展语言。通过直接使用 Python 外部 API，可以从 Python 代码中访问示例中展示的类。你学习了如何在 Python 维护的数据结构中转换数据。在第二个编码示例中，你学习了如何使用 `boost::python` 库，该库提供了一种更简洁的方式将 C++ 数据类型导出到 Python。我还讨论了每种方法的一些优缺点。

Lua 是另一种在过去几年中越来越流行的语言。凭借其小巧的体积，Lua 是作为用 C++ 编写的库的扩展语言的理想选择。由于其简单性和模块化，你可以轻松地将 Lua 解释器嵌入到 C++ 应用程序中。在本章中，你看到了一种展示如何轻松将 Lua 集成到应用程序中的 C++ 编码技术。

将你的 C++ 代码用作外部库是与其它工具和环境连接的众多方式之一。另一种选择是将你的金融 C++ 代码集成到现有的科学编程工具中。最常用于数据分析的两个科学工具是 R 和 Maxima。在第 16 章中，你将进一步了解这些工具以及如何将 C++ 集成到这些应用程序提供的工作流程中。