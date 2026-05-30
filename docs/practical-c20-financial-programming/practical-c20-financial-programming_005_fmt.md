# 4. 金融应用的常用库

用 C++实现的金融代码使用编程库来简化快速、符合标准类的创建。此类库的最佳例子就是 STL（标准模板库）本身，这是一个附带在符合标准的 C++编译器中的便捷库。STL 提供了一套通用的、常用的容器，几乎可以应用于任何情况。了解如何很好地运用 STL 是进行高效 C++编程所需的主要技能之一，尤其是在高性能软件开发（这是金融应用的常见要求）的背景下。在本章中，你将学习一些编程示例，阐明 STL 在金融编程中的一些最常见用途，包括容器和算法。

Boost 项目提供了另一套常用的类。尽管标准语言委员会并未官方支持 Boost 库，但其中一些已被用作最近几个版本 C++标准库新增功能的基础。因此，充分理解 Boost 库中包含的类和模板很有价值，这可以让你提前访问那些后来才会在所有 C++实现中可用的功能。

在接下来的几个小节中，我们将探讨一些 C++示例，演示这些类和模板如何在金融应用中使用。这里探讨的重要库组件示例包括：

- **向量（Vectors）**：这些是用于操作同类型对象的容器。
- **映射（Maps）**：STL 容器，可用于将值关联到一组键，键可以是任何类型。
- **算法（Algorithms）**：STL 还提供了一套丰富的算法，可用于操作标准容器。你也可以扩展现有算法，使其应用于你自己的数据结构。同样，你可以利用 STL 提供的思路来实现你自己的算法。
- **Boost 库**：STL 是其他有用库的基础。Boost 库由一些在语言委员会工作的 C++专家编写。许多以前包含在 Boost 库中的组件，如`shared_ptr`，后来已成为语言的一部分。
- **时间和日期处理**：金融应用通常与处理特定时间段内的价格有关。为此，有必要使用库来统一处理日期。

在接下来的几节中，你将看到几个精选的编程示例，它们探讨了这些 C++库在金融应用背景下的使用。

## 处理分析师推荐

围绕某只股票的常见事件之一就是分析师推荐的发布。创建一个处理分析师推荐并返回该股票平均目标价的 C++类。

### 解决方案

分析师推荐是华尔街生态系统的重要组成部分。许多金融机构，如养老基金和保险公司，以及散户投资者，都将分析师的推荐作为衡量关于某只股票主流观点的标尺。这反过来又可用于决定未来对股票组合的资本配置。

分析师推荐来自提供股票投资公开分析的几家机构之一，通常来自一些主要的投资银行。对某只特定股票的推荐包括一个明确的动作，如“买入”、“卖出”或“持有”。推荐通常还包括一个目标价，它决定了分析师认为该工具的“公平价格”是多少。

由于有这么多分析师覆盖股票市场，跟踪推荐是机构投资者工作的重要组成部分。在本节中，您将创建一个 C++类来存储此类信息，并回答一些基本问题，例如“某只特定股票的平均目标价是多少？”

这个问题的解决方案涉及使用 STL 容器来保存数据。具体来说，你将使用向量来提供对数据的快速访问。

### 关于 STL 向量和映射的更多内容

STL 是一个包含标准数据结构和算法的代码库，在大多数编程领域都非常有用。特别是在金融应用中，使用 STL 极为重要，因为其组件针对高速运算进行了优化。例如，在 C++ 中编写应用程序时，目前首选的方法是使用像向量这样的 STL 组件，而不是使用原始的 C++ 数组或类似的数据容器类。编译器供应商在使 STL 组件在各种应用中既快速又安全方面做得非常出色，因此程序员无需担心诸如内存分配、异常处理和算法复杂度等复杂问题。

STL 向量用途广泛，因为它们可以动态增长。因此，当你不清楚底层数据结构中将存储多少个元素时（只要你不介意向量调整大小带来的开销），它们就能派上用场。例如，如果你正在读取特定时间段内的交易数据，你通常不知道在该特定时间框架内发生了多少笔交易。在这种情况下，使用一个可以初始化为少量元素然后根据需要增长的向量，会比使用一个预定义（且固定）大小的数组更容易。通过这种方式使用 STL 向量，你无需担心容器的内存分配和异常安全性。

向量模板暴露了一个接口，其中包含可应用于一组元素的操作，例如添加、移除、查找和比较。这些常见操作可以在不同的具体实现中通用，无需进行任何手动修改。例如，声明为`std::vector<int>`的向量可以使用`push_back`成员函数将`int`元素添加到向量的末尾。同样，声明为`std::vector<std::string>`的另一个向量也可以使用相同的基于模板的成员函数添加`std::string`元素。

以下是`std::vector`模板中最常用的成员函数的快速列表：

*   `template <class T> vector(const T &c, int n)`：构造函数，用于创建一个新向量，并用常量元素`c`的`n`个副本进行初始化。
*   `template <class T> vector(const vector<T> &v)`：构造函数，用于创建一个新向量，并用现有向量对象`v`的副本进行初始化。
*   `template <class T> T &operator [] (int pos)`：该运算符使`std::vector`对象的行为类似于原生数组。你可以使用`v[i]`表示法来访问或更新存储在向量`v`中位置`i`处的元素值。
*   `template <class T> void push_back(const T &c)`：用于在向量末尾添加一个新元素，必要时会分配额外的内存。这是向向量添加新元素最常用的方法，因为它在必要时会负责分配内存来存储新元素，这与`operator []`不同，后者在访问未定义位置时会导致应用程序崩溃。
*   `template <class T> void pop_back()`：此成员函数执行与`push_back`相反的操作，移除存储在向量最后一个位置的元素。但是，为该元素分配的内存不会立即被回收，可能会被后续操作使用。
*   `template <class T> size_t size()`：此成员函数返回当前存储在`std::vector`中的元素数量。请注意，这可能小于向量当前使用的总内存，因为根据之前添加或预留的元素数量，向量可能分配了比当前需求更多的内存。

