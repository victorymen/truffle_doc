# 通过NPM进行包管理

Truffle标配`npm`集成，并且知道`node_modules`项目中的目录（如果存在）。这意味着您可以通过使用和分发合约，dapps和启用以太坊的库`npm`，使您的代码可供其他人使用，也可以使用其他代码。

## 包装布局

使用Truffle创建的项目默认具有特定的布局，这使得它们可以用作包。这种布局不是必需的，但如果用作通用惯例 - 或“事实上的标准” - 那么通过NPM分配合同和dapp将变得更加容易。

Truffle包中最重要的目录如下：

* `/contracts`
* `/build`
  （包括
  `/build/contracts`
  由Truffle创建）

第一个目录是您的合约目录，其中包括您的原始Solidity合同。第二个目录是构建目录，更具体地说`/build/contracts`，它以`.json`文件的形式保存构建工件。在您的包中包含原始合同将允许其他人在他们自己的可靠性代码中导入这些合同。同样，`.json`在您的包中包含您的构建工件将允许其他人从JavaScript无缝地与您的合同进行交互，这可以在dapps，脚本和迁移中使用。

## 使用包

在您自己的项目中使用软件包时，请务必注意，您可能有两个地方可能对使用其他合同代码感兴趣：在合同内和Javascript代码中（迁移和测试）。以下提供了每种情况的示例，并讨论了充分利用其他合同和构建工件的技术。

### 安装

在本例中，我们将使用[Example Truffle Library](https://github.com/ConsenSys/example-truffle-library)，它提供了一个部署到Morden测试网络的简单名称注册表。为了将它用作依赖项，我们必须首先在我们的项目中通过`npm`以下方式安装它：

```
$ cd my_project

$ npm install example-truffle-library
```

请注意，上面的最后一个命令会下载程序包并将其放在`my_project/node_modules`目录中，这对下面的示例很重要。有关使用安装包的帮助，请参阅[npm文档](https://docs.npmjs.com/)`npm`。

### 在你的合同中

要在合约中使用包中的合约，这可以像Solidity的[import](http://solidity.readthedocs.io/en/develop/layout-of-source-files.html?#importing-other-source-files)语句一样简单。当您的导入路径不是[显式相对或绝对](https://truffleframework.com/docs/getting_started/compile#dependencies)路径时，这表示您正在寻找您正在寻找特定命名包中的文件的Truffle。使用上面提到的示例truffle库来考虑此示例：

```
import "example-truffle-library/contracts/SimpleNameRegistry.sol";

```

由于路径没有开始`./`，Truffle知道在项目的`node_modules`目录中查找该`example-truffle-library`文件夹。从那里，它解决了为您提供所需合同的路径。

### 在JavaScript代码中

要在JavaScript代码中与包的契约进行交互，您只需要`require`该包的`.json`文件，然后使用[truffle-contract](https://github.com/trufflesuite/truffle-contract)模块将它们转换为可用的抽象：

```
var contract = require("truffle-contract");

var data = require("example-truffle-library/build/contracts/SimpleNameRegistry.json");

var SimpleNameRegistry = contract(data);
```

要使用这些抽象，请参阅“[与您的合同交互”](https://truffleframework.com/docs/getting_started/contracts)部分以获取更多详细信息。

### 包的已部署地址

有时您希望合同与包的先前部署的合同进行交互。由于部署的地址存在于包的`.json`文件中，因此您必须执行额外的步骤以将这些地址放入合同中。为此，请使您的合同接受依赖关系合同的地址，然后使用迁移。以下是项目中存在的示例合同以及示例迁移：

合同：`MyContract.sol`

```
pragma solidity 
^
0.4
.13
;
import
"example-truffle-library/contracts/SimpleNameRegistry.sol"
;

contract MyContract 
{

  SimpleNameRegistry registry
;

  address 
public
 owner
;
function
 MyContract 
{

    owner 
=
 msg
.
sender
;
}
// Simple example that uses the deployed registry from the package.
function
getModule
(
bytes32 name
)
returns
(
address
)
{
return
 registry
.
names
(
name
)
;
}
// Set the registry if you're the owner.
function
setRegistry
(
address addr
)
{
require
(
msg
.
sender 
==
 owner
)
;

    registry 
=
SimpleNameRegistry
(
addr
)
;
}
}
```

移民：`3_hook_up_example_library.js`

```
// Note that artifacts.require takes care of creating an abstraction for us.
var
 SimpleNameRegistry 
=
 artifacts
.
require
(
"example-truffle-library/SimpleNameRegistry"
)
;

module
.
exports
=
function
(
deployer
)
{
// Deploy our contract, then set the address of the registry.

  deployer
.
deploy
(
MyContract
)
.
then
(
function
(
)
{
return
 MyContract
.
deployed
(
)
;
}
)
.
then
(
function
(
deployed
)
{
return
 deployed
.
setRegistry
(
SimpleNameRegistry
.
address
)
;
}
)
;
}
;
```

### 发布之前

当使用像默认`develop`网络那样配置为匹配任何以太坊客户端（如[Ganache](https://truffleframework.com/ganache)或Truffle Develop）的网络时，您必然会有不想发布的网络工件。在发布程序包之前，请考虑运行以下命令以删除任何无关的网络工件：

```
$ truffle networks --clean

```

有关更多信息，请参阅[命令参考](https://truffleframework.com/docs/advanced/commands#networks)。

