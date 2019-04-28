# Operation Tutorial

## Configuration file folder: conf

The configuration files of FISCO BCOS generator is placed under ./conf folder, including Genesis Block configuration file `group_genesis.ini` and node configuration file `node_deployment.ini`.

By modifying the configuration files under conf folder, users configure the specific information of generated node.

### Metadata file folder: meta

The meta folder of FISCO BCOS generator is placed with metadata files, including binary file `fisco bcos`, chain certificate `ca.crt`, agency certificate`agency.crt`, private key certificate and Genesis Block file, etc..

The format of certificates should be cert_p2pip_port.crt. For example, cert_127.0.0.1_30300.crt.

FISCO BCOS generator will generate a node configuration file folder, according to the certificates and configuration files user places under meta folder or conf folder.

### group_genesis.ini

Through modifying the configuration of `group_genesis.ini`, user generates the configuration file for new Genesis Block under assigned section and meta folder. For example, `group.1.genesis`.

```ini
[group]
group_id=1

[nodes]
node0=127.0.0.1:30300
;the node p2p ip of Genesis Block
node1=127.0.0.1:30301
node2=127.0.0.1:30302
node3=127.0.0.1:30303
```

```eval_rst
.. important::
    The creation of Genesis Block needs node certificates. In the above example, it needs certificates of 4 nodes, including cert_127.0.0.1_30301.crt，cert_127.0.0.1_30302.crt，cert_127.0.0.1_30303.crt和cert_127.0.0.1_30304.crt.
```

### node_deployment.ini

Through modifying the configuration of `node_deployment.ini`, user can generate node configuration file folder under assigned section that contains no private key using --build_install_package command. `section[node]` is the node configuration file folder generated and configured by user. `section[peers]` contains the p2p information of the connected peer nodes.

Example of configuration file:

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

In the above example, user will generates a node configuration file folder named node_127.0.0.1_30300、node_127.0.0.1_30301 after executing build command. The generated node will belong to Group 1.

```eval_rst
.. note::
    The generation of node configuration file folder needs the node certificate only. In the above example, the certificate will be cert_127.0.0.1_30300.crt和cert_127.0.0.1_30301.crt
```

## Command in detail

FISCO BCOS generator provides multiple functions of node generation, expansion, group division and certificate. Here is a brief introduction:

| Command | Basic function |
| :-: | :-: |
| create_group_genesis | assign folder | generate group Genesis Block in assigned folder according to group_genesis.ini and the certificates in meta folder |
| build_install_package | generate <br> node configuration file folder of node_deployment.ini in assigned folder (node certificate should be placed in meta folder) |
| generate_all_certificate | generate node certificate and private key according to node_deployment.ini |
| generate_*_certificates | generate chain, agency, node, sdk certificate or private key |
| merge_config | merge p2p parts of the two configuration files |
| deploy_private_key | import private keys in batch to the generated node configuration file folder |
| add_peers | import node connection files in batch to node configuration file folder |
| add_group | import Genesis Blocks in batch to node configuration file folder |
| version | print current version code |
| h/help | help command |

### create_group_genesis (-c)

|  |  |
| :-: | :-: |
| Command description | generate group Genesis Block |
| Premise of use | user needs to configure `group_genesis.ini` and node certificates under meta folder.  |
| Parameter setting | assign and generate node configuration file folder for group |
| Function | generate configuration of new group under assigned section according to the certificates user configured under meta folder |
| Adaptable scenario | the existed nodes on consortium chain needs to form new group |

Operation Tutorial

```bash
$ cp node0/node.crt ./meta/cert_127.0.0.1_3030n.crt
...
$ vim ./conf/group_genesis.ini
$ ./generator --create_group_genesis ~/mydata
```

After the program is executed, `group.i.genesis` of mgroup.ini will be generated under ~/mydata folder

The generated `group.i.genesis` is the Genesis Block of the new group.

```eval_rst
.. note::
    In FISCO BCOS 2.0, each group has one Genesis Block.
```

### build_install_package (-b)

|  |  |
| :-: | :-: |
| Command description | deploy new node and new group |
| Premise of use | user needs to configure `node_deployment.ini` and assign p2p connection file  |
| Parameter setting | assign file folder for the location of configuration file folder |
| Function | generate node configuration file folder according to Genesis Block, node certificate and node information |
| Adaptable scenario | generate node configuration file folder |

