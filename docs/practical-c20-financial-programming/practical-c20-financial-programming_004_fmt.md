# 金融领域的 C++ 编程技术

`C++` 语言是作为 `C` 语言的扩展而设计的，这意味着大多数用 `C` 语言编写的程序同样也是有效的 `C++` 程序。然而，经验丰富的程序员通常会利用 `C++` 独有的一组高级特性来控制程序的复杂度，其中包含了 `C++20` 标准中引入的特性。这对于金融软件开发尤为重要，因为我们希望创建快速且表达力强的应用程序。

在本章中，我们将探讨一些金融程序员多年来用于编写更优 `C++` 代码的基本技术，这些技术能让他们事半功倍。这些技术是从 `C++` 提供的众多特性中精选出来的，它们在提升代码质量和表达力方面效果显著。这些特性包括：

-   `模板`：一种允许创建通用软件的特性，其类和函数可以应用于一组可能不相关的类型，只要这些类型满足特定操作所需的要求集。

-   `共享指针`：一种减少直接操作指针需求的编程技术。使用共享指针，你可以避免 `C++` 程序在管理内存和其他资源时固有的大量错误来源。

-   `运算符重载`：通过重载，你可以将语言中已有的标准运算符应用于你自己的类和结构体。

-   `C++20 特性`：`C++` 标准的最新迭代引入了许多新特性，有助于控制程序的复杂度。这些特性，包括共享指针和自动类型检测，可以轻松用于金融软件的创建。

在接下来的章节中，你将看到一些精选的编程示例，这些示例在金融应用的背景下探索了部分 `C++` 特性。

## 计算投资工具的利率

利率是固定收益投资者的一个基本概念。设计一个 `C++` 解决方案，给定一个提供诸如 `getMonthlyPayment` 和 `getPrincipal` 等方法的通用工具类，使其能够返回年利率。

### 解决方案

上述问题在设计利率计算引擎时很常见。你可以使用多种策略（如类层次结构）来创建解决方案，但出于性能和设计考虑，使用模板是组合来自代表投资工具的不相关类的利率数据最合适的方法。

`模板`是一种机制，配合特殊的语法，用于创建能处理不同底层数据类型的代码。使用模板，可以创建能够用相同代码支持不同类型的函数、成员函数和类。基于模板的编程技术生成的代码被称为通用代码，因为它可以用于不同类型（既可以是诸如 `int` 或 `double` 这样的基础类型，也可以是用户定义的类和结构体）。通用函数和类型通过尖括号中的目标类型名称来实例化，这指示了所需函数或类型的特定版本。在最新的标准修订版中，通用函数也可以使用自动参数推导来定义。

创建和使用通用代码之所以成为可能，是因为当编译器遇到模板时，它不会立即生成代码。相反，代码只会在模板对象或函数被实例化的地方生成。当这种情况发生时，编译器会检测表达式中涉及的类型，然后模板被实例化。只有在此时，传统的编译步骤（如语法分析和代码生成）才会在实例化后的代码上执行，任何由此产生的错误都会被检测到并报告给程序员。

为了解决利率计算问题，你可以使用模板来实现一个名为 `IntRateEngine` 的利率引擎类。这个类的定义方式使其可以应用于任何实现了 `getMonthlyPayment` 和 `getPrincipal` 方法的类。我包含了两个实现了这些方法的示例类：`BondInstrument` 类和 `MortgageInstrument` 类。然而，使用模板的一大优势在于，你不需要让这些类派生自某个特定的基类。你可以使用任何提供了相同方法的类，编译器会完成组合这些类的艰巨工作。这意味着投资工具和利率计算引擎之间没有耦合。事实上，如果你查看 `IntRateEngine` 的文件，不会找到任何对投资工具类的引用。

以下是 `BondInstrument` 类相关部分的快速浏览。

```
class BondInstrument {
public:
double getMonthlyPayment();
double getPrincipal();
// 此处为其他方法...
};
```

使用这个类，我们可以实例化计算年利率的模板。执行计算的模板类定义如下：

```
template
class IntRateEngine {
public:
void setInstrument(T &inv);
double getAnnualIntRate();
// 此处为其他方法...
private:
T m_instrument;
};
```

请注意，工具的类型被留空，作为类型参数 `T`。这种参数化允许不同的类与同一个模板一起使用。同样地，你可以看到 `getAnnualIntRate` 方法的实现。

```
template
double IntRateEngine::getAnnualIntRate()
{
double payment = m_instrument.getMonthlyPayment();
double principal = m_instrument.getPrincipal();
return (12 * payment) / principal;
}
```

请注意，该方法仅要求参数 `T` 提供 `getMonthlyPayment` 和 `getPrincipal` 方法。任何支持这两个方法的类型都可以被 `IntRateEngine` 无障碍地使用。

### 完整代码

前面描述的算法已在 `BondInstrument`、`MortgageInstrument` 和 `IntRateEngine` 类中实现，如代码清单 `[3-1]` 所示。