在我们的问题中，我们使用向量来存储针对特定股票的建议。该类涵盖的每只股票都会有一个建议向量。每个建议只是`Recommendation`类的一个对象，该类存储按以下方式定义的建议：

```cpp
enum class RecommendationType {
BUY,
SELL,
HOLD,
NO_RECOMMENDATION
};
```

`Recommendation`类定义为：

```cpp
class Recommendation {
public:
Recommendation();
Recommendation(const std::string &ticker, RecommendationType rec, double target);
~Recommendation();
Recommendation(const Recommendation &r);
Recommendation & =(const Recommendation &r);
double getTarget() const;
RecommendationType getRecommendation() const;
std::string getTicker() const;
// 私有成员
};
```

这个简单的`Recommendation`类存储了股票的代码，以及其建议类型和目标价格。请注意，存储在`std::vector`中的对象必须来自可复制或可移动的类，因为`std::vector`中的元素是按值存储的。这意味着，每当需要将对象移动到某个位置时，就会创建一个副本，除非该类具有移动构造函数。

使用`std::vector`我们可以跟踪针对某只股票的单个建议。但是，我们想要跟踪的股票池中有多只股票。为了找到正确的建议，我们分配了一种基于股票代码检索对象的方法。这是通过使用`std::map`模板来完成的。使用映射还将简化实现其他有用操作所需的代码，例如添加新建议或计算平均建议。

`std::map`模板提供了一种将任意键关联到数据对象的方法，以便你可以轻松检索原始数据。映射最棒的地方在于，键可以是任何提供比较操作（例如小于（`<`）运算符）的对象。例如，在我们的例子中，你可以使用一个字符串来指示每只特定股票的唯一切入点。基于该代码，你可以检索包含该特定股票建议的向量。

以下是`std::map`定义的重要操作的一个简短列表：

*   `template <class K, class T> iterator<T> find(const K&)`：返回一个指向与给定键关联的数据对象的迭代器。如果该键没有关联的对象，该函数返回`end()`迭代器。
*   `template <class K, class T> T &operator[](const K&)`：将一个键与一个特定的数据对象关联起来。你可以使用此运算符从映射容器中检索元素以及插入新元素。

*   `template <class K, class T> size_t erase(const K&)`：删除与给定键关联的数据。

`std::map` 模板在此代码示例中用于存储和检索针对特定股票发布的建议。`RecommendationProcessor` 是负责存储、处理以及回答与股票建议相关的查询的类。`RecommendationProcessor` 使用的一般算法包括将新建议存储在一个内部数据结构中。然后，在未来的任何时刻，你可以使用 `averageTargetPrice` 成员函数查询存储的数据。

此类中的第一个成员函数 `addRecommendation` 负责将新建议存储到 `m_recommendations` 成员变量中。

```
void RecommendationsProcessor::addRecommendation(const std::string &ticker,
RecommendationType rec, double targetPrice)
{
Recommendation r(ticker, rec, targetPrice);
m_recommendations[ticker].push_back(r);
}
```

在这里，首先需要根据传入的信息（如股票代码、建议类型和目标价格）创建一个新的建议对象。然后，我们使用 `m_recommendations` 映射来访问建议向量。最后，我们使用 `push_back` 方法将新建议添加到该特定股票的建议列表中。

另一个有趣的成员函数是计算平均目标价格的函数。它会查看所有建议以计算平均目标。

```cpp
double RecommendationsProcessor::averageTargetPrice(const std::string &ticker)
{
if (m_recommendations.find(ticker) == m_recommendations.end())
return 0;
auto vrec = m_recommendations[ticker];
std::vector prices;
for (auto i=0; i<vrec.size(); ++i)
{
prices.push_back(vrec[i].getTarget());
}
return std::accumulate(prices.begin(), prices.end(), 0) / prices.size();
}
```

在这段代码中，你需要做的第一件事是检查该股票是否有任何推荐。如果没有，函数返回零值。否则，你可以使用 `[]` 运算符获取推荐。我将所有目标价格添加到一个临时价格向量中，并使用 `std::accumulate` 算法计算所有价格的总和。最后，该成员函数返回总和除以这些价格推荐的数量，也就是所需的平均目标价格。

### 完整代码

清单 4-1 展示了上一节描述的 `Recommendation` 类的完整代码。该清单展示了你需要将此类包含到项目中所需的头文件和实现文件。