Operation Tutorial

```bash
$ vim ./conf/node_deployment.ini
$ ./generator --build_install_package ./peers.txt ~/mydata
```

After the program is executed, a few file folders named node_hostip_port will be generated under ~/mydata folder and pushed to the relative server to activate node.

### generate_chain_certificate

|  |  |
| :-: | :-: |
| Command description | generate root certificate |
| Premise of use | no |
| Parameter setting | assign file folder for root certificate |
| Function | generate root certificate and private key under assigned section |
| Adaptable scenario | user needs to generate self-sign root certificate |

```bash
$ ./generator --generate_chain_certificate ./dir_chain_ca
```

Now, user can find root certificate `ca.crt` and private key `ca.key` under ./dir_chain_ca folder.

### generate_agency_certificate

|  |  |
| :-: | :-: |
| Command description | generate agency certificate |
| Premise of use | root certificate and private key have been created |
| Parameter setting | assign section for agency certificate, chain certificate and private key and create agency name |
| Function | generate agency certificate and private key under assigned secton |
| Adaptable scenario | user needs to generate self-sign agency certificate |

```bash
$ ./generator --generate_agency_certificate ./dir_agency_ca ./chain_ca_dir The_Agency_Name
```

Now, user can locate The_Agency_Name folder containing agency certificate `agency.crt` and private key `agency.key` through route ./dir_agency_ca.

### generate_node_certificate

|  |  |
| :-: | :-: |
| Command description | generate node certificate |
| Premise of use | agency certificate and private key have been created |
| Parameter setting | assign section for node certificate, agency certificate and private key and create node name |
| Function | generate node certificate and private key under assigned secton |
| Adaptable scenario | user need to generate self-sign node certificate |

```bash
$ ./generator --generate_node_certificate node_dir(SET) ./agency_dir  node_p2pip_port
```

Then user can locate node certificate `node.crt` and private key `node.key` through route node_dir.

### generate_sdk_certificate

|  |  |
| :-: | :-: |
| Command description | generate SDK certificate |
| Premise of use | agency certificate and private key have been created |
| Parameter setting | assign section for node certificate, agency certificate and private key and create node name |
| Function | generate SDK certificate and privte key under assigned section |
| Adaptable senario | user needs to generate self-sign SDK certificate |

```bash
$ ./generator --generate_sdk_certificate ./dir_sdk_ca ./dir_agency_ca
```

Now user can locate SDK file folder containing SDK certificate `node.crt` and private key `node.key` through route ./dir_sdk_ca.

### generate_all_certificates

|  |  |
| :-: | :-: |
| Command descrition | generate certificates and private key according to node_deployment.ini |
| Premise of use | no |
| Parameter setting | assign section for node certificate |
| Function | generate private key and node certificate under meta folder, generate node certificate for exchanging and p2p connection file under assigned section according to node_deployment.ini |
| Adaptable scenario | user needs to exchange data with other nodes |

```
$ ./generator --generate_all_certificates ./cert
```

```eval_rst
.. note::
    the above command will create node certificate according to ca.crt, agency certificate agency.crt and agency private key agency.key of meta folder.

    - absence of any of the above 3 files will fail the creation of node certificate, and the program will throw an exception

```

Once the execution is done, user can find node certificate and private key under ./cert folder, and node certificate under ./meta folder.

### merge_config (-m)

--merge_config command can merge p2p sections of 2 config.ini

For instance, the p2p section in config.ini of A is:

```ini
[p2p]
listen_ip = 127.0.0.1
listen_port = 30300
node.0 = 127.0.0.1:30300
node.1 = 127.0.0.1:30301
node.2 = 127.0.0.1:30302
node.3 = 127.0.0.1:30303
```

the p2p section in config.ini of B is:

```ini
[p2p]
listen_ip = 127.0.0.1
listen_port = 30303
node.0 = 127.0.0.1:30300
node.1 = 127.0.0.1:30303
node.2 = 192.167.1.1:30300
node.3 = 192.167.1.1:30301
```
the command will result in:

