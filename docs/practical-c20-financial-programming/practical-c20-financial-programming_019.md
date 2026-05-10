# 17. 多线程

C++20 应用程序用于对计算性能要求极高的场景。在金融应用（如高频交易）中，对性能的需求更为突出，因为盈利与亏损的差距可能仅有几微秒。在需要高性能的情况下，充分利用现代 CPU 提供的资源至关重要。特别是，多核处理是新 CPU 提供的主要功能之一，可用内核数量随着芯片复杂度的提升而不断增长。

然而，要受益于多核处理器，C++ 程序员需要学习一些并行编程技术，这些技术在过去的几十年中已迅速成为 C++ 标准库的一部分。使用多个进程是利用这种计算能力的一种可能方式。多线程是在同一进程内运行多个并发任务的方法，是另一种能够同时利用两个或更多内核的技术。

在本章中，您将看到一些探索 C++ 程序员多线程策略的示例。借助本章提供的知识，您将能够在应用程序中充分利用现有的多核系统。虽然多线程是当今应用程序中一种有用的策略，但未来它将变得更加重要，因为桌面和服务器制造商预计将继续为其处理器添加更多内核。

在传统的多线程方法中，C++ 程序员使用为方便访问操作系统提供的多线程功能而创建的库。一个流行的例子是 `pthreads` 库。然而，当前的 C++20 标准提供了另一种方法，即通过 `<thread>` 头文件中的模板，语言直接支持线程的使用。在本章中，您将学习这两种方法。这很重要，因为许多现有的多线程代码都使用非标准库。但是，新代码应优先使用标准库提供的模板。

以下是本章讨论的部分主题：

- **使用 `pthreads` 库**：`pthreads` 是一个标准库，可用于在同一进程中创建和维护多个线程。在多核机器上，创建线程是利用当前架构并行能力的最常见方法之一。您将学习如何创建使用 `pthreads` 库实现并行计算的应用程序。

- **在并行线程中运行算法**：您将了解如何将问题分解到不同的线程中，并将它们的结果组合成新的解决方案。作为示例，我提出了一种改进的并行算法来计算期权概率。

- **线程同步**：使用多个线程会引入资源同步的问题。您将学习如何使用互斥锁等同步原语，确保每次只有一个线程可以访问和修改资源。

- **STL 线程**：C++ 标准最新版本提供的新多线程类和模板。

## 使用 Pthreads 库创建线程

创建一个 C++ 类，该类使用标准的 `pthreads` 库将其工作分配到多个处理线程中。

### 解决方案

多线程是为支持并行计算而创建的软件解决方案之一。线程是一个处理单元，可以与程序的其他部分并行执行，从而使得程序的两个或多个分段可以并发运行。在多核机器上，这意味着同一程序可以有效地利用两个或多个内核来执行额外的工作。根据代码的组织方式，谨慎使用多线程技术为提高整个算法的吞吐量提供了良好的机会。

然而，要使用多线程，操作系统必须提供支持。由于每个操作系统内部都以自己的方式实现多线程，因此多线程应用程序过去常常依赖于所使用的操作系统和架构。为避免此问题，提出了一个标准的 `pthreads`（POSIX 线程）库，并将其采纳为 POSIX 的一部分。`pthreads` 库可用于多种操作系统，包括 UNIX、Mac OS X，甚至 Windows（例如，您可以使用 Cygwin 库在 Windows 上访问 `pthreads`）。表 17-1 提供了 `pthreads` API（应用程序编程接口）中可供应用程序开发人员使用的函数快速汇总。

**表 17-1** `pthreads` 库中常用函数列表

| 函数名 | 描述 |
| --- | --- |
| `pthread_create` | 创建一个新线程 |
| `pthread_exit` | 结束一个现有线程 |
| `pthread_join` | 等待一个现有线程，直到该线程退出后才返回 |
| `pthread_detach` | 分离一个线程 |
| `pthread_attr_init` | 初始化属性数据结构 |
| `pthread_attr_destroy` | 销毁属性数据结构 |
| `pthread_attr_setstacksize` | 设置新线程的栈大小 |
| `pthread_cancel` | 取消线程执行 |
| `pthread_mutex_init` | 初始化互斥锁同步原语 |
| `pthread_mutex_destroy` | 销毁互斥锁 |
| `pthread_mutex_lock` | 锁定互斥锁 |
| `pthread_mutex_unlock` | 解锁现有互斥锁 |
| `sem_init` | 初始化信号量同步原语 |
| `sem_destroy` | 销毁信号量 |
| `sem_wait` | 等待信号量 |



