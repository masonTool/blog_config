---
title: 10分钟带你认识区块链
date: 2018-06-07 12:12:33
categories:
- 技术研究
---


# 10分钟带你认识区块链

刚接触区块链这个概念的时候, 相信很多人都会有跟我一样的疑问: 区块链倒底是什么? 拿来干什么用的? 怎么用? 它和比特币是什么关系?  

你会带着一头的问号打开搜索引擎. 一般会得到如下的解答: 

* 区块链会引发下一轮的技术革命
* 区块链是人类历史上在金融领域最大的突破,  
* 区块链会引发社会和经济的重大变革.  
* 有一些反对的意见, 认为区块链的价值被夸大了,  比特币是人类史上最大的泡沫 ....

还有更专业一些的回答:

* 区块链就是一个去中心化的分布式帐本. 
* 区块链信息不可篡改. 
* 区块链是比特币的底层协议. 类比于信息互联网的HTTP协议, 区块链就是"价值互联网"的"HTTP协议". 
* 区块链的最大价值在于它的智能合约系统

这很容易给人留下一个复杂, 高深又神秘的印像. 其实抛开用于解决具体问题的一系列复杂的场景. 区块链不过就是一个简单的数据结构. 

我认为区块链的复杂不在于他的复杂场景的处理逻辑, 而是在于它的生态形成的内在逻辑. 这个逻辑是比特币之所以流行的原因. 也是众人讨论的焦点. 不过各位不用担心, 在你读完这篇文章的时候, 你一定也可以理解这一点, 我会带着你们由浅入深的进行探讨. 

## 认识区块链

区块链这个词源于中本聪的比特币白皮书<比特币: 一种点对点的电子现金系统>, 在文中其实并没有区块链这个词, 只有"区块"(Block)和"链"(Chain). 我们就从这两个词来入手. 其实区块链确实就是用"链"连接起来的"区块"而已. 

![](blockchain.png)

也许你会说, 这未免也太简单了吧! 这么个结构,  怎么不可篡改的? 怎么存储帐本的? 怎么去中心化的? 怎么实现比特币的? ... 更多的疑问向你袭来. 不要急, 让我们一个一个来分析.

#### 怎样不可篡改?

区块链是如何实现不可篡改的呢? 我们把上面的图复杂一下. 

![](head.png)

区块的连接方式是, 下一个新的区块总是指向上一区块的头部的Hash值. 而不可篡改的原因, 正是因为这种Hash指针的连接方式. 

这个Hash值是根据区块内容以及一些其它参数经过Hash函数的计算生成的. Hash的原理有兴趣的同学可以自行查阅, 我们只需要知道它的几个主要特点:

* 对于不同的两个初始值, 只有极低到可以忽略不计的机率找到相同的Hash值. 
* 对于不同的Hash值, 其对应的初始值一定不同. 
* 不可逆, 无法通过Hash值倒推出初始值

这也就是说, 当一个区块有效生成的时候, 区块的内容是固定的, 其头部的Hash值也是固定的. 这样, 区块链在它最初始的区块(称为创世区块)到最新的区块的链条里面, 其内容都是不容修改的. 任何一个区块的修改, 都会引起链条的脱节. 

了解了这一点, 你又会有问题了: 难倒区块链这么脆弱, 如果有黑客把区块的内容改了, 造成了区块链的断开, 那整个区块链会不会挂掉了? 这就涉及到我们要说的下一个问题了.

#### 如何去中心化?

前面也提到了, 区块链的一个特点就是去中心化的. 所谓去中心化是与中心化相反的概念. 我们平时使用的网页是中心化的, 因为它需要一个服务器来提供网页服务, 网页服务器挂掉了大家都看不了网页内容. 银行系统是中心化的, 因为它的数据由统一的服务器数据库来保存, 如果数据库挂掉了, 大家的钱就都不见了. 去中心化刚好相反, 没有一个统一的中心服务器来管理, 区块链的数据保存在网络中的每个区块链的节点上. 