```ini
[p2p]
listen_ip = 127.0.0.1
listen_port = 30304
node.0 = 127.0.0.1:30300
node.1 = 127.0.0.1:30301
node.2 = 192.167.1.1:30302
node.3 = 192.167.1.1:30303
node.4 = 192.167.1.1:30300
node.5 = 192.167.1.1:30301
```

User Case

```bash
$ ./generator --merge_config ~/mydata/node_A/config.ini  ~/mydata/node_B/config.ini
```

使用成功后会将node_A和node_B的config.ini中p2p section合并与 ~/mydata/node_B/config.ini的文件中

### deploy_private_key (-d)

使用--deploy_private_key可以将路径下名称相同的节点私钥导入到生成好的配置文件夹中。

使用示例:

```bash
$./generator --deploy_private_key ./cert ./data
```

如./cert下有名为node_127.0.0.1_30300，node_127.0.0.1_30301的文件夹，文件夹中有节点私钥文件node.key

./data下有名为node_127.0.0.1_30300，node_127.0.0.1_30301的配置文件夹

执行完成后可以将./cert下的对应的节点私钥导入./data的配置文件夹中

### add_peers (-p)

使用--add_peers可以指定的peers文件导入到生成好的节点配置文件夹中。

使用示例:

```bash
$./generator --add_peers ./meta/peers.txt ./data
```

./data下有名为node_127.0.0.1_30300，node_127.0.0.1_30301的配置文件夹

执行完成后可以将peers文件中的连接信息导入./data下所有节点的配置文件`config.ini`中

### add_group (-a)

使用--add_group可以指定的peers文件导入到生成好的节点配置文件夹中。

使用示例:

```bash
$./generator --add_group ./meta/group.2.genesis ./data
```

./data下有名为node_127.0.0.1_30300，node_127.0.0.1_30301的配置文件夹

执行完成后可以将群组2的连接信息导入./data下所有节点的`conf`文件夹中

### download_fisco

使用--download_fisco可以指定的目录下下载`fisco-bcos`二进制文件。

使用示例:

```bash
$./generator --download_fisco ./meta
```

执行完成后会在./meta文件夹下下载`fisco-bcos`可执行二进制文件

### download_console

使用--download_console可以指定的目录下下载并配置控制台。

使用示例:

```bash
$./generator --download_console ./meta
```

执行完成后会在./meta文件夹下根据`node_deployment.ini`完成对控制台的配置

### get_sdk_file

使用--get_sdk_file可以指定的目录下下获取控制台和sdk配置所需要的`node.crt`、`node.key`、`ca.crt`及`applicationContext.xml`。

使用示例:

```bash
$./generator --get_sdk_file ./sdk
```

执行完成后会在./sdk文件夹下根据`node_deployment.ini`生成上述配置文件

### version (-v)

使用--version命令查看当前部署工具的版本号。

```bash
$ ./generator --version
```

### help (-h)

用户可以使用-h或--help命令查看帮助菜单

使用示例：

```bash
$ ./generator -h
usage: generator [-h] [-v] [-b peer_path data_dir] [-c data_dir]
                 [--generate_chain_certificate chain_dir]
                 [--generate_agency_certificate agency_dir chain_dir agency_name]
                 [--generate_node_certificate node_dir agency_dir node_name]
                 [--generate_sdk_certificate sdk_dir agency_dir] [-g]
                 [--generate_all_certificates cert_dir] [-d cert_dir pkg_dir]
                 [-m config.ini config.ini] [-p peers config.ini]
                 [-a group genesis config.ini]
```

## 国密操作相关

FISCO BCOS generator的所有命令同时支持国密版`fisco-bcos`，使用时，国密证书、私钥均加以前缀`gm`。基本使用解释如下

### 国密开关 (-g)

国密开关-g打开时，生成证书、节点、群组创世区块的操作会相应生成国密版的上述文件。

### 生成证书操作

如generate_*_certificate操作时，配合-g命令会生成相应的国密证书。

操作示例：

```
$ ./generator --generate_all_certificates ./cert -g
```

```eval_rst
.. note::
    上述命令会根据meta目录下存放的gmca.crt、机构证书gmagency.crt和机构私钥gmagency.key生成相应的节点证书。

    - 如果用户缺少上述三个文件，则无法生成节点证书，程序会抛出异常。
```