虽然 `pthreads` 库是为与纯 C 程序兼容而编写的，但它可以轻松地作为 C++ 应用程序的一部分使用。可以简单地围绕使用 `pthreads` 创建的线程创建一个包装器，以便从 C++ 代码中更容易地访问它们。在本节中，您将看到一个使用 `pthreads` 创建简单多线程应用程序的 C++ 代码示例。所使用的技术以及线程创建和同步的一般概念可以在您自己的程序中使用。

要在程序中创建一个独立的执行线程，您需要使用 `pthread_create` 函数。它接收的参数包括：一个标识符（整数值）、一个指向属性结构的指针（如果未使用可以为 `null`）、一个指向将由线程执行的函数的指针，以及一个指向线程函数参数的指针。如果未发生错误，该函数返回零；否则，它返回一个整数错误标识符。

在执行 `pthread_create` 函数后，程序会从指定的函数启动另一个线程。该线程独立于原始程序，并且可以在相同或独立的核心上运行（如果宿主机器有可用核心，具体由操作系统的线程调度器决定）。线程可以通过到达线程函数的末尾或显式调用 `pthread_exit` 函数来终止。

在本节中，我将展示如何从 C++ 类访问 `pthreads` 库提供的功能。为此，我引入了 `Thread` 类，它封装了运行中线程的概念。该类的目标是成为具体线程类的基类。在每个 `Thread` 的新子类中，唯一需要的成员函数是 `run()`，它决定了将由新线程执行的代码。

`Thread` 类中值得注意的方法如下：

- `start`：需要被调用来启动线程的执行。
- `endThread`：可以被调用来终止当前线程。
- `setJoinable`：决定该线程是否可以被其他线程连接（join）。
- `join`：允许调用者连接此线程，这样调用者将仅在线程终止后才继续执行。
- `run`：此成员函数需要在每个子类中实现，并定义将在其自身线程中运行的代码。

`Thread` 类使用了一个名为 `thread_function` 的 C 函数，并在 `Thread.cpp` 文件中定义：

```c
void *thread_function(void *data)
{
    Thread *t = reinterpret_cast(data);
    t->run();
    return nullptr;
}
```

该函数的签名由 `pthreads` 库决定。一旦创建新线程，就会调用该函数。主要思想是，传递给函数的数据指针实际上是一个指向 `Thread` 对象的指针。一旦使用 `reinterpret_cast` 运算符检索到它，该对象就可以用来执行 `run` 成员函数。根据 `Thread` 的具体子类，`run` 方法可以执行子类创建者想要的任何任务。这足以保证代码将作为并行线程运行。

**注意**

> 请记住，`reinterpret_cast` 运算符可用于在 C++ 中的任意两种类型之间进行转换。因此，使用此运算符时务必小心，因为一旦应用，编译器就不会执行类型检查。

除此之外，`start()` 和 `endThread()` 函数分别使用相应的 pthread API 函数来创建新线程和退出已有线程。这些函数的实现方式如下：

```c
void Thread::start()
{
    pthread_create(&m_data->m_thread, &m_data->m_attr, thread_function, this);
}
void Thread::endThread()
{
    pthread_exit(nullptr);
}
```

**完整代码**

您可以在清单 17-1 中找到 `Thread` 类的完整实现。