```cpp
//
//  Recommendation.h
#ifndef __FinancialSamples__Recommendation__
#define __FinancialSamples__Recommendation__
#include 
enum RecommendationType {
BUY,
SELL,
HOLD,
NO_RECOMMENDATION
};
class Recommendation {
public:
Recommendation();
Recommendation(const std::string &ticker, RecommendationType rec, double target);
~Recommendation();
Recommendation(const Recommendation &r);
Recommendation &operator =(const Recommendation &r);
double getTarget() const;
RecommendationType getRecommendation() const;
const std::string &getTicker() const;
private:
std::string m_ticker;
RecommendationType m_recType;
double m_target;
};
#endif /* defined(__FinancialSamples__Recommendation__) */
//
//  Recommendation.cpp
#include "Recommendation.h"
Recommendation::Recommendation()
: m_recType(HOLD),
m_target(0)
{
}
Recommendation::Recommendation(const std::string &ticker, RecommendationType rec, double target)
: m_ticker(ticker),
m_recType(rec),
m_target(target)
{
}
Recommendation::~Recommendation()
{
}
Recommendation::Recommendation(const Recommendation &r)
: m_ticker(r.m_ticker),
m_recType(r.m_recType),
m_target(r.m_target)
{
}
Recommendation &Recommendation::operator =(const Recommendation &r)
{
if (this != &r)
{
m_ticker = r.m_ticker;
m_recType = r.m_recType;
m_target = r.m_target;
}
return *this;
}
double Recommendation::getTarget() const
{
return m_target;
}
RecommendationType Recommendation::getRecommendation() const
{
return m_recType;
}
const std::string &Recommendation::getTicker() const
{
return m_ticker;
}
//
//  RecommendationsProcessor.h
#ifndef __FinancialSamples__RecommendationsProcessor__
#define __FinancialSamples__RecommendationsProcessor__
#include 
#include 
#include "Recommendation.h"
class RecommendationsProcessor {
public:
RecommendationsProcessor();
~RecommendationsProcessor();
RecommendationsProcessor(const RecommendationsProcessor &);
RecommendationsProcessor &operator =(const RecommendationsProcessor &);
void addRecommendation(const std::string &ticker, RecommendationType rec, double
targetPrice);
double averageTargetPrice(const std::string &ticker);
RecommendationType averageRecommendation(const std::string &ticker);
private:
std::map > m_recommendations;
};
#endif /* defined(__FinancialSamples__RecommendationsProcessor__) */
//
//  RecommendationsProcessor.cpp
#include "RecommendationsProcessor.h"
#include 
RecommendationsProcessor::RecommendationsProcessor()
{
}
RecommendationsProcessor::~RecommendationsProcessor()
{
}
RecommendationsProcessor::RecommendationsProcessor(const RecommendationsProcessor &r)
: m_recommendations(r.m_recommendations)
{
}
RecommendationsProcessor &RecommendationsProcessor::operator
=(const RecommendationsProcessor &r)
{
if (this != &r)
{
m_recommendations = r.m_recommendations;
}
return *this;
}
void RecommendationsProcessor::addRecommendation(const std::string &ticker,
RecommendationType rec, double targetPrice)
{
Recommendation r(ticker, rec, targetPrice);
m_recommendations[ticker].push_back(r);
}
double RecommendationsProcessor::averageTargetPrice(const std::string &ticker)
{
if (m_recommendations.find(ticker) == m_recommendations.end())
return 0;
auto vrec = m_recommendations[ticker];
std::vector prices;
for (auto i=0; i recommendations;
for (auto i=0; i<vrec.size(); ++i)
{
recommendations.push_back((int)vrec[i].getRecommendation()+1);
}
return (RecommendationType) (int) (avg / recommendations.size());
}
```

清单 4-1 `Recommendation` 类的定义与实现

## 执行时间序列变换

创建一个可用于执行常见时间序列变换的类，例如对价格进行加减运算以及移除不需要的值。

### 解决方案

时间序列数据过滤，是指识别并移除一系列数据点中的值（包括短期和长期趋势）的任务。从编程角度来看，这是通过对存储在容器中的数据进行一系列转换来完成的。这是一个非常常见的任务，STL（标准模板库）对此提供了恰当的支持。STL 提供了多个模板，这些模板简化了诸如排序和选择等常见算法的执行，这些算法可应用于向量和列表等数据容器。在 C++ 代码中，通过包含头文件 `<algorithm>` 即可访问这些算法。程序员无需进行额外的工作。

`<algorithm>` 头文件为许多有用的函数提供了声明。其中包括：

- `copy`：此模板函数用于将指定容器中的元素范围复制到第二个容器。请注意，与其他通用算法一样，容器不必是同一类型。例如，可以将一个向量的范围复制到一个映射中，反之亦然。

- `copy_backward`：与 `copy` 函数类似，但处理过程是从指定范围的最后一个元素到第一个元素。例如，这可用于按相反顺序写入容器的元素。

- `for_each`：此算法可用于对容器中的一组元素应用特定的函数或函数对象。`for_each` 算法可用于避免对容器元素使用 `for` 循环。如果循环定义的操作可以快速封装在函数或函数对象中，那么 `for_each` 算法是执行相同操作的一种更简洁的方式。

- `find_if`：用于在通用容器的指定范围内查找元素。`find` 算法的最后一个参数可以是一个函数或函数对象，用于确定所需元素满足的属性。

- `count`：返回通用容器指定范围内元素的数量。

- `count_if`：返回通用容器指定范围内满足作为第三个参数传入的函数或函数对象的元素数量。

- `transform`：此通用函数接受一个容器中的元素范围、一个目标容器和一个转换函数对象。输入容器的元素使用转换函数进行转换，并存储在目标容器中。

- `fill`：此函数用于用单个元素填充容器的指定范围。

- `reverse`：改变通用容器的指定范围，使得容器中元素的顺序反转。

- `sort`：一个通用算法，可用于对存储在 STL 容器中的元素序列进行排序。该算法的前两个参数定义了元素的范围。第三个参数是一个比较函数，用于确定两个元素是否有序正确。

- `binary_search`：在容器中已排序的范围内对元素执行二分查找。

- `min_element`：返回给定容器中存储的元素中具有最小值的元素。

- `max_element`：返回给定容器中存储的元素中具有最大值的元素。

这些是 STL 提供的最常见的算法。虽然这些算法在大多数情况下编写简单，但使用 STL 算法模板相比手动实现有以下一些优点：

