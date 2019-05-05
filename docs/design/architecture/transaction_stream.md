# Transaction flow

## 1 Solution

User sends RPC request to node through SDK or curl command to start transaction. Node adds transaction to TxPool after receiving it. Sealer will constantly take out transactions from TxPool and pack them into block through certain conditions. After the blocks are generated, they will be verified as consensus by concensus engine. If there are no mistakes and consensus is made among nodes, the blocks will be taken on chain. When node using sync model to download missing blocks from other nodes, the execution and verification on blocks will be conducted also. 

## 2 Structure
The overall structure is as below:

![](../../../images/architecture/transaction_stream.png)

**Node**: Node of block

**TxPool**: transaction pool, the memory area maintained by node itself for temporarily saving recerived transactions

**Sealer**: block packager 

**Consensus Engine**: consensus engine

**BlockVerifier**: block verifier, to verify the correctness of a block

**Executor**: execution engine, to execute single transaction

**BlockChain**: administration model of blockchain, the only authorized model. User should input block data as well as execute contextual data before submiting block interface. This administration model will combine the two types of data into one and send to the bottom storage

**Storage**: the bottom storage

main relations are as followed:

 1. User starts transaction through SDK or curl command to the connected node.
 2. 节点收到交易后，若当前交易池未满则将交易附加至TxPool中并向自己所连的节点广播该交易；否则丢弃交易并输出告警。
 3. Sealer会不断从交易池中取出交易，并立即将收集到的交易打包为区块并发送至共识引擎。
 4. 共识引擎调用BlockVerifier对区块进行验证并在网络中进行共识，BlockVerifier调用Executor执行区块中的每笔交易。当区块验证无误且网络中节点达成一致后，共识引擎将区块发送至BlockChain。
 5. BlockChain收到区块，对区块信息（如块高等）进行检查，并将区块数据与表数据写入底层存储中，完成区块上链。

## 3 方案流程

### 3.1 合约执行流程

执行引擎基于执行上下文（Executive Context）执行单个交易，其中执行上下文由区块验证器创建用于缓存暂存区块执行过程中执行引擎产生的所有数据，执行引擎同时支持EVM合约与预编译合约，其中EVM合约可以通过交易创建合约、合约创建合约两种方式来创建，其执行流程如下：

![](../../../images/architecture/EVM_contract_execution.png)

EVM合约创建后，保存到执行上下文的_sys_contracts_表中，EVM合约的地址在区块链全局状态内自增，从0x1000001开始（可定制），EVM合约执行过程中，Storage变量保存到执行上下文的_contract_data_(合约地址)_表中。

预编译合约分永久和临时两种：(1) 永久预编译合约，整合在底层或插件中，合约地址固定；(2) 临时预编译合约，EVM合约或预编译合约执行时动态创建，合约地址在执行上下文内自增，从0x1000开始，至0x1000000截止，临时预编译合约仅在执行上下文内有效预编译合约没有Storage变量，只能操作表，其执行流程如下：

![](../../../images/architecture/precompiled_contract_execution.png)