### 生成国密群组创世区块

操作示例

```bash
$ cp node0/gmnode.crt ./meta/gmcert_127.0.0.1_3030n.crt
...
$ vim ./conf/group_genesis.ini
$ ./generator --create_group_genesis ~/mydata -g
```

程序执行完成后，会在~/mydata文件夹下生成mgroup.ini中配置的`group.i.genesis`

用户生成的`group.i.genesis`即为群组的创世区块，即可完成新群组划分操作。

### 生成国密节点配置文件夹

操作示例

```bash
$ vim ./conf/node_deployment.ini
$ ./generator --build_install_package ./peers.txt ~/mydata -g
```

程序执行完成后，会在~/mydata文件夹下生成多个名为node_hostip_port的文件夹，推送到对应服务器后即可启动节点

## 监控设计

FISCO BCOS generator 生成的节点配置文件夹中提供了内置的监控脚本，用户可以通过对其进行配置，将节点的告警信息发送至指定地址。FISCO BCOS generator会将monitor脚本放置于生成节点配置文件的指定目录下，假设用户指定生成的文件夹名为data，则monitor脚本会在data目录下的monitor文件夹下

使用方式如下：

```
$ cd ./data/monitor
```

用途如下：

1. 监控节点是否存活, 并且可以重新启动挂掉的节点.
2. 获取节点的块高和view信息, 判断节点共识是否正常.
3. 分析最近一分钟的节点日志打印, 收集日志关键错误打印信息, 准实时判断节点的状态.
4. 指定日志文件或者指定时间段, 分析节点的共识消息处理, 出块, 交易数量等信息, 判断节点的健康度. 

### 配置告警服务

用户使用前，首先需要配置告警信息服务，这里以[server酱](http://sc.ftqq.com/3.version)的微信推送为例，可以参考配置[server酱](http://sc.ftqq.com/3.version)

绑定自己的github账号，以及微信后，可以使用本脚本向微信发送告警信息，使用本脚本的-s命令 可以向指定微信发送告警信息

如果用户希望使用其他服务，可以修改monitor.sh中的alarm() {
    # change http server  
}函数，个性化配置为自己需要的服务

### help命令

使用help命令查看脚本使用方式

```bash
$ ./monitor.sh -h
Usage : bash monitor.sh
   -s : send alert to your address
   -m : monitor, statistics.  default : monitor .
   -f : log file to be analyzed.
   -o : dirpath
   -p : name of the monitored program , default is fisco-bcos
   -g : specified the group list to be analized
   -d : log analyze time range. default : 10(min), it should not bigger than max value : 60(min).
   -r : setting alert receiver
   -h : help.
 example :
   bash  monitor.sh -s YourHttpAddr -o nodes -r your_name
   bash  monitor.sh -s YourHttpAddr -m statistics -o nodes -r your_name
   bash  monitor.sh -s YourHttpAddr -m statistics -f node0/log/log_2019021314.log -g 1 2 -r your_name
```

命令解释如下：

- -s 指定告警配置地址，可以配置为告警上报服务的ip
- -m 设定监控模式，可以配置为statistics和monitor两种模式，默认为monitor模式。
- -f 分析节点log
- -o 指定节点路径
- -p 设定监控上报名称，默认为fisco-bcos
- -g 指定监控群组，默认分析所有群组
- -d log分析时间范围，默认10分钟内的log，最大不超过60分钟
- -r 指定上报接收者名称
- -h 帮助命令

### 使用示例

- 使用脚本监控指定路径下节点，发送给接收者Alice：

```bash
$ bash monitor.sh -s https://sc.ftqq.com/[SCKEY(登入后可见)].send -o alice/nodes -r Alice
```

- 使用脚本统计指定路径下节点信息，发送给接收者Alice

```bash
$ bash monitor.sh -s https://sc.ftqq.com/[SCKEY(登入后可见)].send -m statistics -o alice/nodes -r Alice
```

- 使用脚本统计指定路径下节点指定log指定群组1和群组2的信息，发送给接收者Alice

```bash
$ bash monitor.sh -s https://sc.ftqq.com/[SCKEY(登入后可见)].send -m statistics -f node0/log/log_2019021314.log -g 1 2 -o alice/nodes -r Alice
```