- 第一个优点是，它们减少了在实现类似操作时出错的可能性。例如，`for_each` 算法只是将同一个函数应用于一个范围内的所有元素。虽然使用 `for` 循环容易做到这一点，但在操作容器的单个元素时，总是存在出错的可能性。然而，`for_each` 算法将所有逻辑都包含在模板定义中，从而减少了出错的可能性。

- STL 中的算法对容器的工作原理有深入的了解。STL 的实现者理解容器的细微差别，这可以大大提升这些算法的性能。通过使用偏特化，STL 的作者可以针对每个容器定制这些算法，以实现最佳性能。使用 STL，您可以在应用程序中自动利用这些知识。

- 算法也是一种简洁地描述您意图的方式。例如，您无需编写另一个 `for` 循环来查找最小元素，而是可以对目标容器应用 `min_element` 算法。

## 使用 STL 算法

为了解决本节中提出的问题，您将创建一个类 `TimeSeriesTransformations`，它实现了一些时间序列转换操作。实现的第一个算法用于减少序列中的价格。该解决方案依赖于 `std::transform` 算法，实现如下：

```cpp
void TimeSeriesTransformations::reducePrices(double val)
{
    std::vector res;
    std::transform(m_prices.begin(), m_prices.end(), res.begin(),
                   std::bind2nd(std::minus(), val));
    m_prices.swap(res);
}
```

在这个成员函数中，第一步是将 `std::transform` 应用于向量 `m_prices`。前两个参数是向量的起始和结束迭代器。然后，您需要传递输出向量的起始迭代器。最后，最后一个参数是用于执行转换的函数对象。模板 `std::bind2nd` 在 `<functional>` 头文件中声明，它允许绑定函数对象的第二个参数（此例中为 `minus` 函数）。结果是，`minus` 函数将被应用于 `m_prices` 向量，其第二个参数被设置为 `val` 定义的值。执行转换后，下一步是将 `m_prices` 中存储的值与结果向量中存储的值进行交换。

第二个函数类似，但我使用了另一种策略。

```cpp
void TimeSeriesTransformations::increasePrices(double val)
{
    std::for_each(m_prices.begin(), m_prices.end(), std::bind1st(std::plus(), val));
}
```

这里，使用函数模板 `std::for_each` 对原始向量的每个元素执行转换。通过这种方式，您可以避免在容器内外交换值的需要。`for_each` 函数应用 `plus` 函数，其第一个参数被绑定到传入的值。结果，所有价格都按预期增加了。

`TimeSeriesTransformations` 类还包含一些其他探索 STL 算法的方法。例如，`removePricesLessThan` 方法使用 `remove_if` 模板来消除小于给定值的价格。成员函数 `removePricesGreaterThan` 类似。最后，`getFirstPriceLessThan` 函数使用 `find_if` 模板函数来识别小于给定值的价格（如果存在这样的价格）。

### 完整代码

前面描述的算法已通过一个名为 `TimeSeriesTransformations` 的 C++ 类实现。它分为一个头文件 `TimeSeriesTransformations.h` 和一个源文件 `TimeSeriesTransformations.cpp`。清单 4-2 包含了完整代码。

```
//
//  TimeSeriesTransformations.h
#ifndef __FinancialSamples__TimeSeriesAnalysis__
#define __FinancialSamples__TimeSeriesAnalysis__
#include 
class TimeSeriesTransformations {
public:
TimeSeriesTransformations();
TimeSeriesTransformations(const TimeSeriesTransformations &);
~TimeSeriesTransformations();
TimeSeriesTransformations &operator=(const TimeSeriesTransformations &);
void reducePrices(double val);
void increasePrices(double val);
void removePricesLessThan(double val);
void removePricesGreaterThan(double val);
double getFirstPriceLessThan(double val);
void addValue(double val);
void addValues(const std::vector &val);
private:
std::vector m_prices;
};
#endif /* defined(__FinancialSamples__TimeSeriesAnalysis__) */
//
//  TimeSeriesTransformations.cpp
#include "TimeSeriesTransformations.h"
#include 
#include 
TimeSeriesTransformations::TimeSeriesTransformations()
: m_prices()
{
}
TimeSeriesTransformations::TimeSeriesTransformations(const TimeSeriesTransformations &s)
: m_prices(s.m_prices)
{
}
TimeSeriesTransformations::~TimeSeriesTransformations()
{
}
TimeSeriesTransformations &TimeSeriesTransformations::operator=(const TimeSeriesTransformations &v)
{
if (this != &v)
{
m_prices = v.m_prices;
}
return *this;
}
void TimeSeriesTransformations::reducePrices(double val)
{
std::vector neg(m_prices.size());
std::transform(m_prices.begin(), m_prices.end(), neg.begin(),
std::bind2nd(std::minus(), val));
m_prices.swap(neg);
}
void TimeSeriesTransformations::increasePrices(double val)
{
std::for_each(m_prices.begin(), m_prices.end(), std::bind1st(std::plus(), val));
}
void TimeSeriesTransformations::removePricesLessThan(double val)
{
std::remove_if(m_prices.begin(), m_prices.end(), std::bind2nd(std::less(), val));
}
void TimeSeriesTransformations::removePricesGreaterThan(double val)
{
std::remove_if(m_prices.begin(), m_prices.end(), std::bind2nd(std::greater(), val));
}
double TimeSeriesTransformations::getFirstPriceLessThan(double val)
{
auto res = std::find_if(m_prices.begin(), m_prices.end(),
std::bind2nd(std::less(), val));
if (res != m_prices.end())
return *res;
return 0;
}
void TimeSeriesTransformations::addValue(double val)
{
m_prices.push_back(val);
}
void TimeSeriesTransformations::addValues(const std::vector &val)
{
m_prices.insert(m_prices.end(), val.begin(), val.end());
}
int main()
{
TimeSeriesTransformations ts;
std::vector vals = {7, 6.4, 2.16, 5, 3, 7};
ts.addValues(vals);
ts.addValue(6.5);
ts.reducePrices(0.5);
std::cout << " price is " <<  ts.getFirstPriceLessThan(6.0) << std::endl;
return 0;
}
```

