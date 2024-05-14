© 作者，独家许可 Springer Nature Switzerland AG 2022 T. Barrau，R. Douady 金融市场人工智能 金融数学与金融科技 [https://doi.org/10.1007/978-3-030-97319-3_7](https://doi.org/10.1007/978-3-030-97319-3_7)

# 7. 遗传算法组合预测

Thomas Barrau^([1](#Aff5)) 和 Raphael Douady^([2](#Aff6)) (1)AXA 投资经理合唱团有限公司，香港，中国香港特别行政区；(2)巴黎第一索邦大学经济中心，法国巴黎

## 摘要

我们提出了一个函数，可以将市场、行业和个别股票收益的预测结合到一个单一的投资组合中。该组合通过双重嵌套优化进行，使用数字优化和遗传优化来考虑交易成本。结果得到的聚合交易策略在扣除交易成本后达到了 0.94 的夏普比率，而市场投资组合仅为 0.43。我们提出的遗传优化也胜过了简单的风险平价基准。

关键词：遗传算法、Polymodel 理论、人工智能、机器学习、α 组合、交易成本、股票收益的横截面、市场定时、交易策略、交易信号、股票收益预测、风险平价

## 7.1 引言

本章的目的是提出一种将第 [4](519851_1_En_4_Chapter.xhtml)、[5](519851_1_En_5_Chapter.xhtml) 和 [6](519851_1_En_6_Chapter.xhtml) 章中获得的预测进行组合的方法。这些预测是通过市场、行业和股票收益的特定部分的不同方式进行的。正如在介绍中所述，将这些不同预测组合起来的投资组合的收益可以表示为以下函数的形式： ![$$ {r}_P={\varOmega}_p\left({r}_M,{\mathcal{F}}_I,{\mathcal{F}}_S\right). $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ1.png)(7.1) *这里的 Ω*[*p*] *是子投资组合收益的聚合函数，r*[*M*] *是市场因子投资组合的收益，* ![$$ {\mathcal{F}}_I $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_IEq1.png) *是由行业因子集合构成的投资组合的收益，而* ![$$ {\mathcal{F}}_S $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_IEq2.png) *则是由特定因子集合构成的投资组合的收益。*在我们的情况下，由于我们各自的集合中只有一个行业和一个特定因子，因此方程 ([7.1](#Equ1)) 可以简化为以下形式： ![$$ {r}_P={\varOmega}_p\left({r}_M,{r}_{AFI,I},{r}_{PP,S}\right). $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ2.png)(7.2) *这里的 r*[*AFI*, *I*] *是反脆弱性指标行业投资组合的收益，而 r*[*PP*, *S*]* 则是 Polymodel 预测特定投资组合的收益。*

因此，本章的目的是提出首个可扩展的ω（omega）的定义。

首先，我们注意到ω在[第 4 章](519851_1_En_4_Chapter.xhtml)中专门讨论市场定时的信号部分已经被部分定义。事实上，由于它是原始因子回报的函数，ω包括了聚合投资组合表现的市场和因子定时元素。但这一点不太重要，只是一种定义上的问题。更有趣的是，我们应该观察到，将特定和行业回报的横截面预测与市场回报的时间序列预测混合在一起并不会导致直观的投资组合构建。因此，由于最终是由对同一投资领域所采取的头寸定义的，我们决定直接聚合不同预测的投资组合头寸（而不是信号），以简化处理。

机器学习方法，尤其是神经网络，最近在预测器的组合方面与经典的线性回归等传统方法相比取得了巨大的改进（正如 Gu 等人 [2018 年](#CR9) 所示）。然而，由于我们只有三个预测要组合，用非线性方式定义ω似乎有些过火。因此，我们选择通过对子投资组合进行线性加权来定义ω。

然而，我们不应该过于简单化。事实上，正如本章后面所强调的，即使在我们的简单情况下，交易成本对策略最终实施的表现也非常重要。因此，我们将交易成本管理纳入到ω中，以产生更优异的*净*回报。

交易成本的管理通过交易的部分执行和适当的子投资组合加权来实现。与每个子投资组合相关联的权重是由专门设计用于此目的的遗传算法确定的。当前章节的主要提议是通过优化不同股票回报组成部分的预测的最佳组合来利用遗传算法来最大化投资组合的净夏普比率。

遗传算法已经被用来组合不同的预测器。Zhang和Maringer（[2016](#CR21)）提出了一种遗传算法来组合横截面预测器，可以理解为因子。虽然他们在算法设计中没有考虑交易成本，但他们的遗传算法的应用仍然类似于我们的。Allen和Karjalainen（[1999](#CR1)）主要使用遗传算法来发现技术交易规则（基于预测器的市场内外信号），但也用来组合它们。同样，他们的算法没有引入交易成本。在他们的[2012](#CR16)年的论文中，Sefiane和Benbouziane展示了用遗传算法构建一个5资产投资组合的效率，在均值方差意义上是最优的。这样的问题也与我们当前的问题相似，因为我们正在寻求最佳地组合三个子投资组合的投资组合，这三个子投资组合可以被视为三个资产。Sinha等人（[2015](#CR17)）也提出了一种用于投资组合优化的遗传算法，没有考虑交易成本。

遗传算法也已被用来限制交易成本。确实，Lin和Wang（[2002](#CR12)）提出了一种考虑交易成本的均值方差优化的遗传算法，但他们的设置略有不同（他们使用固定的交易成本和整数股数，而我们使用比例交易成本，我们认为这更加现实）。

当然，减少交易成本的同时组合不同的预测器（或alpha）的问题已经在Gârleanu和Pedersen的开创性论文中讨论过（[2013](#CR7)），他们建议使用动态规划来减少交易费用。我们在当前章节中重新使用了他们框架的一部分，这可以理解为在他们第五部分（“理论应用”）的案例2（“基于安全特征的相对价值交易”）中提到的理论案例的经验实现。除了他们的研究外，还有一些其他方法被开发用来减少交易成本。例如，Ruf和Xie（[2019](#CR15)）提出了一种方法来限制交易成本对系统生成的投资组合的影响，尽管他们没有为此目的使用遗传算法。

因此，据我们所知，一种基于遗传算法的方法，允许我们组合alpha以及净交易成本的表现的方法似乎在文献中不存在，这就是我们的提议。

本章的其余部分组织如下：

+   在第一部分中，我们介绍了我们即将聚合的不同预测器，以及它们的定义、投资组合构建方法和经济原理。

+   在第二部分中，我们提出了一个非常简单的组合方法，在文献中是标准的，是遗传算法超越的基准。我们在这里介绍了交易成本的问题，以及一种“a la Gârleanu–Pedersen”的方法来减少它们。

+   在第三节中，我们介绍了用于加权相应预测的子组合的遗传算法。

+   最后，我们进行了一系列鲁棒性测试，并总结了本章。

## 7.2 策略分析

### 7.2.1 市场回报的预测

市场回报的预测是在第[4](519851_1_En_4_Chapter.xhtml)章中开发的。用于预测的信号是通过估算 S&P 500 的 polymodel 构建的。然后测量每个基本模型的 RMSE，以形成 RMSE 的分布。这个分布捕捉了市场与其整个经济环境之间的联系的强度。根据滚动方法，信号然后学习了发生在熊市之前的 RMSE 的分布。然后将该分布与当前分布进行比较，以形成对下个月市场回报方向的预测。

因此，信号是一个开关型投资信号，通过在风险关闭时以 0.5（空头头寸）的动态杠杆投资于市场组合，当风险开启时为 +2（多头头寸）。结果的组合相对缓慢，半衰期^([1](#Fn1))为 47 天。平均而言，该组合的杠杆为 1.37。下面是基于此市场定时组合的累积表现^([2](#Fn2))，从 2005-01-01 到 2018-28-09（图[7.1](#Fig1)）。该时期的夏普比率为 0.92。![](../images/519851_1_En_7_Chapter/519851_1_En_7_Fig1_HTML.png)

图 7.1

基于市场回报预测的组合表现

### 7.2.2 行业回报的预测

行业回报的预测是在第[5](519851_1_En_5_Chapter.xhtml)章中开发的。用于预测的信号是使用 LNLM 模型构建的，其中每个行业回报作为目标变量，市场回报作为解释变量。然后根据每个行业对市场的凹凸性计算脆弱性的度量（即塔勒布的反脆弱性概念（见塔勒布，[2012](#CR19)））。然后根据每个行业对市场的凹凸性计算脆弱性的度量，然后计算脆弱性的度量，一个大的正信号对应一个高度脆弱的行业。事实上，脆弱的行业似乎受益于风险溢价，因为它们对极端市场偏差的敞口通过更高的回报得到补偿。

信号然后以长短方式实施，平均预期对市场暴露较小。投资组合的头寸是使用特征投资组合（Grinold & Kahn，[2000](#CR8)）获得的，这基本上是对信号进行均值方差优化，实现目标波动率为10%。结果投资组合也是一个（非常）慢的投资组合，半衰期约为400天，但是，平均杠杆达到8.31，比市场投资组合更具杠杆。请注意，对于长短组合来说，这种杠杆水平比仅多头组合更少问题，因为多头头寸的成本可以部分由空头头寸融资。下面是行业投资组合的累积表现，从2005-01-01到2018-28-09（图 [7.2](#Fig2)）。达到的夏普比率为0.96。![](../images/519851_1_En_7_Chapter/519851_1_En_7_Fig2_HTML.png)

图 7.2

基于行业收益预测的投资组合表现

### 7.2.3 特定收益的预测

特定收益的预测是在第 [6](519851_1_En_6_Chapter.xhtml) 章中进行的。信号是通过对多模型进行直接应用而获得的：进行预测。对于股票收益的横截面的每家公司，我们使用大量市场变量（股票指数，货币，商品等）估计一个多模型，目标变量是公司的特定收益。然后使用该多模型进行预测。在对预测进行选择和聚合方法后，为每家公司获得一个组合预测，从而形成整个股票收益的预测。

与行业预测类似，信号然后通过目标波动率为10%的长短特征投资组合实施。由于我们选择的投资组合构建方式，结果投资组合变化迅速，半衰期为9天，并且平均杠杆与行业投资组合相似（平均为8.27）。下面是特定投资组合的累积表现，从2005-01-01到2018-28-09（图 [7.3](#Fig3)）。达到的夏普比率为1.07。![](../images/519851_1_En_7_Chapter/519851_1_En_7_Fig3_HTML.png)

图 7.3

基于特定收益预测的投资组合表现

### 7.2.4 相关性分析

对不同投资组合之间以及与市场投资组合的收益相关性进行分析，可以帮助我们理解将它们组合的潜在分散效益。下面是每日收益的相关性矩阵（图 [7.4](#Fig4)）：![](../images/519851_1_En_7_Chapter/519851_1_En_7_Fig4_HTML.png)

图 7.4

交易策略每日收益的相关性矩阵

市场投资组合（基准）和市场定时投资组合 unsurprisingly 相关联，但相关性仅为 30%，考虑到信号实施方法，这似乎相对较低。特定投资组合似乎是一个很好的分散风险者，而行业预测显示与市场投资组合存在一些相关性。如果我们使用每周收益率考虑相关性，如下所示（图 [7.5](#Fig5)）：

图 7.5

交易策略的每周收益相关性矩阵

使用月度收益率，市场定时投资组合似乎与基准更相关，而行业预测明显更不相关（图 [7.6](#Fig6)）：

图 7.6

交易策略的每月收益相关性矩阵

我们在每日表格（图 [7.4](#Fig4)）中观察到的行业预测的相对高（23%）相关性主要是由其与市场投资组合的尾部相关性所解释的。事实上，仅保留两端的 1% 最极端的市场收益来计算相关性，会导致市场和行业投资组合之间的尾部相关性达到 41%。因此，在极端波动期间，行业投资组合与市场更相关。由于行业投资组合的收益来自于对极端市场风险的暴露，预计即使是长/短期投资组合构建也无法完全消除市场风险。

在我们即将构建的总投资组合中，这种负特性得到了负的尾部相关性的补偿，特定投资组合（-12%）和市场定时投资组合（-31%）与基准之间存在着负的尾部相关性。

## 风险平价组合

### 引入风险平价

可以使用 1/3 权重来组合三种不同的投资组合。遵循这种 1/N 方法已被证明是一种强大的聚合预测方法，在许多情况下很难击败（见 Rapach 等，[2010](#CR14)；Timmermann，[2006](#CR20)）。然而，我们使用的预测，即使考虑相同的资产，也受到不同投资组合构建方法的扭曲，因此更适合聚合子投资组合而不是预测。此外，子投资组合的相关性足够低，可以被视为不同的资产。在组合多个资产的投资组合时，也发现 1/N 方法是强大的（DeMiguel 等，[2009](#CR6)）。

然而，在我们的设定中，1/N应该是什么并不清楚。给予具有非常不同杠杆的投资组合相同的资金似乎是不公平的，这些杠杆范围从1到8不等。一个解决方案可能是调整投资于不同投资组合的资本，以便所有投资组合实际上都投资了相同数量的资金（这等于资本×杠杆）。但是，我们直接遇到的问题是市场定时策略的表现得益于动态杠杆。因此，资本的调整似乎不适合创建1/N策略。

另一种方法是给予每个策略相同的波动率预算，导致1/波动率的权重。这种方法称为“风险平价”，已经以各种格式实施，无论是在学术作品中还是在经验投资组合中，都表现出良好的结果（Clarke等人，[2013](#CR5)）。我们将其用作定义聚合方法基准的起点。使用滚动5年窗口^([3](#Fn3))来计算波动性得到的权重如下（图[7.7](#Fig7)）：![](../images/519851_1_En_7_Chapter/519851_1_En_7_Fig7_HTML.png)

图7.7

风险平价组合：随时间变化的策略权重

我们观察到权重非常稳定，这是一个有趣的特性，因为不稳定的权重会导致投资组合更多的换手，因此会有更多的交易成本。我们还注意到特定预测投资组合的波动性相对较小，这导致了风险平价加权投资组合的权重更高。

以下是使用此方法获得的聚合投资组合的随时间变化的表现（图[7.8](#Fig8)）：![](../images/519851_1_En_7_Chapter/519851_1_En_7_Fig8_HTML.png)

图7.8

风险平价投资组合的总体表现

除了在很长时间内非常稳定外，所获得的表现还显示出非常令人羡慕的指标：夏普比率达到了1.58，年均收益率为18%，实现波动率为10%，最差回撤为9%。

### 7.3.2 交易成本很重要

我们提出了一个第一个，直观的聚合投资组合的实现，我们看到了一个很高的*总体*表现。然而，更多的不仅仅是作为交易策略预测能力的有用指标，模拟投资组合的表现间接地暗示着该策略可能是有利可图的。这种盈利性，要有效，必须通过在现实世界中实施的最终测试。尽管这超出了我们在此进行的学术工作的范围，但我们仍然可以通过在我们的建模中引入交易成本来减少实际世界的损益和我们的模拟之间的距离。

股票市场的交易成本由几个因素组成：经纪人费用，不同的税费，当然还有交易的市场影响。历史上已发现，包括这些不同类型成本的比例聚合交易成本每单位周转量约为10个基点至25个基点^([4](#Fn4))（Sweeney，[1988](#CR18)）；Allen & Karjalainen，[1999](#CR1)）。更近期的Guo（[2006](#CR10)）发现了25个基点的值，这似乎与最新研究保持一致（例如Brière等人（[2019](#CR3)）进行了一项使用大型资产管理公司数据的研究，使他们的结果在我们的交易策略实施背景下尤其相关）。

我们选择保守地考虑比例交易成本的25个基点数量，将其加到风险平价投资组合的总收益中，以形成投资组合的净收益。让我们称投资组合在我们的500支股票投资领域内采取的仓位向量为“w”。在日期t：![$$ {w}_t=\left({w}_{1,t},{w}_{2,t},\dots, {w}_{500,t}\right). $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ3.png)(7.3)交易“Δ”由两个日期之间的仓位差异给出：![$$ {\varDelta}_t={w}_t-{w}_{t-1} $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ4.png)(7.4)因此，除去交易成本后的投资组合绩效为：![$$ {r}_{P,t}=\sum \limits_{a=1}^{500}{w}_{a,t}\times {r}_{a,t}-\left|{\varDelta}_{a,t}\right|\times 0.0025\. $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ5.png)(7.5)由于交易成本的增加，绩效大幅下降，夏普比率变为-4.88，这强调了理论与实际策略实施之间差距的重要性。

### 7.3.3 交易成本的ex-ante最优降低

将交易成本纳入考虑后观察到的绩效显著下降可以通过更少天真的投资组合构建大大减少。实际上，交易明显导致了过高的成本。

Gârleanu 和 Pedersen ([2013](#CR7)) 在金融领域推广了动态规划的应用。 他们降低投资组合交易成本的提议基于两个核心原则：目标前瞻，部分交易朝向目标。 我们这里着重讨论第二个原则，该原则在他们的论文中由方程 ([7.6](#Equ6)) 反映出来：![$$ {w}_t=\left(1-\theta \right){w}_{t-1}+\theta\ {aim}_t. $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ6.png)(7.6)*这里 w 是投资组合所持头寸的横截面，aim 是在没有交易成本的世界中我们想要实现的投资组合，θ 是最佳交易速率，即将总交易量与目标投资组合之间的百分比，最大化净收益。*我们通过数值计算前期最佳交易速率来实现这一原则，使用 5 年滚动窗口（在模拟开始时窗口再次扩展）。 因此，θ 成为一个时间相关参数：![$$ {w}_t=\left(1-{\theta}_t\right){w}_{t-1}+{\theta}_t\ {aim}_t. $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ7.png)(7.7)下面是 θ 随时间变化的取值（图 [7.9](#Fig9)）：![](../images/519851_1_En_7_Chapter/519851_1_En_7_Fig9_HTML.png)

图 7.9

前期最佳交易速率随时间变化

尽管这些值可能看起来很小，考虑到相对较高的交易成本水平（回想一下基本投资组合的净夏普为−4.88），这并不完全令人惊讶。 而且，较高的值很快导致较低的回报，正如表 [7.1](#Tab1) 所示：^([5](#Fn5))表 7.1

不同交易速率的年度净收益

| 交易速率（%） | 平均年净收益率（%） |
| --- | --- |
| 1.0 | 9.42 |
| 1.5 | 9.07 |
| 2.0 | 8.37 |
| 2.5 | 7.54 |
| 3.0 | 6.67 |
| 4.0 | 4.98 |
| 5.0 | 3.42 |

从这张表中可以清楚地看出，探索更高的交易速率值是不值得的。

使用我们的前期滚动最佳策略，净收益达到 9.10%，这使得滚动方法的结果与最佳后期交易速率表现可比。 这些诚实的结果应该与我们现在考虑的夏普比一起考虑，考虑净收益：0.81。 下面是信号的净累积表现（图 [7.10](#Fig10)）：![](../images/519851_1_En_7_Chapter/519851_1_En_7_Fig10_HTML.png)

图 7.10

风险平价投资组合净表现，带动态规划平滑处理

显然实现的表现表明，适当的投资组合构建对于宣称该策略可能是有利可图的至关重要。 正如以前的文献中所显示的那样（Ciliberti & Gualdi，[2020](#CR4)），投资组合构建问题因此是交易策略的经济表现的重要决定因素。

## 7.4 基于遗传算法的组合

### 7.4.1 方法论

我们看到，风险平价允许我们在组合投资组合时实现显著的总体表现，当引入交易成本时，可以部分保留，通过大幅减少交易量。然而，交易成本的存在使得人们对风险平价作为组合方法的选择是否恰当产生了不确定性。特别是，对策略的分析表明，特定回报的预测对应于变化最大的子组合（远远超过），使其成为聚合组合的交易成本的潜在主要贡献者。与此同时，特定投资组合是波动性最低的投资组合，使得风险平价分配给该投资组合的比例为50%。因此，扣除交易成本后，使用风险平价可能是一个次优的加权选择。

一个合适的加权算法应该平衡总体性能、交易成本和风险，风险以组合的波动性来衡量。因此，它将导致一个更优越的净夏普比率，可以作为一个优化的指标。然而，如果我们保持之前提出的滚动最优交易率机制，那么子组合加权优化问题将嵌套在交易率优化问题中。因此，使用数值优化算法可能是保持我们最终设置相对简单的合适选择。

遗传算法将是这项任务的一个合适的候选者。由Holland在1975年引入（有关原始论文的更近期版本，请参阅Holland ([1992](#CR11))），遗传算法已被广泛应用于金融领域的各种优化问题（有关遗传算法在金融中的介绍和应用的评论，请参见Pereira ([2000](#CR13))）。作为一种优化算法，它确实适用于子组合加权问题。

遗传算法将优化问题以个体（或染色体）的人口形式表示，这些个体在模仿自然选择的机制下逐步演变。我们以下描述染色体人口如何演变，直到它达到最终状态，解决了日期t的优化问题，使用经典算法来适应我们当前的优化问题。

使用遗传算法意味着优化以一组竞争个体的形式表示。其中每个个体都是优化问题解的表示。个体的特征由其基因型确定，通常由0和1的向量表示。在我们的情况下，第*v*个体的基因型是一个包含每个子组合权重的向量（为了保持合理的多样化水平，权重为正且不低于15%）：![$$ {g}_v=\left({\beta}_{v,M},{\beta}_{v,I},{\beta}_{v,S}\right)\kern1.5em s.t.\kern1.25em {\beta}_{v,M}+{\beta}_{v,I}+{\beta}_{v,S}=1\kern1.75em s.t.\kern1.25em {\beta}_{v, sub}\ge 0.15\  for\  sub\  in\ \left\{M,I,S\right\}. $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ8.png)(7.8)*这里的 β[v, M] 是市场子组合的权重，β[v, I] 是（反脆弱性指标）行业子组合的权重，而 β[v, S] 是（Polymodels 预测）具体子组合的权重。*这个基因型随着时间而演变，一个简单的动态表示如下：![$$ {g}_{v,t}=\left({\beta}_{v,t,M},{\beta}_{v,t,I},{\beta}_{v,t,S}\right). $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ9.png)(7.9)算法从生成随机初始种群“IP”开始，即一组具有基因型的50个个体，这些基因型是在均匀分布中随机选择的：![$$ {IP}_t=\left\{{g}_{1,t},{g}_{2,t},\dots, {g}_{50,t}\right\}. $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ10.png)(7.10)然后根据适应性评分对这些个体进行评估。在我们的情况下，个体的适应性评分是通过投资组合的净夏普比率（在交易费率优化后）加权计算的。在时刻 t，聚合投资组合*w*，定义为与个体*v*对应的位置向量，因此为：![$$ {w}_{v,t}=\left(1-{\theta}_{v,t}\right){w}_{v,t-1}+{\theta}_{v,t}\ \left({\beta}_{v,t,M}\times {w}_{t,M}+{\beta}_{v,t,I}\times {w}_{t,I}+{\beta}_{v,t,S}\times {w}_{t,S}\right). $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ11.png)(7.11)在这里，最优交易速率每天都会计算，使用先前描述的过程。请注意，这样的聚合投资组合构建意味着聚合函数 omega 是线性的，取决于交易速率：![$$ {\varOmega}_{v,t}\left({r}_M,{r}_{AFI,I},{r}_{PP,S}\right)\Rightarrow {\varOmega}_{v,t}\left({\theta}_{v,t},{\beta}_{v,t,M},{\beta}_{v,t,I},{\beta}_{v,t,S}\right). $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ12.png)(7.12)因此，与个体*v*相关的投资组合的实现净收益是：![$$ {r}_{v,t}=\sum \limits_{a=1}^{500}{r}_{a,t}\times {w}_{a,v,t}-\left|{\varDelta}_{a,v,t}\right|\times 0.0025 $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ13.png)(7.13)因此，个体的适应性评分简单地是其夏普比率，我们使用过去10年的净投资组合收益来计算：![$$ {S}_{v,t}=\left(\sum \limits_{s=t-252\ast 10}^t{r}_{v,s}-{risk}_{-}{free}_{-}{rate}_s\right)/{\sigma}_{v,s\to t}\times \sqrt{252}. $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ14.png)(7.14)*这里 σ[v, s → t] 是截至日期 t 的过去10年中投资组合超额日收益的标准差。*适应性评分被归一化，以便它们都是正值，通过从所有适应性评分中减去最小的评分来实现：![$$ S{\prime}_{v,t}={S}_{v,t}-\mathit{\min}\left({S}_t\right). $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ15.png)(7.15)个体的适应性评分决定了其在下一时期的繁殖概率，定义为：![$$ {rp}_{v,t}=S{\prime}_{v,t}/\sum \limits_{v=1}^{50}S{\prime}_{v,t}. $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ16.png)(7.16)然后繁殖阶段开始，结构如下：

+   两个个体，被称为“父母”，是从初始种群中随机选择的，其选择概率来自于上述方程（Eq. ([7.16](#Equ16)）。

+   这两个父母产生两个孩子，这是通过一种称为“交叉”的操作获得的：父母交换他们的基因型的一部分，在我们的案例中是1个元素（基因型的1/3），即1个子投资组合权重。

+   每个孩子有30%的变异概率。如果其中一个孩子突变了，就给它一个随机的基因型。这个特性允许种群保持遗传多样性，即它防止优化算法过快收敛。

重复生殖阶段25次，生成由50个个体组成的新种群“NP”。这个新种群将成为下一个“时期”的初始种群，它代表了种群的新进化时期:![$$ {NP}_{t, epoch=1}={IP}_{t, epoch=2}. $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ17.png)(7.17)这个完整的过程，它规定了一个初始种群如何进化成为一个新种群，然后重复20次，使用t时刻可用的最近10年的相同数据:![$$ {IP}_{t, epoch=1}\to {NP}_{t, epoch=1}={IP}_{t, epoch=2}\to {NP}_{t, epoch=2}=\dots ={IP}_{t, epoch=20}\to {NP}_{t, epoch=20}. $$](../images/519851_1_En_7_Chapter/519851_1_En_7_Chapter_TeX_Equ18.png)(7.18)例如，从2015年12月31日开始，我们使用2006年1月1日至2015年12月31日的数据。我们创建一个初始的随机基因型，并按照上述的繁殖/选择过程更新每个时期的初始种群，使其成为上一个时期的新种群。最后，我们计算最后一代个体的基因型的平均值。在这个阶段，种群的多样性已经减少，因为选择过程随着时间的推移倾向于消除不适应的个体。

因此，平均基因型由我们在日期t获得的最终子投资组合权重给出。然后，这些权重用于产生我们在接下来的一年中考虑的最终汇总投资组合的回报。回到之前的例子，我们使用了截止到2015年12月31日可用的数据来产生子投资组合权重。通过这种方式获得的汇总投资组合绩效仅在2016年及以后感兴趣（否则所呈现的绩效将因内样本优化而受到影响）。我们仅考虑2016年的这种绩效，之后完整的过程会重复进行，以生成2017年及以后的汇总投资组合的外样本绩效，并且以这种滚动方法持续下去。^([6](#Fn6))

### 7.4.2 结果

下面是遗传算法产生的子投资组合权重（图[7.11](#Fig11)）:![](../images/519851_1_En_7_Chapter/519851_1_En_7_Fig11_HTML.png)

图7.11

遗传算法组合：策略的权重随时间变化

预料之中，由于个体的适应度分数基于它们的*净*收益，相比于风险平价，该算法倾向于低估特定投资组合的权重，因为该投资组合在交易成本方面可能非常不利。除此之外，我们可以看到即使权重随时间变化，子投资组合的加权方案也不会出现极端的重组，即使市场投资组合似乎随着时间的推移更多地投资。

该策略的净夏普比率为0.94，而风险平价投资组合为0.81，而年净回报分别为10.40%和9.10%。以下是两个累积损益曲线（图[7.12](#Fig12)）：

图 7.12

风险平价和遗传算法组合绩效的比较

从2006-01的价值为100开始，到2018-10，遗传算法组合投资组合和风险平价组合的最终价值分别为353和295。遗传算法投资组合似乎在大多数时间内提供更高的回报，正如我们可以从两种策略的累积损益差异（图[7.13](#Fig13)）中评估的那样：

图 7.13

组合方法之间随时间的累积损益差异

除了经济上显著的绩效增长，改进在统计上也是显著的。这可以通过使用线性模型对遗传组合回报建模来评估。该模型包括一个常数和风险平价回报。经OLS估计，该模型的常数参数显示了一个t值为2.30，对应于2.2%的*p*-值。

通过遗传算法聚合的投资组合也超越了市场投资组合。以下是两个投资组合的损益曲线（标准化为10%年波动率以进行比较）（图[7.14](#Fig14)）：

图 7.14

遗传算法组合和市场投资组合绩效的比较

最终的综合投资组合与市场投资组合呈40%的相关性。然而，遗传组合的夏普比率在该时期达到了0.94，而市场投资组合仅为0.43。如果我们考虑到市场投资组合达到的最大回撤，市场投资组合为-55%，而遗传投资组合仅为-22%。

## 7.5 鲁棒性测试

我们对遗传算法使用的不同参数对策略绩效的敏感性进行了分析。

### 7.5.1 变异概率

上述算法版本中使用的变异概率为30%。下面，我们展示了策略对于10%、20%、30%、40%和50%变异概率的净夏普比率：

性能似乎对该参数的变化相对稳健。它似乎在30%达到了一个最优水平，这个高水平是合理的，因为突变概率是算法防止过拟合的主要防线。对于我们给定的周期数量，较低的概率会导致潜在的早期收敛到“极端”解决方案，而逐渐增加概率则可以防止算法的收敛。需要注意的是，对这种推理的支持更多地来自于其内在的有效性，而不是检查表[7.2](#Tab2)的数字，因为我们对夏普比率差异为0.02的统计显著性表示怀疑。表7.2

对夏普比率对突变概率的敏感性

| 突变概率 | 10% | 20% | 30% | 40% | 50% |
| --- | --- | --- | --- | --- | --- |
| 净夏普比率 | 0.91 | 0.92 | 0.94 | 0.91 | 0.92 |

### 7.5.2 染色体数量

在上述算法的版本中，我们使用了50个染色体。下面，我们展示了策略在20、35、50、75和100个染色体内的净夏普比率（表[7.3](#Tab3)）：表7.3

对夏普比率对染色体数量的敏感性

| 染色体数量 | 20 | 35 | 50 | 75 | 100 |
| --- | --- | --- | --- | --- | --- |
| 净夏普比率 | 0.92 | 0.92 | 0.94 | 0.89 | 0.92 |

更多的染色体数量可能会提供更稳健的结果，但也会以更高的计算时间为代价，这似乎并不符合性能的要求。以75个染色体实现的性能似乎有点令人担忧。策略的实证实现应该考虑到这一点，例如通过对不同数量的染色体获得的子组合权重进行平均。

### 7.5.3 周期数量

在上述算法的版本中，我们使用了20个周期。下面，我们展示了策略在5、10、20、30和50个周期内的净夏普比率（表[7.4](#Tab4)）：表7.4

对夏普比率对周期数量的敏感性

| 周期数量 | 5 | 10 | 20 | 30 | 50 |
| --- | --- | --- | --- | --- | --- |
| 净夏普比率 | 0.88 | 0.92 | 0.94 | 0.93 | 0.91 |

在这里，我们再次看到所选择的参数对过度和不足拟合的问题相对应于一个最优值。其他参数配置，如果联合调整，可能会导致类似或更高的性能（例如，具有更大的突变概率和更多的周期）。

### 7.5.4 种子

用于在上述算法版本中获得可复制的随机性的种子标记为＃1。随机性用于选择突变的染色体，生成初始种群权重和选择适合的个体进行繁殖。下面，我们展示了种子＃1、＃2、＃3、＃4和＃5的策略的净夏普比率（表[7.5](#Tab5)）：表7.5

对夏普比率对随机种子的敏感性

| 种子 | #1 | #2 | #3 | #4 | #5 |
| --- | --- | --- | --- | --- | --- |
| 净夏普比率 | 0.94 | 0.92 | 0.93 | 0.94 | 0.89 |

除了选定的种子之外，其他种子提供了可比较的表现，除了种子#5。这证明了需要对从不同种子获得的子组合权重进行平均以避免性能对种子的敏感性。

### 7.5.5 最优交易速率窗口

遗传算法投资组合考虑到交易成本，这要归功于使用前期最优交易速率，该速率在给定滚动窗口上测得最大的净夏普比率。上述算法版本中使用的滚动窗口长度为5年。下面，我们显示了策略在3、4、5、6和7年期间的净夏普比率（表[7.6](#Tab6)）：表7.6

净夏普比率对最优交易速率窗口的敏感性

| 最优交易速率窗口 | 3年 | 4年 | 5年 | 6年 | 7年 |
| --- | --- | --- | --- | --- | --- |
| 净夏普比率 | 0.92 | 0.92 | 0.94 | 0.92 | 0.93 |

再次表现出对该参数变化的敏感性似乎相当小。

总的来说，使用基于遗传算法组合得到的聚合策略的表现并不特别受到采用算法参数的其他配置的影响。这种行为表明上述结果具有很高的鲁棒性。

## 7.6 结论

本章表明，投资组合构建对于实施交易策略至关重要。它提出了一种解决股票回报率不同预测的子投资组合加权问题的解决方案，同时显著降低了交易成本。这样的解决方案并不是琐碎的，因为它涉及一个双层嵌套的优化问题。

还可以探索其他优化方法，例如（随机）梯度下降，鉴于它在其他机器学习应用（如神经网络）中的成功。

然而，所呈现的结果表明，遗传算法适用于α组合问题，使我们能够在有效地考虑交易成本的同时执行α组合。这种投资组合构建方法似乎在经典基准之上增加了价值，并且据我们所知，它尚未在文献中提出过。此外，聚合投资组合在考虑期间内呈现出市场的两倍夏普比率，与市场的相关性降低以及回撤显著减少相关联。

从聚合预测的角度来看，这表明所得到的交易策略可能是有利可图的。事实上，由于包含了交易成本，当前的设置相当现实。然而，所呈现的模拟的真实性仍然不完善，因为评估交易策略的盈利性的唯一方法是实施它（这是唯一一个消除了所有可能的偏见，如前瞻性偏见、选择性偏见、技术偏见（...）的解决方案）。然而，我们预计通过包含交易成本已经在一定程度上填补了这种真实水平的差距。请注意，我们间接考虑了头寸的融资，因为在所呈现的夏普比率的计算中从收益中扣除了无风险利率。然而，杠杆成本尚未包含在内，可能会在进一步的研究中予以考虑。