这也就解决了上一节提出的问题. 如果你的区块链被改动断开了, 对整个区块链是没有影响的,  因为还有其它节点没有断开. 在整个区块链网络中. 你的这个改动会被直接忽略了. 

在网络中节点是这样一个概念. 

![](jiedian.png)

> 一个网络中的节点存储区块链的所有信息.  这个节点有多大呢?
比特币的单个区块, 最大是1M, 大约每10分钟生成一个.  其数据量一年也才增加50G左右. 因为比特币前些年的交易量较小, 一个区块远没有1M,  近两年才可能到达1M, 所以到现在区块总大小也才200G不到而已. 
PS: 其实比特币的区块大小限制, 以及生成规则限制, 直接决定了比特币能处理的上限为每秒约3.5个交易.  所以近些年比特币圈一直在讨论关于扩容的事.

每个节点都存储着整个区块链的所有信息, 这些节点称为全节点, 也叫挖矿节点(我们后面会介绍). 在整个网络, 存在众多这样的节点, 它们交织形成区块链的网络. 区块链中每个节点都能与其它的相邻几个节点相互通信连接. 

节点间通信的一个最重要的内容就是商量出一个结论: 决定一个区块链应该包含哪些区块. 我们知道, 网络是不可信的, 存在延迟和同步等等问题, 一个一致的结论要如何达成呢?

这就涉及到了区块链的共识算法. 简单来说, 就是所有节点达成一个共识:  只要发现了其它节点的区块链上的链条比自己的链条长, 那就认为那条最长的是区块链, 本地不在这条最长链上的区块, 会被抛弃. 在上面提到的区块链被篡改的例子里, 之所以被改动的区块链会被忽略也是因为这个原因. 

讲到这里时, 你可能会问: 如果有一个黑客, 把自己的区块链条改的很长, 会不会控制了整个区块链?  下面一节可以给以解答.

#### 什么是挖矿?

在现实世界中, 挖矿是为了赚取收益的, 在区块链中也是一样. 以比特币为例. 比特币的矿工每发现一个新区块, 现在可以赚取12.5个比特币的收益(这个收益每四年减半), 按现在市值大概11W美金, 这个收益还是相当可观的.  

之所以用挖矿来比喻发现一个新区块, 是因为要找到一个新的区块并不简单. 从上文中知道, 区块之间是通过Hash值进行链接的. 但是这个Hash值一定要符合特定的规则才算是一个有效的区块. 

我们把上面的区块继续细化, 假设矿工想要"挖"到一个有效的新区块, 他需要进行如下的计算: 

![](wakuang.png) 

有效区块需满足:    `H(A, B, C) < 目标Hash值`.    其中:

* H是Hash函数, 比特币使用sha-256算法.
* A是区块的内容的Hash值(需注意并不是当前区块的Hash值) 
* B是一个32位的随机数. 矿工需要通过调整这个值, 来使上面的公式满足.
* C称为币基参数, 包含矿工的个人信息和随机数. 当经过2^32次运算后, 如果依然不能使条件满足, 就需要调整币基参数中的随机数, 继续进行遍历B的运算. 

前面在介绍Hash函数时介绍过, Hash函数是不可逆的. 因此矿工只能通过不断的遍历随机数, 才可能得到目标Hash值的解. 在区块链网络中, 所有的矿工都在竞争去做这样的一道运算题, 期待自己可以先得到答案. 

先解出答案的矿工会把生成的新区块连接到自己的区块链的末端, 然后向相邻的节点广播自己最新的区块状态. 相邻节点收到通知后, 会更新自己的区块状态, 然后继续广播出去.  

接到通知的节点除了更新自己的区块状态以外, 还会中止自己在前一区块的挖矿运算, 在新的区块基础上开始新的挖矿运算. 很显然如果它还在原来的基础上挖矿, 即使得到了最终的解,  还是会因为落后其它节点一个区块的长度, 而被废弃.

在这里可以看出, 挖矿的难度非常大, 而且竞争非常激烈. 因此上一节中的问题基本不可能发生, 因为没有一个黑客有如此大的算力可以一直领先其它矿工找到新的区块.