清单 4-2 类 `TimeSeriesTransformations`

### 运行代码

我在 `main()` 函数中包含了一些使用 `TimeSeriesTransformations` 类的示例代码。这样，你就可以将上一节中给出的文件编译成一个示例应用程序。在借助 `gcc` 或 `Visual Studio` 等 C++ 编译器构建应用程序后，你可以使用以下命令行运行它：

```
./TimeSeriesTransformations
price is 5.9
```

## 复制交易文件

创建一个用于将交易文件复制到临时存储的类。

### 解决方案

文件操作是许多编程领域中的常见需求，在金融应用程序中也不例外。数据通常以 CSV 和 XML 等格式存储，因为它们需要被其他应用程序处理和过滤。日志记录功能对于确保正确地处理调试和错误信息也是必要的。

这类文件操作问题可以使用传统的 C 接口来解决，这些接口在所有主要操作系统（如 UNIX 和 Windows）上均可用。然而，此类原生接口存在一些缺点，应尽可能避免使用。我借此机会概述一种更好的方法，即使用 C++ 库的 boost 仓库，特别是 `filesystem` 库。掌握了 boost 的基本工作原理后，你将能够在接下来的几章中使用其他更复杂的类。

### Boost 库

标准 C++ 库提供了大量的类、容器和算法。然而，由于创建新 C++ 语言标准需要付出巨大努力，因此所包含的库集通常是最简化的，只包含大多数程序员所需的基本功能。此外，标准库只包含那些经过实际应用充分验证和确立、经得起时间考验的类和函数。

由于将新功能纳入语言标准的过程缓慢，因此需要一种更敏捷的软件分发与开发策略，以便更好地融入 C++ 社区认可的新库。Boost 库的诞生就是为了填补标准中纳入新功能的缓慢过程所留下的空白。与传统的单一供应商库不同，Boost 开发人员的目标是创建可在广泛领域和架构中复用的高级组件。许多 Boost 贡献者本身就参与了 C++ 标准的制定工作，这意味着目前 Boost 发行版中包含的许多库，日后将成为标准库的一部分。例如，`std::shared_ptr` 等模板类就源自 `boost::shared_ptr`。

要在你的应用程序中使用 Boost 库，你需要从 `boost.org` 网站下载并安装它们。由于 Boost 类的特性，这个过程相对简单。因为 Boost 中的大多数组件都被定义为模板类，所以除了少数例外，完整代码都包含在头文件中。这意味着你只需在代码中添加一个头文件，就可以使用某些 Boost 库中的所有功能。

表 4-1 列出了 Boost 发行版中可用的一些库。

**表 4-1** Boost 发行版中一些最常用的组件

| 库 | 描述 |
| --- | --- |
| `boost::any` | 一种多态数据类型，旨在用作任何其他数据类型的容器 |
| `Bind` | 允许现有函数被其他函数对象使用 |
| `Circular Buffer` | 定义了一个可用作循环缓冲区的存储模板 |
| `Chrono` | 一组与时间相关的实用工具 |
| `Filesystem` | 常见文件操作的实现，包括复制、移动和创建文件或目录 |

| `Foreach` | 引入了一种循环结构（已被 C++11 的新特性弃用） |
| `Function` | 一组定义函数包装器的模板 |
| `Geometry` | 常见几何算法的实现 |
| `Graph` | 定义了图形数据类型，以及常见的图论技术和算法 |
| `Hash` | 一种简单的哈希表数据类型，定义为模板 |
| `Lambda` | 一组可用于编写函数式编程 lambda 表达式的模板 |
| `Log` | 用于 C++ 应用程序的通用日志记录功能 |
| `Math` | 额外的数学函数 |
| `MPI` | 消息传递接口库 |
| `PropertyMap` | 定义了一个通用属性模板，可用于定义动态属性 |
| `SmartPtr` | 一组用于存储堆分配对象的智能指针类型 |

在本章及下一章中，我们将有机会探索其中一些库，因为本书介绍的其他金融 C++ 代码中将会用到它们。在本例中，我们关注的是 `filesystem` 库，它用于提供与文件和目录相关的操作。

C 和 C++ 语言传统上在其所实现的各个平台中，都提供了各自独有的文件处理接口。为此，平台厂商创建了独立的库，从而导致了如 UNIX 及其他符合 Posix 标准的系统、Windows、OS/2、VMS 等操作系统拥有不同的接口。然而，这些接口之间的差异使得应用难以跨系统移植。实际上，应用程序编写者创建了抽象层，这些抽象层根据需要与每个不同的系统进行交互。

STL 中的 `filesystem` 库旨在提供一组用于文件操作的跨平台类和模板。可以使用相同的类和模板来处理 STL 所支持的各个平台上的文件。这减少了应用程序程序员的工作量，同时生成的代码可以安全地复用于其他平台。

该库的主要组件包含在 `std::filesystem` 命名空间中。这些组件允许你对文件和目录执行常用操作。该库中包含的类可以轻松支持诸如复制、移动、更改权限和删除等操作。接下来，我将向你展示其中一些重要的组件，以及如何使用它们编写操作文件系统内容的代码。