```cpp
//
//  InvestmentInstrument.h
#ifndef __FinancialSamples__InvestmentInstrument__
#define __FinancialSamples__InvestmentInstrument__
#include 
class BondInstrument {
public:
BondInstrument(double principal, double monthlyPayment);
~BondInstrument();
BondInstrument(const BondInstrument &a);
BondInstrument &operator =(const BondInstrument &a);
double getMonthlyPayment();
double getPrincipal();
// 其他方法……
private:
double
m_monthlyPay,
m_principal;
};
class MortgageInstrument {
public:
MortgageInstrument(double monthlyPay, double propertyValue, double downpayment);
~MortgageInstrument();
MortgageInstrument(const MortgageInstrument &a);
MortgageInstrument &operator =(const MortgageInstrument &a);
double getMonthlyPayment();
double getPrincipal();
// 其他方法……
private:
double
m_monthlyPay,
m_propertyValue,
m_downPayment;
};
#endif /* defined(__FinancialSamples__InvestmentInstrument__) */
//
//  InvestmentInstrument.cpp
#include "InvestmentInstrument.h"
BondInstrument::BondInstrument(double principal, double monthlyPayment)
: m_principal(principal),
m_monthlyPay(monthlyPayment)
{
}
BondInstrument::~BondInstrument()
{
}
BondInstrument::BondInstrument(const BondInstrument &a)
: m_monthlyPay(a.m_monthlyPay),
m_principal(a.m_principal)
{
}
BondInstrument &BondInstrument::operator =(const BondInstrument &a)
{
if (this != &a)
{
m_principal = a.m_principal;
m_monthlyPay = a.m_monthlyPay;
}
return *this;
}
double BondInstrument::getMonthlyPayment()
{
return m_monthlyPay;
}
double BondInstrument::getPrincipal()
{
return m_principal;
}
/////////////
MortgageInstrument::MortgageInstrument(double monthlyPay, double propertyValue, double downpayment)
: m_monthlyPay(monthlyPay),
m_propertyValue(propertyValue),
m_downPayment(downpayment)
{
}
MortgageInstrument::~MortgageInstrument()
{
}
MortgageInstrument::MortgageInstrument(const MortgageInstrument &a)
: m_downPayment(a.m_downPayment),
m_propertyValue(a.m_propertyValue),
m_monthlyPay(a.m_monthlyPay)
{
}
MortgageInstrument &MortgageInstrument::operator =(const MortgageInstrument &a)
{
if (this != &a)
{
m_downPayment = a.m_downPayment;
m_propertyValue = a.m_propertyValue;
m_monthlyPay = a.m_monthlyPay;
}
return *this;
}
double MortgageInstrument::getMonthlyPayment()
{
return m_monthlyPay;
}
double MortgageInstrument::getPrincipal()
{
return m_propertyValue - m_downPayment;
}
//
//  IntRateEngine.h
#ifndef __FinancialSamples__IntRateEngine__
#define __FinancialSamples__IntRateEngine__
#include 
template 
class IntRateEngine {
public:
~IntRateEngine();
IntRateEngine(const IntRateEngine &a);
IntRateEngine &operator =(const IntRateEngine &a);
void setInstrument(T &inv);
double getAnnualIntRate();
private:
T m_instrument;
};
template 
IntRateEngine::~IntRateEngine()
{
}
template 
IntRateEngine::IntRateEngine(const IntRateEngine &a)
: m_instrument(a.m_instrument)
{
}
template 
IntRateEngine &IntRateEngine::operator =(const IntRateEngine &a)
{
if (this != &a)
{
m_instrument = a.m_instrument;
}
return *this;
}
template 
void IntRateEngine::setInstrument(T &inv)
{
m_instrument = inv;
}
template 
double IntRateEngine::getAnnualIntRate()
{
double payment = m_instrument.getMonthlyPayment();
double principal = m_instrument.getPrincipal();
return (12 *payment) / principal;
}
#endif /* defined(__FinancialSamples__IntRateEngine__) */
//
//  main.cpp
#include "InvestmentInstrument.h"
#include "IntRateEngine.h"
#include 
int main()
{
IntRateEngine engineA;
IntRateEngine engineB;
BondInstrument bond(40000, 250);
MortgageInstrument mortgage(250, 50000, 5000);
engineA.setInstrument(bond);
engineB.setInstrument(mortgage);
std::cout << "债券年利率：" << engineA.getAnnualIntRate()*100 << "%"
<< std::endl;
std::cout << "按揭贷款年利率：" << engineB.getAnnualIntRate()*100 << "%"
<< std::endl;
return 0;
}
```

清单 3-1 `InvestmentInstrument.h`

### 运行代码

您可以使用任何符合标准的编译器编译清单 3-1 中的代码。例如，使用 `gcc` 时，可以使用以下命令行：

```
> gcc -o intRate main.cpp InvestmentInstrument.cpp IntRateEngine.cpp
```

您无需任何参数即可使用生成的测试程序。

```
> ./intRate
债券年利率：7.5%
按揭贷款年利率：6.66667%
```

## 创建财务报表对象

创建一个能计算财务报表并将其返回给调用客户端的类。同时要避免返回数据时可能出现的内存泄漏。

### 解决方案

为了解决这个问题，您将创建一个简单的财务报表类，以及一个返回指向该财务报表指针的函数。解决方案中应避免的主要问题是返回财务报表时可能发生的内存泄漏。为此，您将学习如何使用智能指针来管理内存。不过，在解释这一点之前，您需要先理解智能指针的概念。

### 智能指针

C 语言普及了指针的概念，并通过在高级语言中提供直接内存访问的简洁语法而实现了创新。指针允许以符合数据底层数据类型的方式操作内存地址。例如，可以使用递增和递减运算符让指针指向连续的内存地址。下面是一个示例：

