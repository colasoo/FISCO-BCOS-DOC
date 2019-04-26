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

#### Agency acquires qualification for certificate

1. Agency E generates `agency.key` locally.

2. Agency applies for networking qualification from the third-party authority, who will issue `agency.crt` according to the root certificate of group 1 `ca.crt`.

#### Agency E generates node certificate

Agency E generates the node certificate cert_127.0.0.1_30304.crt using its private key and agency certificate.

### Agency E generates the configuration file for node expansion in group 1.

Since group 1 has been formed, the networking process will be as below:

1. Agency E applies for group 1 configuration file and Genesis Block `group.1.genesis` from Agency A or B.

2. Agency E places the Node 4 certificate, fisco-bcos executable program and group 1 Genesis Block `group.1.genesis` under meta folder.

3. Agency E modifies information in `node_deployment.ini` and configures Node 4.

4. Agency E generates Node 4 configuration file folder using [build_install_package](](./operation.html#build-install-package-b) instruction and activates node.

5. Agency E sends access request to Agency A or B, who will later start permission process through console or SDK and register Node 4.
Now, Agency E has joined group 1.

```eval_rst
.. note::

    There can be 3 kinds of scenarios according to general cases.
```

## Initial networking

The peer agencies A、B、C negotiate to build a chain. At first they need only one ledger, then generate node configuration file and activate node for initialization. Process is as below:

1. Agencies A、B、C share node certificate and information off chain (The collection work can be done by one or all of the three).
2. Given that A is the collector, A places the certificates under meta section in specified format, configures `group_genesis.ini`, assigns groupid, generates Genesis Block and sends to B and C.
3. Agencies A、B、C modify `node` information in `node_deployment.ini` and execute [build_install_package](./operation.html#build-install-package-b) instruction. The section assigned for node configuration file will be placed with configuration file folders that contain no private key.
4. Each agency sends the node configuration file to its server and copies private key to node configuration file, then activates node.

## Add new node to group

Take the above group as an example, if a new node needs to join the group and network with the existed nodes, the process will be as below:
Given that Agency D wants to join group 1 formed by Agencies A, B, C.

1. Agency D collects the Genesis Block `group.1.genesis` generated by A, B, C, and places it under meta folder.
2. Agency D configures `node` in `node_deployment.ini` and executes it, then places the certificate of the extended node under meta section in specified format.
3. Agency D assigns the output path of extended node configuration file using [build_install_package](](./operation.html#build-install-package-b) instruction, then generates the node configuration file folder through the output path.
4. Agency D imports private key to the extended node configuration file folder and sends the folder to the assigned server, and activates node.
5. Agency D requests one of the three Agencies A, B, C to register its node in group 1 to join the group.

## Form new group

The case is that the nodes on chain want to form a new group. 

If networked nodes needs to form new group, given that the above Agencies A, B, C, D wants to form group 3, they should follow the steps below:

1. Agencies A, B, C, D share node certificate and information off chain (The collection work can be done by one or all of the four).
2. A configures `group_genesis.ini` and places the agreed certificate under meta folder.
3. A executes [create_group_genesis](./operation.html#create-group-config-c) instruction to generate `group.3.genesis` and `group.3.ini`, then sends the configuration information to B, C, D.
4. A, B, C, D generate `group.3.genesis` and `group.3.ini`, copy to the existed folder and reactivate node to form a new group.

## Networking models

In the above scenarios, FISCO BCOS generator concludes 4 networking models.

Given the 4 node each is in following address and Agency A is generator of Genesis Block:

| Node no. |   P2P address     |   RPC/channel address     | Belonged agency |
| :-----------: | :-------------: | :-------------: | :-------------: |
|   Node 0     | 127.0.0.1:30300| 127.0.0.1:8545/:20200 | Agency A |
|   Node 1     | 127.0.0.1:30301| 127.0.0.1:8546/:20201 | Agency A |
|   Node 2     | 127.0.0.1:30302| 127.0.0.1:8547/:20202 | Agency B |
|   Node 3     | 127.0.0.1:30303| 127.0.0.1:8548/:20203 | Agency C |

### Simple model networking

![](../../images/enterprise/simple.png)

Simple model networking can be applied in most single business application. All nodes on chain are in the one-and-only group.

When data needs to be commonly agreed by all nodes on blockchain, this model of networking is preferred.

Demonstration:

User should follow the steps below:

1.User generates certificate. Agency A collects all certificates.
2.Agency A modifies `group_genesis.ini` as below, generates Genesis Block `group.1.genesis` and sends to B, c:

```ini
[group]
group_id=1

[nodes]
node0=127.0.0.1:30300
node1=127.0.0.1:30301
node2=127.0.0.1:30302
node3=127.0.0.1:30303
```

3.Each agency modifies configuration of `node_deployment.ini`

Agency A modifies as below:

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

Agency B modifies as below:

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

Agency C modifies as below:

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

3.Generate node configuration file using [build_install_package](./operation.html#build-install-package-b) instruction

4.Activate node

### Webbed model networking

![](../../images/enterprise/net.png)

Webbed model networking adapts to multiple application scenarios. For example, nodes on a chain needs multiple commonly-agreed ledgers. When the scenarios and the ledgerare are independent, this model of networking is preferred.

Demonstration:

User should follow the steps below:

1.User generates certificate. Agency A collects certificates.

2.Agency A modifies `group_genesis.ini` as below, generates Genesis Block `group.1.genesis` and sends to Agency B.

3.Agency A modifies `group_genesis.ini` as below, generates Genesis Block `group.2.genesis` and sends to Agency B.

4.Each agency modifies the configuration of `node_deployment.ini`.

Agency A modifies as below:

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

Agency B modifies as below:

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

Agency C modifies as below:

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

5.Each agency generates node configuration file folder using [build_install_package](./operation.html#build-install-package-b) instruction. Agency A imports group.2.genesis to Node 0 conf folder. Agency B imports group.2.genesis to Node 2 conf folder.

6.Activate node.

### Star schema networking

![](../../images/enterprise/star.png)

Star schema networking is applied when an agency interacts with other agencies, and there are little communication between other agencies.

In this model, Node 0 interacts with other nodes 1, 2, 3, each of which focus only on the communication with node 0.

Demonstration:

User should follow the steps below:

1.All agencies generates certificates. Agency A collects certificates.

2.Agency A modifies `group_genesis.ini` as below:
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

generates `group.1.genesis` for networking, sends `group.2.genesis` to Agency B and `group.3.genesis` to Agency C.

3.Each agency modifies configuration of `node_deployment.ini` as below:

Agency A:

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

Agency B:

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

Agency C:

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

According to the configuration file above, each agency generates node configuration file folder using [build_install_package](./operation.html#build-install-package-b) command.

4.Agency A needs to place `group.2.genesis`, `group.2.ini`, `group.3.genesis`, `group.2.ini` under node 0 conf section and activate node.

### Isolated model networking

![](../../images/enterprise/ring.png)

Isolated model of networking is applied when agencies need to isolate their ledgers.

Demonstration:

User should follow the steps below:

1.Each agency generates certificate. Agency A collects certificates.

2.Agency A modifies `group_genesis.ini` as below:

```ini
# Group 1 configuration file
[group]
group_id=1

[nodes]
node0=127.0.0.1:30300
node2=127.0.0.1:30302
# Group 2 configuration file
[group]
group_id=2

[nodes]
node0=127.0.0.1:30301
node2=127.0.0.1:30303
```

3.Each agency modifies configuration of `node_deployment.ini` as below:

Agency A:

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

Agency B:

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

Agency C:

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

4.Each agency generates node configuration file folder using [build_install_package](./operation.html#build-install-package-b) command.

5.Agency A replaces the Genesis Block of Node 1 with `group.2.genesis` and `group.2.ini` and activates node.