在此还有三点需要说明:

* 每个节点计算的题目都是不同的. 因为大部分情况下区块的内容A都不相同. 即使A完全相同, C也会不同.  所以不存在同解的情况. 
* 如果两个节点同时发现了新的区块. 相邻节点会根据通知的到达顺序, 来决定使用谁的区块. 在传播的过程中, 总有一个会被淘汰.  
* 条件中`目标Hash值`不是固定的.  它会根据固定区块产生的频率来自动调整难度. 目标Hash值越小, 难度越大. 最终新区块产生的频率会维持在一个均值. (比特币是10分钟左右)

到这里, 我们已经介绍完了, 区块链的产生, 数据结构, 以及组织方式. 不过稍微有点知识的朋友, 可能会有一个关于区块链终极问题的考虑: 区块链的赖以生存的条件就是新区块的产生, 而新区块的产生有赖于挖矿节点对利益的需求, 如果有一天矿被"挖空"了怎么办?

#### 区块链的终极问题

矿被"挖空"的情况并不是危言耸听, 这一天总是会到来的. 以比特币为例, 比特币的总量为2100万, 到现在已经挖了1700万, 预计到2140年, 全部比特币会被挖空. 这一天的到来意味着, 新产生的区块不会再有比特币奖励. 还会有矿工愿意贡献自己的电量, 存储, 以及硬件设备, 来维持区块链的正常运转吗?

其实这一点我们可以不用担心. 矿工在挖矿中的收益除了来自于区块奖励, 还有一部分就是矿工将交易纳入区块中的手续费用. 这一部分的收益现在还比较低, 但是相信将来应该会成为矿工的主要收益来源. 

#### 总结

我想你应该已经大概理解了. 

* 区块链通过区块来存储数据, 使用Hash指针连接区块, 来保证区块链上内容不可篡改.  
* 区块链网络中存在很多存储全量区块数据的节点,  节点间通过共识算法使用最长的区块作为区块链,  以此达到去中心化.  
* 矿工通过挖矿赚取收益，由此保证新区块的产生.  
* 区块链通过设置有难度的挖矿难题, 来保证区块链的健康运作. 

## 认识比特币

仅管理解了区块链的一些原理, 可能你还会有些迷惑: 比特币是如何在区块链上交易的? 它和我们平时使用的货币有什么区别? 在一个去中心化的网络中, 要怎么证明一个比特币是属于我的呢? ...

不用急, 我们先从最基本的谈起. 谈谈比特币是什么?

#### 比特币是什么?

我们平时接触的是人民银行发行的流通货币, 称为法币. 拿着它可以跟商店老板一手交钱一手交货, 也可以存入微信, 支付宝刷二维码付款. 它的价值就体现在流通上. 

比特币的作用也是类似. 你可以认为比特币就是一种流通的介质. 只不过比特币不同于法币, 无法拿出一个称为比特币的硬币来, 它的存在就是网络世界中的代码. 所以你看到图片中一个着B字的黄色硬币图案, 其实是不存的, 那都是为了表示抽象概念的形象表示而已.

![](bitcoin.png) 

比特币在区块链的代码中, 不是一个数据结构, 也不是一个对象. 其实就是一个交易的记录. 

比特币只能在挖矿得到新区块的奖励中产生, 所以矿工挖到的新区块, 会带有一个初始交易(称为币基交易). 这个初始交易的输入为空, 输出为矿工的帐户和比特币的数量. 因此矿工的帐户增加了相应数额的比特币. 

记得我们之前挖矿的一节提到过, 有时需要调整币基参数来遍历解题, 矿工的帐户信息就存在于这个币基参数之中.  

那么, 在比特币中帐户是怎么来的呢?

#### 什么是比特币的帐户?

一提到帐户, 读者心中一定会联想起在银行大厅中开户的繁琐流程. 开户, 注册, 提交资料....这些流程无一不是和中心化的体系相联系的. 在一个去中心化的比特币系统中则要简单的多. 

比特币帐户的核心是使用密码学中的数字签名方案:

* 用户使用数字签名算法(比特币使用ECDSA)生成一对公钥PK和私钥SK: `PK, SK = 生成签名(随机数)`
* 对于一段特定的信息message, 可以生成信息签名: `sig = 签名函数(SK, message)`
* 则可以得到一个验证: `isvalid = 验证(PK, message, sig)`. 
* 如果验证通过, 可以得到这样一个结论: 信息message是由PK和SK这一对组合发出的, 因为只有PK能验证通过. 换句话说`信息message是属于PK和SK的, 签名就是证据`

所以, 在比特币中就把公钥看做一个用户的帐户. 不过一般公钥比较长, 比特币就使用公钥的Hash值看做是用户的收款地址. 所以用户帐户为:

```
PK, SK = 生成签名(随机数);
用户帐户 = Hash(PK);  
```

同时, 用户需要保存自己的私钥PK. 以便于交易时, 证明比特币由用户自身持有, 如:

```
message = "用户A持有2比特币";//message是资产的证明, 实际中代表区块链中一个真实的交易记录
sig = 签名函数(SK, message);
验证通过 = 验证(PK, message, sig) && (Hash(PK)==用户A的帐户); 
```

这里的逻辑稍有些复杂, 读者朋友需细细体会. 不太了解也可以先略过. 你只需要知道帐户就是用户定义的一个公钥(银行帐号), 用户需要保存自己的私钥(取钱密码)来使用帐上的钱.

好了. 你现在已经有了一个比特币帐户, 而且帐户上可能还有一些数量的比特币, 应该如何交易呢?

#### 用户如何交易?

在刚一开始了解比特币交易时, 我有一个误解. 误以为比特币的交易类似于淘宝, 京东的交易清单, 需要金钱与实物的关联. 可想而知那个数据量需要多么的庞大. 

但其实比特币的交易就像是我们银行帐户的里面的转入和转出, 只有转帐地址与转帐数额(交易时间等必要信息)的记录, 其数据量是相当小的. 至于交易的原因, 则由交易双方线下完成. 

说到交易就是提到一系列的安全问题:

* 首先你要证明你拥用这笔钱. (对应: 银行帐户的余额)
* 保证收款人收到了这笔钱. (对应: 收款人收到钱)
* 保证你没有把这笔钱重复转给了两个人. (对应: 已经转给了帐户A, 就无法再转给帐户B)

比特币在交易的设计用一种巧妙的方式解决了这些问题, 我们来一一解答:

##### 归属问题

在前面介绍比特币来源时, 我们提到了`币基交易`, 这是由新区块奖励产生的只有输出, 没有输入的一个交易. 币基交易是一类特殊的交易,  用户之间的交易是必须同时存在输入和输出的. 而交易的输入就是之前已存在的交易的输出.

可以这样说: 帐户持有的比特币, 就是帐户对应的交易输出. (这些输出没有作为输入进行交易过). 如下图:

![](jiaoyi.png) 

图中交易流程表示:帐户A通过币基交易得到12.5个币, 然后交易给帐户B 5个币, 又交易给帐户C 3个币.

我们可以看到, 在对于帐户A一系列的交易记录中, 只有交易3的第二个输出没有作为输入交易过, 所以帐户A的余额就是4.5个币. 

你也许会说, 一个帐户可能对应的交易有很多啊. B也可以转给A几个币, C也可以转给A几个币, 这个怎么统计? 如果A想要花一笔大价钱买东西怎么办?

* A可以把多个交易向自己的输出作为输入, 去向其它帐户交易. 
* A也可以为了方便, 通过这种方式, 把多个零散的交易输出作为输入, 指定自己为输出, 完成以零换整的过程.

另外, 交易的输入和输出不一定是相等的. 用户可以选择输出小于输入, 将这一部分差额作为交易的费用支付给矿工. 因为矿工是负责的记帐的人.

##### 记帐过程

要确保一项交易的完成, 就需要记帐. 记下帐来就既能确定收帐人拿到了币, 又能保证交款人不能抵赖, 拿同样的钱再去进行别的交易. 比特币是如何记帐的呢?