```cpp
int numbers[200];
// 在此处初始化 numbers...
//
int *parray = numbers;
for (int i=0; i<200; ++i) {
std::cout << "value is " << *parray << "\n";
parray++;
}
```

在上面的示例中，`parray` 是一个指向整数的指针，它可用于指向内存中任意整数值的位置。特别地，你可以用它来存储 `numbers` 数组第一个元素的地址。`for` 循环中的代码随后用于打印 `parray` 当前指向的值，并通过递增运算符更新其地址，从而将指针移动到下一个整数位置。请注意，即使每个整数所占的字节数大于 1，这种操作也是可行的。实际上，递增运算符会识别所使用的指针类型，并将地址更改为指向声明类型下一个值的精确位置。

虽然指针的表示法非常强大，并且 C++ 完全支持，但程序员应尽可能避免在 C++ 代码中使用指针。多年来，指针一直与不良的编程实践相关联，这些实践可能导致资源泄漏、内存损坏以及与安全相关的错误。由于指针允许对计算机内存进行无差别访问，因此相对容易误用，从而导致难以修复的错误。

一些 C++ 类和模板为指针提供了极佳的替代方案，开销很小，且具备许多相同特性。避免使用传统指针的主要技术是使用智能指针模板。这类智能指针是简单的模板，能够以更安全的方式存储地址。例如，`std::shared_ptr` 类型的智能指针是一种模板类型对象，它允许代码中的两个或多个部分使用同一个地址。

与传统 C++ 指针相比，智能指针的主要优势在于，智能指针知道在不再需要时如何自行清理。这样一来，就能避免因程序员未释放指针而产生的一大类问题。清理机制遵循 RAII（资源获取即初始化）惯用法的规则：智能指针中包含的资源在模板对象的构造函数中初始化，并在析构时释放——这通常发生在相关对象超出作用域时。

智能指针有多种类型，每种类型都针对特定的使用场景而设计。C++ 代码中最常用的智能指针是唯一指针和共享指针。

唯一指针（模板类型 `unique_ptr`）为传统 C++ 指针提供了一个包装。然而，该模板定义了对象所有权的语义，因此在所有权转移之后，指向该指针的其他引用将不再有效。唯一指针适用于接收方将完全控制所指向对象及其关联资源的场景。因此，传递给 `unique_ptr` 对象的指针不应在 `unique_ptr` 使用环境之外的其他环境中再次被引用。

共享指针（模板类型 `shared_ptr`）是一种模板，可用于包装现有的 C++ 原生指针。与唯一指针不同，共享指针可以被代码中的两个或多个部分使用。在内部，共享指针维护着一个计数器，用于确定指向原始指针的引用已创建了多少个。这种机制称为引用计数。每当一个共享指针对象被销毁时，它会检查其内部引用计数器。如果计数器大于零，则不会删除内部对象。然而，如果引用计数器变为零，则被引用的对象会被删除，其析构函数会被激活。

在本节中，你将看到使用唯一指针的详细信息。在下一节中，我将介绍共享指针。

### 使用唯一指针

我们的内存管理问题的解决方案涉及使用唯一指针。唯一指针通过标准类 `std::unique_ptr` 实现，并且与其他智能指针一样，可用于提供自动清理和资源所有权。

`std::unique_ptr` 采用的策略确保一旦它被分配了指针的所有权，该指针中存储的内存就不再被其他任何人拥有。因此，`std::unique_ptr` 的语义包括：一旦对象超出作用域，关联数据会自动销毁。如果唯一指针的所有者不想销毁这块内存，它可以选择将其转移到另一个唯一指针，这是一个将所有权转移给另一个对象的过程。这就是我解决与返回的 `FinancialStatement` 关联内存管理问题的方法。

首先，考虑 `FinancialStatement` 类的定义。

```cpp
class FinancialStatement {
public:
FinancialStatement();
~FinancialStatement();
FinancialStatement(const FinancialStatement&);
FinancialStatement &operator=(FinancialStatement &);
double getReturn();
void addTransaction(const std::string &security, double val);
private:
double m_return;
std::vector> m_transactions;
};
```

还有一个用于创建示例财务报表的函数。

```cpp
std::unique_ptr getSampleStatement();
```

该函数返回一个指向示例报表的唯一指针。这意味着代码的调用者拥有返回的内存，因为调用者将持有一个已用指向结果对象的指针初始化过的唯一指针。`getSampleStatement` 的实现如下：

```cpp
std::unique_ptr getSampleStatement()
{
std::unique_ptr fs(new FinancialStatement);
fs->addTransaction("IBM", 102.2);
fs->addTransaction("AAPL", 523.0);
return fs;
}
```

在分配 `FinancialStatement` 对象并用它创建唯一指针之后，该对象被初始化并最终返回给调用者。由于返回语句将指针的所有权转移给了返回的对象，因此原始的 `FinancialStatement` 对象不会被销毁。相反，它现在由 `getSampleStatement` 函数的调用者拥有。

### 完整代码

清单 3-2 展示了 `FinancialStatement` 类的代码，该代码分为头文件和实现文件。

