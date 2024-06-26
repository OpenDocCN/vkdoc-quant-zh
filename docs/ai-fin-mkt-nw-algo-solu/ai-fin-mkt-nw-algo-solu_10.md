

# 第十章：观点动态

Luca Marconi^(1  )(1)意大利国家研究委员会认知科学与技术研究所，人工智能、大脑、心灵和社会高级学校，罗马，意大利 Via San Martino Della Battaglia 44Luca Marconi 电子邮件：luca.marc@hotmail.it

## 摘要

计算社会科学是一个具有挑战性的跨学科研究领域，旨在通过将传统社会科学研究与计算和数据驱动的方法相结合来研究社会现象。在复杂系统科学的概念框架中，这些现象所处和行动的环境可以被视为一个社会系统，由许多元素组成，这些元素是代理人，形成一个社会复杂网络：因此，代理人可以被网络的节点所代表，它们的相互位置决定了所产生的网络结构。因此，复杂系统科学的视角特别有助于研究社会环境中发生的相互作用：事实上，这些机制导致了系统水平上出现的集体行为。因此，计算社会科学的目标是研究不同的相互作用机制，以便理解它们在集体社会现象的产生中的作用。为了进行有效和稳健的研究，已经提出了许多模型，范围从一般社会动态和社会传播到群体行为和观点，文化或语言动态，或层次结构或分离形成。在这种背景下，观点动态绝对是一个需要研究的基本社会行为。通过日常经验，最重要的社会现象之一是一致意见：特别是当涉及到涉及不同观点的讨论时，达成至少部分一致的过程在人类思维演变过程中被证明是一个基本的催化剂。从单个个体的相互作用动态到大规模人口的观点动态，社会和政治生活中有许多情况下，社区需要达成共识。通过在不同主题上逐步达成共识的过程，一些观点传播，一些观点消失，一些观点通过合并和非线性模因过程演变：通过这种方式，主导的宏观文化在社会中形成并演变。因此，观点动态模型可以被看作是所谓的共识模型的一个子集。通常，共识模型允许理解当选择多个选项时一组相互作用的代理人是否以及如何达成共识：政治投票，观点，文化特征是其中的著名例子。基于代理人的建模方法特别有助于表示个体的行为并分析人口动态。在文献中，已经有许多模型旨在研究观点动态：每个提出的模型通常可以通过各种变体进行修改。在本章中，我们将通过展示主要的挑战和方法来研究和分析异质设置和条件下共识形成的整体情景。

关键词意见动态计算社会科学社会物理学卢卡·马尔科尼

目前是米兰比科卡大学计算机科学博士候选人，在人工智能和决策系统、以及米兰一家人工智能公司的业务战略师和 AI 研究员研究领域有丰富经验。他还曾担任知名媒体情报和金融传播私人顾问的研究顾问和顾问。他拥有米兰理工大学管理工程的理学和学士学位，以及巴利阿里群岛大学交叉学科物理与复杂系统研究所（IFISC UIB-CSIC）的复杂系统物理理学硕士学位。他还持有意大利国家研究理事会认知科学和技术研究所（ISTC-CNR）举办的人工智能、大脑、心灵和社会高级学校的研究生文凭，他在那里与 ISTC-CNR 的基于代理人的社会模拟实验室合作开展了计算社会科学领域的研究项目。他的研究兴趣涉及人工智能、认知科学、社会系统动力学、复杂系统和管理科学等领域。

## 10.1 引言：舆论动态学

舆论动态学旨在以形式化和数学的方式研究、建模和评估在异质社会个体之间的社会互动过程中*观点*演变的过程（Xia 等人，2011；Zha 等人，2020）。虽然这个研究领域在文献中并不是最近才出现的，但它的受欢迎程度如今已经增长：实际上，对*复杂系统*和*计算社会科学*的日益关注（Lazer 等人，2009；Conte 等人，2012）明显刺激了这一学科的发展，以及它在政治、社会学甚至金融等广泛范围的具体现实场景中的应用。在这个视角下，舆论动态学致力于模拟代理人之间的互动过程中发生的复杂现象，这些现象在系统层面上产生了*新兴行为*：一般来说，代理人通过一些简单或复杂的*社交网络*相互连接，从而实现一些特定的互动、*协调*和*信息交换*过程（Patterson 和 Bamieh，2010；Fotakis 等人，2018）。这些机制在其本质上是有限和局限的，它们指导着潜在的动态，从而使学者和研究人员能够将他们的研究集中在特定的现象和情境上：在计算社会科学的视角下，挑战在于有效地模拟互动机制，以便理解它们在*集体社会现象*的产生中的作用。

在这个概念框架中，舆论动态研究了在某种社会网络中组织的群体之间的舆论演化过程。通过日常经验，最重要的社会现象之一是*一致*或*共识*：特别是当涉及到讨论不同观点的讨论时，至少达成部分一致的过程被证明是人类思维演化的基本催化剂之一。其重要性已经被哲学和逻辑学指出，从赫拉克利特到黑格尔，都有着*论点-反论点*的概念化。社会和政治生活中有许多情况需要社区达成共识。通过对不同主题的逐步达成一致的过程，一些观点传播开来，一些观点消失，另一些通过合并和非线性的*模因过程*演化（Mueller and Tan 2018; Nye 2011）：这样，主导的宏观文化就形成并在社会中演化。因此，对参与社会群体中观点演化的不同社会和文化现象的数学和统计研究，对于帮助异质组织中的从业者、决策者、管理者和利益相关者在各种潜在的实际情况下改善和优化决策过程是非常重要的。