比特币记帐就是把我们上面提到的交易记录, 存储到区块上. 之前我们提到过生成的区块已经不能修改, 所以新的交易都是记录在新的区块上, 这依赖于矿工的工作. 

回到我们之前的挖矿工作上来:

![](meikl.png) 

矿工在挖矿的时候, 会选择一些交易纳入到区块内容中. 以图中的组织形式(称为梅克尔树)进行编译, 所以得到了挖矿运算需要用到的一个参数 `Hash值A`. 根据Hash的特性, 对于`Hash值A`, 就是代表了整个树的信息. 对树上任何交易的修改都会引起Hash值的变化. 

刚刚提到矿工会选择交易, 有必要说明一下:

* 每个挖矿节点都会存在一个交易池, 交易池可以监听到区块网络中传递过来的交易信息. 
* 矿工是可以挑选哪些交易进入区块的, 比如交易费比较高的交易. 最终如果生成了有效的区块, 矿工就可以得到相应的交易的差额.

是不是新成了包含相应交易的有效区块, 就算交易成功了呢? 不一定哦! 我们之前说过, 新成了有效区块, 并不代表新区块会被纳入区块链, 还有可能被丢弃. 只有在得到区块链中其它节点的共识后, 才算真正的记帐成功. 所以比特币中存在一个`交易确认`的过程. 一般认为经过6次确认就算交易成功了, 即当前区块链后又生成了6个新的区块. 所以要确定你的交易已经成功, 需要经过大概1个小时. (前面讲过, 新区块生成间隔为大概10分钟) 

如果你能够耐心看下来, 流程应该已经了解得差不多了. 想要试着去交易一下了. 但是一个难题摆在面前, 200G的区块链全量数据还是有点太大了, 你又不想去挖矿, 要怎么才能交易呢?

##### 轻量节点:SPV

其实在比特币系统里, 大部分都是跟你一样的用户, 只关心与自己相关的一小部分交易. 这部分用户就是做为比特币网络中的轻量节点. 这类节点不存储整个比特币区块链, 不会决定区块是否加入区块链, 不挖矿, 只存储和核验与自己相关的一小部分信息. 

这类用户使用简单付款验证(SPV)客户端进行交易. 只需要几十M的数据. 

讲到这里关于比特币你已了解得差不多了. 原理讲清了, 但好像还缺点什么? 

## 最后你要知道的

#### 智能合约

智能合约是区块链交易的一个中间环节, 我们知道在比特币交易时, 需要一个输入和输出, 在上面我们举的例子里, 输出只是一个帐户的地址和金额, 在智能合约交易中可以使用脚本地址作为输出地址. 

类似于这样:

```
OP_DUP OP_HASH160 be10f0a78f5ac63e8746f7f2e62a5663eed05788 OP_EQUALVERIFY OP_CHECKSIG
```

在交易被纳入区块链时, 相应的脚本指令会执行. 可以实现如: 第三方支付, 网络众筹, 博彩, 抵押担保, 保险等场景. 因为区块链去中心化以及不可篡改的安全性, 消除了机购存在欺诈的可能性, 提高了效率, 也消除了信任成本, 所以意义非常重大. 

#### 其它数字货币

现在基于区块链的数字货币层出不穷, 大部分币种都是根据比特币衍生出来的. 它们的区别, 在于对比特币一些规则的改动和优化. 如: 调整挖矿算法, 调整区块大小, 调整共识规则, 调整区块生成时间等等. 最典型的是以太币, 它优化了智能合约, 创建了一个图灵完备的脚本系统, 大大拓展了智能合约的使用场景. 

#### 我的看法

区块链技术不是什么新兴的技术, 只是原有技术的优化组合. 但它构成了一个自驱动, 自发展, 安全自治的生态系统, 为现有的价值体系提供了新的思路. 相信一定会有更好的发展. 

不过当今概念炒作盛行, 很多人在还没有理解的情况下, 看到有利可图就一窝蜂涌入实在不可取. 希望有更多的人能理解区块链, 用好区块链, 发展区块链, 而不是炒作区块链. 