```
//
//  FinancialStatement.h
#ifndef __FinancialSamples__FinancialStatement__
#define __FinancialSamples__FinancialStatement__
#include <string>
#include <vector>
#include <memory>
class FinancialStatement {
public:
FinancialStatement();
~FinancialStatement();
FinancialStatement(const FinancialStatement&);
FinancialStatement &operator=(FinancialStatement &);
double getReturn();
void addTransaction(const std::string &security, double val);
private:
double m_return;
std::vector<std::pair<std::string, double>> m_transactions;
};
std::unique_ptr<FinancialStatement> getSampleStatement();
void transferFinancialStatement(std::unique_ptr<FinancialStatement> &statement);
#endif /* defined(__FinancialSamples__FinancialStatement__) */
//
//  FinancialStatement.cpp
#include "FinancialStatement.h"
FinancialStatement::FinancialStatement()
: m_return(0)
{
}
FinancialStatement::~FinancialStatement()
{
}
FinancialStatement::FinancialStatement(const FinancialStatement &v)
: m_return(v.m_return),
m_transactions(v.m_transactions)
{
}
FinancialStatement &FinancialStatement::operator=(FinancialStatement &v)
{
if (this != &v)
{
m_return = v.m_return;
m_transactions = v.m_transactions;
}
return *this;
}
double FinancialStatement::getReturn()
{
return m_return;
}
void FinancialStatement::addTransaction(const std::string &security, double val)
{
m_transactions.push_back(std::make_pair(security, val));
}
// returns a sample statement that includes a few common stocks
std::unique_ptr<FinancialStatement> getSampleStatement()
{
std::unique_ptr<FinancialStatement> fs(new FinancialStatement);
fs->addTransaction("IBM", 102.2);
fs->addTransaction("AAPL", 523.0);
return fs;
}
void transferFinancialStatement(std::unique_ptr<FinancialStatement> statement)
{
// perform transfer here
// ...
// statement is still valid
std::cout << statement->getReturn() << std::endl;
}
int main()
{
std::unique_ptr<FinancialStatement> fs = getSampleStatement();
// do some real work here...
return 0;
// the unique pointer is released at the end of the scope...
}
```

### 转移所有权

在前面的例子中，您看到了如何使用 `unique` 指针将对象的所有权转移给函数的调用者。这种智能指针的另一个重要用途是告知调用者：被调用函数将接管所传递对象的所有权。

例如，考虑 `transferFinancialStatement` 函数，其定义如下：

```
void transferFinancialStatement(std::unique_ptr<FinancialStatement> statement);
```

参数 `statement` 是一个指向 `FinancialStatement` 对象的 `unique` 指针，这意味着一旦它接收到该特定类型的参数，它将成为该对象的所有者。因此，`transferFinancialStatement` 可以放心地使用 `statement` 对象，因为它知道自己是其内容的唯一所有者。根据需要对对象执行的操作，这可能是一个重要的优势。

该函数的示例实现如下：

```
void transferFinancialStatement(std::unique_ptr<FinancialStatement> &statement)
{
// perform transfer here
// ...
// statement is still valid
std::unique_ptr<FinancialStatement> another = std::move(statement);
std::cout << another->getReturn() << std::endl;
// statement is released here
}
```

这里需要理解的关键点是，被指向的对象会在 `transferFinancialStatement` 结束时被销毁，因为参数已被转移，从而在函数块的末尾超出作用域。

### Unique 指针的陷阱

由于 `unique` 指针要求对引用数据拥有所有权的语义，程序员在尝试访问这种智能指针中存储的数据时，可能会发生一些错误。例如，一个常见的错误来源是使用接管指向对象所有权的函数。您可以通过我之前展示的 `transferFinancialStatement` 函数来观察这类错误。当一个 `unique` 指针对象被传递给一个接管内存所有权的函数后，该 `unique` 指针将不再有效，下次访问时可能导致程序崩溃。

```
int main()
{
std::unique_ptr<FinancialStatement> fs = getSampleStatement();
transferFinancialStatement(fs);
// the unique_ptr object is invalid here, the next access can crash the program
std::cout << fs->getReturn() << "\n";
return 0;
}
```

这个例子展示了在 `unique` 指针转移了其指向对象的所有权后，程序是多么容易崩溃。这种情况发生在您调用 `transferFinancialStatement` 函数时。

下一行试图访问返回值，这会产生无效访问。在许多平台上，这将导致段错误。为了避免这种无效访问，程序员需要谨慎区分调用接受 `unique` 指针的函数与接受原生指针的函数。

`Unique` 指针的另一个陷阱是它们对 STL 容器缺乏支持。例如，不可能将 `unique` 指针作为 `std::vector` 的成员。这是因为大多数容器通过按值复制元素来工作，这实际上会使现有 `unique` 指针中存储的数据失效。此外，许多 STL 容器的算法会对其包含的数据执行内部复制。这意味着此类算法可能会在没有预先通知的情况下销毁原始元素：这是一个非常尴尬的情况。出于这些原因，建议在处理 STL 容器时避免使用 `unique` 指针。

为了避免 `std::unique_ptr` 带来的问题，引入了另一种智能指针类型：`std::shared_ptr` 模板提供了可由不同所有者共享的指针语义。在下一节中，您将看到一个如何使用这种共享指针对象的示例。

## 确定信用评级

创建一个类来确定给定证券的信用评级。

### 解决方案

信用风险评级由认可的评级机构定义，用于确定机构的风险。借助这些信息，投资者可以确定特定债券或持股的风险水平，并控制他们愿意承担的风险程度。例如，养老基金和保险公司等风险规避型投资者通常不愿投资于任何未经认证为顶级（通常为 AAA）的机构。全球金融机构主要使用三家信用评级机构：穆迪、标准普尔和惠誉。它们不仅为公司，还为地方、州和国家层面的政府定义风险等级。

