# CLI 命令参考

打开命令行，定位到 neo-cli 所在目录，输入以下命令即可启动 NEO 的命令行钱包。

`dotnet neo-cli.dll`

本篇教程将介绍命令行钱包的所有命令，你可以通过输入命令操作钱包，如创建打开钱包、导入导出私钥、转账、启动共识等。

## 命令概览

以下表格列出了所有 CLI 命令。在命令行中输入 `help` 也可以查看所有命令。

本文中的命令格式均遵循以下约定：

- `<>` ：表示参数
- `[]` ：可选参数
- `|` ：表示所填的参数可以是其中任意一种
- `=` ：表示可选参数在不输入情况下的默认值

#### 控制台命令

| 命令               | 说明                                   |
| :----------------- | -------------------------------------- |
| version            | 显示当前软件的版本                     |
| help [plugin-name] | 帮助菜单，也可以查看部分插件的提示信息 |
| clear              | 清除屏幕                               |
| exit               | 退出程序                               |

#### 合约命令

| 命令                                                         | 功能说明 |
| ------------------------------------------------------------ | -------- |
| deploy \<nefFilePath> [manifestFile]                         | 发布合约 |
| invoke \<scripthash> \<command> [optionally quoted params separated by space] | 调用合约 |

#### 钱包命令

| 命令                                    | 说明                                                   | 备注         |
| --------------------------------------- | ------------------------------------------------------ | ------------ |
| create wallet \<path>                   | 创建钱包文件                                           |              |
| open wallet \<path>                     | 打开钱包文件                                           |              |
| close wallet                            | 关闭钱包文件                                           |              |
| upgrade wallet \<path>                  | 升级旧版钱包文件                                       |              |
| list address                            | 列出钱包中的所有账户                                   | 需要打开钱包 |
| list asset                              | 列出钱包中的所有资产                                   | 需要打开钱包 |
| list key                                | 列出钱包中的所有公钥                                   | 需要打开钱包 |
| show gas                                | 列出钱包中的所有未提取的 GAS                           | 需要打开钱包 |
| create address [n为正整数，不填默认为1] | 创建地址 / 批量创建地址                                | 需要打开钱包 |
| import key \<wif\|path>                 | 导入私钥 / 批量导入私钥                                | 需要打开钱包 |
| export key \[address] [path]            | 导出私钥                                               | 需要打开钱包 |
| send \<id\|alias> \<address> \<value>   | 向指定地址转账 参数分别为：资产 ID，对方地址，转账金额 | 需要打开钱包 |
| import multisigaddress m pubkeys...     | 创建多方签名合约                                       | 需要打开钱包 |
| sign \<jsonObjectToSign>                | 签名  参数为：记录交易内容的 json 字符串               | 需要打开钱包 |

#### 节点命令

| 命令                      | 说明                                           |
| ------------------------- | ---------------------------------------------- |
| show state                | 显示当前区块链同步状态                         |
| show pool [verbose]       | 显示内存池中的交易（这些交易处于零确认的状态） |
| relay \<jsonObjectToSign> | 广播 参数为：记录交易内容的 json 字符串        |

#### 插件命令

| 命令                    | 说明         |
| ----------------------- | ---------------- |
| plugins                 | 显示已加载的插件 |
| install [Plugin name]   | 安装指定插件     |
| uninstall [Plugin name] | 卸载指定插件     |
| export block[s] [path=chain.acc] | 导出全部区块数据，导出的结果可以用作离线同步|
| export block[s] \<start> [count] | 从指定区块高度导出指定数量的区块数据，导出的结果可以用作离线同步 |
| dump storage \<key> | 导出全部或指定的状态量数据 |
#### 高级命令

| 命令            | 说明     |
| --------------- | -------- |
| start consensus | 启动共识 |

## 命令说明

###  deploy

将智能合约发布到链上。

##### 句法

`deploy <nefFilePath> [manifestFile]` 

##### 参数

- nefFilePath：NeoVM的可执行文件 .nef 的路径
- manifestFile：manifest.json文件的路径。manifest记录了合约的各个接口信息以及配置内容。

##### 示例

```
neo> deploy Template.nef Template.manifest.json  
Script hash: 0x1e5ce27b9af630aed82bc94695fa8d424cdbe5c6
Gas Consumed: 100000000

Signed and relayed transaction with hash=0xab6dd63ea36a7c95580b241f34ba756e62c767813be5d53e02a983f4e561d284
```

