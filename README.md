# 知识图谱流程梳理  
毕业论文是做的知识图谱方向的，来整理一下做的整个流程，以供参考。  
## 知识图谱构建流程  
### 知识图谱相关软件  
1. protege  
   是当前做本体构建部分最为常用的软件，用于做本体概念的定义如实体及属性的概念定义、层级关系等
   以下链接是最新版本的Protege:  
   https://protege.stanford.edu/software.php#desktop-protege
2. Neo4j  
   这个链接讲的挺全面的，从下载安装、环境配置、实验neo4j是否可行等都还是可以的，甩个链接：  
   https://blog.csdn.net/ZHOUPUYU/article/details/144583605  
   我去到官网上现在已经发布最新版的了 但功能都是一样的  
3. pycharm  
   这个软件很常见了 一般要做项目的都需要人手必备 这个就不做过多赘述 网上也有安装教程  
   vscode也可以 但是从使用的感受来说 还是更倾向于pycharm  
   jupyter一般用于小型的demo 或者用来记笔记或者实验用的  
### 基本概念  
**1. 知识图谱是什么？要干什么？**  
其实知识图谱就是一个图数据库，它的应用较为广泛，常见的有基于问答系统、检索系统等相关应用：  
https://blog.csdn.net/Cocktail_py/article/details/119907693  
**2. 知识图谱需要什么**  
——需要进行关系抽取  
——那么这里论文一般涉及到联合抽取模式(joint extraction)以及先后抽取模式（pipeline extraction)  
——这两个的优劣势蛮明显的，具体可看一些有关这种抽取模式的综述论文，比如这篇：  
——田玲,张谨川,张晋豪,等.知识图谱综述——表示、构建、推理与知识超图理论[J].计算机应用,2021,41(08):2161-2186.  
——小结一下：先后抽取模式出现较早，由于两个模块互不关联，互相独立的特点，一开始被广泛应用，但数据冗余度较高且存在误差传递的累计导致很难在知识图谱的呈现中有较好的效果，因此，联合抽取模式被提出了，这种模式使得两个模块能够形成于语义上的连结，并且效率显著提高，现在基本上看到的论文都是偏向于联合抽取的，但是这种模式也存在实体重叠和三元组重叠的弊端，因此，具体情况具体分析会好些。  
### 抽取流程
正如上述所述，知识图谱有两种抽取模式，一类流水线式抽取，一类联合抽取，因此，我对两种模式都进行了试验  
#### 先后抽取流程
1. 实体抽取  
BERT-BiLSTM-CRF  
这个模型用到烂了，但似乎丝毫不影响它在学术界的应用，在github上也有较多实现代码  
我之前用了一个知乎博主的代码 后来加了个预测函数以及做了stratified-fold的一个优化流程  
可以移步至https://github.com/YRStudent/NER-add-prediction-
有关对比实验 实在是没有精力做了 所以抱歉 你们可以在github上去找一些其他NER识别任务的~
3. 关系抽取  
R-BERT
这个是参考的这篇论文，我觉得很逻辑很精简，对于只抽取简单关系的，这个模型其实还行，但一旦面对复杂的关系语义，这个模型就不中了。。
Wu S, He Y. Enriching pre-trained language model with entity information for relation classification[C]//Proceedings of the 28th ACM international conference on information and knowledge management. New York, NY, USA: Association for Computing Machinery, 2019: 2361-2364.
我看wos上也有针对这个模型进行改进的 可自行搜索
——主要流程移步至：https://github.com/YRStudent/R-BERT-for-relation-extraction
4. 实体及关系的连接  
——构建了一张连接规则表  
——这里就设计了单箭头指向的首尾实体类型+关系类型的一张规则表  
——由于R-BERT的模式是需要输入头尾实体进去的，因此，在这里做了一个特别笨的方法，就是将一句话中，从头到尾的实体依次进行排列组合，并结合实体抽取模型部分识别出的实体类型，形成一个（头实体，尾实体，头实体类型，尾实体类型），但将它输送进去之前，因为存在太多杂乱的元组了，那么这里的规则表就发挥着若头尾实体类型在规则表内就保留，若不存在那就删掉，最后把这些实体对输送进R-BERT模型中，让它来帮忙预测实体对间的关系。
——其实这一步骤就能感受出来误差是在传递的，因为一旦实体部分的实体类型预测错了，跟着后面的关系筛选也要错的...
#### 联合抽取模型
这部分就主要参考的BERT4torch部分了，甩个链接：  
https://github.com/Tongjilibo/bert4torch




