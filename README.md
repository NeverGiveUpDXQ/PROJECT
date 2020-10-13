# PROJECT_Kaggle_Credit_Card_Fraud_Detection

## 1. 项目描述

数据来源于[Kaggle](https://www.kaggle.com/mlg-ulb/creditcardfraud)。<br>
目的是识别出「信用卡欺诈交易」，也就是二分类问题，如何划分正常交易与欺诈交易。<br>

## 2. 项目思路

数据中的部分变量已经过PCA处理，所以首先通过可视化进行特征挑选。<br>
接着，将数据划分为训练集：测试集 = 7:3，用训练集数据训练模型，（再用交叉检验从训练集中划分出验证数据用于调整参数），最后用测试集评估模型。<br>
这里，主要基于两个模型：逻辑回归以及随机森林。

- 逻辑回归

优点：最常用的二分类模型(baseline)

- 随机森林
    - 等价于大量的决策树的集合体，输出的结果取决于大多数树的结果
    - 可以处理复杂数据且不容易过拟合

“*Random Forest prevents overfitting most of the time, by **creating random subsets of the features and building smaller trees using these subsets.** Afterwards, it combines the subtrees of subsamples of features, so it does not tend to overfit to your entire feature set the way "deep" Decisions Trees do.*”

## 3. 项目结论

### 3.1 基于「数据」

共有284807条数据，其中正样本仅占0.7%，数据严重不平衡。

### 3.2 基于「可视化」

  - 在交易金额方面，欺诈交易多以小额转账为主(500美元以下)，但是数据波动较大，而正常交易的金额则相对较大，集中在5000美元以下，该变量并不能很好的对交易进行判别。
  - 在交易时间方面，欺诈交易的高频时间为凌晨，而正常交易频数相对较低的在午夜到凌晨，也就是「睡眠时间」，但由于其他时间两者的交易密度相当，尝试增加变量「睡眠时间」或者「清醒时间」。
  - 在交易时间x交易金额方面，欺诈交易中的偏大额交易并没有明显的时间偏好。另外，通过对主成分变量的密度图，我们可以进行变量筛选，两个分布的交叉面积越大，说明起到的区分作用越小。

### 3.3 基于「模型」

对比：变量更新前后各模型变化**【更新：考虑将时间划分为 睡眠时间+清醒时间】**
   - 逻辑回归：评价指标基本无变化，只有正样本的recall提高0.01且新增1个正样本被成功检测出来，其代价是80个误杀。
   - 随机森林：评价指标小幅度提升，正样本的recall值，f1-score以及AUC均提高0.01。且新增2个正样本被成功检测出来，其代价是2个误杀。
   - 模型融合：<font color=red>该模型在原模型的基础上有所提高，正样本的precision，recall和f1-score，AUC均提高0.01，对应绝对值为 0.89，0.84，0.86和0.92。且新增2个正样本被成功检测出来，同时减少1个负样本误杀。但从正样本来看，其被成功检测的概率约为**84%**。</font>
   
最后，选择**逻辑回归+随机森林的融合模型**。


## 4. 项目反思

- 实际意义

提供的数据为PCA处理后的数据，且对应的变量名为V+数字，也就是实际的指标未知。该项目过了遍流程，但未结合变量的实际意义。

- 数据不平衡的处理
    - 调整逻辑回归中的参数[「class_weight」](https://blog.csdn.net/kingzone_2008/article/details/81067036)
    - Borderline-SMOTE
SMOTE增加了类之间重叠的可能性，即有可能生成一些没有提供有效信息的样本。改进的方法为**Borderline-SMOTE**。Borderline-SMOTE的思想是只为小众类的边界样本生成新样本，即那些周围大部分是大众类样本的小众类样本生成新样本。为每个小众类样本找出其最近邻的k个样本，若k个样本中有一半以上都是大众类样本，则为该小众类样本生成新样本。生成新样本时使用SMOTE。<br>

- 变量选择

可以先把所有的变量都加入模型，再用随机森林的特征重要度来选择变量。

- 调参

并没有对随机森林的min_samples_leaf,n_estimators两个参数进行调参，因为数据量过大，运行时间多长。

- 可供选择的其他模型
    - 神经网络（适合非线性模型+复杂）
    - ~~决策树~~（常用于欺诈行为监测+容易解释，但是容易**过拟合**）
    - 调整的随机森林，直接用于不平衡的原始数据
    - 集成算法
    - ... ...
    
    
## I 数据来源
[Kaggle:Credit Card Fraud Detection](https://www.kaggle.com/mlg-ulb/creditcardfraud)

## II 参考文章
1. [Resampling strategies for imbalanced datasets](https://www.kaggle.com/rafjaa/resampling-strategies-for-imbalanced-datasets)
2. [训练集的类别不平衡问题](https://www.jianshu.com/p/2149d94963cc)
3. [trenton3983.github](https://trenton3983.github.io/files/projects/2019-07-19_fraud_detection_python/2019-07-19_fraud_detection_python.html)
4. [数据不平衡时分类器性能评价（ROC曲线）](https://blog.csdn.net/xwd18280820053/article/details/77508524)
5. [kaggle 欺诈信用卡预测(由浅入深（一）之数据探索及过采样)](https://blog.csdn.net/weixin_38569817/article/details/88645037?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param)
6. [kaggle 欺诈信用卡预测——Smote+LR](https://www.cnblogs.com/tan2810/archive/2019/03/25/10594752.html)
7. [Credit Card Fraud Detection Predictive Models](https://www.kaggle.com/gpreda/credit-card-fraud-detection-predictive-models/notebook)
8. [【机器学习】数据不平衡问题](https://blog.csdn.net/j790675692/article/details/78244517)
9. [以随机森林为例解释特征重要性](https://zhuanlan.zhihu.com/p/257139517?utm_source=cn.wiz.note)
10. [商品数据化运营-基于逻辑回归、随机森林、Bagging（套袋法）概率投票组合器模型的异常检测](https://zhuanlan.zhihu.com/p/75677686)
11. [《Python数据分析与机器学习实战-唐宇迪》读书笔记第6章--逻辑回归项目实战 ——信用卡欺诈检测](https://www.cnblogs.com/downmoon/p/12654324.html)
12. [用R测试逻辑回归、决策树、随机森林、神经网络、SVM，及集成学习的分类性能（一）](https://bbs.pinggu.org/thread-5993232-1-1.html)
13. [用R测试逻辑回归、决策树、随机森林、神经网络、SVM，及集成学习的分类性能（二）](https://bbs.pinggu.org/thread-6001962-1-1.html)
