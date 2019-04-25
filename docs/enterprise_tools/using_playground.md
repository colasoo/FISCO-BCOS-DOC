# Case analysis

```eval_rst
.. important::
    We recommend users to read `tutorial for deployment tool <../tutorial/enterprise_quick_start.html>`_ 
```

## Networking example

We will take a common scenario as an example in this section. Here are 4 agencies, A, B, C, D, of which the nodes are deployed as below: 

| Node no. |   P2P address     |   RPC/channel address     |   Belonged agency     |   Belonged group     |
| :-----------: | :-------------: | :-------------: | :-------------: | :-------------: |
|   Node 0     | 127.0.0.1:30300| 127.0.0.1:8545/:20200 | Agency A | Group 1、2、3 |
|   Node 1     | 127.0.0.1:30301| 127.0.0.1:8546/:20201 | Agency B | Group 1 |
|   Node 2     | 127.0.0.1:30302| 127.0.0.1:8547/:20202 | Agency C | Group 2 |
|   Node 3     | 127.0.0.1:30303| 127.0.0.1:8548/:20203 | Agency D | Group 3 |

![](../../images/enterprise/star.png)

Agency A、B、C、D maintains one node each. Node 0 of A is placed in 3 groups. Now, we will explain how to deploy the above multi-group consortium chain in a peer-to-peer way.

### Certificate agreement

#### Aquire qualification for certificate

1. Agency A, B, C, D generate `agency.key` locally.

2. Agency A, B, C, D apply for networking qualification from the third-party authority, who later generates root certificate `ca.crt`, to get certificate `agency.crt`.

#### Node certificate agreement

1. Agency A, B, C, D each generates node certificate through private key `agency.key` and certificate `agency.crt`cert_127.0.0.1_30300.crt，cert_127.0.0.1_30301.crt，cert_127.0.0.1_30302.crt，cert_127.0.0.1_30303.crt.

2. Agency A agrees on node certificate respectively with B, C, D, and places under meta folder.

```eval_rst
.. note::
    
    In this scenario, we suppose that A is the generator of Genesis Block. 
```

### Agency A and B form group 1

Networking process is as below:

![](../../images/enterprise/sample_star_1.png)

1. Agency A places fisco-bcos executable program under meta folder, modifies information in `group_genesis.ini`, generates the Genesis Block of group 1 and deliver it to Agency B.