为了在 C++ 中建模信用评级，我创建了一个独立的类，封装了信用风险评级背后的基本概念。`CreditRisk` 类有一个名为 `getRating` 的成员函数，它返回特定股票或债券的当前风险评级。第二个类名为 `RiskCalculator`，用于根据投资组合中每个组成部分的一组风险评级，对特定投资组合的相关风险进行简单分析。

### 使用共享指针

由于 `RiskCalculator` 类需要维护一组信用评级，因此拥有一个容器来保存这些信息是合理的。然而，我们希望避免复制数据。由于 `CreditRisk` 类非常简单，因此复制与否差别不大。但是，类可能会变得更加复杂，在大型应用程序中，这些内存需求会逐渐累积。因此，更好的设计是避免在将 `CreditRisk` 对象添加到集合时进行复制。然而，我们已经看到，`std::unique_ptr` 不适合纳入集合，这让我们需要存储对象的传统指针。

更好的解决方案是使用 `std::shared_ptr` 类来处理与 `CreditRisk` 对象关联的内存。共享指针不仅知道如何清理指针引用的数据，还能够与其他对象共享引用。这样，直到最后一个引用也被销毁时，该对象才会被销毁。

共享指针通过使用引用计数机制来实现此行为。共享指针对象维护一个计数器，该计数器确定引用对象存在多少个副本。当共享指针被销毁时，它会检查此计数器以确定是否存在其他引用。如果计数器为正，则指向的对象不会被销毁。当创建共享指针的新副本时，计数器也会更新。计数器会递增，以指示存在对象引用的另一个副本。通过这种方式，同一个共享指针的多个副本可以存在于内存中，并且它们都将正确管理底层数据，直到最后一个副本被销毁并且原始对象从内存中移除。

我使用共享指针来处理 `RiskCalculator` 类的内存需求。`CreditRisk` 对象的集合被维护为一个共享指针向量，声明如下：

```cpp
std::vector<std::shared_ptr<CreditRisk>> m_creditRisks;
```

在此声明中，向量的每个元素都是一个共享指针。由于共享指针知道如何复制自身，因此它们可以有效地用作向量或任何其他容器的成员，这与独占指针不同。

使用共享指针很容易，因为它们会自动执行所需的清理操作。例如，复制共享指针只需使用赋值运算符即可。通过 `addCreditRisk` 成员函数向 `m_creditRisks` 向量添加新元素。

```cpp
void RiskCalculator::addCreditRisk(std::shared_ptr<CreditRisk> risk)
{
    m_creditRisks.push_back(risk);
}
```

### 完整代码

在代码清单 3-3 中，您拥有上一节描述的示例的完整代码。该类名为 `CreditRisk`，包含在一个头文件和一个实现文件中。

```cpp
//
//  CreditRisk.h
#ifndef __FinancialSamples__CreditRisk__
#define __FinancialSamples__CreditRisk__
// 一个表示信用风险评估的简单类
class CreditRisk {
public:
    // 这些是评级机构确定的风险等级
    enum RiskType {
        AAA,
        AAPlus,
        AA,
        APlus,
        A,
        BPlus,
        B,
        CPlus,
        C
    };
    // 其他方法在此处...
};
#endif /* defined(__FinancialSamples__CreditRisk__) */

//
//  RiskCalculator.h
#ifndef __FinancialSamples__RiskCalculator__
#define __FinancialSamples__RiskCalculator__
#include "CreditRisk.h"
#include <vector>
#include <memory>
// 计算与投资组合相关的风险
class RiskCalculator {
public:
    RiskCalculator();
    ~RiskCalculator();
    RiskCalculator(const RiskCalculator &);
    RiskCalculator &operator =(const RiskCalculator &);
    void addCreditRisk(std::shared_ptr<CreditRisk> risk);
    CreditRisk::RiskType portfolioMaxRisk();
    CreditRisk::RiskType portfolioMinRisk();
private:
    std::vector<std::shared_ptr<CreditRisk>> m_creditRisks;
};
#endif /* defined(__FinancialSamples__RiskCalculator__) */

//
//  RiskCalculator.cpp
#include "RiskCalculator.h"
RiskCalculator::RiskCalculator()
{
}
RiskCalculator::~RiskCalculator()
{
}
RiskCalculator::RiskCalculator(const RiskCalculator &v)
: m_creditRisks(v.m_creditRisks)
{
}
RiskCalculator &RiskCalculator::operator =(const RiskCalculator &v)
{
    if (this != &v)
    {
        m_creditRisks = v.m_creditRisks;
    }
    return *this;
}
void RiskCalculator::addCreditRisk(std::shared_ptr<CreditRisk> risk)
{
    m_creditRisks.push_back(risk);
}
CreditRisk::RiskType RiskCalculator::portfolioMaxRisk()
{
    CreditRisk::RiskType risk = CreditRisk::RiskType::AAA;
    for (int i=0; i<m_creditRisks.size(); ++i)
    {
        if (m_creditRisks[i]->getRating() < risk)
        {
            risk = m_creditRisks[i]->getRating();
        }
    }
    return risk;
}
CreditRisk::RiskType RiskCalculator::portfolioMinRisk()
{
    CreditRisk::RiskType risk = CreditRisk::RiskType::C;
    for (int i=0; i<m_creditRisks.size(); ++i)
    {
        if (m_creditRisks[i]->getRating() > risk)
        {
            risk = m_creditRisks[i]->getRating();
        }
    }
    return risk;
}
```

