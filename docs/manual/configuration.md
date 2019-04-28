# Configuration files and configuration items

FISCO BCOS supports multiple ledger. Each chain includes multiple unique ledgers, whose data among them are isolated from each other. And the transaction processing among groups are alos isolated. Each node includes a main configuration `config.ini` and multiple ledger configurations `group.group_id.genesis`, `group.group_id.ini`.

- `config.ini`: main configurations file. It mainly configures information such as RPC, P2P, SSL certificate, and ledger configuration file path.

- `group.group_id.genesis`：group configurations file. All nodes in the group are consistent. After node launches, you cannot manually change the configuration including items like group consensus algorithm, storage type, and maximum gas limit, etc.
- `group.group_id.ini`：group variable configuration file, including the transaction pool size, etc.. All configuration changes are effective after node restarts.


## Hardware requirements

```eval_rst
.. note::
    Since multiple nodes share network bandwidth, CPU, and memory resources, it is not recommended to configure too much nodes on one machine in order to ensure the stability of service.

```

The following table is a recommended configuration for single-group and single-node. Node consumes resources in a linear relationship with the number of groups. You can configure the number of nodes reasonably according to actual business requirement and machine resource.


```eval_rst
+----------+---------+---------------------------------------------+
| configuration     | minimum | recommended                                    |
+==========+=========+=============================================+
| CPU      | 1.5GHz  | 2.4GHz                                      |
+----------+---------+---------------------------------------------+
| memory     | 1GB     | 8GB                                         |
+----------+---------+---------------------------------------------+
| core     | 1 core     | 4 cores                                         |
+----------+---------+---------------------------------------------+
| bandwidth     | 1Mb     | 10Mb                                        |
+----------+---------+---------------------------------------------+
```

## Main configuration file config.ini

`config.ini` uses `ini` format. It mainly includes the configuration items like ** rpc, p2p, group, secure and log **. 


```eval_rst
.. important::
    - The public IP addresses of the cloud host are virtual IP addresses. If listen_ip is filled in external network IP address, the binding fails. You must fill in 0.0.0.0.

    - RPC/P2P/Channel listening port must be in the range of 1024-65535 and cannot conflict with other application listening ports on the machine.

```

### Configure RPC