在舆论动态中，设计、调整、选择和评估在特定静态或演变条件和边界下的正确模型的过程需要双重视角：一方面，有必要对社会群体中代理之间的单一、精确的*本地*相互作用进行建模，这些代理通常在物理上或社会上*邻近*或在某种程度上“相似”。必须准确建立和预定义*邻域*、*相似性*、能力或特定*阈值*的概念，以允许这种相互作用。一般来说，代理之间的局部相互作用由所谓的*更新规则*来建模，这些规则封装了具体的机制和关系，同时考虑了代理行动和相互作用的网络特征和结构。简单、局限的局部相互作用和知识交换过程导致在整个社会网络水平上产生*全局*、一般的、新兴的行为（Bolzern 等人 2020; Giardini 等人 2015）。因此，舆论动态的主要目标之一是研究和分析社会系统达成共识所需的方式和整体时间，在几种*边界条件*下。一般来说，最终状态倾向于假定三种经典的*稳定状态*之一：共识、*极化*和*分裂*（Zha 等人 2020）。正如每个人的日常经验所表明的那样，在社会群体或交互中，并没有保证总是达成共识：在某些情况下，会出现两种最终意见，将人口极化为两个群集，或者，在其他情况下，没有出现主导意见，因此人口意见在某些群集中分裂。因此，舆论动态考虑了一些构建有效和完整模型的具体概念要素：意见表达格式，即意见如何表达和建模（例如，连续、离散、二进制等）、意见演变规则，即管理系统动态的更新规则，以及舆论动态环境，即在特定的、局限的社会或物理关系下围绕代理的社会网络。

在本章中，我们将通过展示主要挑战和方法来研究和分析异质环境和条件下共识形成的整体情景，来呈现舆论动态的总体情况。我们将从将计算社会科学的一般概念框架置于上下文中开始，作为有效研究和分析社会现象的手段，然后我们将提出文献中主要舆论动态模型的初步分类，这对我们的目标是有用的。然后，这些模型将被概括描述，旨在突出考虑的主要特征和行为。最后，我们将强调它们对金融领域的优势和潜在应用，以帮助读者欣赏和理解这个引人入胜的学科对于真实、具体和当前的金融情景和环境的潜力。

## 10.2 计算社会科学视角

*计算社会科学*（CSS）是一个充满挑战和前景的高度跨学科研究领域，旨在通过将传统社会科学研究与计算和*数据驱动*方法相结合，研究可能的任何*社会现象*（Lazer 等人 2009; Conte 等人 2012）。因此，CSS 专注于让社会科学家利用定量、客观的*以数据为中心*的方法，将他们的经典和传统的定性研究与来自于社会现象的定量分析的新证据相结合，这些证据来自于真实社会环境和社区。用于设计和评估计算模型的数据可以来自于多种*数据来源*，例如社交媒体、互联网、其他数字媒体或数字化档案。因此，CSS 本质上是跨学科和多层面的：它允许通过广泛而不断发展的一系列方法和途径进行关于人类和社会行为的推断，例如统计方法、*数据挖掘*和*文本分析*技术，以及*多主体系统*和*基于代理人的模型*。另一方面，可能的应用领域涵盖了文化和政治社会学、社会心理学，甚至经济学和人口统计学。

在复杂系统科学的概念框架中，这些现象存在和作用的环境可以被视为一个*社会复杂系统*，由许多元素，即*代理人*组成，它们形成了一个*社会复杂网络*：因此，代理人可以被表示为网络的节点，它们之间的相互位置决定了生成的网络结构。创建和调整“正确”的和最“有用”的网络结构证明是 CSS 方法和算法设计和开发中的基本步骤。节点之间发生的不同关系可以产生整个网络中的一些普遍和新兴的行为。然后，复杂系统科学的视角特别有用于研究社会环境中生成和发展的相互作用：这些机制导致了系统级别上的*新兴集体行为*。因此，整个网络的某些属性源自节点之间发生的各种关系。作为一个复杂系统，新兴行为不能从单个代理人的行为或属性中推断出来，根据亚里士多德的话：“整体大于部分之和”（Grieves and Vickers 2017; Aristotle and Armstrong 1933）。在任何社会系统中，代理人之间的相互作用在所有社会过程中都起着关键作用，从组织中的*决策*和*协作*，到社会群体中的意见动态和*感知*，再到大型人类社会中的*文化演化*和*扩散*。因此，CSS 面临的主要挑战是识别、分析和预测涉及的代理人之间的社会相互作用导致的异质社会行为和现象的演变。

总的来说，CSS 必须处理两个主要的宏观问题：*社会计算*和*算法建模*问题。前者与 CSS 相关的一般需求和挑战有关，例如将定性社会研究与数据分析和计算方法相结合，以及评估和模拟代理人在不同条件和社会情境中的*协调*、交互和社会过程的需求。这些交互和协调过程可能涉及代理人之间的*信息*或*知识生产*和*分享*，就像在观点动态模型中一样，导致网络层面存在或不存在*最终状态*，例如在整个系统中达成一致或*同步*。相反，后者与设计和建模不同交互机制相关的具体问题和算法问题有关，旨在理解它们在集体社会现象的出现中的作用。同样，信息和知识的创造与交流的作用必须经过算法调查，因为它们在定向人口社会和文化演变中起着重要作用。随后要面对的步骤是选择、设计、实施和评估适合的模型，在特定情况和涉及的社会动态中进行调整甚至扩展。

文献中提出的模型涉及许多不同类型的社会现象和过程，从一般的*社会动态*（González 等人 2008; Balcan 等人 2009）和*社会传播*（Conover 等人 2011; Gleeson 等人 2014）到*人群行为*（Helbing 2001）和舆论动态（Holley 和 Liggett 1975; Granovsky 和 Madras 1995; Vazquez 等人 2003; Castellano 等人 2003; Vilone 和 Castellano 2004; Scheucher 和 Spohn 1988; Clifford 和 Sudbury 1973; Fernández-Gracia 等人 2014），*文化动态*（Axelrod 1997），*语言动态*（Baronchelli 等人 2012; Castelló等人 2006），*层次结构*（Bonabeau 等人 1995）和社会人群中的*分离形成*。所涉及的一般宏观方法利用了复杂社会系统中*微观*和*宏观*水平之间的二分法：模型通常关注系统中包含的少数和简单机制，引导代理之间的局部交互，以研究社会动态随基本交互规则的变化而变化，从而引发特定现象对社会系统集体行为的单一贡献。通过在局部层面具体包含和调整社会机制，模型能够反映真实世界社会情况的日益复杂性，使从业者和学者既能专注于某些特定机制，又能评估全局网络中出现的整体行为和现象。