```cpp
//
//  Thread.h
#ifndef __FinancialSamples__Thread__
#define __FinancialSamples__Thread__

struct ThreadData;

class Thread {
public:
    Thread();
    virtual ~Thread();

private:
    Thread(const Thread &p); // no copy allowed
    Thread &operator=(const Thread &p); // no assignment allowed

public:
    virtual void run() = 0;
    void start();
    void endThread();
    void setJoinable(bool yes);
    void join();

private:
    ThreadData *m_data;
    bool m_joinable;
};

#endif /* defined(__FinancialSamples__Thread__) */

//
//  Thread.cpp
#include "Thread.h"
#include <iostream>
#include <pthread.h>

using std::cout;
using std::endl;

struct ThreadData {
    pthread_t m_thread;
    pthread_attr_t m_attr;
};

namespace {
    void *thread_function(void *data)
    {
        Thread *t = reinterpret_cast<Thread *>(data);
        t->run();
        return nullptr;
    }
}

Thread::Thread()
: m_data(new ThreadData),
  m_joinable(false)
{
    pthread_attr_init(&m_data->m_attr);
}

Thread::~Thread()
{
    if (m_data)
    {
        delete m_data;
    }
}

void Thread::start()
{
    pthread_create(&m_data->m_thread, &m_data->m_attr, thread_function, this);
}

void Thread::endThread()
{
    pthread_exit(nullptr);
}

void Thread::setJoinable(bool yes)
{
    pthread_attr_setdetachstate(&m_data->m_attr,
                                yes ? PTHREAD_CREATE_JOINABLE : PTHREAD_CREATE_DETACHED);
    m_joinable = yes;
}

void Thread::run()
{
    cout << "Thread running" << endl;
}

void Thread::join()
{
    if (m_joinable)
    {
        void *result;
        pthread_join(m_data->m_thread, &result);
    }
}

// --- sample implementation
class TestThread : public Thread {
public:
    virtual void run();
};

void TestThread::run()
{
    cout << "this is a test implementation" << endl;
}

// sample usage
int main()
{
    Thread *myThread = new TestThread;
    myThread->setJoinable(true);
    myThread->start();
    myThread->join();
    return 0;
}
```

清单 17-1 线程类及示例实现

**运行代码**

清单 17-1 中显示的代码可以使用任何符合标准的编译器（如 `gcc`、`llvm` 或 Visual Studio）来构建。请记住添加包含 `pthreads` 库的链接行。以下是用于构建示例应用程序的命令行（在 Mac OS X 上测试）：

```bash
$ gcc –o threadTest Thread.cpp -lpthreads
$ ./threadTest
this is a test implementation
```

**并行计算期权概率**

创建计算期权概率类的多处理版本。使用 `pthreads` 库在多个线程之间分配工作。



### 解决方案

对于需要大量计算的问题，强烈建议采用并行处理技术。特别是当问题易于分解时，就变成了如何为每个线程分配适量工作并等待结果的问题。

蒙特卡洛算法就是这类过程的典型示例。模拟过程可以在任意数量的线程中运行，其结果可以轻松合并为单个数值。例如，计算期权概率时就是这种情况。正如你在第 14 章所见，蒙特卡洛技术在期权概率模拟中非常有效。每一步只需模拟新的随机游走，并利用该信息改进当前的概率估计。

要将蒙特卡洛算法应用于期权概率计算，第一步是正确定义问题的分解方式。这里很容易实现，因为每次循环计算都相互独立。在这种情况下，你可以通过指示每个线程运行蒙特卡洛方法的特定迭代次数来实现。最后，合并每个线程的结果，并将所有线程返回值的平均值作为最终结果。

上述算法在`ParallelOptionsProbabilities`类中实现。该类是一个外层框架，负责调用多个线程来运行所需算法。实际工作由派生自`Thread`类的`RandomWalkThread`类完成。作为`Thread`的任何其他子类，它需要实现从独立线程调用的`run()`成员函数。在`RandomWalkThread`内部，有一个成员变量`m_result`，用于存储蒙特卡洛过程的输出。线程完成后，可通过此成员变量获取计算的最终值。

`run`成员函数与你之前在`OptionsProbabilities`类中看到的代码非常相似。主要区别在于输出存储在`m_result`成员变量中。`RandomWalkThread`对象的工作在`ParallelOptionsProbabilities`类内部进行编排。关键成员函数是`probFinishAboveStrike()`。

```
double ParallelOptionsProbabilities::probFinishAboveStrike()
{
    const int numThreads = 20;
    vector<Thread *> threads(numThreads);
    for (int i=0; i<numThreads; i++) {
        threads[i] = new RandomWalkThread(m_numSteps, m_step, m_strikePrice);
        threads[i]->setJoinable(true);
        threads[i]->start();
    }
    for (int i=0; i<numThreads; i++) {
        threads[i]->join();
    }
    double nAbove = 0;
    for (int i=0; i<numThreads; i++) {
        nAbove += threads[i]->result();
        delete threads[i];
    }
    return nAbove/(double)(numThreads);
}
```