### invoke

调用合约

##### 句法

`invoke <scripthash> <command> [optionally quoted params separated by space]` 

##### 参数

- scripthash：要调用的合约脚本hash
- command：合约内方法名，后面可以输入传入参数，以空格隔开

##### 示例

```
neo> invoke 0x1e5ce27b9af630aed82bc94695fa8d424cdbe5c6 name
Invoking script with: '00c1046e616d6514c6e5db4c428dfa9546c92bd8ae30f69a7be25c1e68627d5b52'
VM State: HALT
Gas Consumed: 4320950
Evaluation Stack: [{"type":"ByteArray","value":"6e616d656f66746865746f6b656e"}]

relay tx(no|yes): no
```

### upgrade wallet

升级旧版 .db3 钱包文件。

##### 句法

 `upgrade wallet <path>` 

##### 示例

```
neo> upgrade wallet test.db3
Wallet file upgrade complete. New wallet file has been auto-saved at: test.json
```

### show gas

列出当前钱包中的所有未提取的 GAS。

##### 句法

`show gas` 

##### 示例

```
neo> show gas
unclaimed gas: 0
```

> [!NOTE]
>
> 这里不包含已提取的 GAS，查看已提取的 GAS 请用 list asset 命令。

在 NEO3 中，某地址每次 NEO 数量发生改变时都会触发其 GAS 的自动提取。

### create address

在当前钱包中创建一个地址或批量创建地址。创建的地址会自动导出到 address.txt 文件中。

##### 句法

`create address [n]` 

##### 参数

n：要创建的地址数量。n 为正整数，不填默认为1

##### 示例

```
neo> create address 3
The file 'address.txt' already exists, do you want to overwrite it? (yes|no): yes
[3/3]
export addresses to address.txt
```

### export key

导出地址对应的私钥到指定的文件。该命令需要验证钱包密码。

##### 句法

 `export key [address] [path]`

##### 参数

- address：指定要导出私钥的地址

- path：指定保存私钥的文件路径


##### 示例

将私钥输出到控制台：

```
neo> export key
password: ********
L4HoTTfKfzjV8tdWv6vRaMY1cBQbsVc4euGqhPW9Mf8z6993fgMH
L3raEwVsJHzovTXfgVG1HWxzmH4Zm3vMia8byszhVrvbJ38YnsXv
KywrMAnBWRXUAb4Aq76ZoCCqDvGNL9nidjVwWwDr3DbMPwg1RPBL
KwhNLC9rULxJmevqwYbQzJhYNvaCDPyAUAm7EkHrW5kQwLYfxTFG
```

```
neo> export key AapRvH8FB2jx9S2fmwntAW4QYdXYyyeqQ9
password: ********
L4HoTTfKfzjV8tdWv6vRaMY1cBQbsVc4euGqhPW9Mf8z6993fgMH
```

将私钥输出到指定文件中：

```
neo> export key key1.txt
password: ********

```

```
neo> export key AapRvH8FB2jx9S2fmwntAW4QYdXYyyeqQ9 key2.txt
password: ********

```

### import key

导入一个私钥或者指定文件中的多个私钥。

##### 句法

 `import key <wif|path>`

##### 参数

wif|path：指定要导入的私钥，或者存放私钥的文件路径。

##### 示例

```
neo> import key L4HoTTfKfzjV8tdWv6vRaMY1cBQbsVc4euGqhPW9Mf8z6993fgMH
address: AapRvH8FB2jx9S2fmwntAW4QYdXYyyeqQ9
 pubkey: 03768c9fc17a01854084b836d3f0ae4122902b4b59b6c11e855a3f3bf8ea6b205f
```

```
neo> import key key1.txt
[4/4]
```

如果是指定文件的话，文件里的私钥格式请参考 export key key.txt 的输出。

### import multisigaddress

创建多方签名的合约地址。

##### 句法

`import multisigaddress m pubkeys...`

##### 参数

- m：以 m 个最小签名数量来创建多方签名的合约地址，例如两个公钥创建的多方签名地址， m 可以为 1 或 2，
- pubkeys：创建多方签名合约地址的各方公钥

##### 示例