在这个一般性的视角下，现在我们将通过关注舆论动态和文献中提出的主要模型来进行。我们将首先描述舆论动态的一般情景，展示现有各种方法的可能分类。然后，我们将遵循提出的分类策略，关注不同的模型和机制，让读者欣赏它们在社会环境中的重要性，最后，在具体的金融应用中。

## 10.3 舆论动态模型分类

意见动态模型在文献中广泛传播和流行，在计算社会科学、数学和**统计物理学**建模人类行为的概念和学科框架中（Castellano 等人 2009；Perc 等人 2017），以及复杂系统中。模型的兴起、演变、适应和修改是正在进行中的过程，因此提供全面和完整的分类是具有挑战性的。对我们的范围来说，我们的目标是让读者能够在我们将在接下来的章节中报告的传统、基本和基本模型中取得方向。最终目标是让读者在涉及金融及其挑战时欣赏它们的特定优势或潜在应用。因此，这项工作绝对不是对文献中模型的穷尽审查。

为了为我们的范围提供一个有用的分类，我们考虑了两个主要维度：**维度**和**意见**的**类型**。实际上，一方面，意见可以用**单维度或多维度**向量来表示（Sîrbu 等人 2017）；另一方面，模拟意见的向量可以是**离散的**或**连续的**（Zha 等人 2020；Sîrbu 等人 2017）。当模拟由一组特征或特征组成的代理的多维度意见时，多维度意见可以很有用。这在 Axelrod 模型的情况下发生（Sîrbu 等人 2017），在该模型中，代理被赋予了不同的文化特征，这些特征模拟了人口中个体的不同信仰或态度。相反，一维的、经典的意见动态模型被广泛用于研究不同条件和人口中的意见动态，重点关注特定类型的意见及其根据系统规则的演变。此外，意见可以是离散的或连续的。在前一种情况下，意见向量的分量只能取离散值，因此封装了有限数量的可能状态，而在后一种情况下，意见向量的分量是连续的，在实数域中取值（如果需要的话，可以具有任何特定的约束）。最后，在文献中也有混合模型，其中不同的可能性根据要研究和模拟的特定社会现象或行为混合，例如在 CODA（连续意见和离散行为）模型中（Zha 等人 2020；Sîrbu 等人 2017）我们将报告。

当然，可以有几种其他方法来提供对文献中不同观点动态模型进行研究和欣赏的分类方法。特别是，可能考虑其他显着的区分要素，具体包括在达成共识形成过程中达到的*稳定状态*的类型，以及代理人的行为、研究的社会现象，甚至利用的社会网络的类型。无论如何，可以通过关注特定建模因素的存在或缺失来构建文献中许多模型的任意分类或分类。值得注意的是，在进一步探索观点动态模型的可能分类时，*信息表示*和*信息共享*的作用可能是至关重要的，特别是当涉及到*外部信息*的角色时，即源自系统人口外部的信息来源（例如大众媒体广播）。然而，我们决定将所选择的分类尽可能简单化，以便专注于主要的基本模型及其在金融中的应用，如前所述。

在图 10.1 中，我们根据所述的分类方案对不同模型进行了简要表示！[](img/524458_1_En_10_Fig1_HTML.png)

4 象限动态模型说明。顶部是连续的，左侧是一维的，底部是离散的，右侧是多维的。

图 10.1

主要观点动态模型的分类定位图

## 10.4 连续观点模型

让我们从介绍具有一维和连续观点的主要观点动态模型开始。这些模型在文献中被广泛利用来分析和模拟现实世界中的情景，其中与特定主题相关的观点（以一维方式）不是离散或二元的，而是在连续的尺度上变化，必要时具有特定的边界或限制。在文献中，这一类别中的主要模型是 De Groot 模型（DeGroot 1974；Das 等人 2014）和有界信心模型：Deffuant-Weisbuch 模型（Weisbuch 等人 2002；Deffuant 等人 2000）以及 Hegselmann-Krause 模型（Hegselmann 和 Krause 2002）。

**De Groot 模型**

