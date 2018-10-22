# 使用TRUFFLE DEVELOP和控制台

有时，以交互方式处理合约以进行测试和调试，或者手动执行事务是很好的。Truffle通过交互式控制台为您提供了两种简单的方法，您可以使用合约并随时使用。

* **truffle console**
  ：连接任何以太坊客户端的基本交互式控制台
* **Truffle Develop**
  ：一个交互式控制台，它也产生了一个开发区块链接口

## 为什么有两种不同的控制台？

拥有两个不同的控制台，您可以根据自己的需要选择最佳工具。

使用truffle**控制台的**原因：

* 您有一个已经使用的客户端，例如
  [Ganache](https://truffleframework.com/docs/ganache/using)
  或geth
* 您想迁移到testnet（或主要的以太坊网络）
* 您想使用特定的助记符或帐户列表

使用truffle**开发的**原因：

* 您正在测试您的项目，而无意立即部署
* 您不需要使用特定帐户（使用默认开发帐户就可以了）
* 您不希望安装和管理单独的区块链客户端

## 命令

所有命令都要求您在项目文件夹中。你不需要在根。

### Console

要启动控制台：

```
truffle console
```

这将查找`development`在配置中调用的网络定义，并连接到该定义（如果可用）。您可以使用该`--network <name>`选项覆盖它。请参阅“[网络”](https://truffleframework.com/docs/advanced/networks)部分中的更多详细信息以及[命令参考](https://truffleframework.com/docs/advanced/commands)。

加载控制台时，您将立即看到以下提示：

```
truffle(development)>
```

这告诉您使用`development`网络在Truffle控制台中运行。

### truffle Develop

推出Truffle Develop：

```
truffle develop
```

这将在端口`9545`上生成本地生成开发区块链，无论您的`truffle.js`配置文件要求什么。

加载Truffle Develop时，您将看到以下内容：

```
Truffle Develop started at http://localhost:9545/

Accounts:
(0) 0x627306090abab3a6e1400e9345bc60c78a8bef57
(1) 0xf17f52151ebef6c7334fad080c5704d77216b732
(2) 0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef
(3) 0x821aea9a577a9b44299b9c15c88cf3087f3b5544
(4) 0x0d1d4e623d10f9fba5db95830f7d3839406c6af2
(5) 0x2932b7a2355d6fecc4b5c0b6bd44cc31df247a2e
(6) 0x2191ef87e392377ec08e7c08eb105ef5448eced5
(7) 0x0f4f2ac550a1b4e2280d04c21cea7ebd822934b5
(8) 0x6330a553fc93768f612722bb8c2ec78ac90b3bbc
(9) 0x5aeda56215b167893e80b4fe645ba6d5bab767de

Private Keys:
(0) c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3
(1) ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f
(2) 0dbbe8e4ae425a6d2687f1a7e3ba17bc98c673636790f1b8ad91193c05875ef1
(3) c88b703fb08cbea894b6aeff5a544fb92e78a18e19814cd85da83b71f772aa6c
(4) 388c684f0ba1ef5017716adb5d21a053ea8e90277d0868337519f97bede61418
(5) 659cbb0e2411a44db63778987b1e22153c086a95eb6b18bdf89de078917abc63
(6) 82d052c865f5763aad42add438569276c00d3d88a2d062d36b2bae914d58b8c8
(7) aa3680d5d48a8283413f7a108367c7299ca73f553735860a87b08f39395618b7
(8) 0f62d96d6675f32685bbdb8ac13cda7c23436f63efbb9d07700d8669ff12b7c4
(9) 8d5366123cb560bb606379f90a0bfd4769eecc0557f1b362dcae9012b548b1e5

Mnemonic: candy maple cake sugar pudding cream honey rich smooth crumble sweet treat
```

这将显示此特定区块链的地址，私钥和助记符。

**注意**：无法更改助记符和地址。如果您想使用不同的助记符或一组地址，我们建议您使用[Ganache](https://truffleframework.com/docs/ganache/using)。

**警告**：请记住永远不要在主网上使用这些地址或助记符。这仅用于开发。

## 特征

Truffle Develop和控制台都提供了Truffle命令行工具中的大多数功能。例如，您可以`migrate --reset`在控制台中键入，并且它将被解释为与`truffle migrate --reset`在命令行上运行时相同。

此外，Truffle Develop和控制台还具有以下功能：

* 您的所有已编译合同均可供使用。
* 在每个命令（例如`migrate --reset`）之后重新配置您的合同，以便您可以立即开始使用新分配的地址和二进制文件。
* 该`web3`库已可用，并设置为连接到您的以太坊客户端。
* 所有返回promise的命令都将自动解析，并打印结果，无需使用`.then()`简单的命令。例如，以下命令：

  ```
  MyContract.at("0xabcd...").getValue.call();
  ```

  将返回如下内容：

  ```
  5
  ```

### 可用的命令

* `build`
* `compile`
* `create`
* `debug`
* `exec`
* `install`
* `migrate`
* `networks`
* `opcode`
* `publish`
* `test`
* `version`

如果Truffle命令不可用，那是因为它与现有项目无关（例如`init`）或没有意义（例如，`develop`或`console`）。

有关更多信息，请参阅完整[命令参考](https://truffleframework.com/docs/advanced/commands)