```
neo> import multisigaddress 1 022b386a0ac6fa5abad4bfabc7dff3c016654fa97176811cb64f4831284a7399ca 0288a99d33b6f7f1b19d3be7a7935d2c076fec52d9591336af03e43eec8ca1b16b
Multisig. Addr.: AYpc268sh4tff7CTj5W4tztt1qheVTUa6P
```

### send

向指定地址转账。该命令需要验证钱包密码。

##### 句法

`send <id|alias> <address> <value>`

##### 参数

- id|alias：资产 ID或资产缩写，如 neo，gas
- address：收款地址
- value：转账金额

##### 示例

将 100 GAS 转到地址 “AMwS5twG1LLJA4USMPFf5UugfUvEfNDz6e”：

```
neo> send a1760976db5fcdfab2a9930e8f6ce875b2d18225 AMwS5twG1LLJA4USMPFf5UugfUvEfNDz6e 100
password: ********
TXID: 0x8f831d8de723093316c05749a053a226514bc06338b2bceb50db690610e0b92f
```

因为第二个参数除了资产 ID，还可以填写资产缩写，所以以上命令可以写成：

```
neo> send gas AMwS5twG1LLJA4USMPFf5UugfUvEfNDz6e 100
password: ********
TXID: 0xae0675797c2d738dcadb21cec3f1809ff453ac291046a05ac679cbd95b79c856
```

要查看资产 ID ，可输入 list asset 命令查看钱包中的所有资产。

当从签名数量为 1 以上的多方签名合约地址中转出资产时，需要多方进行签名，此时会返回一段待签名的 json 字符串，如下所示： 

```
neo> send gas ARfyrX28D2H2wP6KR6xxaUbvqvkv5SbMNe 2
password: ********
SignatureContext:
{"type":"Neo.Network.P2P.Payloads.Transaction","hex":"0071c0992d42e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c00e1f50500000000ac0c240000000000cb152300000142e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c01550400c2eb0b146c93f190909dea8dfe3caeb2ee90530b4ef21e861442e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c53c1087472616e73666572142582d1b275e86c8f0e93a9b2facd5fdb760976a168627d5b52f1","items":{"0x0c2a95d2bba739e2b2e1b0e55d3b768b2ca6e242":{"script":"5221032528d085e55de82b801374ea91cc51b5e6e990ba2eddb2f461c4d95da54aff002102685dd451efbf38cf859a80f250815f503303dd7b9f6546786164de219ede87735268c7c34cba","parameters":[{"type":"Signature"},{"type":"Signature"}],"signatures":{"032528d085e55de82b801374ea91cc51b5e6e990ba2eddb2f461c4d95da54aff00":"d9ac57bac4260c60707e0b641585c70789e1a2eb5438c95de972af9aff99f5f4485b81cd2382218583b7f4950da54dbd8d1468f72b91809e14bb1c8139cca637"}}}}
```

### sign

从签名数量为 1 以上的多方签名合约中提取资产时，需要多方进行签名。 签名完整后才能广播出去。广播交易的方法请参照 relay 方法。

##### 句法

`sign <jsonObjectToSign>` 

##### 参数

jsonObjectToSign：记录多方签名交易的 json 字符串。

##### 示例

```
neo> sign {"type":"Neo.Network.P2P.Payloads.Transaction","hex":"0071c0992d42e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c00e1f50500000000ac0c240000000000cb152300000142e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c01550400c2eb0b146c93f190909dea8dfe3caeb2ee90530b4ef21e861442e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c53c1087472616e73666572142582d1b275e86c8f0e93a9b2facd5fdb760976a168627d5b52f1","items":{"0x0c2a95d2bba739e2b2e1b0e55d3b768b2ca6e242":{"script":"5221032528d085e55de82b801374ea91cc51b5e6e990ba2eddb2f461c4d95da54aff002102685dd451efbf38cf859a80f250815f503303dd7b9f6546786164de219ede87735268c7c34cba","parameters":[{"type":"Signature"},{"type":"Signature"}],"signatures":{"032528d085e55de82b801374ea91cc51b5e6e990ba2eddb2f461c4d95da54aff00":"d9ac57bac4260c60707e0b641585c70789e1a2eb5438c95de972af9aff99f5f4485b81cd2382218583b7f4950da54dbd8d1468f72b91809e14bb1c8139cca637"}}}}
Signed Output:
{"type":"Neo.Network.P2P.Payloads.Transaction","hex":"0071c0992d42e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c00e1f50500000000ac0c240000000000cb152300000142e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c01550400c2eb0b146c93f190909dea8dfe3caeb2ee90530b4ef21e861442e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c53c1087472616e73666572142582d1b275e86c8f0e93a9b2facd5fdb760976a168627d5b52f1","items":{"0x0c2a95d2bba739e2b2e1b0e55d3b768b2ca6e242":{"script":"5221032528d085e55de82b801374ea91cc51b5e6e990ba2eddb2f461c4d95da54aff002102685dd451efbf38cf859a80f250815f503303dd7b9f6546786164de219ede87735268c7c34cba","parameters":[{"type":"Signature","value":"794f87a810bd30b15f90ddc1898e2e592c1a3fae4b14e34d8a411305e7913d44ab56e388125ef597be46a8958b2ed8c5e298076c2d69ab3337c944f5356c462b"},{"type":"Signature","value":"d9ac57bac4260c60707e0b641585c70789e1a2eb5438c95de972af9aff99f5f4485b81cd2382218583b7f4950da54dbd8d1468f72b91809e14bb1c8139cca637"}]}}}
```