代码清单 3-3 `CreditRisk.h`

### 使用 `auto` 关键字

在 C++11 标准引入的众多 C++ 新增特性中，`auto` 关键字是最实用、最容易理解的特性之一。该扩展的基本思想是，编译器可以对许多类别的变量和表达式执行类型检测工作。只要可能，程序员就可以在变量声明中使用 `auto` 关键字，而不是输入所需对象的完整类型。这样，程序员可以决定让编译器自动进行类型检测，而仅在需要且方便进行视觉标记时才使用类型名称。

与其他形式的类型声明相比，`auto` 关键字具有几个优点。它：

-   减少了程序员的手动工作量，因为它使用编译器本身来分析表达式并确定特定变量或表达式的确切类型

-   增加了代码的统一性，因为大多数变量使用相同的风格声明

-   保留了程序员根据需要输入确切类型的能力，从而可以避免任何冲突

-   简化了模板的使用，因为数据类型可以在模板的每次实例化期间在编译时自动检测

作为使用 `auto` 关键字的示例，我们可以修改 `RiskCalculator` 类中的某些成员函数，以执行变量类型的自动检测。这对于简化在使用模板集合时非常常见的某些表达式很有用。例如，以下是 `portfolioMaxRisk` 成员函数：

```
CreditRisk::RiskType RiskCalculator::portfolioMaxRisk()
{
auto risk = CreditRisk::RiskType::AAA;
for (auto &p : m_creditRisks)
{
if ((*p)->getRating() < risk)
{
risk = (*p)->getRating();
}
}
return risk;
}
```

这段代码片段展示了如何自动检测局部变量`risk`的类型，因此您无需输入重复的类型名称`CreditRisk::RiskType`。类似地，下一行展示了如何使用`auto`关键字遍历对象集合以确定迭代器的正确类型。经验丰富的 STL 程序员知道，使用迭代器可能会为一段代码引入许多额外的类型，这常常会使程序的原始意图变得模糊不清。作为对比，请注意，如果没有`auto`关键字，上述`for`循环需要写成：

```
for (std::vector<std::shared_ptr<CreditRisk>>::iterator p = m_creditRisks.begin();
p != m_creditRisks.end(); ++p)
{
// ...
}
```

这不仅更难手动输入，而且使代码更难理解和修改。

## 收集交易数据

创建一个解决方案来处理交易订单的问题，包括`BUY`、`SELL`或`SELL SHORT`，这些订单存储在一个文件中。该解决方案必须正确处理编程异常。

### 解决方案

为解决这个问题，我们创建了一个能处理交易事务并执行必要操作的类。该类负责接收文件名并执行文件中存储的指令，同时负责处理此过程中发生的任何错误，包括文件读取错误以及发送给应用程序的错误交易请求。

在此编码示例中，允许的操作很简单，仅包括买入、卖出和卖空。因此，我将重点放在处理文件和应对程序中意外错误的问题上。我们遵循使用 C++异常处理机制的最佳实践，创建了一个名为`TransactionHandler`的新类，该类能够从文件中读取数据，并在成员函数`handleTransactions`中执行必要的操作。最终的代码能够执行文件中存储的交易操作，并使用 C++语言提供的`try`/`catch`/`throw`机制处理可能出现的异常，详情见下一节。

### 异常处理

程序员面临的基本问题之一是检测错误并从错误中恢复。虽然我们努力避免自身造成的错误，但即使正确的程序也需要处理许多异常情况。例如，当文件系统已满、没有更多空间保存当前文件时，代码应该怎么办？当网络连接关闭，服务器无法完成下载时，又能做些什么？程序员需要决定如何应对此类异常情况，多年来已设计出多种策略来应对这些状况。

C++采用基于异常的模型来处理程序执行期间出现的意外情况。该模型使用标准的`try`/`catch`块来封装需要保护的代码。当异常发生时，编译器会抛出一个对象来指示意外情况。因此，包围的代码块也会销毁在该特定上下文中创建的所有局部对象，以避免资源泄露。

C++异常处理的另一个方面是使用异常对象来告知程序员所发生错误的类别。这些对象使用`throw`关键字创建，并通过`catch`块捕获，该块接收异常对象的引用，并利用它来理解并可能从意外状态中恢复。应用程序可以自由创建新的异常类，以提供关于触发异常的错误的额外信息。

在此示例中，我创建了两个异常类。它们都派生自`std::runtime_error`，该类提供了运行时异常的基本行为。第一个类`FileError`用于标记读取文件过程中发生的任何错误。其定义如下：

```
class FileError :public std::runtime_error {
public:
FileError (const std::string &description);
};
```

第二个异常类`TransactionTypeError`在文件中发现除`TRANSACTION_SELL`、`TRANSACTION_BUY`或`TRANSACTION_SHORT`之外的未知交易类型时抛出。其定义与`FileError`类似。这些类随后在测试代码中用于确定遇到的错误类型以及后续操作。在测试程序中，我们在终止程序前仅使用`what()`成员函数返回的字符串打印一条描述性的错误信息。