在该成员函数开始时，创建了多个线程并添加到`threads`向量中。需要将这些线程定义为可加入的，以便能够等待每个线程的结果。下一步是启动线程，使每个线程都能执行所需计算。然后，通过第二个循环来加入已创建的线程。这样，主线程可以在并行计算进行时等待。当所有线程完成后，调用`join()`的结果是主线程恢复执行。最后，可以使用`result()`成员函数存储每个线程返回的数据。此时可以删除线程对象以避免内存泄漏。在`probFinishAboveStrike`成员函数的最后一行，可以看到如何合并计算数据。本例中，只需返回高于执行价格的值的总和，并除以使用的线程数。

### 完整代码

清单 17-2 展示了`ParallelRandomWalk`类。列表末尾提供了一个可用于测试的示例`main()`函数。

```
//
//
//  ParallelRandomWalk.h
#ifndef __FinancialSamples__ParallelRandomWalk__
#define __FinancialSamples__ParallelRandomWalk__

class ParallelOptionsProbabilities  {
public:
    ParallelOptionsProbabilities(int size, double strike, double sigma);
    ParallelOptionsProbabilities(const ParallelOptionsProbabilities &p);
    ~ParallelOptionsProbabilities();
    ParallelOptionsProbabilities &operator=(const ParallelOptionsProbabilities &p);
    double probFinishAboveStrike();
private:
    int m_numSteps;       // number of steps
    double m_step;        // size of each step (in percentage)
    double m_strikePrice; // starting price
};
#endif /* defined(__FinancialSamples__ParallelRandomWalk__) */

//
//  ParallelOptionsProbabilities.cpp
#include "ParallelOptionsProbabilities.h"
#include "Thread.h"
#include <vector>
#include <iostream>
#include <boost/random.hpp>
#include <boost/random/normal_distribution.hpp>
#include <cmath>

using std::vector;
using std::cout;
using std::endl;

static boost::rand48 random_generator;
using std::vector;

/// ---
class RandomWalkThread : public Thread {
public:
    RandomWalkThread(int num_steps, double sigma, double startPrice);
    ~RandomWalkThread();
    virtual void run();
    double gaussianValue(double mean, double sigma);
    double getLastPriceOfWalk();
    double result();
private:
    int m_numberOfSteps;     // number of steps
    double m_sigma;          // size of each step (in percentage)
    double m_startingPrice;  // starting price
    double m_result;
};

RandomWalkThread::RandomWalkThread(int numSteps, double sigma, double startingPrice)
    : m_numberOfSteps(numSteps),
      m_sigma(sigma),
      m_startingPrice(startingPrice)
{
}

RandomWalkThread::~RandomWalkThread()
{
}

double RandomWalkThread::gaussianValue(double mean, double sigma)
{
    boost::random::normal_distribution<> distrib(mean, sigma);
    return distrib(random_generator);
}

double RandomWalkThread::result()
{
    return m_result;
}

double RandomWalkThread::getLastPriceOfWalk()
{
    double prev = m_startingPrice;
    for (int i=0; i<m_numberOfSteps; i++) {
        double g = gaussianValue(0, m_sigma);
        double next = prev * (1 + g);
        prev = next;
    }
    return prev;
}

void RandomWalkThread::run()
{
    double nAbove = 0;
    for (int i=0; i<m_numberOfSteps; i++) {
        double val = getLastPriceOfWalk();
        if (val >= m_startingPrice)
        {
            nAbove++;
        }
    }
    m_result = nAbove/(double)m_numberOfSteps;
}
// ---

ParallelOptionsProbabilities::ParallelOptionsProbabilities(int size, double start, double step)
    : m_numSteps(size),
      m_strikePrice(start),
      m_step(step)
{
}

ParallelOptionsProbabilities::ParallelOptionsProbabilities(const ParallelOptionsProbabilities &p)
    : m_numSteps(p.m_numSteps),
      m_strikePrice(p.m_strikePrice),
      m_step(p.m_step)
{
}

ParallelOptionsProbabilities::~ParallelOptionsProbabilities()
{
}

ParallelOptionsProbabilities &ParallelOptionsProbabilities::operator=(const ParallelOptionsProbabilities &p)
{
    if (this != &p)
    {
        m_numSteps = p.m_numSteps;
        m_strikePrice = p.m_strikePrice;
        m_step = p.m_step;
    }
    return *this;
}

double ParallelOptionsProbabilities::probFinishAboveStrike()
{
    const int numThreads = 20;
    vector<Thread *> threads(numThreads);
    for (int i=0; i<numThreads; i++) {
        threads[i] = new RandomWalkThread(m_numSteps, m_step, m_strikePrice);
        threads[i]->setJoinable(true);
        threads[i]->start();
    }
    for (int i=0; i<numThreads; i++) {
        threads[i]->join();
    }
    double nAbove = 0;
    for (int i=0; i<numThreads; i++) {
        nAbove += threads[i]->result();
        delete threads[i];
    }
    return nAbove/(double)(numThreads);
}

int main()
{
    ParallelOptionsProbabilities rw(100, 50.0, 52.0);
    double r = rw.probFinishAboveStrike();
    cout << " result is " << r << endl;
    return 0;
}

清单 17-2
类 ParallelRandomWalk
```

