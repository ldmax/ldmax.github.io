# 数据缺失 Missing Data

因为受试者提前终止试验而造成的缺失数据(missing data),尤其一些主要或者次要研究终点（primary or secondary endpoint）的缺失数据，是临床实验分析中经常遇到的一个很棘手的问题。
![Figure1](assets\endpoint.png)

在很多临床试验的统计分析计划(SAP)中，会通过要求有效的基线后评估（post-baseline assessment）来定义分析人群(analysis population)去排除这些没有主要或者次要研究终点的病人。在EMA起草的《Guideline missing data confirmatory clinical trials》就指出排除这些病人的处理会影响到：1 各个治疗组（包括安慰剂组）之间的可比较性；2 实验样本相对应的目标人群的代表性。因此，可能会削减统计功效，对实验效果的评估产生偏差，临床结果的可靠性得不到保障。

想要降低数据缺失在临床研究方面的影响，首先我们需要了解一下数据缺失的类型。Little 和 Rubin提出了缺失数据的分类方法如下：

1. 完全随机缺失（Missing Completely at Random, MCAR):观察对象的数据缺失完全是由随机因素造成的，不取决于观察到的数据也不取决于未被观察到的数据。例如：一个参加临床研究的病人因为非健康因素个人原因搬家去其他城市退组。
2. 随机缺失（Missing at Random, MAR):观察对象的数据缺失取决于观察到的数据。这是一种我们可以通过观测到的数据预期数据缺失的情况。例如：一个病人因为长期服药无效退组，那么后面我们也可以预期到结果依然是没有药效。
3. 非随机缺失（Missing Not at Random, MNAR）:观察对象的数据缺失取决于未观察到的数据。例如： 一个病人在一系列访视中都体现了很好的治疗效果，但是随着长期服药耐药性增加疗效降低，最终提前退组。

在缺失数据的三种分类里，非随机缺失是我们不能预测的。但是我们可以通过假设数据缺失的类型是MAR或者MCAR去得到试验结论，并加以一些波动（shift）去估计在MCAR的情况下，这个实验结论是否依然可靠(robust)。

在了解了缺失数据类型之后，对于MAR和MCAR，我们日常临床研究中有一些常见的数据缺失处理方法。比如，末次观测值结转法（Last observation carried forward，LOCF）。LOCF作为一种单一的imputation 方法，有其自身的缺陷，比如说一个病人因为吃药导致某个实验室评估指标不断增加，医生决定让病人停止吃药。很显然，病人在停止吃药之后，这个实验室指标会有所降低，但是如果我们采用末次观测值结转法，末次观测值很可能记录了病人这个实验室指标的最大值。针对这种情况，有一个种更合适的单一imputation方法，基线观测值结转法（Baseline observation carried forward,BOCF）。

此外基于MAR和MCAR假设，我们有一些更复杂的imputation 方法比如说重复测量的混合效果模型（Mixed Models- Repeated Measures,MMRM)和多重填补（Multiple Imputation,MI)。值得一提的是，MI和MMRM在MAR的假设情况剩下是不存在偏差（bias）的。随着缺失数据类型的划分以及复杂的imputation方法出现，相比较于LOCF，BOCF，FDA越来越倾向于接受MI和MMRM等复杂方法。下一篇会介绍MI这种方法。