这是经典的意见动态模型，被广泛研究为更高级模型或扩展的基本方法。根据 De Groot 的说法，*意见形成* 过程及其动态是根据相互作用代理的初始意见的*线性组合* 来定义的。然后，让我们考虑代理的集合![$$A=\{{a}_{1},{a}_{2},\dots ,{a}_{m}\}$$](img/524458_1_En_10_Chapter_TeX_IEq1.png)，并让![$${o}_{i}^{t}\in R$$](img/524458_1_En_10_Chapter_TeX_IEq2.png) 表示第![$$t$$](img/524458_1_En_10_Chapter_TeX_IEq4.png) 轮时代理![$${a}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq3.png) 的意见。由于模型使用线性组合来定义代理的演变意见，我们引入了权重![$${w}_{ij}$$](img/524458_1_En_10_Chapter_TeX_IEq5.png)，即代理![$${a}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq6.png) 分配给代理![$${a}_{j}$$](img/524458_1_En_10_Chapter_TeX_IEq7.png) 的条件![$${w}_{ij}\ge 0$$](img/524458_1_En_10_Chapter_TeX_IEq8.png) 和![$${\sum }_{j=1}^{m}{w}_{ij}=1$$](img/524458_1_En_10_Chapter_TeX_IEq9.png) 必须满足。然后，De Groot 模型利用以下更新规则，控制代理![$${a}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq10.png) 的意见演变：![$${o}_{i}^{t+1}={w}_{i1}{o}_{1}^{t}+{w}_{i2}{o}_{2}^{t}+\dots +{w}_{\mathfrak{I}}{o}_{m}^{t}t=\mathrm{0,1},2,\dots$$](img/524458_1_En_10_Chapter_TeX_Equa.png)这个更新规则是明确等价于：![$${O}^{t+1}=W\times {O}^{t}t=\mathrm{0,1},2,\dots$$](img/524458_1_En_10_Chapter_TeX_Equb.png)或者，替代地：![$${O}^{t}=W\times {O}^{\left(t-1\right)}={W}^{2}\times {O}^{\left(t-2\right)}=\dots ={W}^{t}\times {O}^{0},$$](img/524458_1_En_10_Chapter_TeX_Equc.png)其中![$$W={\left({w}_{ij}\right)}_{m\times m}$$](img/524458_1_En_10_Chapter_TeX_IEq11.png) 是封装了代理使用的不同权重的行随机权重矩阵，其中![$${W}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq12.png) 是它的![$$t$$](img/524458_1_En_10_Chapter_TeX_IEq13.png) 次幂，而![$${O}^{t}={\left({o}_{1}^{t},{o}_{2}^{t},\dots ,{o}_{m}^{t}\right)}^{T}\in {R}^{m}$$](img/524458_1_En_10_Chapter_TeX_IEq14.png) 。这个模型本质上是线性的，而且随着时间的推移是可追踪的。它是一个迭代平均模型，可以达成共识：根据 De Groot 的说法，如果存在一个时间![$$t$$](img/524458_1_En_10_Chapter_TeX_IEq15.png) ，使得矩阵![$${W}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq16.png) 中至少一列的每个元素都是正的，则可以达成共识。换句话说，如果权重矩阵![$${W}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq18.png) 具有严格正列，则可以达成共识。由于其简单和线性性，De Groot 模型在文献中有几个扩展：这些是所谓的 DeGrootian 模型（Degroot 1974; Noorazar et al. 2020; Noorazar 2020）。其中一个最广泛使用的是*Friedkin-Johnsen*模型（Noorazar 2020; Fried

**有界信心模型**

有界信心模型可以看作是 De Groot 模型的扩展，其中代理根据其观点之间的距离的给定*阈值*进行交互，而权重随时间或代理的观点变化而变化。主要思想是观点之间的*距离*决定了交互的发生：如果两个考虑的代理之间的差异低于给定的阈值，那么代理将进行交互，他们的观点可能会发生变化，否则他们彼此绝对不会考虑，因为两种想法相距太远。如果阈值对所有代理都采用相同的值，则有界信心模型是*均匀*的；否则，模型是*异质*的。两种主要的有界信心模型是 Deffuant-Weisbuch 模型，这是一个*成对*模型，以及 Hegselmann-Krause 模型，这是最常见的*同步*版本。

***Deffuant-Weisbuch 模型***

在 Deffuant 模型中，每个离散时间步骤从代理集合中随机选择两个相邻代理，并以成对方式进行交互。仅当它们的观点差异低于给定的阈值 ![$$\varepsilon$$](img/524458_1_En_10_Chapter_TeX_IEq20.png) 时，交互的结果是一种朝着彼此观点的妥协。否则，他们的观点不会发生修改。然后，如果 ![$$\left|{o}_{i}^{t}-{o}_{j}^{t}\right|\leq\varepsilon$$](img/524458_1_En_10_Chapter_TeX_IEq21.png)，代理将进行交互并更改他们的观点，否则他们将不会进行交互。如果交互发生，代理 i 的更新规则如下:![$${o}_{i}^{t+1}={o}_{i}^{t}+\mu \left({o}_{j}^{t}-{o}_{i}^{t}\right),$$](img/524458_1_En_10_Chapter_TeX_Equd.png)而代理 j 的更新规则如下:![$${o}_{j}^{t+1}={o}_{j}^{t}+\mu \left({o}_{i}^{t}-{o}_{j}^{t}\right).$$](img/524458_1_En_10_Chapter_TeX_Eque.png)

接着，在这个模型中有两个主要参数需要考虑：*收敛参数*![$$\mu$$](img/524458_1_En_10_Chapter_TeX_IEq22.png)，也称为*谨慎参数*，它控制收敛的速度，或者换句话说，一个代理在人口中受到另一个代理意见影响的能力；以及*置信半径*![$$\varepsilon ,$$](img/524458_1_En_10_Chapter_TeX_IEq23.png)，即控制一个代理与人口中另一个代理实际交互能力的阈值。Deffuant-Weisbuch 模型在与这两个参数相关的许多条件下进行了广泛研究。根据两个参数在不同情况下和取值的不同，该模型可以导致三种可能的稳定状态：共识、极化和碎片化。特别是在特殊情况下，当![$$\mu =\frac{1}{2}$$](img/524458_1_En_10_Chapter_TeX_IEq24.png)，交互的结果将是以他们先前意见的平均值完全一致。文献中还发现了几种扩展，考虑到诸如*噪声*、相互作用的代理类型以及参数值上的特定边界或条件等方面。

***Hegselmann-Krause 模型***

这是最为广为人知和传播的有界信心模型的同步版本。再次强调，这个模型有许多版本。我们的目标只是呈现最简单和最常用的版本，以便读者能够欣赏到这种方法在现实世界应用中的潜力。这里最简单的更新规则由以下公式给出：![$${o}_{i}^{t+1}=\frac{1}{\left|{N}_{i}^{t}\right|}\sum\limits _{j\in {N}_{i}^{t}}{o}_{j}^{t}$$](img/524458_1_En_10_Chapter_TeX_Equf.png)，其中![$${N}_{i}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq25.png)是时间![$$j$$](img/524458_1_En_10_Chapter_TeX_IEq27.png)处代理![$$i$$](img/524458_1_En_10_Chapter_TeX_IEq26.png)的邻居集，包括代理![$$i$$](img/524458_1_En_10_Chapter_TeX_IEq28.png)本身，其中满足条件![$$\left|{o}_{j}^{t}-{o}_{i}^{t}\right|\le r$$](img/524458_1_En_10_Chapter_TeX_IEq29.png)，即可能相互作用的代理意见之间的差异小于给定的阈值。这个模型着重研究了邻居意见动态及其收敛特性，甚至在扩展的多维情况下也是如此。