### 运行代码

我在 Mac OS X 机器上使用`gcc`编译器编译并执行了清单 17-2 中的代码。任何符合标准的编译器均可用于此目的。以下是预期输出的示例：

```
./parallelOptProb
running thread
running thread
...
running thread
result is 0.487
```

## 使用互斥锁防止非同步访问

在本节中，我们将编写一个 C++ 类，实现使用互斥锁同步共享数据的并行算法。


```markdown

### 解决方案

多线程是一种将计算工作分发到两个或更多处理器核心的便捷方式，这可以提升整个应用程序的性能。然而，虽然多线程有许多优点，但它也增加了软件设计的复杂性。例如，多线程架构中需要解决的问题之一是线程之间共享资源的访问。如果内存中的一个变量被两个或多个线程使用，那么其访问需要进行同步，以防止不同线程同时尝试修改值。

一旦创建了一个新线程，就需要使用 `pthread` 库提供的机制来管理它。在最简单的情况下，新线程是独立的，不需要与原始（父）线程同步。然而，更常见的情况是，需要使用相同数据的多个线程之间需要进行同步。同步需求越大，管理共享数据所需的工作量也就越大。

一段两个或多个线程可以访问共享资源的代码区域被称为 **临界区**（critical section）。临界区是代码中需要保护共享资源以避免冲突的区域。

为了避免临界区固有的冲突，大多数多线程 API 提供了可用于实现同步操作的原语。这些原语在实现线程间的资源共享方面非常有效。这类原语有很多，例如信号量（semaphores）、互斥锁（mutexes）和消息（messages）等。`pthreads` 库直接支持其中一些最常见的机制，包括互斥锁，它可以用来保证只有一个线程能够访问特定的临界区。

**互斥锁**（mutex）是一种可用于协调两个或多个线程工作的同步机制。互斥锁的状态用于确定线程是否有权操作特定资源（例如内存中的变量）。当线程尝试访问互斥锁的值时，可能发生两种情况：如果互斥锁状态指示临界区可用，则线程可以直接进入临界区；然而，如果互斥锁状态指示忙碌状态，发起请求的线程将停止执行，并被发送到操作系统创建的等待区。只有当资源被其他线程释放后，操作才会恢复。所有这些等待和恢复活动都由操作系统协调。

互斥锁在 `pthreads` API 中实现，其类型为 `thread_mutex_t`。可以使用函数 `pthread_mutex_init` 创建一个新的互斥锁，并使用函数 `pthread_mutex_destroy` 销毁它。当共享资源即将被使用时，需要获取并锁定互斥锁。这保证了该互斥锁一次只能被一个线程使用。这是通过函数 `pthread_mutex_lock` 完成的。如果互斥锁不可用，该函数会自动中断线程，并强制线程等待直到互斥锁被释放。你也可以使用 `pthread_mutex_trylock` 尝试访问互斥锁而无需强制等待。如果互斥锁当前不可用，该函数将返回一个错误代码，你可以稍后重试。最后，一旦获取了互斥锁，你需要在同步操作结束时解锁它。这对于确保其他线程能够进入临界区并使用最近释放的资源是必要的。你可以使用函数 `pthread_mutex_unlock` 解锁互斥锁。

虽然互斥锁有效性的理论证明可能很复杂，但其使用非常简单。在清单 17-3 的编码示例中，你将看到一个名为 `Mutex` 的类，它封装了互斥锁同步操作的概念。这个类提供了两个主要函数：`lock` 和 `unlock`。第一个成员函数在临界区的开头调用。第二个重要的成员函数是 `unlock`，应在临界区的末尾调用。`Mutex` 类还负责在构造函数中使用 `pthread_mutex_init` 初始化 `pthread` 互斥锁，并在析构函数中使用 `pthread_mutex_destroy` 销毁它。

用于封装互斥锁概念的第二个类是 `MutexAccess`。该类负责保证对互斥锁的每次访问都由一对对 `Mutex` 的 `lock()` 和 `unlock()` 成员函数的调用组成。`lock()` 成员函数直接在构造函数中调用，而 `unlock()` 在 `MutexAccess` 的析构函数中自动调用。因此，如果临界区正好在声明 `MutexAccess` 对象的作用域结束时结束，你无需担心解锁问题，因为 RAII 惯用法保证了当析构函数被调用时，互斥锁会被自动解锁。

在 `MutexTestThread` 中，我们有一个在线程内使用 `Mutex` 类的示例。所演示的任务非常简单，但它说明了如何使用互斥锁来提供对共享资源的同步访问。这里，共享资源是 `double` 类型的变量 `result`。该变量用于保存所需的计算结果；然而，它被应用程序中的所有线程访问。为了同步对该变量的访问，你需要使用互斥锁。可以实例化 `MutexAccess` 类的一个对象，这会导致互斥锁（名为 `m_globalMutex`）被锁定。获取锁之后，你现在可以安全地检查该值并对引用变量进行更改。最后，在 `run()` 成员函数的末尾，锁将被自动释放。

```


