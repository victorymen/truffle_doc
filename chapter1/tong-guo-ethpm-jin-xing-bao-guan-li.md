# 通过ETHPM进行包管理

EthPM是以太坊的新[包装注册表](https://www.ethpm.com/)。它遵循[ERC190规范](https://github.com/ethereum/EIPs/issues/190)发布和使用智能合约包，并获得了许多不同的以太坊开发工具的广泛支持。为了表示我们的支持，我们已将以太坊软件包注册表直接集成到Truffle中。

## 安装包

从EthPM安装软件包几乎与通过NPM安装软件包一样简单。您只需运行以下命令：

```
$ truffle install <package name>
```

您还可以在特定版本上安装软件包：

```
$ truffle install <package name>@<version>
```

与NPM一样，EthPM版本遵循[semver](http://semver.org/)。您可以在[以太坊包注册表中](https://www.ethpm.com/registry)找到所有可用包的列表。

## 安装依赖项

您的项目可以定义一个`ethpm.json`文件，该文件可以将您的项目固定到特定的依赖项和版本。要安装`ethpm.json`文件中列出的所有依赖项，请运行：

```
$ truffle install
```

有关该`ethpm.json`文件的更多详细信息，请参阅下面的[包配置](https://truffleframework.com/docs/getting_started/packages-ethpm#package-configuration)。

## 使用已安装的合约

已安装的软件包将放在`installed_contracts`项目文件夹中的目录中。如果没有`installed_contracts`目录，则会为您创建。您应该像处理`node_modules`NPM文件夹一样处理此文件夹 - 也就是说，除非您知道自己在做什么，否则不应编辑内部内容。:\)

已安装的软件包可以在您的测试，迁移和可靠性合约文件中使用，`import`或者`require`通过名称来包含和收缩。例如，以下Solidity合约将从包中导入`owned.sol`文件`owned`：

```
pragma solidity ^0.4.2;

import "owned/owned.sol";

contract MyContract is owned {
  // ...
}
```

同样，以下迁移文件将使用包中的`ENS.sol`合约`ens`：

文件：`./migrations/2_deploy_contracts.js`

```
var ENS = artifacts.require("ens/ENS");
var MyContract = artifacts.require("MyContract");

module.exports = function(deployer) {
  // Only deploy ENS if there's not already an address already.
  // i.e., don't deploy if we're using the canonical ENS address,
  // but do deploy it if we're on a test network and ENS doesn't exist.
  deployer.deploy(ENS, {overwrite: false}).then(function() {
    return deployer.deploy(MyContract, ENS.address);
  });
};
```

请注意，在上面的迁移中，我们使用`ens`包并根据ENS是否已设置地址来有条件地部署ENS合同。这是[部署者](https://truffleframework.com/docs/getting_started/migrations#deployer-deploy-contract-args-options-)为您提供的一种奇特技巧，可以根据网络工件的存在更轻松地编写迁移。在这种情况下，如果我们在Ropsten网络上运行迁移，则此迁移**将不会**部署`ENS`合同，因为（在撰写本文时）Ropsten是规范`ENS`合同存在的地方 - 我们不希望部署我们自己的。但是，如果我们正在针对不同的网络或测试网络运行迁移，那么我们就要部署`ENS`合同，以便我们可以使用依赖合同。

## 发布您自己的包

发布您自己的软件包与安装一样简单，但与NPM一样，需要更多配置。

### Ropsten，Ropsten，Ropsten

以太坊软件包注册表目前存在于Ropsten测试网络中。要发布到注册表，我们需要设置自己的Ropsten配置，因为我们将进行需要签名的事务。

在这个例子中，我们将使用Infura发布包和`truffle-hdwallet-provider`NPM模块以及代表我们在Ropsten网络上的以太坊地址的12字高清钱包助记符。首先，`truffle-hdwallet-provider`在项目目录中安装via NPM：

```
$ npm install truffle-hdwallet-provider --save
```

然后编辑您的配置以`ropsten`使用12个字的助记符添加网络：

文件：`truffle.js`

```
var
 HDWalletProvider 
=
require
(
"truffle-hdwallet-provider"
)
;
// 12-word mnemonic
var
 mnemonic 
=
"opinion destroy betray ..."
;

module
.
exports 
=
{

  networks
:
{

    development
:
{

      host
:
"127.0.0.1"
,

      port
:
8545
,

      network_id
:
"*"
// Match any network id
}
,

    ropsten
:
{

      provider
:
new
HDWalletProvider
(
mnemonic
,
"https://ropsten.infura.io/"
)
,

      network_id
:
3
// official id of the ropsten network
}
}
}
;
```

### 包配置

与NPM一样，EthPM的配置选项位于一个名为的单独JSON文件中`ethpm.json`。此文件与您的Truffle配置一起提供，并为Truffle提供发布包所需的所有信息。您可以在“[配置”](https://truffleframework.com/docs/advanced/configuration)部分中查看可用选项的完整列表。

文件：`ethpm.json`

```
{
"package_name"
:
"adder"
,
"version"
:
"0.0.3"
,
"description"
:
"Simple contract to add two numbers"
,
"authors"
:
[
"Tim Coulter 
<
tim.coulter@consensys.net
>
"
]
,
"keywords"
:
[
"ethereum"
,
"addition"
]
,
"dependencies"
:
{
"owned"
:
"^0.0.1"
}
,
"license"
:
"MIT"
}
```

### 命令

配置完成后，发布很简单：

```
$ truffle publish
```

您将看到与下面类似的输出，并确认您的包已成功发布。

```
$ truffle publish

Gathering contracts...

Finding publishable artifacts...

Uploading sources and publishing to registry...

+ adder@0.0.3
```

### 发布之前

当使用像默认`develop`网络那样配置为匹配任何以太坊客户端（如[Ganache](https://truffleframework.com/ganache)或Truffle Develop）的网络时，您必然会有不想发布的网络工件。在发布程序包之前，请考虑运行以下命令以删除任何无关的网络工件：

```
$ truffle networks --clean
```

有关更多信息，请参阅[命令参考](https://truffleframework.com/docs/advanced/commands#networks)。