`std::filesystem::path` 类表示文件系统中的路径。拥有一个路径类很有意义，因为它允许程序员在使用同一个对象的同时，表示不同系统中的目录路径。例如，UNIX 系统中的路径使用“/”分隔符，而在 Windows 中，则使用“\”分隔符。为避免与这些不同约定相关的问题，`filesystem` 库使用了一种通用表示。库中提供的许多函数随后都使用了 `path` 类。

`filesystem` 库的另一个重要概念是目录迭代器。你可以使用 `directory_iterator` 函数为特定路径创建一个迭代器。该函数返回一个迭代器对象，该对象具有 `++` 和 `--` 等运算符，允许程序员在给定目录的元素之间移动。

除了为路径和迭代器提供合适的抽象外，`filesystem` 库还提供了一组函数，可用于对文件和目录执行单个更改。这些函数包括以下内容：

*   `is_regular_file`：如果提供的路径指向一个普通文件（而非目录），则返回 true
*   `is_directory`：如果路径指向一个目录，则返回 true
*   `file_size`：返回作为参数传入的文件名的大小，以字节为单位
*   `exists`：如果作为参数传入的路径在文件系统中存在，则返回 true
*   `status`：返回一个 `file_status` 对象，该对象封装了给定文件的属性，例如可见性和类型
*   `create_directory`：在文件系统中创建一个具有指定路径的新目录
*   `copy`：将指定路径复制到第二个参数指示的位置
*   `remove`：从文件系统中移除指定路径
*   `current_path`：返回一个 `path` 对象，指示应用程序当前使用的路径

这些函数可以轻松组合以操作文件系统。我使用其中一些函数来实现 `FileManager` 类所需的所有功能。例如，以下是 `getContents` 成员函数的编码方式：

```cpp
std::vector FileManager::getContents(const std::string &prefix)
{
std::vector results;
path aPath(prefix);
if (!is_directory(aPath))
{
std::cout << "not a directory " << prefix << std::endl;
return results;
}
std::vector<path> contents;
copy(directory_iterator(aPath), directory_iterator(), back_inserter(contents));
for (int i=0; i<contents.size(); ++i)
{
results.push_back(contents[i].string());
}
return results;
}
```

第一步是根据作为参数传入的字符串创建一个路径对象。一旦创建了路径，就可以使用 `is_directory` 函数测试它是否指向一个目录。如果路径正确，那么可以使用 `directory_iterator` 函数创建一个迭代器对象，该对象被传递给 `copy` 函数。`copy` 函数的唯一工作是将迭代器指向的每个元素复制到 `contents` 向量中。然后，该容器中的元素被转换为字符串并添加到 `results` 向量中。

最后，可以使用以下代码将文件从一个目录复制到指定目的地的函数添加到 `FileManager` 类中：

```cpp
void FileManager::copyToTempDirectory(const std::string &prefix)
{
path tmpPath("/tmp/");
path aPath(prefix);
if (!is_directory(aPath))
{
std::cout << "not a directory " << prefix << std::endl;
return;
}
listContents(prefix);
for (auto it = directory_iterator(aPath); it != directory_iterator(); ++it)
{
if (is_regular_file(it->path()))
{
copy_file(it->path(), tmpPath);
}
}
}
```

这里，你首先检查给定的路径前缀，以确保它是对一个目录的引用。然后，我添加了一些代码，以日志形式列出内容。下一步是使用 `directory_iterator` 函数返回的迭代器遍历目录中的内容。对于目录中的每个元素，你可以测试它是否是一个常规文件，然后使用 `copy_file` 函数执行复制。

### 完整代码

你可以在代码清单 4-3 中看到 `FileManager` 类的完整定义。在代码清单的末尾，你可以看到一个如何在 `main()` 函数中使用该类的示例。

```
//
//  FileManager.h
#ifndef __FinancialSamples__FileManager__
#define __FinancialSamples__FileManager__
#include 
#include 
class FileManager {
public:
FileManager(const std::string &basePath);
FileManager(const FileManager &);
~FileManager();
FileManager &operator=(const FileManager &);
void removeFiles();
std::vector getDirectoryContents();
void listContents();
void copyToTempDirectory(const std::string &prefix);
private:
std::string m_basePath;
};
#endif /* defined(__FinancialSamples__FileManager__) */
//
//  FileManager.cpp
#include "FileManager.h"
#include 
#include 
using namespace std::filesystem;
FileManager::FileManager(const std::string &basePath)
: m_basePath(basePath)
{
}
FileManager::FileManager(const FileManager &v)
: m_basePath(v.m_basePath)
{
}
FileManager::~FileManager()
{
}
FileManager &FileManager::operator=(const FileManager &v)
{
if (this != &v)
{
m_basePath = v.m_basePath;
}
return *this;
}
void FileManager::removeFiles()
{
std::vector files = getDirectoryContents();
for (unsigned i=0; i<files.size(); ++i)
{
path aPath(files[i]);
if (!remove(aPath))
{
std::cout << "could not remove: " << files[i] << std::endl;
}
}
}
std::vector FileManager::getDirectoryContents()
{
std::vector results;
path aPath(m_basePath);
if (!is_directory(aPath))
{
std::cout << "not a directory " << m_basePath << std::endl;
return results;
}
std::vector<path> contents;
copy(directory_iterator(aPath), directory_iterator(), back_inserter(contents));
for (unsigned i=0; i<contents.size(); ++i)
{
results.push_back(contents[i].string());
}
return results;
}
void FileManager::listContents()
{
std::vector<path> contents;
path aPath(m_basePath);
copy(directory_iterator(aPath), directory_iterator(), back_inserter(contents));
for (unsigned i=0; i < contents.size(); ++i)
{
std::cout << contents[i].string() << std::endl;
}
}
void FileManager::copyToTempDirectory(const std::string &prefix)
{
path tmpPath("/tmp/");
path aPath(prefix);
if (!is_directory(aPath))
{
std::cout << "not a directory " << prefix << std::endl;
return;
}
listContents();
std::vector contents = getDirectoryContents();
for (auto it = directory_iterator(aPath); it != directory_iterator(); ++it)
{
if (is_regular_file(it->path()))
{
copy_file(it->path(), tmpPath);
}
}
}
int main()
{
// 为 /tmp 目录创建一个 FileManager 对象
//
FileManager fm("/tmp/");
std::vector contents = fm.getDirectoryContents();
std::cout << "entries: " << std::endl;
for (std::string entry : contents)
{
std::cout << entry << std::endl;
}
return 0;
}
```