## 10.5 离散意见模型

这些模型被广泛用于探索和分析现实生活中意见呈离散范围的情况。通常，许多模型假设二元意见，至少在模型的初始版本中是这样，随后的版本中会对多种离散意见进行扩展。通常这样的模型被用来研究和模拟政治情景，就像特朗普的胜利一样。再次强调，我们这里的目的不是涵盖文献中设计和应用的所有模型，而是呈现用作更复杂和先进方法基础的最重要和基本的模型：我们的目的是向读者介绍这些方法在金融领域的许多潜力，我们将在本章的最后一节中探讨这些潜力。

**投票者模型**

选民模型是最古典和最简单的离散观点动态模型之一。正如其名称所示，它已被广泛应用于选举竞争和政治系统的研究。让我们考虑一个由 ![$$c$$](img/524458_1_En_10_Chapter_TeX_IEq30.png) 个代理组成的人口，并且让 ![$$A=\left\{{a}_{1},{a}_{2},\dots ,{a}_{n}\right\}$$](img/524458_1_En_10_Chapter_TeX_IEq31.png) 代表人口中的代理集合，其中 ![$${o}_{i}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq32.png) 是时间 ![$$t$$](img/524458_1_En_10_Chapter_TeX_IEq34.png) 时代理 ![$$i$$](img/524458_1_En_10_Chapter_TeX_IEq33.png) 的观点，假设为二进制状态，即 ![$${o}_{i}^{t}=1$$](img/524458_1_En_10_Chapter_TeX_IEq35.png) 或 ![$${o}_{i}^{t}=-1$$](img/524458_1_En_10_Chapter_TeX_IEq36.png)。然后，在每个时间 ![$$t$$](img/524458_1_En_10_Chapter_TeX_IEq37.png)，选择一个随机代理 ![$${a}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq38.png)。接着，选择第二个随机代理 ![$${a}_{j}$$](img/524458_1_En_10_Chapter_TeX_IEq39.png)，并且代理 ![$${a}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq41.png) 的观点 ![$${o}_{i}^{t+1}$$](img/524458_1_En_10_Chapter_TeX_IEq40.png) 将采用代理 ![$${a}_{j}$$](img/524458_1_En_10_Chapter_TeX_IEq41.png) 的值，即 ![$${o}_{i}^{t+1}={o}_{j}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq43.png)。通过这种方式，在交互之后，代理 ![$$i$$](img/524458_1_En_10_Chapter_TeX_IEq44.png) 实际上采用了随机选择邻居的状态。在这个模型中，有两种可能的最终共识状态，并且人口达成的最终状态取决于代理的初始观点分布。这个模型在位于二维正则格点的代理人群中已被广泛和深入地研究。此外，许多研究与 ![$$d$$](img/524458_1_En_10_Chapter_TeX_IEq45.png) 维超立方体格点有关。在一个无限系统中，已经证明了对于 ![$$d\le 2$$](img/524458_1_En_10_Chapter_TeX_IEq46.png)，会达成共识。

**Sznajd 模型**

Snajzd 模型背后的理念可以通过简单的格言“团结就是力量，分裂就是失败”有效传达。换句话说，该模型概括了社会系统中可能发生的两种特定社会行为，即在邻域级别上：*社会验证* 和 *纷争破坏*。前者涉及在邻居之间传播特定观点：如果邻域内的两个（或更多）代理人持有相同观点，则他们的邻居将同意他们，即该观点将在该邻域内传播并成为主导观点。而后者则涉及邻域内观点的分裂：如果两个（或更多）代理人持有不同意见，即他们持有相反的观点，则他们的邻居将与他们争论，因为每个代理人都将更改自己的观点，直到他们不再与相邻的邻居达成一致意见。更正式地说，让 ![$${o}_{i}^{t}=1$$](img/524458_1_En_10_Chapter_TeX_IEq47.png) 或 ![$${o}_{i}^{t}=-1$$](img/524458_1_En_10_Chapter_TeX_IEq48.png) 表示时间为 ![$$t$$](img/524458_1_En_10_Chapter_TeX_IEq50.png) 时代理人 ![$${a}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq49.png) 的观点。控制观点演变的更新规则如下：

1.  1.

    在每个时刻 ![$$t$$](img/524458_1_En_10_Chapter_TeX_IEq51.png)，随机选择一对代理人 ![$${a}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq52.png) 和 ![$${a}_{i+1}$$](img/524458_1_En_10_Chapter_TeX_IEq53.png)，他们的邻居分别是 ![$${a}_{i-1}$$](img/524458_1_En_10_Chapter_TeX_IEq54.png) 和 ![$${a}_{i+2}$$](img/524458_1_En_10_Chapter_TeX_IEq55.png)。

1.  2.

    如果 ![$${a}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq56.png) 和 ![$${a}_{i+1}$$](img/524458_1_En_10_Chapter_TeX_IEq57.png) 持有相同的观点，它将传播到 ![$${a}_{i-1}$$](img/524458_1_En_10_Chapter_TeX_IEq58.png) 和 ![$${a}_{i+2}$$](img/524458_1_En_10_Chapter_TeX_IEq59.png)，即如果 ![$${o}_{i}^{t}={o}_{i+1}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq60.png)，那么 ![$${o}_{i-1}^{t+1}={o}_{i+2}^{t+1}={o}_{i}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq61.png)。

1.  3.

    如果![$${a}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq62.png)和![$${a}_{i+1}$$](img/524458_1_En_10_Chapter_TeX_IEq63.png)没有相同的观点，则不同的观点将传播给代理的邻居，即如果![$${o}_{i}^{t}={-o}_{i+1}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq64.png)，那么![$${o}_{i-1}^{t+1}={o}_{i+1}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq65.png)和![$${o}_{i+2}^{t+1}={o}_{i}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq66.png)。

