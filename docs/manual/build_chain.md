# Chain building script

```eval_rst
.. important::
    The goal of the script is to let users apply FISCO BCOS as quickly as possible. For the enterprise applications deploying FISCO BCOS, please refer to `Enterprise Deployment Tools <../enterprise_tools/index.html>`_ .
```

FISCO BCOS has provided `build_chain` script to help users quickly build FISCO BCOS alliance chain. By default, the script downloads `master` branch of the latest version pre-compiles executable program from [GitHub](https://github.com/FISCO-BCOS/FISCO-BCOS)for building related environment.

## Script Introduction

- `build_chain` script is used to quickly generate configuration files of a chain node. For the script that depends on `openssl`, please according your own operating system to install `openssl 1.0.2` version and above. The source code of script is located at [FISCO-BCOS/tools/build_chain.sh][build_chain].
- For quick experience can use the `-l` option to specify the node IP and number. `-f` option supports the creation of FISCO BCOS chains for complex business scenarios by using a configuration file in a specified format. **`-l` and `-f` options must be specified uniquely and cannot coexist**.
- It is recommended to use `-T` and `-i` options for testing. `-T` enables log level to DEBUG. `-i` sets RPC and channel to listen for `0.0.0.0` while p2p module listens for `0.0.0.0` by default.

## Help

```bash
Usage:
    -l <IP list>                        [Required] "ip1:nodeNum1,ip2:nodeNum2" e.g:"192.168.0.1:2,192.168.0.2:3"
    -f <IP list file>                   [Optional] split by line, every line should be "ip:nodeNum agencyName groupList". eg "127.0.0.1:4 agency1 1,2"
    -e <FISCO-BCOS binary path>         Default download fisco-bcos from GitHub. If set -e, use the binary at the specified location
    -o <Output Dir>                     Default ./nodes/
    -p <Start Port>                     Default 30300,20200,8545 means p2p_port start from 30300, channel_port from 20200, jsonrpc_port from 8545
    -i <Host ip>                        Default 127.0.0.1. If set -i, listen 0.0.0.0
    -c <Consensus Algorithm>            Default PBFT. If set -c, use Raft
    -s <State type>                     Default storage. if set -s, use mpt 
    -g <Generate guomi nodes>           Default no
    -z <Generate tar packet>            Default no
    -t <Cert config file>               Default auto generate
    -T <Enable debug log>               Default off. If set -T, enable debug log
    -h Help
e.g
    build_chain.sh -l "127.0.0.1:4"
```

## Option Introduction

- **`l`option:** 
Use to specify the chain to be generated and the number of nodes under each IP, separated by commas. The script generates configuration file of corresponding node according to the input parameters. The port number of each node is incremented from 30300 by default. All nodes belong to the same organization and Group.

- **`f`option** 
    + Use to generate node according to configuration file. It supports more customization than `l` option.
    + Split by row. Each row represents a server, in the format of `IP:NUM AgencyName GroupList`. Items in each line are separated by spaces, and there must be no blank lines.
    + `IP:NUM` represents the IP address of the machine and the number of nodes on the machine.`AgencyName`represents the name of the institution to specifies the institution certificate to use. `GroupList` represents the group that the generated node belong to, split by`,`. For example, `192.168.0.1:2 agency1 1,2` represents that a machine with `ip` which is `192.168.0.1` exists two nodes. For example, 192.168.0.1:2 agency1 1,2 represents that there are two nodes on the machine with ip 192.168.0.1. These two nodes belong to agency `agency1` and belong to group1 and group2.

The following is an example of a configuration file. Each configuration item separated by a space, where `GroupList` represents the group that the server belongs to.

```bash
192.168.0.1:2 agency1 1,2
192.168.0.1:2 agency1 1,3
192.168.0.2:3 agency2 1
192.168.0.3:5 agency3 2,3
192.168.0.4:2 agency2 3
```

**Suppose the above file is named `ipconf`**, using the following command to build a chain, which indicates to use configuration file, to set the log level to `DEBUG`, and to listen for `0.0.0.0`.

```bash
$ bash build_chain.sh -f ipconf -T -i
```

- **`e`option[**Optional**]**
is used to specify **full path** where `fisco-bcos` binary is located.Script will cope `fisco-bcos` to the directory named by IP number. If no path to be specified, the latest binary program of `master` branch is downloaded from GitHub by default.


```bash
# download the latest release binary from GitHub to generate native 4 nodes
$ bash build_chain.sh -l "127.0.0.1:4"
# use bin/fisco-bcos binary to generate native 4 nodes
$ bash build_chain.sh -l "127.0.0.1:4" -e bin/fisco-bcos 
```

- **`o`option[**Optional**]**
specifies the directory where the generated configuration is located.

- **`p`option[**Optional**]**
specifies the starting port of the node. Each node occupies three ports which are p2p, channel, and jsonrpc, respectively. The ports are split by, and three ports must be specified. The ports used by different nodes under the same IP address are incremented from the starting port.

```bash
# Two nodes occupies `30300,20200,8545` and `30301,20201,8546` respectively.
$ bash build_chain -l 127.0.0.1:2 -p 30300,20200,8545
```
- **`i`option[**Optional**]**
No parameter option. When setting this option, to set the node's RPC and channel for listening to `0.0.0.0`.

- **`c`option[**Optional**]**
No parameter option. When setting this option, to set the node’s consensus algorithm as [Raft](../design/consensus/raft.md), otherwise to set as [PBFT](../design/consensus/pbft.md) by default.

- **`s`option[**Optional**]**
No parameter option. When setting this option, to use [mptstate](../design/storage/mpt.md) to store contract local variables, otherwise to use [storagestate](../design/storage/storage.md) by default.

- **`g`option[**Optional**]**
No parameter option. When setting this option, to set up the state secret version of FISCO BCOS. **The binary fisoc-bcos is required to be a state secret version when using the `g` option**.

- **`z`option[**Optional**]**
No parameter option. When setting this option, to generate the tarball of node.

- **`t`option[**Optional**]**
is used to specify the certificate configuration file when the certificate is generated.

- **`T`option[**Optional**]**
No parameter option. When setting this option, to set the log level of the node to DEBUG. The related configuration of log can refer to [here](log_access.md).

## Node file organization

- cert folder stores root certificate and organization certificate of the chain.
- The folder named by IP address stores the certificate configuration file required by related configuration of all nodes , `fisco-bcos` executable program, and SDK in the server.
- The `node*` folder under each IP folder stores configuration file required by the node. `config.ini` is the main configuration of node. In `conf` directory, to store certificate files and group related configurations. For the configuration detail, please refer to [here](configuration.md). Each node provides two scripts which are used to start and stop the node.
- Under each IP folder, two scripts providing `start_all.sh` and `stop_all.sh` are used to start and stop all nodes.

```bash
nodes/
├── 127.0.0.1
│   ├── fisco-bcos
│   ├── node0
│   │   ├── conf
│   │   │   ├── ca.crt
│   │   │   ├── group.1.ini
│   │   │   ├── node.crt
│   │   │   ├── node.key
│   │   │   ├── node.nodeid
│   │   ├── config.ini
│   │   ├── start.sh
│   │   └── stop.sh
│   ├── node1
│   │.....
│   ├── node2
│   │.....
│   ├── node3
│   │.....
│   ├── sdk
│   │   ├── ca.crt
│   │   ├── node.crt
│   │   └── node.key
├── cert
│   ├── agency
│   │   ├── agency.crt
│   │   ├── agency.key
│   │   ├── agency.srl
│   │   ├── ca-agency.crt
│   │   ├── ca.crt
│   │   └── cert.cnf
│   ├── ca.crt
│   ├── ca.key
│   ├── ca.srl
│   └── cert.cnf
```

## Example

### single-server and single-group

To build a 4-node FISCO BCOS alliance chain on native machine for using the default start port `30300,20200,8545` (4 nodes will occupy `30300-30303`,`20200-20203`,`8545-8548`) and listening to the external network `Channel` and `jsonrpc` ports while allowing the external network interacts with node through SDK or API.

```bash
# to build FISCO BCOS alliance chain
$ bash build_chain.sh -l "127.0.0.1:4" -i
# after generating successes, to output `All completed` to mention
Generating CA key...
==============================================================
Generating keys ...
Processing IP:127.0.0.1 Total:4 Agency:agency Groups:1
==============================================================
Generating configurations...
Processing IP:127.0.0.1 Total:4 Agency:agency Groups:1
==============================================================
[INFO] FISCO-BCOS Path   : bin/fisco-bcos
[INFO] Start Port        : 30300 20200 8545
[INFO] Server IP         : 127.0.0.1:4
[INFO] State Type        : storage
[INFO] RPC listen IP     : 0.0.0.0
[INFO] Output Dir        : /Users/fisco/WorkSpace/FISCO-BCOS/tools/nodes
[INFO] CA Key Path       : /Users/fisco/WorkSpace/FISCO-BCOS/tools/nodes/cert/ca.key
==============================================================
[INFO] All completed. Files in /Users/fisco/WorkSpace/FISCO-BCOS/tools/nodes
```

### Multi-server and muti-group

Using the build_chain script to build a multi-server and multi-group FISCO BCOS alliance chain requires the script configuration file. For details, please refer to [here](../tutorial/group_use_cases.md).

[build_chain]:https://github.com/FISCO-BCOS/FISCO-BCOS/blob/master/tools/build_chain.sh