代码清单 4-3

类 `FileManager` 的定义和实现

### 运行代码

清单 4-3 中展示的 `FileManager` 类可以使用任何标准 C++ 编译器（例如 `gcc` 或 Visual Studio）进行构建。如果你使用的是较新的编译器（至少是 C++17），则无需额外库即可使用 `std::filesystem` 类。许多现代 Linux 发行版已经包含了最新版本的 `gcc`，但如果你使用其他操作系统，请检查其支持情况。

例如，我系统中使用 `gcc` 构建该类所需的命令行如下：

```
gcc -o FileManager FileManager.cpp
```

生成应用程序后，你可以在 UNIX 系统上按如下方式运行它：

```
./FileManager
```

这将显示 `/tmp` 目录中存储的文件列表（你可以根据需要更改该目录，以便在自己的系统路径上进行测试）。

## 处理日期

让我们看看如何创建一个类，用于确定普通证券（即周一至周五进行交易）的交易日期。

### 解决方案

日期是金融数据中非常常见的部分，因此你应该有明确的方法来处理它们。日期是历史价格的重要组成部分，也是股票分析中重要事件（如盈利发布、股息、拆股和其他监管行动）的关键要素。固定收益、衍生品及其他投资类别也是如此。C++ 提供了丰富的功能，可用于存储、计算日期并将其从一种格式转换为另一种格式。

尽管 C++ 中有许多与时间和日期相关的函数和类，但其中许多机制继承自 C 标准库，使用起来不如 STL 的其他组件方便。为了平滑其与 STL 的集成过程，boost 库中包含了一个 `date_time` 库，专门处理不同日期表示形式，并为基于不同日期格式的计算提供了基本支持。

为了解决本节提出的问题，你将使用一个名为 `Date` 的类，它封装了应用程序中使用的日期概念。其成员变量仅是三个表示年、月和日的值。此外，还有星期的概念，通过枚举进行编码。

```
enum class DayOfWeek {
Sun,
Mon,
Tue,
Wed,
Thu,
Fri,
Sat
};
```

`Date` 类提供了多个成员函数，可用于回答常见请求，例如 `getDayOfWeek`（返回当前日期的星期几）和 `isLeapYear`（判断某年的二月是否有 29 天）。以下是 `Date` 的成员函数快速列表。

```
Date(int year, int month, int day);
~Date();
bool isLeapYear();
Date &operator++();
bool operator<(const Date &d);
DayOfWeek getDayOfWeek();
int daysInterval(const Date &);
bool isTradingDay();
```

`isLeapYear` 方法的实现使用了常见的闰年定义，即考虑能被 4、100 和 400 整除的年份：

```
bool Date::isLeapYear()
{
if (m_year % 4 != 0) return false;
if (m_year % 100 != 0) return true;
if (m_year % 400 != 0) return false;
return true;
}
```

`getDayOfWeek` 查找 1900 年 1 月 1 日（星期一）之后任何日期的星期几。它通过计算自该日期以来的天数，并相应更新年、月、日来实现此功能。正确添加到当前日期的任务由 `operator +` 处理。

最后，`Date` 类借助 boost 库中的 `date_time` 计算日期差异。在 `date_time` 中，日期根据日历进行分类。西方世界使用的日历称为公历。在包含以下头文件后即可使用它：

```cpp
#include <boost/date_time/gregorian/gregorian.hpp>
```

`date_time` 库定义了一些可用于日期操作的通用数据类型。在本例中，我们关注 `date` 和 `date_duration` 类型。`date` 类型仅是日期的表示形式，可以用年、月、日进行初始化。`date_duration` 用于存储日期之间的差异。可以使用 `days()` 成员函数将持续时间类型转换为整数类型。以下是实现 `daysInterval` 的方法：

```cpp
int Date::daysInterval(const Date &d)
{
date bdate1(m_year, m_month, m_day);
date bdate2(d.m_year, d.m_month, d.m_day);
boost::gregorian::date_duration duration = bdate1 - bdate2;
return (int) duration.days();
}
```

## 完整代码

你可以在清单 4-4 中看到 `Date` 类的完整代码。清单 4-4 包含该类的头文件和一个实现文件。你还会看到一个 `main()` 函数，它创建了两个 `Date` 类型的对象，并对它们执行了一些简单操作。

