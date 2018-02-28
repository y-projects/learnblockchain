---
title: 智能合约语言 Solidity 教程系列8 - Solidity API 
date: 2018-02-22 19:36:22
categories: 
    - 以太坊
    - Solidity
tags:
    - Solidity手册
author: Tiny熊
---

Solidity教程系列第8篇 - Solidity API 即一些内置的特殊变量及函数，

<!-- more -->

## 写在前面

Solidity 是以太坊智能合约编程语言，阅读本文前，你应该对以太坊、智能合约有所了解，
如果你还不了解，建议你先看[以太坊是什么](https://learnblockchain.cn/2017/11/20/whatiseth/)

欢迎订阅[区块链技术专栏](https://xiaozhuanlan.com/blockchaincore)阅读更全面的分析文章。

# 特殊的变量及函数
内置函数 API


有一些特殊的变量和函数存在于全局命名空间里，用来提供一些区块链当前的信息。
## 区块和交易的属性（Block And Transaction Properties）
* block.blockhash(uint blockNumber) returns (bytes32)：返回给定区块号的哈希值，只支持最近256个区块，且不包含当前区块。
* block.coinbase (address): 当前块矿工的地址。
* block.difficulty (uint):当前块的难度。
* block.gaslimit (uint):当前块的gaslimit。
* block.number (uint):当前区块的块号。
* block.timestamp (uint): 当前块的Unix时间戳（从1970/1/1 00:00:00 UTC开始所经过的秒数）
* msg.data (bytes): 完整的调用数据（calldata）。
* msg.gas (uint): 当前还剩的gas。
* msg.sender (address): 当前调用发起人的地址。
* msg.sig (bytes4):调用数据(calldata)的前四个字节（例如为：函数标识符）。
* msg.value (uint): 这个消息所附带的以太币，单位为wei。
* now (uint): 当前块的时间戳(block.timestamp的别名)
* tx.gasprice (uint) : 交易的gas价格。
* tx.origin (address): 交易的发送者（全调用链）

注意：
msg的所有成员值，如msg.sender,msg.value的值可以因为每一次外部函数调用，或库函数调用发生变化（因为msg就是和调用相关的全局变量）。

不应该依据 block.timestamp, now 和 block.blockhash来产生一个随机数（除非你确实需要这样做），这几个值在一定程度上被矿工影响（比如在赌博合约里，不诚实的矿工可能会重试去选择一个对自己有利的hash）。

对于同一个链上连续的区块来说，当前区块的时间戳（timestamp）总是会大于上一个区块的时间戳。

为了可扩展性的原因，你只能查最近256个块，所有其它的将返回0.

## 错误处理
* assert(bool condition)

用于判断内部错误，条件不满足时抛出异常
* require(bool condition):
用于判断输入或外部组件错误，条件不满足时抛出异常

* revert():
终止执行并还原改变的状态

## 数学及加密功能
* addmod(uint x, uint y, uint k) returns (uint):
计算(x + y) % k，加法支持任意的精度且不会在2**256处溢出，从0.5.0版本开始断言k != 0。
* mulmod(uint x, uint y, uint k) returns (uint):
计算 (x * y) % k， 乘法支持任意的精度且不会在2**256处溢出， 从0.5.0版本开始断言k != 0。
* keccak256(...) returns (bytes32):
使用以太坊的（Keccak-256）计算HASH值。紧密打包参数。
* sha256(...) returns (bytes32):
使用SHA-256计算hash值，紧密打包参数。
* sha3(...) returns (bytes32):
keccak256的别名
* ripemd160(...) returns (bytes20):
使用RIPEMD-160计算HASH值。紧密打包参数。
* ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address):
通过椭圆曲线签名来恢复与公钥关联的地址，或者在错误时返回零。

通过签名信息恢复非对称加密算法公匙地址。如果出错会返回0，附录提供了一个例子
recover the address associated with the public key from elliptic curve signature or return zero on error (example usage)
紧密打包参数（tightly packed）意思是说参数不会补位，是直接连接在一起的，下面几个是相等的。

```
keccak256("ab", "c")
keccak256("abc")

keccak256(0x616263)  // hex
keccak256(6382179)
keccak256(97, 98, 99)   //ascii
```
如果需要填充，可以使用显式类型转换：keccak256("\x00\x12") 与keccak256(uint16(0x12))相同。

注意，常量将使用存储它们所需的最少字节数来打包。 这意味着，例如keccak256(0) == keccak256(uint8(0))和keccak256(0x12345678) == keccak256(uint32(0x12345678))

在私链(private blockchain)上运行sha256,ripemd160或ecrecover可能会出现Out-Of-Gas报错。因为它们实现了一种预编译合约，合约要在收到第一个消息后才会真正存在（虽然他们的合约代码是硬编码的）。向一个不存在的合约发送消息，非常昂贵，所以才会导致Out-Of-Gas的问题。一种解决办法（workaround）是每个在你真正使用它们之前先发送1 wei到这些合约上来完成初始化。在官方和测试链上没有这个问题。


## 地址相关

* <address>.balance (uint256):
Address的余额，以wei为单位。

* <address>.transfer(uint256 amount):
发送给定数量的ether到某个地址，以wei为单位。失败时抛出异常。

* <address>.send(uint256 amount) returns (bool):
发送给定数量的ether到某个地址，以wei为单位, 失败时返回false。

* <address>.call(...) returns (bool):
发起底层的call调用。失败时返回false。

* <address>.callcode(...) returns (bool):
发起底层的callcode调用，失败时返回false。
不鼓励使用，未来可能会移除。

* <address>.delegatecall(...) returns (bool):
发起底层的delegatecall调用，失败时返回false

更多信息参考地址章节。

**警告**：send() 执行有一些风险：如果调用栈的深度超过1024或gas耗光，交易都会失败。因此，为了保证安全，必须检查send的返回值，如果交易失败，会回退以太币。如果用transfer会更好。

## 合约相关

* this（当前合约的类型）:
表示当前合约，可以显式的转换为Address
* selfdestruct(address recipient):
销毁当前合约，并把它所有资金发送到给定的地址。
* suicide(address recipient):
selfdestruct的别名

另外，当前合约里的所有函数均可支持调用，包括当前函数本身。