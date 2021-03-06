## reality-of-Dream-of-Red-Mansions
Comparision analysis of words use between 1~80 chapters and 80~120 chapters of **A Dream of Red Mansions**. And then construct Support Vectors to determine who write the test chapters.

在学界一般认为，《红楼梦》后 40 回并非曹雪芹所著。本文尝试应用机器学习的方法来分析原著文本中作者的用词习惯，从技术角度去说明《红楼梦》前 80 回和后 40 回的写作风格差别，继而可以确认后 40 回非原作者所写。

## 原理

每个作者写作都有自己的用词习惯和风格，即使是故意模仿也会留下很多痕迹。

在文言文中，文言虚词分布均匀，书中每个回目都会出现很多文言虚词，差别在于出现频率不同，我们把文言虚词的出现频率作为特征。

不只文言虚词，还有其他的词在所有回目中出现频率很多。比如对第 80 回进行词频统计，得到

```
了   172
的   142
我   70
宝玉  65
你   61
道   54
他   51
也   50
着   48
是   40
说   38
```

这些高频词汇也可以作为特征向量。

本文将 20~29 回(诗词曲比较均衡)作为类别 1 的学习样本，将 110~119 回作为类别 2 的学习样本。

将两个类别的特征向量输入到 SVM(支持向量机) 进行训练得出一个分类模型。再对剩余回目进行分类，看它们分别偏向于哪个类别。

SVM 相关原理参见 NG 的公开课 [Machine Learning](https://www.coursera.org/learn/machine-learning/) 和 [scikit-learn 库](http://scikit-learn.org/stable/modules/svm.html#svm)

相关学术论文参见

> 施建军. (2011). 基于支持向量机技术的《 红楼梦》 作者研究. 红楼梦学刊, (5), 35-52.
> 
> 李贤平. (1978).《红楼梦》成书新说. 复旦学报(社会科学版).



## 特征选取

```
[
 '之', '其', '或', '亦', '方', '于', '即', '皆', '因', '仍', 
 '故', '尚', '呢', '了', '的', '着', '一', '不', '乃', '呀', 
 '吗', '咧', '啊', '把', '让', '向', '往', '是', '在', '越', 
 '再', '更', '比', '很', '偏', '别', '好', '可', '便', '就',
 '但', '儿',                  # 42 个文言虚词
 '又', '也', '都', '要',       # 高频副词 
 '这', '那', '你', '我', '他'  # 高频代词
 '来', '去', '道', '笑', '说'  #高频动词
] 
```

选取常用的 42 个文言虚词和通过词频统计得到的高频使用的词作为特征，分别计算它们在各个回目中出现的频率作为特征向量。

在源码中由 `modelBuilder.py` 中的 `build_feature_vector` 函数实现。


## 目录结构

```
.
├── README.md
├── textProcesser.py  # 文本处理
├── modelBuilder.py   # 模型建立
├── decisionMaker.py  # 作出判断
├── neg_trainset.npy  # 正例训练集
├── pos_trainset.npy  # 负例训练集
├── trainset.npy      # 训练集
├── testset.npy       # 测试集
├── text              
│   ├── redmansions.txt # 原著文本
│   ├── chapter-1       # 按章分开，第一章
│   ├── chapter-n
│   ├── chapter-words-1 # 第一章分词结果
│   ├── chapter-words-n
│   ├── chapter-wordcount-1 # 第一章词频统计结果
│   └── chapter-wordcount-n
```


## 使用步骤

+ 运行 `textProcesser.py`，将原著文本分为章节，分词，词频统计
+ 运行 `modelBuilder.py`，对文本章节提取特征向量，建立分类模型
+ 运行 `decisionMaker.py`，对文本进行分类


## 结论

```
1~80 
[ 1.  1.  1.  1.  1.  2.  2.  1.  1.  2.  
  2.  1.  1.  1.  1.  1.  1.  1.  1.  1.  
  1.  1.  1.  1.  1.  1.  1.  1.  1.  1.  
  1.  1.  1.  1.  1.  1.  1.  1.  1.  1.  
  1.  1.  1.  1.  1.  1.  1.  1.  1.  1.  
  1.  1.  1.  1.  1.  1.  1.  1.  1.  2.  
  2.  2.  1.  1.  1.  1.  1.  2.  1.  1.  
  1.  1.  1.  1.  1.  1.  1.  1.  1.  1.]

81~120
[ 1.  1.  2.  1.  1.  2.  2.  1.  1.  2.  
  1.  2.  2.  2.  2.  2.  2.  1.  2.  2.  
  1.  2.  2.  2.  2.  2.  2.  1.  2.  2.  
  2.  2.  2.  2.  2.  2.  2.  2.  2.  2.]
```

1 指该回目属于类别 1，2 指该回目属于类别 2。

可以得出结论

* 前 80 回属于一类，后 40 回属于一类
* 80 回左右是分界点
* 后 40 回风格不同于前 80 回

81~120 回中有一些被分成了 1 类，这与特征选取有关，还与使用的原著版本有关。这里的版本是网上下的电子版，版本不明，建议使用人民文学出版社 1982 年出版的《红楼梦》作为研究对象。

1~80 回有一些被分成了 2 类，可能是后 40 回作者在续写过程中对部分章节进行了修改。

## 参考

http://fuzhii.com/2016/01/16/redmansions/