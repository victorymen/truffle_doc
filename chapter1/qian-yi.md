# 运行迁移

迁移是JavaScript文件，可帮助您将合约部署到以太坊网络。这些文件负责暂存您的部署任务，并且假设您的部署需求会随着时间的推移而改变。随着项目的发展，您将创建新的迁移脚本，以进一步推动区块链的发展。先前运行的迁移历史记录通过特殊`Migrations`合同在链上记录，详情如下。

## 命令

要运行迁移，请运行以下命令：

```
$ truffle migrate

```

这将运行位于项目`migrations`目录中的所有迁移。最简单的迁移只是一组托管部署脚本。如果先前成功运行了迁移，`truffle migrate`则将从上次运行的迁移开始执行，仅运行新创建的迁移。如果不存在新的迁移，`truffle migrate`则根本不执行任何操作。您可以使用该`--reset`选项从头开始运行所有迁移。对于本地测试，请确保在执行之前安装并运行了一个测试区块链，例如[Ganache](https://truffleframework.com/ganache)`migrate`。

## 迁移文件

一个简单的迁移文件如下所示：

文件名：`4_example_migration.js`

```
var
 MyContract 
=
 artifacts
.
require
(
"MyContract"
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
// deployment steps

  deployer
.
deploy
(
MyContract
)
;
}
;
```

请注意，文件名以数字为前缀，后缀为描述。编号前缀是必需的，以便记录迁移是否成功运行。后缀纯粹是为了人类的可读性和理解力。

### artifacts.require（）

在迁移开始时，我们告诉Truffle我们想要通过该`artifacts.require()`方法与哪些合同进行交互。这个方法类似于Node`require`，但在我们的例子中，它特别返回了一个契约抽象，我们可以在其余的部署脚本中使用它。指定的名称应与**该**源文件**中的合同定义的名称**匹配。不传递源文件的名称，因为文件可以包含多个合同。

考虑这个示例，其中在同一源文件中指定了两个合同：

文件名：`./contracts/Contracts.sol`

```

contract ContractOne 
{
// ...
}

contract ContractTwo 
{
// ...
}
```

要仅使用`ContractTwo`，您的`artifacts.require()`语句将如下所示：

```
var ContractTwo = artifacts.require("ContractTwo");

```

要使用这两个合同，您需要两个`artifacts.require()`语句：

```
var ContractOne = artifacts.require("ContractOne");

var ContractTwo = artifacts.require("ContractTwo");
```

### module.exports

所有迁移都必须通过`module.exports`语法导出函数。每次迁移导出的函数都应接受一个`deployer`对象作为其第一个参数。此对象通过为部署智能合约提供清晰的语法以及执行某些部署更普通的职责（例如保存已部署的工件以供以后使用）来帮助部署。该`deployer`对象是用于暂存部署任务的主界面，其API在本页底部描述。

您的迁移功能也可以接受其他参数。请参阅以下示例。

## 初始迁移

Truffle要求您拥有迁移合同才能使用迁移功能。此合同必须包含特定的界面，但您可以随意编辑此合同。对于大多数项目，此合同最初将作为第一次迁移进行部署，不会再次更新。在创建新项目时，您也会默认收到此合同`truffle init`。

文件名：`contracts/Migrations.sol`

    pragma solidity ^0.4.8;

    contract Migrations {

      address public owner;

      // A function with the signature `last_completed_migration()`, returning a uint, is required.

      uint public last_completed_migration;

      modifier restricted() {

        if (msg.sender == owner) _;

      }

      function Migrations() {

        owner = msg.sender;

      }

      // A function with the signature `setCompleted(uint)` is required.

      function setCompleted(uint completed) restricted {

        last_completed_migration = completed;

      }

      function upgrade(address new_address) restricted {

        Migrations upgraded = Migrations(new_address);

        upgraded.setCompleted(last_completed_migration);

      }

    }

您必须在第一次迁移中部署此合同才能利用迁移功能。为此，请创建以下迁移：

文件名：`migrations/1_initial_migration.js`

```
var
 Migrations 
=
 artifacts
.
require
(
"Migrations"
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
// Deploy the Migrations contract as our only task

  deployer
.
deploy
(
Migrations
)
;
}
;
```

从此处，您可以使用增加的编号前缀创建新迁移，以部署其他合同并执行进一步的部署步骤。

## 部署

您的迁移文件将使用部署程序来部署部署任务。因此，您可以同步编写部署任务，它们将以正确的顺序执行：

```
// Stage deploying A before B

deployer
.
deploy
(
A
)
;

deployer
.
deploy
(
B
)
;
```

或者，部署程序上的每个函数都可以用作Promise，以排队依赖于前一个任务执行的部署任务：

```
// Deploy A, then deploy B, passing in A's newly deployed address

deployer
.
deploy
(
A
)
.
then
(
function
(
)
{
return
 deployer
.
deploy
(
B
,
A
.
address
)
;
}
)
;
```

如果您发现语法更清晰，则可以将部署编写为单个承诺链。部署API将在本页底部讨论。

## 网络考虑因素

可以根据部署到的网络有条件地运行部署步骤。这是一项高级功能，因此请在继续之前先查看“[网络”](https://truffleframework.com/docs/advanced/networks)部分。

要有条件地暂存部署步骤，请编写迁移，以便它们接受第二个参数，称为`network`。例：

```
module
.
exports
=
function
(
deployer
,
 network
)
{
if
(
network 
==
"live"
)
{
// Do something specific to the network named "live".
}
else
{
// Perform a different step otherwise.
}
}
```

## 可用帐户

迁移也会通过您的以太坊客户端和Web3提供商提供给您的帐户列表，供您在部署期间使用。这是从中返回的完全相同的帐户列表`web3.eth.getAccounts()`。

```
module
.
exports
=
function
(
deployer
,
 network
,
 accounts
)
{
// Use the accounts within your migrations.
}
```

## 部署者API

部署程序包含许多可用于简化迁移的功能。

### deployer.deploy（contract，args ...，options）

使用可选的构造函数参数部署由合同对象指定的特定合同。这对单例合约很有用，因此dapp只存在此合约的一个实例。这将在部署后设置合同的地址（即，`Contract.address`将等于新部署的地址），并且它将覆盖存储的任何先前地址。

您可以选择传递一组合同或一组数组，以加快多个合同的部署。此外，最后一个参数是一个可选对象，可以包含名称`overwrite`以及其他事务参数（如`gas`和）`from`。如果`overwrite`设置为`false`，则部署者将不会部署此合同（如果已经部署了该合同）。这对于由外部依赖项提供合同地址的某些情况很有用。

请注意，在调用之前，您需要首先部署和链接合同所依赖的任何库`deploy`。有关`link`详细信息，请参阅下面的功能。

有关更多信息，请参阅[松露合同](https://github.com/trufflesuite/truffle-contract)文档。

例子：

    // Deploy a single contract without constructor arguments

    deployer
    .
    deploy
    (
    A
    )
    ;
    // Deploy a single contract with constructor arguments

    deployer
    .
    deploy
    (
    A
    ,
     arg1
    ,
     arg2
    ,
    ...
    )
    ;
    // Don't deploy this contract if it has already been deployed

    deployer
    .
    deploy
    (
    A
    ,
    {
    overwrite
    :
    false
    }
    )
    ;
    // Set a maximum amount of gas and `from` address for the deployment

    deployer
    .
    deploy
    (
    A
    ,
    {
    gas
    :
    4612388
    ,
    from
    :
    "0x...."
    }
    )
    ;
    // Deploy multiple contracts, some with arguments and some without.
    // This is quicker than writing three `deployer.deploy()` statements as the deployer
    // can perform the deployment as a single batched request.

    deployer
    .
    deploy
    (
    [
    [
    A
    ,
     arg1
    ,
     arg2
    ,
    ...
    ]
    ,
    B
    ,
    [
    C
    ,
     arg1
    ]
    ]
    )
    ;
    // External dependency example:
    //
    // For this example, our dependency provides an address when we're deploying to the
    // live network, but not for any other networks like testing and development.
    // When we're deploying to the live network we want it to use that address, but in
    // testing and development we need to deploy a version of our own. Instead of writing
    // a bunch of conditionals, we can simply use the `overwrite` key.

    deployer
    .
    deploy
    (
    SomeDependency
    ,
    {
    overwrite
    :
    false
    }
    )
    ;

### deployer.link（图书馆，目的地）

将已部署的库链接到合同或多个合同。`destinations`可以是单个合同，也可以是多个合同的数组。如果目的地内的任何合同不依赖于链接的库，则合同将被忽略。

例：

```
// Deploy library LibA, then link LibA to contract B, then deploy B.

deployer
.
deploy
(
LibA
)
;

deployer
.
link
(
LibA
,
B
)
;

deployer
.
deploy
(
B
)
;
// Link LibA to many contracts

deployer
.
link
(
LibA
,
[
B
,
C
,
D
]
)
;
```

### deployer.then（function（）{...}）

就像承诺一样，运行任意部署步骤。使用此选项可在迁移期间调用特定的合同函数，以添加，编辑和重新组织合同数据。

例：

    var
     a
    ,
     b
    ;

    deployer
    .
    then
    (
    function
    (
    )
    {
    // Create a new version of A
    return
    A
    .
    new
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
    instance
    )
    {

      a 
    =
     instance
    ;
    // Get the deployed instance of B
    return
    B
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
    instance
    )
    {

      b 
    =
     instance
    ;
    // Set the new instance of A's address on B via B's setA() function.
    return
     b
    .
    setA
    (
    运行迁移
    迁移是JavaScript文件，可帮助您将合同部署到以太坊网络。这些文件负责暂存您的部署任务，并且假设您的部署需求会随着时间的推移而改变。随着项目的发展，您将创建新的迁移脚本，以进一步推动区块链的发展。先前运行的迁移历史记录通过特殊Migrations合同在链上记录，详情如下。

    命令
    要运行迁移，请运行以下命令：

    $ truffle migrate
    这将运行位于项目migrations目录中的所有迁移。最简单的迁移只是一组托管部署脚本。如果先前成功运行了迁移，truffle migrate则将从上次运行的迁移开始执行，仅运行新创建的迁移。如果不存在新的迁移，truffle migrate则根本不执行任何操作。您可以使用该--reset选项从头开始运行所有迁移。对于本地测试，请确保在执行之前安装并运行了一个测试区块链，例如Ganachemigrate。

    迁移文件
    一个简单的迁移文件如下所示：

    文件名： 4_example_migration.js

    var MyContract = artifacts.require("MyContract");

    module.exports = function(deployer) {
      // deployment steps
      deployer.deploy(MyContract);
    };
    请注意，文件名以数字为前缀，后缀为描述。编号前缀是必需的，以便记录迁移是否成功运行。后缀纯粹是为了人类的可读性和理解力。

    artifacts.require（）
    在迁移开始时，我们告诉Truffle我们想要通过该artifacts.require()方法与哪些合同进行交互。这个方法类似于Node require，但在我们的例子中，它特别返回了一个契约抽象，我们可以在其余的部署脚本中使用它。指定的名称应与该源文件中的合同定义的名称匹配。不传递源文件的名称，因为文件可以包含多个合同。

    考虑这个示例，其中在同一源文件中指定了两个合同：

    文件名： ./contracts/Contracts.sol


    contract ContractOne {
      // ...
    }

    contract ContractTwo {
      // ...
    }
    要仅使用ContractTwo，您的artifacts.require()语句将如下所示：

    var ContractTwo = artifacts.require("ContractTwo");
    要使用这两个合同，您需要两个artifacts.require()语句：

    var ContractOne = artifacts.require("ContractOne");
    var ContractTwo = artifacts.require("ContractTwo");
    module.exports
    所有迁移都必须通过module.exports语法导出函数。每次迁移导出的函数都应接受一个deployer对象作为其第一个参数。此对象通过为部署智能合约提供清晰的语法以及执行某些部署更普通的职责（例如保存已部署的工件以供以后使用）来帮助部署。该deployer对象是用于暂存部署任务的主界面，其API在本页底部描述。

    您的迁移功能也可以接受其他参数。请参阅以下示例。

    初始迁移
    Truffle要求您拥有迁移合同才能使用迁移功能。此合同必须包含特定的界面，但您可以随意编辑此合同。对于大多数项目，此合同最初将作为第一次迁移进行部署，不会再次更新。在创建新项目时，您也会默认收到此合同truffle init。

    文件名： contracts/Migrations.sol

    pragma solidity ^0.4.8;

    contract Migrations {
      address public owner;

      // A function with the signature `last_completed_migration()`, returning a uint, is required.
      uint public last_completed_migration;

      modifier restricted() {
        if (msg.sender == owner) _;
      }

      function Migrations() {
        owner = msg.sender;
      }

      // A function with the signature `setCompleted(uint)` is required.
      function setCompleted(uint completed) restricted {
        last_completed_migration = completed;
      }

      function upgrade(address new_address) restricted {
        Migrations upgraded = Migrations(new_address);
        upgraded.setCompleted(last_completed_migration);
      }
    }
    您必须在第一次迁移中部署此合同才能利用迁移功能。为此，请创建以下迁移：

    文件名： migrations/1_initial_migration.js

    var Migrations = artifacts.require("Migrations");

    module.exports = function(deployer) {
      // Deploy the Migrations contract as our only task
      deployer.deploy(Migrations);
    };
    从此处，您可以使用增加的编号前缀创建新迁移，以部署其他合同并执行进一步的部署步骤。

    部署
    您的迁移文件将使用部署程序来部署部署任务。因此，您可以同步编写部署任务，它们将以正确的顺序执行：

    // Stage deploying A before B
    deployer.deploy(A);
    deployer.deploy(B);
    或者，部署程序上的每个函数都可以用作Promise，以排队依赖于前一个任务执行的部署任务：

    // Deploy A, then deploy B, passing in A's newly deployed address
    deployer.deploy(A).then(function() {
      return deployer.deploy(B, A.address);
    });
    如果您发现语法更清晰，则可以将部署编写为单个承诺链。部署API将在本页底部讨论。

    网络考虑因素
    可以根据部署到的网络有条件地运行部署步骤。这是一项高级功能，因此请在继续之前先查看“ 网络”部分。

    要有条件地暂存部署步骤，请编写迁移，以便它们接受第二个参数，称为network。例：

    module.exports = function(deployer, network) {
      if (network == "live") {
        // Do something specific to the network named "live".
      } else {
        // Perform a different step otherwise.
      }
    }
    可用帐户
    迁移也会通过您的以太坊客户端和Web3提供商提供给您的帐户列表，供您在部署期间使用。这是从中返回的完全相同的帐户列表web3.eth.getAccounts()。

    module.exports = function(deployer, network, accounts) {
      // Use the accounts within your migrations.
    }
    部署者API
    部署程序包含许多可用于简化迁移的功能。

    deployer.deploy（contract，args ...，options）
    使用可选的构造函数参数部署由合同对象指定的特定合同。这对单例合约很有用，因此dapp只存在此合约的一个实例。这将在部署后设置合同的地址（即，Contract.address将等于新部署的地址），并且它将覆盖存储的任何先前地址。

    您可以选择传递一组合同或一组数组，以加快多个合同的部署。此外，最后一个参数是一个可选对象，可以包含名称overwrite以及其他事务参数（如gas和）from。如果overwrite设置为false，则部署者将不会部署此合同（如果已经部署了该合同）。这对于由外部依赖项提供合同地址的某些情况很有用。

    请注意，在调用之前，您需要首先部署和链接合同所依赖的任何库deploy。有关link详细信息，请参阅下面的功能。

    有关更多信息，请参阅松露合同文档。

    例子：

    // Deploy a single contract without constructor arguments
    deployer.deploy(A);

    // Deploy a single contract with constructor arguments
    deployer.deploy(A, arg1, arg2, ...);

    // Don't deploy this contract if it has already been deployed
    deployer.deploy(A, {overwrite: false});

    // Set a maximum amount of gas and `from` address for the deployment
    deployer.deploy(A, {gas: 4612388, from: "0x...."});

    // Deploy multiple contracts, some with arguments and some without.
    // This is quicker than writing three `deployer.deploy()` statements as the deployer
    // can perform the deployment as a single batched request.
    deployer.deploy([
      [A, arg1, arg2, ...],
      B,
      [C, arg1]
    ]);

    // External dependency example:
    //
    // For this example, our dependency provides an address when we're deploying to the
    // live network, but not for any other networks like testing and development.
    // When we're deploying to the live network we want it to use that address, but in
    // testing and development we need to deploy a version of our own. Instead of writing
    // a bunch of conditionals, we can simply use the `overwrite` key.
    deployer.deploy(SomeDependency, {overwrite: false});
    deployer.link（图书馆，目的地）
    将已部署的库链接到合同或多个合同。destinations可以是单个合同，也可以是多个合同的数组。如果目的地内的任何合同不依赖于链接的库，则合同将被忽略。

    例：

    // Deploy library LibA, then link LibA to contract B, then deploy B.
    deployer.deploy(LibA);
    deployer.link(LibA, B);
    deployer.deploy(B);

    // Link LibA to many contracts
    deployer.link(LibA, [B, C, D]);
    deployer.then（function（）{...}）
    就像承诺一样，运行任意部署步骤。使用此选项可在迁移期间调用特定的合同函数，以添加，编辑和重新组织合同数据。

    例：

    var a, b;
    deployer.then(function() {
      // Create a new version of A
      return A.new();
    }).then(function(instance) {
      a = instance;
      // Get the deployed instance of B
      return B.deployed();
    }).then(function(instance) {
      b = instance;
      // Set the new instance of A's address on B via B's setA() function.
      return b.setA(a.address);
    });

    address
    )
    ;
    }
    )
    ;

  