### 完整代码

您可以在清单 17-3 中查看 `Mutex` 和 `MutexAccess` 类的完整代码。`MutexTestThread` 类中也展示了它们的使用示例。

```
//
//  Mutex.h
#ifndef __FinancialSamples__Mutex__
#define __FinancialSamples__Mutex__
struct MutexData;
class Mutex {
public:
Mutex();
~Mutex();
void lock();
void unlock();
private:
Mutex(const Mutex &p);  // 禁止复制
Mutex &operator=(const Mutex &p);  // 禁止赋值
MutexData *m_data;
};
class MutexAccess {
public:
MutexAccess(Mutex &m);
~MutexAccess();
private:
MutexAccess &operator=(const MutexAccess &p);
MutexAccess(const MutexAccess &p);
Mutex &m_mutex;
};
#endif /* defined(__FinancialSamples__Mutex__) */
//
//  Mutex.cpp
#include "Mutex.h"
#include "Thread.h"
#include 
#include 
#include 
#include 
using std::vector;
using std::cout;
using std::endl;
struct MutexData {
pthread_mutex_t m_mutex;
};
Mutex::Mutex()
: m_data(new MutexData)
{
pthread_mutex_init(&m_data->m_mutex, NULL);
}
Mutex::~Mutex()
{
if (m_data)
{
pthread_mutex_destroy(&m_data->m_mutex);
delete m_data;
}
}
void Mutex::lock()
{
pthread_mutex_lock(&m_data->m_mutex);
}
void Mutex::unlock()
{
pthread_mutex_unlock(&m_data->m_mutex);
}
/// ----
MutexAccess::MutexAccess(Mutex &m)
: m_mutex(m)
{
m_mutex.lock();
}
MutexAccess::~MutexAccess()
{
m_mutex.unlock();
}
/// ----
class MutexTestThread  : public Thread {
public:
MutexTestThread(double &result, double incVal);
~MutexTestThread();
void run();
private:
double &m_result;
double m_incValue;
static Mutex m_globalMutex;
};
Mutex MutexTestThread::m_globalMutex;  // 全局互斥锁是静态的
MutexTestThread::MutexTestThread(double &result, double incVal)
: m_result(result),
m_incValue(incVal)
{
}
MutexTestThread::~MutexTestThread()
{
}
void MutexTestThread::run()
{
MutexAccess maccess(m_globalMutex);  // 在此处锁定互斥锁
cout << "accessing data" << endl;
if (m_result > m_incValue)
{
m_result -= m_incValue;
}
else
{
m_incValue += m_incValue;
}
// 互斥锁自动解锁
}
int main()
{
int nThreads = 10;
vector<Thread *> threads(nThreads);
double price = rand() % 25;
for (int i=0; i<nThreads; ++i)
{
threads[i] = new MutexTestThread(price, i);
threads[i]->setJoinable(true);
threads[i]->start();
}
for (int i=0; i<nThreads; ++i)
{
threads[i]->join();
}
cout << " final price is " << price << endl;
return 0;
}
清单 17-3
Mutex 类
```

