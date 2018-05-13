# "中国法研杯"司法人工智能挑战赛数据说明

## 一、简介

法律智能旨在赋予机器阅读理解法律文本与定量分析案例的能力，完成罪名预测、法律条款推荐、刑期预测等具有实际应用需求的任务，有望辅助法官、律师等人士更加高效地进行法律判决。近年来，以深度学习和自然语言处理为代表的人工智能技术取得巨大突破，也开始在法律智能领域崭露头角，受到学术界和产业界的广泛关注。

为了促进法律智能相关技术的发展，在最高人民法院信息中心、共青团中央青年发展部的指导下，中国司法大数据研究院、中国中文信息学会、中电科系统团委联合清华大学、北京大学、中国科学院软件研究所共同举办“2018中国‘法研杯’法律智能挑战赛（CAIL2018）”。挑战赛将提供海量的刑事法律文书数据作为数据集，旨在为研究者提供学术交流平台，推动语言理解和人工智能领域技术在法律领域的应用，促进法律人工智能事业的发展。每年比赛结束后将举办技术交流和颁奖活动。诚邀学术界和工业界的研究者和开发者积极参与该挑战赛！


## 二、任务说明

### 2.1 介绍

* 任务一（罪名预测）：根据刑事法律文书中的案情描述和事实部分，预测被告人被判的罪名；
* 任务二（法条推荐）：根据刑事法律文书中的案情描述和事实部分，预测本案涉及的相关法条；
* 任务三（刑期预测）：根据刑事法律文书中的案情描述和事实部分，预测被告人的刑期长短。

参赛者可选择一个或者多个任务参与挑战赛。同时，为了鼓励参赛者参与到更多的任务中，组委会将单独奖励参与更多任务的参赛者。

### 2.2 数据介绍

本次挑战赛所使用的数据集是来自“[中国裁判文书网](http://wenshu.court.gov.cn/)”公开的刑事法律文书，其中每份数据由法律文书中的案情描述和事实部分组成，同时也包括每个案件所涉及的法条、被告人被判的罪名和刑期长短等要素。

数据集共包括`268万刑法法律文书`，共涉及[183条罪名](meta/accu.txt)，[202条法条](meta/law.txt)，刑期长短包括**0-25年、无期、死刑**。

我们将先后发布CAIL2018-Small和CAIL2018-Large两组数据集。CAIL2018-Small包括19.6万份文书样例，直接在该网站发布，包括15万训练集，1.6万验证集和3万测试集。这部分数据可以[注册下载](http://cail.cipsc.org.cn)，供参赛者前期训练和测试。

比赛开始2-3周后（具体时间请关注比赛新闻），我们将通过网络下载向有资格的参赛队伍定向发布CAIL2018-Large数据集，包括150万文书样例。最后，剩余90万份文书将作为第一阶段的测试数据CAIL2018-Large-test。

#### 2.2.1 字段及意义
数据利用json格式储存，每一行为一条数据，每条数据均为一个字典。

* **fact**: 事实描述  
* **meta**: 标注信息，标注信息中包括:   
    * **criminals**: 被告(数据中均只含一个被告)  
    * **punish\_of\_money**: 罚款(单位：元)
    * **accusation**: 罪名  
    * **relevant\_articles**: 相关法条  
    * **term\_of\_imprisonment**: 刑期  
        刑期格式(单位：月)
        * **death\_penalty**: 是否死刑  
        * **life\_imprisonment**: 是否无期
        * **imprisonment**: 有期徒刑刑期

这里是简单的一条数据展示：

### 2.3 评价方法

本次挑战赛使用的数据集均为来自中国裁判文书网上的刑事法律文书，标准答案是案件的判决结果。我们提供了评测时使用的评分程序共选手使用，评测方法、环境和模型提交说明请看[链接](https://github.com/thunlp/CAIL2018/tree/725245b50d9cc5d274f92ec8d3e72580c8382ab6)。

每项任务满分100分，下面将对三项任务的评价方法分别进行说明：

#### 2.3.1 任务一、任务二

任务一（罪名预测）、任务二（法条推荐）两项任务将采用分类任务中的微平均F1值（Micro-F1-measure）和宏平均F1值（Macro-F1-measure）作为评价指标，其计算方式为：

![f1](pic/f1.png)

则任务的最终分数为：

![score1](pic/score_1.png)

#### 2.3.2 任务三

任务三（刑期预测）将采用下列公式，根据预测出的刑期与案件标准刑期之间的差值距离作为评价指标。设预测出的刑期为`lp`，标准答案为`la`，则

![v](pic/v.png)

```
若v≤0.2，则score=1；
若0.2<v≤0.4，则score=0.8
……
以此类推。
```
**特殊的情况**

若案件刑期的标准答案为**死刑**或**无期**，则需预测出的刑期与案件标准刑期完全相同才可得1分，否则为0，即：

```
如果la == 死刑或无期：
若lp == la，则score=1；
否则score=0；
```

最后，将任务三所有测试点的分数相加并除以测试点总数乘以100作为任务三的评价得分：

![score3](pic/score_3.png)

#### 2.3.3 三项任务总分的计算方式

每个任务的满分均为100，则总分为：

![score_all](pic/score_all.png)


### 2.4 基线系统

竞赛组织方稍后会提供了两个开源的针对不同任务的基线系统（LibSVM、RNN）。

## FAQ

#### 1. 
