---
title: filecoin技术架构分析一：filecoin概念
permalink: filecoin-code-analysis-1
date: 2019-02-18 16:35:33
categories:
    - FileCoin
tags:
    - FileCoin
author: 先河系统杨尉
un_reward: true
---

我是先河系统CTO杨尉，欢迎大加关注的的Github: [waynewyang](https://github.com/waynewyang)，本文是[filecoin技术架构分析系列文章](https://learnblockchain.cn/2019/03/11/filecoin-code-analysis-0/)第一章介绍filecoin概念。

<!-- more -->

## filecoin的定义<a name="filecoin的定义"></a>
- Filecoin是一个去中心化的存储网络（**DSN**），是一个**云存储的自由交易市场**，通过Filecoin**项目**来实现其**协议**。矿工通过提供数据存储或检索来获得**token**（也称为“filecoin”）。相反，客户向矿工支付token以存储或分发数据并进行检索。

- Filecoin的多重含义：
    - 网络
    - 市场
    - 项目
    - 协议
    - Token

## filecoin的设计目的<a name="filecoin的设计目的"></a>
- filecoin设计符合**激励相容**，每一个参与者的最有利可图的选择（包括目标客户，矿工，投资者和开发人员）将是采取行动提高网络服务质量，这也是他们的最优策略。

- 以超高竞争力的价格可靠地存储文件（低成本、高效率）

- 客户可以调整其存储策略以满足他们的需求，在冗余，检索速度和成本之间创建自定义平衡。全球的Filecoin存储和检索市场使供应商竞争以最优惠的价格为客户提供灵活的选择


## filecoin与IPFS的关系<a name="filecoin与ipfs的关系"></a>

### filecoin与IPFS属性对比<a name="filecoin与ipfs属性对比"></a>
|类别| IPFS | Filecoin |
|:-------:|:--------|:--------|
|功能 |基于内容寻址的分布式存储基础设施|IPFS网络之上的激励层，提供一个云存储领域的自由交易市场|
|对标对象|HTTP|大型集中式孤岛存储提供商，如国外的aws、国内的aliyun等|
|存储权限|对有所有权的IPFS节点具备存储权限|1 除对有所有权的IPFS节点具备存储权限外<br/>2 还可以通过支付的方式，在其供应商的节点之上具备存储权限|
|读取权限|ALL（只要知道内容cid）|ALL（只要知道内容cid）|
|架构设计|**另行文章补充分析**|原则上需要无缝对接到IPFS<br/>1Filecoin将IPLD用于区块链数据结构<br/>2 Filecoin节点使用libp2p建立彼此的安全连接<br/>3 节点和Filecoin块传播之间的消息传递使用libp2p pubsub|
|使用场景|1 存储自己的节点数据，分享数据等，类似BT<br/>2 基于IPFS或其中部分组件构建企业自己的分布式云存储架构、区块链架构等|1 成为filecoin矿工，提供分布式检索及存储服务<br/>2 成为filecoin客户，支付费用享受filecoin网络的检索及存储服务<br/>3 基于filecoin，开发第三方管理系统|

- IPFS现在和将来都可以免费下载，运行和使用，并且将独立于Filecoin运行。一旦Filecoin正式网络启动，IPFS节点还可以免费或利润地在Filecoin检索市场上提供其文件的检索。


### IPFS的对标对象<a name="ipfs的对标对象"></a>
|特点| HTTP | IPFS |
|:-------:|:-------:|:-------:|
|**寻址方式** |**位置寻址**<br />一维寻址，低效、脆弱|**内容寻址**<br />多维寻址，高效、稳定|
|效率|低效|高效|
|稳定性|脆弱|稳定|
|开放性|封闭、垄断|开放、共享|

### filecoin的对标对象<a name="filecoin的对标对象"></a>
|特点| 传统云存储提供商（大型集中式孤岛存储网络） | Filecoin |
|:-------:|:--------|:-------:|
|网络模式 |集中式 |DSN|
|加入门槛 |高，从硬件底层基础设施、一直到软件、服务的提供，小企业很难插足 |低、自由交易市场，Filecoin做好基础设施|
|宏观视野：闲置存储空间 |高|低|
|价格 |昂贵，垄断、可人为保持高水平|便宜，自由竞争市场|
|安全性 |差，破坏隐私<br />1 云存储上可查看用户隐私<br />2 甚至许多密码鉴权信息都没有隐私可言<br />3 单个提供商的故障影响大|强<br />1 无第三方或者中心机构，文件加密安全得到保障<br />2 单个云提供商的故障小|
|利益分配群体 |巨头 |All|

## filecoin网络中的角色<a name="filecoin网络中的角色"></a>
|角色| 说明 |主要影响因素|
|:-------:|:--------|:--------|
|存储矿工 |存储矿工通过为客户存储数据来获得Filecoin；获得区块奖励和交易费用的概率与矿工对Filecoin网络的存储量成正比 |存储容量|
|检索矿工 |检索矿工的带宽和交易的出价/响应时间（即延迟和与客户的接近度）将决定其在网络上关闭检索交易的能力 |带宽|
|检索客户|支付filecoin获取检索服务||
|存储客户|支付filecoin获取存储服务||

### 矿工收益方式类比理解<a name="矿工收益方式类比理解"></a>
- 类比**filecoin为一家股份公司，类比存储矿工为股东（股份出资人）**

|收益来源| 类比分析 |
|:-------:|:--------|
|提供存储服务|存储矿工收益来自两部分<br/>1 工资（提供存储并收取服务费用）<br/>2 按照出资比例分红（区块奖励就是按照有效存储占比来实现的） |
|提供检索服务|检索矿工是offchain的，不参与挖矿，收益来自<br/>1 工资（提供检索并收取服务费用） |

### 存储矿工<a name="存储矿工"></a>
存储两类数据，**存储整个区块链所需的总存储量将远低于矿工为交易存储的密封数据**。
- 密封客户的存储数据
- blockchain数据的数据的副本

### 检索矿工<a name="检索矿工"></a>
- 提供检索的途径
	- 可以存储热门数据（非存储矿工），以便更优质提供服务
	- 自己同时做存储矿工，或者从存储矿工处获取
	- 不限于从filecoin网络获取，可以从免费的IPFS网络获取
- 检索效率的保证
  	- 检索矿工是不运行在blockchain中的，是off blockchain的。
  	- 全球分布式


### 客户(检索客户和存储客户)选择filecoin的理由<a name="检索客户和存储客户"></a>
- 企业客户愿意使用filecoin来支付数据存储和检索的理由
  - filecoin是一套激励相容的系统，filecoin的设计目标保证了每个参与者（包括客户，矿工，投资者和开发人员）的最有利可图的选择或者说是最优策略是采取行动来提高网络的服务质量。具备技术先进性。
  - 数据更为安全
  	- 抵押机制促使矿工提供稳定安全服务，预计会出现声誉系统。矿工需要自行保证系统内的稳定性。
  	- 即便提供商出现故障，**filecoin网络可以在多个存储提供商之间进行额外的修复。**
  	- 客户可以根据数据安全等级选择副本数量。
  	
  - 价格更为廉价
  	- 内容寻址的本质决定了其全局冗余度低。
  	- filecoin作为全球性的分布式存储系统，可以做全球性去重，从而降低整个网络存储成本。

- 个人客户选择使用filecoin的理由
    - 预计filecoin将提供允许一方支付另一方来检索数据的结构
      - 包括web 2.0网站的主要内容分发模型，在该模型中，网站所有者为基础设施服务付费，以免费向其用户提供数据，然后以其他方式通过内容获利。
	- filecoin的设计目标，让用户和内容创作者能够探索各种新的内容分发和经济模型。
		- 例如版权问题的解决

[深入浅出区块链](https://learnblockchain.cn/) - 系统学习区块链，打造最好的区块链技术博客。