### 运行代码

您可以使用任何标准 C++ 编译器编译此代码。我在一台运行 Mac OS X 操作系统的机器上进行了测试。以下是示例结果的显示：

```
accessing data
accessing data   ...
accessing data
accessing data
accessing data
final price is 2
```

### 使用标准库创建线程

在上一节中，您学习了如何使用 `pthreads` 库创建多线程程序。在 C++20 中，也可以使用标准库创建多线程代码。该支持通过 `<thread>` 头文件提供。

要使用 STL 创建简单的多线程程序，无需创建新的类或对象。`std::thread` 类已经能够通过接收函数、lambda 表达式或函数对象作为输入来执行多线程操作。

请考虑以下示例：

```
#include <iostream>
#include <thread>
#include <vector>
void compute_max(const std::vector<double> &values)
{
auto total = 0.0;
for (auto v : values)
{
total += v;
}
std::cout << total << std::endl;
}
int main()
{
std::vector<double> v = {0, 5, 3, 2, 5, 3};
std::thread first_tread(compute_max, v);
first_tread.join();
return 0;
}
```

在这里，我们定义了一个名为 `compute_max` 的简单函数，它接收一个 `double` 类型的向量作为参数。这个函数可以是任何需要长时间运行的操作类型，我们希望对它进行独立线程处理。要使用此函数创建新线程，我们只需使用 `<thread>` 头文件中的 `std::thread` 类。

`std::thread` 类接收您想要使用的函数（或函数对象）的名称，以及零个或多个将传递给该函数的参数。在前面的示例中，我们有一个名为 `v` 的向量作为唯一参数。如果 `compute_max` 函数需要，这可以扩展到其他参数。

最后，`first_thread` 对象调用 `join()` 方法，以指示 `main` 函数将等待该线程执行完毕，直到其完成。如果我们不想等待线程完成，可以使用 `detach()` 方法，该方法允许线程独立运行，同时当前函数继续执行其操作。

### 结论

多核处理器和架构的不断发展极大地扩展了现代机器的计算能力。然而，为了利用这种多核架构，有必要改变编程方式。现代高性能编程越来越关注多处理技术，这些技术允许应用程序访问多个内核，从而提高系统性能。

多线程是一种编程技术，它允许每个进程拥有多个执行线程。如果机器拥有多个处理器，多线程允许您访问这些处理核心来执行额外的工作。在本章中，您学习了如何使用标准的 pthreads 库创建、终止和管理线程。

在第一个编程示例中，您了解了 pthreads 库以及如何使用它来创建新线程。您学习了如何设计一个 C++ 类来封装 pthreads 函数调用。使用 pthreads，您可以简化多线程应用程序，因为它抽象了与系统相关的多线程 API。

接下来，您学习了如何将 pthreads 应用于期权中的一个常见问题。您了解到，对于某些问题，很容易将必要的工作分配到不同的计算单元中。使用 C++，您可以将此类代码段封装到不同的对象中。

在下一节中，您学习了同步原语以及如何使用 pthreads 库实现它们。我介绍了一个可用于模拟互斥锁操作的类。您可以随时将 `Mutex` 类应用于其他金融编程项目。

最后，我还解释了新的 C++ 标准 C++20 如何在不使用像 pthreads 这样的单独库的情况下，直接提供对线程的支持。因此，对于新代码，可以简化应用程序并依赖 STL。虽然许多现有的多线程代码仍然使用像 pthread 这样的库，但学习如何使用标准库并在新项目中使用它非常重要。

在本章中，我完成了用于使用 C++ 创建高性能金融应用程序的技术工具的总体介绍。我希望您喜欢了解 C++ 的特性以及如何将它们应用于解决金融行业的常见问题。