**伊辛模型**

伊辛模型是物理学中一个众所周知且广泛研究的模型，是统计力学中分析铁磁材料中*相变*最古典的方法之一。在观点动态领域，它被用来模拟社交互动的情况，其中代理人有二进制观点，由*自旋*表示，可以是*上*或*下*。这种选择似乎本质上有限，但在人们需要在特定主题上做出两个相反选项之间的选择时，证明它是有用的。自旋耦合被用来表示代理之间的同伴互动。此外，该模型还考虑了另一个因素，即外部信息（例如媒体推广），在模型中由磁场表示。因此，交互能量，称为![$$E=-J{o}_{i}{o}_{j}$$](img/524458_1_En_10_Chapter_TeX_IEq67.png)代表了代理之间的冲突程度，其中![$${o}_{i}=\pm 1$$](img/524458_1_En_10_Chapter_TeX_IEq68.png)和![$${o}_{j}=\pm 1$$](img/524458_1_En_10_Chapter_TeX_IEq69.png)代表代理的自旋，分别对应上升和下降的状态，而![$$J$$](img/524458_1_En_10_Chapter_TeX_IEq70.png)是能量耦合常数，表示代理之间的相互作用强度。此外，观点和外部信息之间存在一种交互能量![$$E=-H{o}_{i}$$](img/524458_1_En_10_Chapter_TeX_IEq71.png)。前者的交互能量在两个代理分享相同观点时最小，而后者在观点和外部场共享相同值时最小。

**多数规则模型**

这个模型由加拉姆引入，用来研究多数规则在社会系统中意见动态演化中的运用。在这种情况下，从代理人群体中随机选择一个由![$$r$$](img/524458_1_En_10_Chapter_TeX_IEq72.png)代理人组成的小组，所有意见都是二进制的。然后采用多数规则更新意见，最后，所有过程再次从其他随机选择的代理人开始。在文献中，已经研究了许多版本的更新规则和条件。在加拉姆版本中，代理人![$${a}_{k}$$](img/524458_1_En_10_Chapter_TeX_IEq74.png)在时间![$$t+1$$](img/524458_1_En_10_Chapter_TeX_IEq75.png)的意见![$${o}_{k}^{t+1}$$](img/524458_1_En_10_Chapter_TeX_IEq73.png)将等于![$$1$$](img/524458_1_En_10_Chapter_TeX_IEq76.png)，如果![$${\sum }_{i=1}^{r}{o}_{i}^{t}/r&gt;0.5$$](img/524458_1_En_10_Chapter_TeX_IEq77.png)，而如果![$${\sum }_{i=1}^{r}{o}_{i}^{t}/r&lt;0.5$$](img/524458_1_En_10_Chapter_TeX_IEq79.png)，则![$${o}_{k}^{t+1}={o}_{k}^{t}$$](img/524458_1_En_10_Chapter_TeX_IEq78.png)，在所有其他可能的情况下![$${o}_{k}^{t+1}=0$$](img/524458_1_En_10_Chapter_TeX_IEq80.png)。加拉姆的工作对于增强和促进对层次系统中意见动态以及政治和选举系统中决策制定的研究至关重要，包括在人口中代理人之间意见的联盟或分裂的情况下。

**格拉诺维特的阈值模型**

这是最著名的阈值模型之一，研究了一种临界阈值在同意决策者数量中的可能存在和作用，如果达到，将决定共识的实现。在这个模型中，每个代理人都有一个确定其选择是否积极参与集体行为（例如特定政治决策或决策过程）的激活阈值。这个阈值基本上取决于特定个体在决定加入群体和过程之前应观察到的其他代理人的数量。然后，一个由![$$N$$](img/524458_1_En_10_Chapter_TeX_IEq81.png)代理人组成的人口，每个个体都被赋予一个特定的阈值，定义了在考虑个体决定参与决策过程和集体行为之前所需的参与代理人数量。在异质条件和人口中研究这些阈值可以预测人口中意见的最终状态。

## 10.6 混合模型

除了文献中可能找到的离散和连续的经典意见动态模型外，还可以找到几种混合方法，将不同类型的观点混合或合并异构模型，目的是相互加强所包含的方法并利用它们的协同作用。不同模型的混合化可以是研究某些行为或机制的一种方式，也可以在更大、更全面的框架中有效地创建一种方法，以更好地模拟它们在实际应用或场景中的情况。在这个简短的部分中，我们简要介绍连续观点和离散行动（CODA）模型，为离散和连续元素在意见动态中的混合提供了一种解释性方法。

**CODA 模型**

在这个模型中，代理处理二进制决策，因此代表了一个（非常常见的）情况，即个体面对两种冲突相反的观点选择之间。然后，每个代理！$$i$$必须在两种离散（二进制）观点之间选择一个选择。在这种情况下，合理假设每个代理！$$i$$都会以概率！$${p}_{i}$$选择两个替代方案之一。CODA 方法有助于建模这种概率，允许在特定条件下建模选择某种离散观点的程度。然后，我们有一个变量代表两个相反的选择，即！$$x=\pm 1$$，所有代理都用连续概率在一个方格网络上表示，其中概率！$${p}_{i}$$表示同意观点！$$+1$$的程度，而概率！$$1-{p}_{i}$$对应于同意观点！$$-1$$的程度。代理通过使用阈值！$${\sigma }_{i}=sign\left({p}_{i}-1/2\right)$$来选择一个离散观点。此外，每个代理都声明哪种选择更受欢迎，从而通知其他代理。所有代理只看到其他人的离散观点，并根据邻居的情况改变相应的概率，通过特定的贝叶斯更新规则，以刺激和促进邻居之间的协议。邻居之间的相互作用可以根据不同的模式进行，可以是选民模型，其中每个代理在每个步骤中与一个邻居互动，也可以是 Sznajd 模型，其中两个邻居能够影响其他人。在任何情况下，即使在开始时没有极端观点，该模型也显示了极端主义的出现。已经研究了模型的几个扩展，例如在代理社交网络中存在迁移的情况，即代理移动并改变他们在网络中相对于其他代理的位置的可能性。其他情况包括允许第三种可能的观点的存在，以及为代理提供对他们对其他个体产生的影响的“意识”的可能性。最后，值得一提的是，在系统中包含一些“信任”的可能性，通过使每个代理能够评判其他代理，将它们归类为“值得信赖”的和“不值得信赖”的。在每种情况下，在几种不同的异质条件下研究了共识或极化的可能出现。

