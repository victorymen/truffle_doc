# TRUFFLE和METAMASK

在浏览器中与智能合约进行交互之前，请确保它们已经编译，部署，并且您通过`web3`客户端JavaScript与它们进行交互。我们建议使用[松露合约](https://github.com/trufflesuite/truffle-contract)库，因为它使合同的交互更容易，更健壮。

**注意**：有关这些主题的更多信息，包括使用`truffle-contract`，请查看我们的[宠物店](https://truffleframework.com/tutorials/pet-shop)或[TutorialToken](https://truffleframework.com/tutorials/robust-smart-contracts-with-openzeppelin)教程。

完成上述操作后，您就可以使用MetaMask了。

## 什么是MetaMask？

[MetaMask](https://metamask.io/)是在浏览器中与dapps进行交互的最简单方法。它是Chrome或Firefox的扩展，可连接到以太网网络而无需在浏览器的计算机上运行完整节点。它可以连接到主要的以太网网络，任何测试网络（Ropsten，Kovan和Rinkeby），或本地区块链，例如由[Ganache](https://truffleframework.com/ganache)或Truffle Develop创建的区域链。

![](https://truffleframework.com/img/docs/truffle/truffle-with-metamask/metamask.png "MetaMask")

对于使用Truffle进行开发，这意味着我们可以像使用者在实时网络上与其进行交互一样使用我们的dapp。

## 安装MetaMask

* 要安装MetaMask for Chrome，请转到[Chrome网上应用店](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn)，然后点击**添加到Chrome**按钮。

* 要安装MetaMask for FireFox，请转到[Firefox Add-ons页面](https://addons.mozilla.org/en-US/firefox/addon/ether-metamask/)，然后单击**Add to Firefox**按钮。

随着我们的前端准备好使用并安装了MetaMask，我们已经准备好看到我们的dapp了。

## 使用MetaMask和Ganache

[Ganache](https://truffleframework.com/ganache)是一个运行区块链的图形应用程序，可用于测试目的。它继续运行`127.0.0.1:7545`。

**注意**：我们建议指定`127.0.0.1`而不是`localhost`因为地址不需要网络连接，因此更适合开发。

### 检测MetaMask的web3注入

在深入研究之前，我们需要确保dapp正在检查MetaMask的`web3`实例，并且扩展本身已经与Ganache正确配置。

MetaMask注入了自己的`web3`实例，所以我们要确保我们检查它。加载窗口后执行以下检查：

```
// Is there is an injected web3 instance?
if
(
typeof
 web3 
!==
'undefined'
)
{

  App
.
web3Provider 
=
 web3
.
currentProvider
;

  web3 
=
new
Web3
(
web3
.
currentProvider
)
;
}
else
{
// If no injected web3 instance is detected, fallback to Ganache.

  App
.
web3Provider 
=
new
web3
.
providers
.
HttpProvider
(
'http://127.0.0.1:7545'
)
;

  web3 
=
new
Web3
(
App
.
web3Provider
)
;
}
```

### 设置MetaMask

要将Ganache与MetaMask一起使用，请单击浏览器中的MetaMask图标，将出现以下屏幕：

![](https://truffleframework.com/img/tutorials/pet-shop/metamask-initial.png "MetaMask初始屏幕")

_MetaMask初始屏幕_

单击“**导入现有DEN”**。在标记为**Wallet Seed**的框中，输入启动Ganache时显示的助记符。

**警告**：请勿在以太网主网络（mainnet）上使用此助记符。确保将网络设置为“专用网络”（使用“自定义RPC”设置）。请参阅下文了解详情。

在其下方输入密码，然后单击“**确定”**。

![](https://truffleframework.com/img/tutorials/pet-shop/metamask-seed.png "MetaMask种子短语")

_MetaMask种子短语_

现在我们需要将MetaMask连接到Ganache创建的区块链。单击显示“主网络”的菜单，然后选择**自定义RPC**。

![](https://truffleframework.com/img/tutorials/pet-shop/metamask-networkmenu.png "MetaMask网络菜单")

_MetaMask网络菜单_

在标题为“新RPC URL”的框中输入`http://127.0.0.1:7545`并单击“**保存”**。

顶部的网络名称将切换为“私人网络”。单击“设置”旁边的向左箭头以关闭页面并返回“帐户”页面。

现在我们已经将MetaMask连接到Ganache，您将被带到帐户屏幕。由Ganache创建的每个帐户都有100个以太网。第一个帐户应该少于其他帐户，因为该帐户为智能合约部署提供燃气。由于您已将智能合约部署到网络，因此该帐户已付费。

单击右上角的帐户图标以创建新帐户，其中前10个将对应于启动Ganache时显示的10个帐户。

![](https://truffleframework.com/img/tutorials/pet-shop/metamask-account1.png "MetaMask帐户")

_MetaMask帐户_

## 使用MetaMask和Truffle Develop

Truffle Develop是一个命令行应用程序，它运行临时区块链，也用于测试目的。它继续运行`127.0.0.1:9545`。

**注意**：我们建议指定`127.0.0.1`而不是`localhost`因为地址不需要网络连接，因此更适合开发。

使用MetaMask和Truffle Develop非常类似于Ganache。唯一的区别是Truffle Develop默认运行`127.0.0.1:9545`，因此您需要编辑上面的web3代码来说：

```
// Is there is an injected web3 instance?
if
(
typeof
 web3 
!==
'undefined'
)
{

    App
.
web3Provider 
=
 web3
.
currentProvider
;

    web3 
=
new
Web3
(
web3
.
currentProvider
)
;
}
else
{
// If no injected web3 instance is detected, fallback to Truffle Develop.

    App
.
web3Provider 
=
new
web3
.
providers
.
HttpProvider
(
'http://127.0.0.1:9545'
)
;

    web3 
=
new
Web3
(
App
.
web3Provider
)
;
}
```

在MetaMask中，输入“新RPC URL”时，输入`http://127.0.0.1:9545`。

## 使用MetaMask和Ganache CLI

使用MetaMask和Ganache CLI也非常类似于Ganache。唯一的区别是Ganache CLI默认运行，`http://127.0.0.1:8545`因此您需要编辑上面的web3代码来说：

```
// Is there is an injected web3 instance?
if
(
typeof
 web3 
!==
'undefined'
)
{

    App
.
web3Provider 
=
 web3
.
currentProvider
;

    web3 
=
new
Web3
(
web3
.
currentProvider
)
;
}
else
{
// If no injected web3 instance is detected, fallback to Ganache CLI.

    App
.
web3Provider 
=
new
web3
.
providers
.
HttpProvider
(
'http://127.0.0.1:8545'
)
;

    web3 
=
new
Web3
(
App
.
web3Provider
)
;
}
```

在MetaMask中，输入“新RPC URL”时，输入`http://127.0.0.1:8545`。

