-----------

#机器学习技巧


##正确定义要求解的问题：
* 很多时候这并不是个容易的问题
* 使用合适的损失函数


##初始值：
* 神经网络对系数的初始值比较敏感，可以考虑多训练几次，然后取最好的结果


##样本处理：
* 在神经网络中，尽量让靠近的样本点差异性大（如不属于同一类），这样有利于SGD更好地起作用。
  * 可以考虑随机重排样本点
* 抽样：如果样本点太多，考虑使用抽样方法
  * Paper: Local Case-Control Sampling Efficient Subsampling in Imbalanced Data Sets
* 如果有时间概念，考虑对早的样本点进行降权处理


##特征处理：


###归一化：
  * 有些模型/优化方法的效果会强烈地依赖于特征是否归一化，如LogisticReg，SVM，NeuralNetwork，SGD等。
  * 有些模型则不受归一化影响，如DecisionTree。
  * 0/1取值的特征通常不需要归一化，归一化会破坏它的稀疏性


###离散化：
  1. 等距离散
  2. 等样本点离散
  3. 以此特征为横坐标，目标值为纵坐标，画图，看趋势和拐点
  4. 单独用此特征和目标值来训练一个决策树，依据决策树的各节点的划分方法来离散化此特征
  * 特征离散化时不一定要保留所有的取值段对应的特征，可以通过特征选择或者领域知识去掉其中的一部分（经常是头部或尾部）
  * facebook用gbdt抽取特征
  * feature hashing：
    * 可以添加新的原始特征而保持hashing后的特征长度不变
    * 可以保持原始特征的稀疏性，既然hashing时只考虑非0原始特征
    * 可以只hashing其中的一部分原始特征，而保留另一部分原始特征（如那些出现collision就会很影响精度的重要特征）
    * 很适合有个性化的应用，因为此时要加入用户id的话会导致原始特征数量变为 (用户数+1)*特征数
    * The result is increased speed and reduced memory usage, at the expense of inspectability;
    * Paper: Feature Hashing for Large Scale Multitask Learning


###特征抽取：
  * 尽可能人为删除特征里的非线性性，不要期待模型自己能很好地自动处理非线性性
  * 间接特征：通过其他模型抽取的特征
    * 如可以利用LDA的聚类结果作为特征
    * 用户画像
    * Predicting Positive and Negative Links in Online Social Networks
    * 如果有时间概念，一般临近时间内的用户行为会较像，可以考虑把前几天的用户行为作为特征


###CTR：
 * 使用动态特征：动态特征在组合特征被命中时舍弃静态特征非0则1的取值方法，采用该组合历史上的点击率作为特征取值
 * 特征选择时，需要过滤出现次数比较少的低频特征以免低频强信号的干扰；譬如当前只有一条样本中包含 region是美国的点击，显然这种低频特征不具备统计意义，因而导致模型不稳定
 * CTR Smoothing：
   * 对点击数和展示数做时间衰减，然后再相除获得点击概率
   * 利用经验Bayes方法计算Beta先验分布中的参数
   * Paper: Click-Through Rate Estimation for Rare Events in Online Advertising
 * COEC：
   * 使用点击数除以通过bias模型预测得到的预测概率，以去除不同展位等bias对点击率预估带来的偏差
 * 特征选择
   * 可以帮助改进模型精度：特征少了，训练需要的数据也会少；另外，缩小了为获得最优解而需要搜索的参数空间，有利于找到最优解，当然，也减少了局部最小点的数量；
 * 其他
   * 紧凑的特征表达更容易解释


##模型：
* 模型、特征、数据不能分开优化
  * 针对线性分类器（如LogisticReg）优化好的特征集合，未必适用于非线性分类器（如RandomForest）
  * 在改变模型后，原来在旧模型里没用的特征，可能在新模型里有用
* 在稀疏特征时，树模型一般效果不好，可以考虑使用线性模型

 
##Ensemble：
* 把重模型（特征多，特征变化较慢，训练慢）的预测结果，作为线上轻模型的特征，也可以利用这些预测来做解释


           Twitter experience：Ensembles with 10m examples better than 100m single classifier! 







[ ]处理非常多类的决策问题，有以下方法：
          预测为每个类的可能性，最后的概率是对这些可能性归一化后的向量
          把类别组织成二叉树，这样做判断的次数就从M下降到log(M) 

[ ]experiment a lot

