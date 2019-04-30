# Group structure

To fit most business scenarios, FISCO BCOS supports various functions including multi-group activation, transactions among groups, data storage and block consensus in separation engined by multi-group structure. It can safely guard the system privacy but also eliminate the difficulty in operation and maintainence of blockchain system.


```eval_rst
.. note::

    For example:

    Agency A, B, C, D consituted a blockchain network to operate Project 1. But now, A and B want to start Project 2 in the condition that C has no access to its data and transactions. How to realize it?

    - **1.3 series FISCO BCOS** : Agency A and B build another chain to operate Project 2. Administrator needs to operate and maintain both chains and their ports.

    - **FISCO BCOS 2.0** ：Agency A and B bulid another group for Project 2. Administrator maintains only one chain.

    Obviously both solutions can achieve privacy protection, but FISCO BCOS 2.0 gets advantages in scalability, operation and maintainence and flexibility.
```

In multi-group structure, Networking is shared among groups. Groups can isolate messages of some ledger through [networking access and whitelist] (../security_control/node_management.md).

![](../../../images/architecture/ledger.png)


Then, data will be isolated with other groups. Every group runs consensus algorithm indepently or differently. Each ledger model contains three layers: Core, Access, Administration (from the bottom to the top). This three layers will cooperate to ensure stable operation of each group in FISCO BCOS platform.

## Core

Core layer is responsible for inputing group data [block](../../tutorial/key_concepts.html#id3), block information, system table and execution result into data base.

Storage is formed by two parts: State and AMDB. State contains MPTState and StorageState. It stores the status information of transations but not the history records of block and has higher performance than MPTState.   ，但不存储区块历史信息；AMDB则向外暴露简单的查询(select)、提交(commit)和更新(update)接口，负责操作合约表、系统表和用户表，具有可插拔特性，后端可支持多种数据库类型，目前仅支持[LevelDB数据库](https://github.com/google/leveldb)，后期会把基于mysql的[AMDB](../storage/storage.md)集成到系统中。

![](../../../images/architecture/storage.png)


## Access

接口层包括交易池(TxPool)、区块链(BlockChain)和区块执行器(BlockVerifier)三个模块。

- **交易池(TxPool)**: 与网络层以及调度层交互，负责缓存客户端或者其他节点广播的交易，调度层(主要是同步和共识模块)从交易池中取出交易进行广播或者区块打包；

- **区块链(BlockChain)**: 与核心层和调度层交互，是调度层访问底层存储的唯一入口，调度层(同步、共识模块)可通过区块链接口查询块高、获取指定区块、提交区块；

- **区块执行器(BlockVerifier)**: 与调度层交互，负责执行从调度层传入的区块，并将区块执行结果返回给调度层。


## Administration

调度层包括共识模块(Consensus)和同步模块(Sync)。

- **共识模块**：包括Sealer线程和Engine线程，分别负责打包交易、执行共识流程。Sealer线程从交易池(TxPool)取交易，并打包成新区块；Engine线程执行共识流程，共识过程会执行区块，共识成功后，将区块以及区块执行结果提交到区块链(BlockChain)，区块链统一将这些信息写入底层存储，并触发交易池删除上链区块中包含的所有交易、将交易执行结果以回调的形式通知客户端，目前FISCO BCOS主要支持[PBFT](../consensus/pbft.md)和[Raft](../storage/storage.md)共识算法；

- **同步模块**：负责广播交易和获取最新区块，
考虑到共识过程中，[leader](../consensus/pbft.html#id1)负责打包区块，而leader随时有可能切换，因此必须保证客户端的交易尽可能发送到每个区块链节点，节点收到新交易后，同步模块将这些新交易广播给所有其他节点；考虑到区块链网络中机器性能不一致或者新节点加入都会导致部分节点区块高度落后于其他节点，同步模块提供了区块同步功能，该模块向其他节点发送自己节点的最新块高，其他节点发现块高落后于其他节点后，会主动下载最新区块。