- `listen_ip`: For security reasons, the chain building script will listen to 127.0.0.1 by default. If you need to access the RPC or use SDK through external network, please listen to **external network IP address of node** or `0.0.0.0`;
- `channel_listen_port`: Channel port, is corresponding to `channel_listen_port` in [Web3SDK](../sdk/sdk.html#id2) configuration;
- `jsonrpc_listen_port`: JSON-RPC port.

RPC configuration example is as follows:

```ini
[rpc]
    listen_ip=127.0.0.1
    channel_listen_port=30301
    jsonrpc_listen_port=30302
```

### Configure P2P

The current version of FISCO BCOS must be configured with `IP` and `Port` of the connection node in the `config.ini` configuration. The P2P related configurations include:

-`listen_ip`: P2P listens for IP, to set `0.0.0.0` by default.
-`listen_port`: Node P2P listening port.
-`node.*`: All nodes `IP:port` which need to be connected to node.

P2P configuration example is as follows:

```ini
[p2p]
    listen_ip=0.0.0.0
    listen_port=30300
    node.0=127.0.0.1:30300
    node.1=127.0.0.1:30304
    node.2=127.0.0.1:30308
    node.3=127.0.0.1:30312
```

### Configure ledger file path

`[group]`To configure all group configuration paths which this node belongs:

- `group_data_path`: Group data storage path.
- `group_config_path`: Group configuration file path.

> Node launches group according to all `.genesis` suffix files in the `group_config_path` path.

```ini
[group]
    ; All group data is placed in the node's data subdirectory
    group_data_path=data/
    ; Program automatically loads all .genesis files in the path
    group_config_path=conf/
```


### Configure certificate information

For security reasons, communication among FISCO BCOS nodes uses SSL encrypted communication.`[secure]` configure to SSL connection certificate information:

- `data_path`：Directory where the certificate and private key file are located.
- `key`: The `data_path` path that node private key relative to.
- `cert`: The `data_path` path that certificate `node.crt` relative to.
- `ca_cert`: ca certificate file path.
- `ca_path`: ca certificate folder, required for multiple ca.

```ini
[secure]
    data_path=conf/
    key=node.key
    cert=node.crt
    ca_cert=ca.crt
    ca_path=
```

### Configure blacklist

For preventing vice, FISCO BCOS allows nodes to configure untrusted node blacklist to reject establishing connections with these blacklist nodes. To configure blacklist through `[crl]`:

> `crl.idx`: Blacklist node's Node ID, can get from `node.nodeid` file; `idx` is index of the blacklist node.

For details of the blacklist, refer to [CA Blacklist].(./certificate_blacklist.md)

Blacklist configuration example is as follows:

```ini
; certificate blacklist
[crl]
    crl.0=4d9752efbb1de1253d1d463a934d34230398e787b3112805728525ed5b9d2ba29e4ad92c6fcde5156ede8baa5aca372a209f94dc8f283c8a4fa63e
3787c338a4
```

### Configure log information

FISCO BCOS supports light weight [easylogging++](https://github.com/zuhd-org/easyloggingpp) and powerful[boostlog](https://www.boost.org/doc/libs/1_63_0/libs/log/doc/html/index.html). It can use these two logs through compiling switch configuration, and it uses boostlog by default.  For details, refer to the [Log Operation Manual] (log_access.md).

- `log_path`:log file patch.
- `level`: log level, currently includes 5 levels which are `trace`、`debug`、`info`、`warning`、`error`.After setting a certain log level, the log file will be entered with a log equal to or larger than this level.The log level is sorted from large to small by `error > warning > info > debug > trace`.
- `max_log_file_size`：Maximum size per log file, ** unit of measure is bytes, default is 200MB**
- `flush`：boostlog enables log auto-refresh by default. To improve system performance, it is recommended to set this value to false.

boostlog configuration example is as follows:

```ini
[log]
    log_path=./log
    level=info
    ; Maximum size per log file, default is 200MB
    max_log_file_size=209715200
    flush=true
```

#### Configure easylogging++

In order to minimize the configuration file, FISCO BCOS concentrates the configuration information of easyloggin++ to `[log]` configuration of config.ini. In general, it is not recommended to manually change the configurations other than the log level. For launching easylogging++ method, refer to [Enable easylogging++](log.html#easylogging).

- `format`：log format.
- `log_flush_threshold`：log refresh frequency setting. Each `log_flush_threshold` line refreshes log to file once.

easylogging++ configuration example is as follows:

```ini
[log]
    log_path=./log
    level=info
    max_log_file_size=209715200
    ; easylog configuration
    format=%level|%datetime{%Y-%M-%d %H:%m:%s:%g}|%msg
    log_flush_threshold=100
```

### Optional configuration: Disk encryption 
In order to protect node data, FISCO BCOS introduces [Disk Encryption](../design/features/storage_security.md) to ensure confidentiality. **Disk Encryption** Operation Manual [Reference](./storage_security.md).

`data_secure` in `config.ini` is used to configure disk encryption. It mainly includes (for the operation of the disk encryption, please refer to [Operation Manual](./storage_security.md)):


- `enable`：whether to launch disk encryption, not to launch by default;

- `key_manager_ip`：[Key Manager](https://github.com/FISCO-BCOS/key-manager)service's deployment IP;

- `key_manager_port`：[Key Manager](https://github.com/FISCO-BCOS/key-manager)service's listening port；

- `cipher_data_key`: ciphertext of node data encryption key. For `cipher_data_key` generation,refer to [disk encryption operation manual](./storage_security.md).


disk encrption configuration example is as follows:

```ini
[data_secure]
enable=true
key_manager_ip=127.0.0.1
key_manager_port=31443
cipher_data_key=ed157f4588b86d61a2e1745efe71e6ea
```

## Group system configuration instruction

Each group has unique separate configuration file, which can be divided into **group system configuration** and **group variable configuration** according to whether it can be changed after launch.
group system configuration is generally located in the `.genesis` suffix configuration file in node's `conf` directory.

For example:`group1` system configuration generally names as `group.1.genesis`. Group system configuration mainly includes the related configuration of **group ID、consensus, storage and gas**.

```eval_rst
.. important::
    When configuring the system configuration, you need to pay attention to:
        
    - **configuration group must be consistent**: group system configuration is used to generate the genesis block (block 0), so the configurations of all nodes in the group must be consistent.
    
    - **node cannot be modified after launching** ：system configuration has been written to the system table as genesis block, so it cannot be modified after chain initializes.

    - After chain is initialized, even if genesis configuration is modified, new configuration will not take effect, and system still uses the genesis configuration when initializing the chain.

    - Since genesis configuration requires all nodes in the group to be consistent, it is recommended to use `build_chain <build_chain.html>`_ to generate the configuration.
```

### Group configuration

`[group]`configurates **group ID**. Node initializes the group according to the group ID.

group2's configuration example is as follows:

```ini
[group]
id=2
```

### Consensus configuration

`[consensus]` involves consensus-related configuration, including:

- `consensus_type`：consensus algorithm type, currently supports [PBFT](../design/consensus/pbft.md) and [Raft](../design/consensus/raft.md). To use PBFT by default;

- `max_trans_num`：a maximum number of transactions that can be packed in a block. The default is 1000. After the chain is initialized, the parameter can be dynamically adjusted through [Console](./console.html#setsystemconfigbykey);

- `node.idx`：consensus node list, has configured with the [Node ID] of the participating consensus nodes. The Node ID can be obtained by the `${data_path}/node.nodeid` file (where `${data_path}` can be obtained by the configuration item `[secure].data_path` of the main configuration `config.ini`)


```ini
; Consensus protocol configuration
[consensus]
    ;consensus algorithm, currently supports PBFT(consensus_type=pbft) and Raft(consensus_type=raft)
    consensus_type=pbft
    ; maximum number of transactions in a block
    max_trans_num=1000
    ; leader node's ID lists
    node.0=123d24a998b54b31f7602972b83d899b5176add03369395e53a5f60c303acb719ec0718ef1ed51feb7e9cf4836f266553df44a1cae5651bc6ddf50
e01789233a
    node.1=70ee8e4bf85eccda9529a8daf5689410ff771ec72fc4322c431d67689efbd6fbd474cb7dc7435f63fa592b98f22b13b2ad3fb416d136878369eb41
3494db8776
    node.2=7a056eb611a43bae685efd86d4841bc65aefafbf20d8c8f6028031d67af27c36c5767c9c79cff201769ed80ff220b96953da63f92ae83554962dc2
922aa0ef50
    node.3=fd6e0bfe509078e273c0b3e23639374f0552b512c2bea1b2d3743012b7fed8a9dec7b47c57090fa6dcc5341922c32b89611eb9d967dba5f5d07be7
4a5aed2b4a
```

### Storage module configuration

Storage mainly includes [state](../design/storage/mpt.html) and [AMDB](../design/storage/storage.html). `state` involves transaction state storage. `AMDB` storage involves system tables. They are configured in `[storage]` and `[state]` respectively:

- `[storage].type`：storage's DB type, currently supports LevelDB only, and it will support Mysql in the future;

- `[state].type`：state type, currently supports [storage state](../design/storage/storage.html#id6) and [MPT state](../design/storage/mpt.html), **storage state by default**. Storage state storing transaction execution result in the system table is more efficient. MPT state storing the transaction execution result in the MPT tree is inefficient, but it contains complete historical information.

```eval_rst
.. important::
    **storage state** is recommended. MPT State is not recommended except for special requirements.

```

```ini
[storage]
    ; db type, now support leveldb 
    type=LevelDB
[state]
    type=storage
```

### Gas configuration


FISCO BCOS is compatible with Ethereum virtual machine ([EVM](../design/virtual_machine/evm.md)). In order to prevent DOS from attacking [EVM](../design/virtual_machine/evm.md), EVM introduces the concept of gas when executing transactions, which is used to measure the computing and storage resources consumed during the execution of smart contracts. The meausre includes the maximum gas limit of transaction and block. If the gas consumed by the transaction or block execution exceeds the gas limit, the transaction or block is discarded.


FISCO BCOS is alliance chain that simplifies gas design. **It retains only maximum gas limit of transaction, and maximum gas of block is constrained together by [consensus configuration max_trans_num](./configs.html#id8) and transaction maximum gas limit.**

FISCO BCOS configures maximum gas limit of the transaction through geneis `[tx].gas_limit`. The default value is 300000000. After chain is initialized, the gas limit can be dynamically adjusted through the [console command](./console.html#setsystemconfigbykey).



```ini
[tx]
    gas_limit=300000000
```


## Ledger variable configuration instruction

Variable configuration of the ledger is located in the file of the `.ini` suffix in the node `conf` directory.

For example: `group1` variable configuration is generally named `group.1.ini`. The variable configuration mainly includes the transaction pool size and the TTL of the consensus message forwarding.


### Transaction pool configuration

FISCO BCOS opens the transaction pool capacity configuration to users. Users can dynamically adjust the transaction pool size according to their business size requirements, stability requirements, and node hardware configuration.

Transaction pool configuration example is as follows:

```ini
[tx_pool]
    limit=10000
```

### PBFT consensus message broadcast configuration

In order to ensure the maximum network fault tolerance of the consensus process, each consensus node broadcasts the message to other nodes after receiving a valid consensus message. In smooth network environment, the consensus message forwarding mechanism will waste additional network bandwidth, so the `TTL` is introduced in the group variable configuration item to control the maximum number of message forwarding. The maximum number of message forwarding is `TTL-1`, and **the configuration item is valid only for PBFT**.


Setting consensus message to be forwarded at most once configuration example is as follows:

```ini
; the ttl for broadcasting pbft message
[consensus]
ttl=2
```

## Dynamically configure system parameters

FISCO BCOS system currently includes the following system parameters (other system parameters will be extended in the future):


```eval_rst
+-----------------+-----------+---------------------------------+
| system parameters        | default value    |             definition                |
+=================+===========+=================================+
| tx_count_limit  | 1000      | maximum number of transactions that can be packaged in one block
  |
+-----------------+-----------+---------------------------------+
| tx_gas_limit    | 300000000 | Maximum block limit for a block            |
+-----------------+-----------+---------------------------------+

```

Console provides **[setSystemConfigByKey](./console.html#setsystemconfigbykey)** command to modify these system parameters.
**[getSystemConfigByKey](./console.html#getsystemconfigbykey)** command can view the current value of the system parameter:


```eval_rst
.. important::

    It is not recommended to modify tx_count_limit and tx_gas_limit arbitrarily. These parameters can be modified as follows:

    - Hardware performance such as machine network or CPU is limited: to reduce tx_count_limit for reducing business pressure;

    - gas is insufficient when executing blocks for comlicated business logic: increase tx_gas_limit.

```

```bash
# To set the maximum number of transactions of a packaged block to 500

> setSystemConfigByKey tx_count_limit 500
# inquiry tx_count_limit
> getSystemConfigByKey tx_count_limit
[500]

# To set block gas limit as  400000000
> getSystemConfigByKey tx_gas_limit 400000000
> getSystemConfigByKey
[400000000]
```