```
try
{
TransactionHandler handler(fileName);
}
catch (FileError &e)
{
std::cerr << "received a file error: " << e.what() << std::endl;
}
catch (TransactionTypeError &e)
{
std::cerr << "received a transaction error: " << e.what() << std::endl;
}
catch (...)
{
std::cerr << "received an unknown error\n";
}
```

### 完整代码

清单 3-4 提供了一个关于异常处理的完整示例。这里展示的类演示了在读取交易文件时如何处理异常。末尾提供了一个示例`main()`函数，展示了这些类如何协同工作。

```
//
//  TransactionHandler.h
#ifndef __FinancialSamples__TransactionHandler__
#define __FinancialSamples__TransactionHandler__
#include 
enum TransactionType {
TRANSACTION_SELL,
TRANSACTION_BUY,
TRANSACTION_SHORT,
};
class FileError :public std::runtime_error {
public:
FileError(const std::string &s);
};
class TransactionTypeError :public std::runtime_error {
public:
TransactionTypeError(const std::string &s);
};
class TransactionHandler {
public:
static const std::string SELL_OP;
static const std::string BUY_OP;
static const std::string SHORT_OP;
TransactionHandler(const std::string &fileName);
TransactionHandler(const TransactionHandler &);
~TransactionHandler();
TransactionHandler &operator=(const TransactionHandler&);
void handleTransactions();
private:
std::string m_fileName;
};
#endif /* defined(__FinancialSamples__TransactionHandler__) */
//
//  TransactionHandler.cpp
#include "TransactionHandler.h"
#include 
FileError::FileError(const std::string &s)
: std::runtime_error(s)
{
}
TransactionTypeError::TransactionTypeError(const std::string &s)
: std::runtime_error(s)
{
}
const std::string TransactionHandler::SELL_OP = "SELL";
const std::string TransactionHandler::BUY_OP = "BUY";
const std::string TransactionHandler::SHORT_OP = "SHORT";
TransactionHandler::TransactionHandler(const std::string &fileName)
: m_fileName(fileName)
{
}
TransactionHandler::TransactionHandler(const TransactionHandler &a)
: m_fileName(a.m_fileName)
{
}
TransactionHandler::~TransactionHandler()
{
}
TransactionHandler &TransactionHandler::operator=(const TransactionHandler&a)
{
if (this != &a)
{
m_fileName = a.m_fileName;
}
return *this;
}
void TransactionHandler::handleTransactions()
{
std::ifstream file;
file.open(m_fileName, std::ifstream::in);
if (file.fail())
{
throw new FileError(std::string("error opening file ") + m_fileName);
}
std::string op;
file >> op;
while (file.good() && !file.eof())
{
if (op != SELL_OP && op != BUY_OP && op !=  SHORT_OP)
{
throw new TransactionTypeError(std::string("unknown transaction ") + op);
}
// process remaining transaction data...
}
}
//
//  main.cpp
#include "TransactionHandler.h"
#include 
int main(int argc, const char **argv)
{
if (argc  \n";
return 1;
}
std::string fileName = argv[1];
try
{
TransactionHandler handler(fileName);
}
catch (FileError &e)
{
std::cerr << "received a file error: " << e.what() << std::endl;
}
catch (TransactionTypeError &e)
{
std::cerr << "received a transaction error: "
<< e.what() << std::endl;
}
catch (...)
{
std::cerr << "received an unknown error\n";
}
return 0;
}
```

### 实现向量运算

在本节中，我们实现数值向量上定义的常见加法和乘法运算符。

### 解决方案

利用 C++的运算符重载功能可以轻松解决此问题。首先我会对这种编程技巧进行通用介绍，然后展示如何用它来实现数值向量运算。

### 运算符重载

大多数编程语言都使用运算符为常见操作提供更简洁的语法。例如，`+` 运算符用于实现数字的加法，无需使用名为 `sum` 的函数。因此，我们可以输入以下表达式：

```
int total = a + b + c + d;
```

而不是下面这种不够便捷的版本：

```
int total = sum(a, sum(b, sum(c, d)));
```

类似地，其他运算符也为其他基本操作执行类似任务，例如减法、乘法、逻辑比较和指针运算。

虽然大多数现代编程语言都提供运算符，但 C++ 是少数允许程序员重新定义现有运算符含义的语言之一，使其适应目标领域中最自然的用法。例如，在向量是常见数据结构的应用中，重新定义 `+` 运算符以执行向量加法，同时保留其传统标量数值加法功能，是合理的做法。

C++ 允许为每个新声明的类型定义运算符。运算符可以定义为类的一部分（作为成员函数），也可以定义为独立函数。例如，考虑一个重新定义了 `+` 运算符的 `Complex` 类。该运算符的声明可以写成：

```
class complex {
public:
// ... 此处为其他方法
complex &operator +(const complex &v);
};
```

编写相同运算符的另一种方式是使用自由函数：

```
Complex &operator +(const complex &a, const complex &b);
```

这两种声明之间的区别在于，后者声明了一个接收两个参数的函数，而成员函数版本只需要一个额外参数（第一个参数是对象本身）。类似地，你可以声明大多数 C++ 运算符的新版本，包括数学、逻辑和指针运算符。

为了解决提出的问题，我们创建了一个名为 `NumVector` 的新类，它是一个简单的数值向量，可用于存储双精度浮点数。为了以自然的方式提供可应用于向量的操作，我们使用声明为自由函数的运算符：

```
NumVector operator +(const NumVector &a, const NumVector &b);
NumVector operator -(const NumVector &a, const NumVector &b);
NumVector operator *(const NumVector &a, const NumVector &b);
```