```cpp
//
//  Date.h
#ifndef __FinancialSamples__Date__
#define __FinancialSamples__Date__
#include 
class Date {
public:
enum class DayOfWeek {
Sun,
Mon,
Tue,
Wed,
Thu,
Fri,
Sat
};
Date(int year, int month, int day);
~Date();
bool isLeapYear();
Date &operator++();
bool operator
#include 
#include 
using namespace boost::gregorian;
Date::Date(int year, int month, int day)
: m_year(year),
m_month(month),
m_day(day)
{
}
Date::~Date()
{
}
bool Date::isLeapYear()
{
if (m_year % 4 != 0) return false;
if (m_year % 100 != 0) return true;
if (m_year % 400 != 0) return false;
return true;
}
Date &Date::operator++()
{
std::vector monthsWith31 = { 1, 3, 5, 7, 8, 10, 12 };
if (m_day == 31)
{
m_day = 1;
m_month++;
}
else if (m_day == 30 &&
std::find(monthsWith31.begin(),
monthsWith31.end(), m_month) == monthsWith31.end())
{
m_day = 1;
m_month++;
}
else if (m_day == 29 && m_month == 2)
{
m_day = 1;
m_month++;
}
else if (m_day == 28 && m_month == 2  && !isLeapYear())
{
m_day = 1;
m_month++;
}
else
{
m_day++;
}
if (m_month > 12)
{
m_month = 1;
m_year++;
}
return *this;
}
int Date::daysInterval(const Date &d)
{
Date bdate1(m_year, m_month, m_day);
Date bdate2(d.m_year, d.m_month, d.m_day);
boost::gregorian::date_duration duration = bdate1 - bdate2;
return (int) duration.days();
}
bool Date::operator<(const Date &d)
{
if (m_year < d.m_year) return true;
if (m_year == d.m_year && m_month < d.m_month) return true;
if (m_year == d.m_year && m_month == d.m_month && m_day < d.m_day) return true;
return false;
}
Date::DayOfWeek Date::getDayOfWeek()
{
int day = 1;
Date d(1900, 1, 1);
for (;d < *this; ++d)
{
if (day == 7) day = 1;
else day++;
}
return (DayOfWeek) day;
}
bool Date::isTradingDay()
{
DayOfWeek dayOfWeek = getDayOfWeek();
if (dayOfWeek == DayOfWeek::Sun || dayOfWeek == DayOfWeek::Sat)
{
return false;
}
return true;
}
std::string Date::toStringDate(Date::DayOfWeek day)
{
switch(day)
{
case DayOfWeek::Sun: return "Sunday";
case DayOfWeek::Mon: return "Monday";
case DayOfWeek::Tue: return "Tuesday";
case DayOfWeek::Wed: return "Wednesday";
case DayOfWeek::Thu: return "Thursday";
case DayOfWeek::Fri: return "Friday";
case DayOfWeek::Sat: return "Saturday";
}
throw std::runtime_error("unknown day of week");
}
int main()
{
Date myDate(2015, 1, 3);
auto dayOfWeek = myDate.getDayOfWeek();
std::cout << " day of week is "
<< myDate.toStringDate(dayOfWeek) << std::endl;
Date secondDate(2014, 12, 5);
++secondDate;  // test increment operator
++secondDate;
int interval = myDate.daysInterval(secondDate);
std::cout << " interval is " << interval << " days" << std::endl;
return 0;
}
清单 4-4
Date 类的实现
```

### 运行代码

清单 4-4 中的代码使用了任何标准 C++ 编译器均可使用的标准类。它还使用了 boost 库，该库是开源的，可以从 `boost.org` 免费下载。你可以在 Linux 和其他 UNIX 系统上使用以下命令行构建应用程序（假设 boost 安装在 `/usr/local/boost`）：

```bash
gcc -o Date -I/usr/local/boost/ Date.cpp
```

执行生成的可执行文件将显示 `main()` 函数中包含的测试代码的输出：

```
./Date
day of week is Saturday
interval is 27 days
```

### 结论

在本章中，我提供了一些编程示例，涵盖了金融编程中使用的基本库。这些库包括 STL，及其容器集（例如 `vector`、`map`）和算法（例如 `sort`、`transform` 和 `for_each` 等）。你还了解了 boost 库，这是一组为填补 C++ 标准化进程缓慢造成的空白而创建的库。

算法是 STL 的重要组成部分。这些算法可用于在 STL 模板定义的任何容器中执行常见操作，例如搜索、复制和转换。在本章中，你还学习了如何将这些算法应用于数据容器以执行数据分析。

第一个示例应用程序展示了如何处理分析师推荐。要正确处理此类信息，你必须使用`STL` `vector`和`map`。第二个示例应用程序使用`STL`提供的算法对时间序列执行简单的转换。这种转换可用于清理数据、执行假设分析，以及根据投资分析新技术的需求更新价格。你已经看到了如何使用`STL`算法和函数模板来实现这一点。

你还学习了如何以独立于平台或操作系统的方式创建处理文件和目录的`C++`代码。这是使用属于`boost`库的`filesystem`库实现的。使用`boost`的主要优势之一在于，虽然其他库与操作系统紧密绑定，但`boost`库是以平台无关的方式编写的，遵循标准库采用的相同策略。事实上，多年来，`boost`的许多组件已成为标准库的一部分。

金融代码的另一个重要方面是频繁使用日期。这类数据与交易、分析师推荐、股息以及许多其他与投资相关的事件相关联。你学习了如何在`boost`的`date_time`库中使用`date`类型，以及如何计算日期其他有趣的属性。

这组编码示例到此结束，它们回顾了现代`C++`编程的一些基本方面。你需要了解这些技术，它们主要基于模板、`STL`及其算法的使用。学习诸如`boost`库之类的扩展库也很重要。在下一章中，你将开始更多地了解数值类的设计。`C++`中的金融应用程序大量使用数值功能，以快速准确地计算不同投资类别的所需属性。我将讨论创建此类数值类的一些基本原则，以及现代`C++`库如何帮助你简化生成的代码。

# 第五章：设计数值类