### relay

将完成签名的交易信息进行广播。

##### 句法

`relay <jsonObjectToSign>` 

##### 参数

jsonObjectToSign：记录签名交易的 json 字符串。

##### 示例

```
neo> relay {"type":"Neo.Network.P2P.Payloads.Transaction","hex":"0071c0992d42e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c00e1f50500000000ac0c240000000000cb152300000142e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c01550400c2eb0b146c93f190909dea8dfe3caeb2ee90530b4ef21e861442e2a62c8b763b5de5b0e1b2e239a7bbd2952a0c53c1087472616e73666572142582d1b275e86c8f0e93a9b2facd5fdb760976a168627d5b52f1","items":{"0x0c2a95d2bba739e2b2e1b0e55d3b768b2ca6e242":{"script":"5221032528d085e55de82b801374ea91cc51b5e6e990ba2eddb2f461c4d95da54aff002102685dd451efbf38cf859a80f250815f503303dd7b9f6546786164de219ede87735268c7c34cba","parameters":[{"type":"Signature","value":"794f87a810bd30b15f90ddc1898e2e592c1a3fae4b14e34d8a411305e7913d44ab56e388125ef597be46a8958b2ed8c5e298076c2d69ab3337c944f5356c462b"},{"type":"Signature","value":"d9ac57bac4260c60707e0b641585c70789e1a2eb5438c95de972af9aff99f5f4485b81cd2382218583b7f4950da54dbd8d1468f72b91809e14bb1c8139cca637"}]}}}
Data relay success, the hash is shown as follows:
0xdcf144d9ed2d64482fb5caafa719cf6706e9afd607ab043e8bfcb9018795e4d1
```

### plugins

显示所有已加载的插件。

##### 句法

`plugins`

##### 示例

```
neo> plugins
Loaded plugins:
        ApplicationLogs
        CoreMetrics
        ImportBlocks
        RpcNep5Tracker
        RpcSecurity
        RpcWallet
        StatesDumper
        SystemLogs
```

### install

安装指定插件

`install [Plugin name]` 

安装指定插件，如下所示。卸载插件与此类似。

```
neo> install ImportBlocks
Downloading from https://github.com/neo-project/neo-plugins/releases/download/v3.0.0-preview1/ImportBlocks.zip
Install successful, please restart neo-cli.
```

以上只是示例插件，更多插件请参考 [安装插件](setup.md)。

###export block[s]

导出全部区块数据，或者从指定区块高度导出指定数量的区块数据，导出的结果可以用作离线同步。要使用此命令，需先安装 ImportBlocks 插件。

##### 句法

`export block[s] [path=chain.acc]`

`export block[s] <start> [count]`

##### 参数

[path=chain.acc]：导出全部区块数据

<start> [count]：指定要导出数据的起始区块高度和区块数量

### dump storage

导出全部或指定的状态量数据

##### 句法

`dump storage <key>`

### start consensus

启动共识。启动共识的前提是该钱包有共识的权限，在 NEO 主网上可以通过投票选举获得共识的权限，如果自己部署的私有链，可以在 `protocol.json` 中设置共识节点的公钥，详情可参考 [私链搭建](../../network/private-chain/private-chain.md)。

> [!NOTE]
>
> 若需要查看共识过程日志，需要先安装 SystemLog 插件 。