除了 CODA 模型外，文献中还研究和探索了许多其他混合方法，从考虑代理人在互动过程中过去关系的模型，到试图创建混合连续-离散动态的尝试，甚至到试图将认知或心理现象或行为纳入结构或代理人之间的互动的模型。为了充分利用某些模型的潜力，有时它们被扩展到多个意见维度。事实上，直到现在，我们只考虑了一维模型，在许多实际场景和具体应用中证明了其有效性。然而，许多情况需要超过一维才能充分表示所考虑的意见范围：因此，值得一提的是多维意见动态模型。

## 10.7 多维意见

在广泛的意见动态模型和方法场景中，有必要并值得提及多维情况，使得可以设计、开发和应用多种方法，无论是专门为这些情况创建的方法还是从一维情况延伸而来的方法。乍一看，这样的模型在实际场景中可能显得有些奇怪或无用。相反，当人群中的代理人的观点由一系列异质变量组成并受到影响时，这样的模型尤其适用和必要，这些变量代表他们的特征或特性，与不同的社会学、政治或文化特征相关联，影响并构成整个观点。因此，多维模型的使用在真实的生活复杂场景中甚至可能比在真实世界更加真实或有用，其中代理人、他们的观点和特征是复杂的，并与几个维度相关联。因此，文献中有大量研究专注于探索此类模型，通常会扩展和调整一维经典情况，就像有界信心模型一样。在文献中特别研究的方面中，有 *信心水平* 的变化、模型 *收敛*、在异质条件下的 *稳健性* 和 *稳定性*，影响在人群中出现共识、极化或观点分段的外观。在这个简短的部分中，我们简要介绍了著名的 Axelrod 模型的主要方面，其中代理人具有不同的文化特征，用于建模其信仰或态度。

**Axelrod 模型**

该模型是为了文化动态而引入的，用于研究和模拟文化形成和演变过程。在人口中，包括两种主要的社会学现象，即同质性和社会影响。前者指的是偏爱与相似的同龄人互动，因此代理人具有一组相似的特征。同质性是一种常见的社会现象，它可以导致在相互作用代理人的人口中出现集群，在许多种类的社会网络和社会群体中都是如此。而后者则指的是个体在互动后变得更加相似的倾向，因此它是在互动过程中相互影响的能力。

在拥有![$$N$$](img/524458_1_En_10_Chapter_TeX_IEq91.png)个个体的群体中，每个代理都有由![$$F$$](img/524458_1_En_10_Chapter_TeX_IEq92.png)个变量建模的文化，因此存在文化特征的向量![$$\left({\sigma }_{1},\dots ,{\sigma }_{F}\right)$$](img/524458_1_En_10_Chapter_TeX_IEq93.png)。它们中的每一个可以假设![$$q$$](img/524458_1_En_10_Chapter_TeX_IEq94.png)个离散值，这些值是不同特征的特性，其中![$${q}_{f}=\mathrm{0,1},\dots ,q-1$$](img/524458_1_En_10_Chapter_TeX_IEq95.png)。这种结构绝对有助于有效且立即地对代理的不同信仰、行为或态度进行建模。两个代理![$$i$$](img/524458_1_En_10_Chapter_TeX_IEq96.png)和![$$j$$](img/524458_1_En_10_Chapter_TeX_IEq97.png)之间的交互根据交互概率![$${o}_{ij}=\frac{1}{F}{\sum }_{f=1}^{F}{\delta }_{{\sigma }_{f\left(i\right),}{\sigma }_{f\left(j\right),}}$$](img/524458_1_En_10_Chapter_TeX_IEq98.png)发生，其中![$$\delta$$](img/524458_1_En_10_Chapter_TeX_IEq99.png)是 Kronecker's delta。然后，根据这样的概率，如果发生交互，将选择一个特征，并设置![$${\sigma }_{f\left(j\right)}={\sigma }_{f\left(i\right)}$$](img/524458_1_En_10_Chapter_TeX_IEq100.png)。否则，交互不会发生，两个代理将保持各自的特征。这种模型导致一个随机初始化的群体达到两种可能的吸收状态：一种是一种共识，即代理共享特征的有序状态，另一种是一种分段，即不同特征的多个集群在群体中共存。最终状态特别取决于可能的初始![$$q$$](img/524458_1_En_10_Chapter_TeX_IEq101.png)个初始特性的数量：如果![$$q$$](img/524458_1_En_10_Chapter_TeX_IEq102.png)很小，那么具有相似特性的代理之间的交互更容易，因此可以达成共识。相反，对于较大的![$$q$$](img/524458_1_En_10_Chapter_TeX_IEq103.png)，没有足够的相似特性共享，因此相似代理之间的交互无法创建和维护不断增长的文化领域。当然，研究两种可能状态之间的相变尤为重要。在正规晶格上，这种相变出现在临界![$${q}_{C}$$](img/524458_1_En_10_Chapter_TeX_IEq104.png)，取决于群体中特征数![$$F$$](img/524458_1_En_10_Chapter_TeX_IEq105.png)的数量。

