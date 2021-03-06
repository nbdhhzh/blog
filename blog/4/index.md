---
layout: app
---

# 游戏AI中的算法大作业——斗地主AI

学校的程设课和AI课同时布置了写斗地主AI的大作业，所以和Universe_hcy，David_xu一起做了这个项目。

作业是在[Botzone](https://botzone.org)平台上进行的，[Botzone](https://botzone.org)平台支持实时的对战评测。

作为一个信息不公开的非0和多人游戏，斗地主是一个非常复杂的问题。在没有高端对局训练集的情况下也较难使用神经网络来进行训练，所以我选择了使用基于专家的局面评估函数来做这个问题。

* * *

### 手牌估价

容易想到可以通过手牌的划分来确定手牌的估价。

简单而言，我们可以把手牌划分成一些combo，考察这些combo价值的和，其中最大的划分价值和就可以作为手牌的估价。

因为一共只有20张手牌，所以在确定每一种combo的价值后，我们可以通过一个简单的DP求出当前手牌的所有子集的估价。

### combo估价

对于每种牌，我们可以通过预测剩下两个人手中的牌来确定这种牌的价值。

我将牌的价值分为三个部分：

被压概率：指的是被更大的牌压的概率

顺牌概率：指的是在别人出牌后跟出的概率

胜利/失败概率：指的时在出完之后自己直接获胜/失败的概率

其中，两个价值的参照指数根据双方手牌的数量可以发生变化，总体而言被压概率应该是一个负贡献，而顺牌概率应该是一个正贡献。

胜利/失败概率则应该乘一个较大的指数，对于地主而言，这部分意味着出了这个combo农民直接获胜的概率，而对于农民而言，这部分意味着出了这个combo地主获胜/另一个农民获胜的概率。

因为在我们出牌时，我们更倾向于先出不容易被压，又不容易顺出去的牌。

所以在算当前局面出什么牌的时候，应该综合参考每个combo被压的概率，失败/胜利的概率以及剩下牌的总估价。

除此之外，如果对于一个combo我们有相同类型但更大的combo时，我们可能会优先考虑先出这种combo，这部分我们可以通过将这两个combo绑在一起来实现。

* * *

### version 1

version 1采取了搜索一步的策略，通过枚举对方的出牌来完成。

但由于预测准确度不行，而且对方的策略也不是纯随机，所以效果不好，在第一次比赛中只取得了50名的成绩，非常不理想。

在代码的具体实现上，我通过预处理的方式将所有出现概率比较大的牌行都算了出来，之后分别统计概率。

但是这样会导致带牌的情况非常多，概率统计会出现很大问题。

* * *

### version 2

具体实现沿用了上个version，只是在估价上进行了稍稍调整，并将一部搜索换成了直接估价。

目前还没有写农民的策略以及终盘游戏结束的估价，先看看晚上练习赛的效果。