2. Agency A, B each modifies information in `node_deployment.ini`, and configures Node 0 and Node 1. In this step, `node` is the node of each agency itself, and `peers` means link information of the counterparty.
3. Agency A, B each generates node configuration file folder using [build_install_package](./operation.html#build-install-package-b) instruction.

4. Agency A, B each imports its private key to the generated node configuration file and activates node.

Then, Agency A, B have formed group 1.

### Agency A and C form group 2

Networking process is as below:

![](../../images/enterprise/sample_star_2.png)

1. Agency A modifies `group_genesis.ini` and generates the Genesis Block of group 2, and deliver it to Agency C.

2. Agency A copies group.2.genesis and group.2.ini to conf section of Node 0 configuration file folder, and reactivate node.

3. Agency C places fisco-bcos executable program under meta folder, modifies `node_deployment.ini` and configures Node 0 and Node 2.

4. Agency C generates node configuration file folder using [build_install_package](./operation.html#build-install-package-b) instruction, imports private key to configuration file and activates node.

Then, Agency A and C have formed group 2

### Agency A and D form group 3

Networking process is as below:

![](../../images/enterprise/star.png)

1. Agency A modifies `group_genesis.ini`, generates the Genesis Block of group 3 and deliver it to Agency D.

2. Agency A copies group.3.genesis and group.3.ini to conf section of Node 0 configuration file folder, and reactivate node.

3. Agency D places fisco-bcos executable program under meta folder, modifies information in `node_deployment.ini` and configures Node 0 and Node 3.

4. Agency D generates node configuration file folder using [build_install_package](./operation.html#build-install-package-b) instruction, imports private key to the generated node configuration file and activates node.

Then, Agency A and D have formed group 3.

### Configure SDK
After finishing the networking process above, agencies needs to accomplish the configuration of SDK and console. The way to connect configuration file is as below:

[Configure Web3sdk](../sdk/sdk.md)

[Configuration console](../manual/console.md)

### Agency E adds new node to join group 1

In this scenario, Agency E needs to add a node to join group 1 as below:

| Node no. |   P2P address     |   RPC/channel address     |   Belonged agency     |   Belonged group     |
| :-----------: | :-------------: | :-------------: | :-------------: | :-------------: |
|   Node 0     | 127.0.0.1:30300| 127.0.0.1:8545/:20200 | Agency A | Group 1、2、3 |
|   Node 1     | 127.0.0.1:30301| 127.0.0.1:8546/:20201 | Agency B | Group 1 |
|   Node 2     | 127.0.0.1:30302| 127.0.0.1:8547/:20202 | Agency C | Group 2 |
|   Node 3     | 127.0.0.1:30303| 127.0.0.1:8548/:20203 | Agency D | Group 3 |
|   Node 4     | 127.0.0.1:30304| 127.0.0.1:8549/:20204 | Agency E | Group 1 |

![](../../images/enterprise/sample_star_3.png)

The process is as below:

#### Agency acquires qualification for certidicate

1. Agency E generates `agency.key` locally.

2. Agency applies for networking qualification from the third-party authority, who will issue `agency.crt` according to the root certificate of group 1 `ca.crt`.

#### Agency E generates node certificate

Agency E generates the node certificate cert_127.0.0.1_30304.crt using its private key and agency certificate.

### Agency E generates the configuration file for node expansion in group 1.

Since group 1 has been formed, the networking process will be as below:

1. Agency E applies for group 1 configuration file and the Genesis Block `group.1.genesis` from Agency A or B.

2. Agency E places the Node 4 certificate, fisco-bcos executable program and group 1 Genesis Block `group.1.genesis` under meta folder.

3. Agency E modifies information in `node_deployment.ini` and configures Node 4.

4. 机构E使用[build_install_package](](./operation.html#build-install-package-b)命令生成节点4配置文件夹，启动节点

5. 机构E向机构A或B发送节点4入网请求，等待机构A或B使用控制台或SDK发送节点入网交易，注册节点4入网。

至此，完成机构E扩容节点加入group1的操作。

```eval_rst
.. note::

    按照一般业务分析，可以将场景分为以下三种，分别是以下三种场景。
```

## 起始组网

A、B、C三个对等机构需要沟通搭建一条链，开始时只需要一个账本，需要生成节点配置文件，启动节点，初始化一个业务。操作步骤如下：

1. A、B、C三个机构采用链下安全的方式共享自己的节点证书与节点信息（此步可选择由某一机构统一收集，或是所有机构都收集）
2. 假设A收集所有资料后，将证书按照指定格式放在meta目录下，并配置`group_genesis.ini`中的node信息，指定配置文件中的groupid，生成群组创世区块，发送给B、C
3. A、B、C机构修改`node_deployment.ini`的`node`信息，执行[build_install_package](./operation.html#build-install-package-b)命令，指定生成节点配置文件的目录，会在指定目录下生成多个不含节点私钥的节点配置文件夹
4. 各个机构并将自己的节点配置文件推送至对应服务器，拷贝节点私钥至节点配置文件下，启动节点

## 新节点加入现有group

在本场景中，节点需要与所有原有节点进行网络连接，并且需要加入现有群组，以上述`起始组网`为例，操作步骤如下：
假设D机构需要新加入节点到A、B、C的组网的group1中

1. D机构收集A、B、C组网生成的群组创世区块`group.1.genesis`，放置于meta文件夹下
2. D机构配置`node_deployment.ini`中的`node`信息，执行信息，将扩容节点的证书按照指定格式放在meta目录下
3. D机构使用[build_install_package](](./operation.html#build-install-package-b)命令指定扩容节点配置文件节点输出路径，在输出路径生成扩容节点节点配置文件夹
4. D机构将私钥导入扩容节点配置文件夹，将节点配置文件夹推送至指定服务器，启动节点
5. D机构请求A、B、C中的某一个机构将自己的节点注册入group1中，完成新节点入网操作

## 节点新建群组

在本场景中，链中的已有节点需要建立新群组。

假设节点均已建立为网络链接，需要建立新群组，如上述场景中A、B、C、D要新建立group3，则需要进行的操作如下：

1. A、B、C、D四个机构采用链下安全的方式共享自己的节点证书与节点信息（此步可选择由某一机构统一收集，或是所有机构都收集）
2. A配置`group_genesis.ini`中的信息，将1中协商的证书放置与meta文件夹下
3. A执行[create_group_genesis](./operation.html#create-group-config-c)命令，生成`group.3.genesis`和`group.3.ini`，将生成的配置信息发送给B、C、D
4. A、B、C、D将生成`group.3.genesis`和`group.3.ini`拷贝到已存在的节点文件夹下，从启节点完成新群组的建立

## 组网模式分析

针对上述场景，FISCO BCOS generator将其总结为以下四种组网模式。

设4个节点的地址为，由机构A生成群组创世区块：

| 节点序号 |   P2P地址     |   RPC/channel地址     | 所属机构 |
| :-----------: | :-------------: | :-------------: | :-------------: |
|   节点0     | 127.0.0.1:30300| 127.0.0.1:8545/:20200 | 机构A |
|   节点1     | 127.0.0.1:30301| 127.0.0.1:8546/:20201 | 机构A |
|   节点2     | 127.0.0.1:30302| 127.0.0.1:8547/:20202 | 机构B |
|   节点3     | 127.0.0.1:30303| 127.0.0.1:8548/:20203 | 机构C |

### 简单模式组网

![](../../images/enterprise/simple.png)

简单模式组网可以适用于大多数单一业务的使用场景。整条链的所有节点都在唯一的一个群组中。

当业务的数据需要由区块链所有节点共识时，一般采用此模式组网

使用示例：

用户操作如下：

1.用户生成证书，机构A收集所有证书

2.机构A修改`group_genesis.ini`如下，生成群组创世区块`group.1.genesis`，发送给机构B、C

```ini
[group]
group_id=1

[nodes]
node0=127.0.0.1:30300
node1=127.0.0.1:30301
node2=127.0.0.1:30302
node3=127.0.0.1:30303
```

3.各个机构修改`node_deployment.ini`中的配置

机构A修改如下：

```ini
[group]
group_id=1

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30300
channel_listen_port=20200
jsonrpc_listen_port=8545

[node1]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30301
channel_listen_port=20201
jsonrpc_listen_port=8546
```

机构B修改如下：

```ini
[group]
group_id=1

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30302
channel_listen_port=20202
jsonrpc_listen_port=8547
```

机构C修改如下：

```ini
[group]
group_id=1

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30303
channel_listen_port=20203
jsonrpc_listen_port=8548
```

3.使用[build_install_package](./operation.html#build-install-package-b)命令生成节点配置文件

4.启动节点

### 网状模式组网

![](../../images/enterprise/net.png)

网状模式组网适合有多个业务的场景，如一条链的不同节点需要有多个需要共识的业务账本。当业务没有交叉时，彼此之间的账本是独立的，可以采用此模式组网。

使用示例：

用户操作如下：

1.用户生成证书，机构A收集所有证书

2.机构A修改`group_genesis.ini`如下，生成群组创世区块`group.1.genesis`，发送给机构B

3.机构A修改`group_genesis.ini`如下，生成群组创世区块`group.2.genesis`，发送给机构B、C

4.各机构修改`node_deployment.ini`中的配置

机构A修改如下：

```ini
[group]
group_id=1

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30300
channel_listen_port=20200
jsonrpc_listen_port=8545

[node1]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30301
channel_listen_port=20201
jsonrpc_listen_port=8546
```

机构B修改如下：

```ini
[group]
group_id=1

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30302
channel_listen_port=20202
jsonrpc_listen_port=8547
```

机构C修改如下：

```ini
[group]
group_id=2

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30303
channel_listen_port=20203
jsonrpc_listen_port=8548
```

5.各机构使用[build_install_package](./operation.html#build-install-package-b)命令生成节点配置文件夹，机构A将group.2.genesis导入节点0的conf文件夹，机构B将group.2.genesis导入节点2的conf文件夹

6.启动节点

### 星型模式组网

![](../../images/enterprise/star.png)

星型模式组网适合某一机构与其他机构有较多的交叉，但是其他机构之间通信较少的业务模式。

在这种模式中，节点0需要与其他所有节点交互，而节点1、2、3只需关心与节点0的交互即可

使用示例：

用户操作如下：

1.所有机构生成证书、机构A收集证书

2.机构A修改`group_genesis.ini`如下所示：

```ini
[group]
group_id=1

[nodes]
node0=127.0.0.1:30300
node1=127.0.0.1:30301
```

```ini
[group]
group_id=2

[nodes]
node0=127.0.0.1:30300
node1=127.0.0.1:30302
```

```ini
[group]
group_id=3

[nodes]
node0=127.0.0.1:30300
node1=127.0.0.1:30303
```

生成`group.1.genesis`用于本机构组网，`group.2.genesis`发送给机构B，`group.3.genesis`发送给机构C。

3.各机构分别修改`node_deployment.ini`中的配置如下所示

机构A：

```ini
[group]
group_id=1

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30300
channel_listen_port=20200
jsonrpc_listen_port=8545

[node1]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30301
channel_listen_port=20201
jsonrpc_listen_port=8546
```

机构B：

```ini
[group]
group_id=2

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30302
channel_listen_port=20202
jsonrpc_listen_port=8547
```

机构C：

```ini
[group]
group_id=2

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30303
channel_listen_port=20203
jsonrpc_listen_port=8548
```

各机构通过上述配置文件，使用[build_install_package](./operation.html#build-install-package-b)命令生成节点配置文件夹

4.机构A还需将`group.2.genesis`，`group.2.ini`，`group.3.genesis`,`group.2.ini`放置于节点0的conf目录下，启动节点

### 隔离模式组网

![](../../images/enterprise/ring.png)

隔离模式组网适合多个机构之间经常有业务账本隔离需求的场景。

使用示例：

用户操作如下：

1.各机构生成证书，机构A收集证书

2.机构A分别修改`group_genesis.ini`如下所示：

```ini
#群组1配置文件
[group]
group_id=1

[nodes]
node0=127.0.0.1:30300
node2=127.0.0.1:30302
# 群组2配置文件
[group]
group_id=2

[nodes]
node0=127.0.0.1:30301
node2=127.0.0.1:30303
```

3.各机构修改`node_deployment.ini`中的配置如下：

机构A：

```ini
[group]
group_id=1

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30300
channel_listen_port=20200
jsonrpc_listen_port=8545

[node1]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30301
channel_listen_port=20201
jsonrpc_listen_port=8546
```

机构B：

```ini
[group]
group_id=1

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30302
channel_listen_port=20202
jsonrpc_listen_port=8547
```

机构C：

```ini
[group]
group_id=2

# Owned nodes
[node0]
p2p_ip=127.0.0.1
rpc_ip=127.0.0.1
p2p_listen_port=30303
channel_listen_port=20203
jsonrpc_listen_port=8548
```

4.机构使用[build_install_package](./operation.html#build-install-package-b)命令生成节点配置文件夹

5.机构A将节点1中节点创世区块替换为`group.2.genesis`，`group.2.ini`，启动节点