当然，这种模型已经在文献中得到了广泛的分析、调整和扩展。总的来说，它具有一个简单而重要的结构，可用于研究文化动态和意见动态在多维变量存在的情况下。因此，这种模型适用于许多可能的调整、修改，甚至扩展到更一般的结构。在可能的修改版本中，值得提到的是修改更新规则的情况，例如始终允许选择一对相互作用的代理人，而不是随机选择它们。其他有趣的模型利用所谓的*文化漂移*，即影响文化集群形成和共识出现的外部*噪声*。最后，还可以改变代理人对于他们意见变化的态度，例如包含一些阈值来判断重叠，确定代理人何时会不同意，以及包含*忠实个体*，他们在任何情况下都不会改变他们的意见。

## 10.8 金融应用

意见动态在金融领域被广泛应用，在商业和组织研究中也是如此。让我们想起金融市场本质上是一个复杂的系统，在这个系统中，大量人类代理人相互作用、交换信息、交易并创建具有内部动态的群体。这是在努力理解意见动态的许多可能应用以及对金融领域的社会行为进行数学建模时需要考虑的基本概念。此外，还有必要提醒，这些代理人具有异质性特征，例如与他们的*文化*、*社会*或*人口统计*特征相关，以及不同的*投资策略*和*风险倾向*。这些特征和行为在*微观*和*中观*层次上引发了 emergent 现象，影响了金融市场的演变，以及整个社会的演变。在这种情况下，意见动态被证明是一个有用的方法论和数学工具，用于模拟金融领域社交网络的结构，模拟可能行为的范围，并研究现实情况下的 emergent 和集体现象。虽然在金融情境中利用二元模型是常见的，允许研究代理人必须在两种相反行动之间选择的情况，但通常可以找到已经提出的基本模型或其扩展的应用，以及新创建的模型，专门设计用于分析特定情况或场景。

在所提出的模型中，Ising 模型是最常应用于金融领域之一，无论是其原始版本还是专门为金融设计的扩展版本。在这种情况下，有必要将研究限制在二元情况下，这并不是问题，因为这是观点动态和金融实际应用中的典型场景。Ising 模型可用于模拟*多头*和*空头*交易者之间的交互和*交易策略*，在一个受到外部力量影响代理之间的交互和行为的环境中。更进一步，有一些扩展能够更好地代表在这种情况下发挥作用的力量：特别是，Ising 模型的基本元素可以扩展到考虑所谓的*相互影响*，即交易者的决定对其他交易者的影响，还有*外部消息*或其他可能影响交易者决策和可能的交易者特征或特质的外部力量。Ising 模型的其他可能扩展扩大了潜在的交易者选择范围，允许代理人决定是买、卖还是不采取行动。因此，Ising 模型被证明在对模拟金融情况和场景进行广泛的模拟时，既对模拟代理的特征和影响其交互的力量有用，又对交易者可能进行的行动种类进行模拟。

Sznajd 模型可以帮助更好地理解和研究在交易员群体中的某些特定观点动态的特征。事实上，像许多其他社会系统一样，这些群体受到*赶羊效应*的影响，一些*追随者*模仿一个*领导者*的决定。由于 Sznajd 模型代表了*社会认可*和*分歧破坏*的社会现象，这种方法可以包含通常存在于金融市场中的一些常见和典型社会机制。实际上，当有一些具有权威、经验和沟通能力及渠道以影响其本地邻居的*本地专家*时，该群体中的许多市场参与者是*趋势追随者*：他们将根据其本地专家的意见做出决定和下订单。相反，当这样的专家在邻里中不存在时，趋势追随者将随机行事，系统中没有占主导地位的意见。此外，模型中还可以添加*理性交易者*：这些交易者精确了解市场上的供求水平，因此能够计算出准确下单的方式。

类似地，选民模型被应用于金融领域，以研究社区中的社会行为，特别是在交易者之间的金融选择和行动的扩散和演变方面。可能的应用涉及使用选民模型来研究市场上代理人的状态：买入、卖出或不作为。其他应用研究了通过选民模型来研究金融市场中的熵波动行为。选民模型最重要的应用之一是使用所谓的“社会温度”，它代表市场温度，影响某些代理人群体的说服力，进而影响代理人的投资策略的不确定性。

使用基本模型分析复杂金融和社会环境中代理人的行为是重要的，从而允许模拟和理解市场演变，以及市场历史上发生的市场趋势、特定泡沫或崩溃等现象。因此，这些模型能够让研究人员和学者欣赏和检验不同种类的相变、稳定状态以及在多种异质条件下市场的一般演变。然而，基本模型有许多可能的扩展，以及专门设计的模型来捕捉一些特定的金融情况和机制或方面。一个主要的扩展是使用连续模型，而不是简单和常见的离散二进制观点和时间。此外，可以通过考虑代理人之间的观点和信息交流来增加复杂性水平。由于模型的结构包含的元素比最经典和基本的方法中的元素更多，因此可以适当地利用模型的分析解，基于微分方程，而不是数值和计算机模拟。所有这些可能性通常都存在于所谓的“动力学模型”中，这些模型用于研究投机市场的演变，或者价格形成和变化以及风险倾向的机制。另外，其他模型尝试将基于代理人的和经典观点动态方法与机器学习相融合，以数据驱动和更具计算性的方式。这种协同作用产生了用于通过使用广泛的概率参数来研究复杂随机情况中的观点演变过程的概率模型。从分析的角度来看，在这种情况下通常使用随机微分方程，以及神经网络结构，以帮助研究社交网络中的非线性依赖性和观点动态。

总的来说，舆论动态是一种非常有前景的方法论工具，可以帮助学者、研究人员和从业者深入了解市场结构和演化，捕捉相关的和隐藏的社会和金融现象，并在广泛的潜在应用和情境中预测未来的市场状态。我们确信，进一步研究这些方法，特别是与机器学习和深度学习的众多计算潜力协同作用的研究，将揭示许多社会和金融机制，从而推动我们向着理解更加迷人的复杂系统之一迈进，这些系统深刻影响着我们的日常生活。