该类还提供了一些成员函数，用于实现这些操作。具体来说，你需要拥有成员函数 `add`（用于向向量添加新元素）、`removeLast`（用于移除最后一个元素）、`get`（用于根据位置（索引）返回其中一个元素），以及一个 `size` 成员函数（用于返回向量的大小）。

所有运算符都以类似的方式实现：它们都检查两个参数是否具有相同的大小，然后执行一个循环，用于执行所需的操作——加法、减法或乘法。

### 完整代码

列表 3-5 提供了一个数值向量的示例实现。你可以使用此类创建数值向量并执行常见的向量操作。

```
//
//  NumVector.h
#ifndef __FinancialSamples__NumVector__
#define __FinancialSamples__NumVector__
#include 
class NumVector {
public:
NumVector();
~NumVector();
NumVector(const NumVector &);
NumVector &operator =(const NumVector &);
void add(double val);
void removeLast();
double get(int pos) const;
size_t size() const;
private:
std::vector m_values;
};
NumVector operator +(const NumVector &a, const NumVector &b);
NumVector operator -(const NumVector &a, const NumVector &b);
NumVector operator *(const NumVector &a, const NumVector &b);
#endif /* defined(__FinancialSamples__NumVector__) */
//
//  NumVector.cpp
#include "NumVector.h"
#include 
NumVector::NumVector()
{
}
NumVector::~NumVector()
{
}
NumVector::NumVector(const NumVector &v)
: m_values(v.m_values)
{
}
NumVector &NumVector::operator=(const NumVector &v)
{
if (this != &v)
{
m_values = v.m_values;
}
return *this;
}
size_t NumVector::size() const
{
return m_values.size();
}
double NumVector::get(int pos) const
{
return m_values[pos];
}
void NumVector::add(double val)
{
m_values.push_back(val);
}
void NumVector::removeLast()
{
m_values.pop_back();
}
NumVector operator +(const NumVector &a, const NumVector &b)
{
if (a.size() != b.size())
{
throw new std::runtime_error("vectors must have the same size");
}
NumVector result;
for (int i=0; i<a.size(); ++i)
{
result.add(a.get(i) + b.get(i));
}
return result;
}
NumVector operator -(const NumVector &a, const NumVector &b)
{
if (a.size() != b.size())
{
throw new std::runtime_error("vectors must have the same size");
}
NumVector result;
for (int i=0; i<a.size(); ++i)
{
result.add(a.get(i) - b.get(i));
}
return result;
}
NumVector operator *(const NumVector &a, const NumVector &b)
{
if (a.size() != b.size())
{
throw new std::runtime_error("vectors must have the same size");
}
NumVector result;
for (int i=0; i<a.size(); ++i)
{
result.add(a.get(i) * b.get(i));
}
return result;
}
```

列表 3-5 `NumVector.h`

### 结论

C++是一种复杂的语言，它提供了使用多种范式（如结构化、面向对象和函数式编程）中的一种或多种来创建软件的机制。因此，有必要开发一套更适用于金融软件开发的技术，同时避免那些混淆程序、妨碍我们修改程序效率低下的做法。多年来，金融工程师成功运用了许多 C++惯用法，使得更易于利用该语言的速度和抽象特性。

在本章中，我讨论了一些编程示例，介绍并回顾了金融应用开发中常用的一些有用的 C++编程技术。首先，我回顾了模板的概念，它允许程序员编写可应用于多个类的通用代码。在第一个代码示例中，你学习了如何设计一个独立于利率类定义之上的利率计算引擎。这种设计在创建大规模金融应用时非常有用。

接下来，你学习了如何定义可以发送到应用程序其他部分的财务报表对象，同时减少内存泄漏的发生。为此，我解释了如何使用智能指针作为自动决定对象生命周期的一种方式。具体来说，你学习了`std::unique_ptr`模板，它实现了自动释放、自有指针的语义。

下一节再次讨论了内存管理问题，这次是在根据评级机构确定信用评级的背景下。在这种情况中，你学习了如何共享评级信息，使得即使多个用户拥有该对象的副本，内存也能自动销毁。为此，你可以了解`std::shared_ptr`模板，它使用引用计数机制来确定删除内存的正确时机，从而避免内存泄漏。你还看到了使用`auto`关键字来简化 STL 容器的类型检测。`shared_ptr`和`auto`关键字都是 C++11 标准引入的新特性，目前所有现代 C++编译器都已实现该标准。

C++中另一项重要技术涉及意外情况的处理。C++提供的基于异常的机制允许程序员以简洁的方式处理不常见的情况。你已经看到了一个将此策略应用于处理存储在数据文件中的交易操作问题的示例。

最后，本章考虑了应用于数字序列的数学运算的实现问题。为此，我们创建了一个名为`NumVector`的新类，它以序列形式存储数字。为了以自然的方式实现向量的加法、减法和乘法，我们使用了 C++提供的运算符重载机制。通过这种方式，应用程序可以使用语言中已有的运算符来执行向量运算，这些运算符经过重新定义后，可以应用于你自己的类型。

在本章中，你了解了已在金融应用中成功使用的一般 C++技术。在下一章中，我将解释如何使用扩展语言并为金融软件开发人员提供有用功能的库。这些库包括新数据容器和算法等功能，以及更高级的内存管理、时间和事件处理技术。