# 构建流程

为了能够为那些需要它的人提供与Truffle的紧密集成，Truffle允许您指定用于引导和配置应用程序的自定义构建管道。truffle提供三种整合方法，如下所述。

## 运行外部命令

如果你想让Truffle在触发构建时运行外部命令，只需将该选项作为字符串包含在项目配置中，如下所示：

    module
    .
    exports
    =
    {
    // This will run the `webpack` command on each build.
    //
    // The following environment variables will be set when running the command:
    // WORKING_DIRECTORY: root location of the project
    // BUILD_DESTINATION_DIRECTORY: expected destination of built assets (important for `truffle serve`)
    // BUILD_CONTRACTS_DIRECTORY: root location of your build contract files (.sol.js)
    //

      build
    :
    "webpack"
    }

请注意，您将获得足够的环境变量来与Truffle集成，如上所述。

## 提供自定义功能

您还可以提供如下所示的自定义构建功能。请注意，您可以获得有关项目的大量信息，您可以使用这些信息与Truffle紧密集成。

    module
    .
    exports
    =
    {

      build
    :
    function
    (
    options
    ,
     callback
    )
    {
    // Do something when a build is required. `options` contains these values:
    //
    // working_directory: root location of the project
    // contracts_directory: root directory of .sol files
    // destination_directory: directory where truffle expects the built assets (important for `truffle serve`)
    }
    }

## 创建自定义模块

您还可以创建一个实现构建器接口的模块或对象（即，是一个包含上述`build`功能的对象）。对于那些希望与Truffle保持更紧密集成并发布包以让其他人的生活更轻松的人来说，这非常有用。

以下是使用Truffle默认构建器的示例：

```
var
 DefaultBuilder
=
require
(
"truffle-default-builder"
)
;

module
.
exports
=
{

  build
:
new
DefaultBuilder
(
...
)
// specify the default builder configuration here.
}
```

## 引导您的应用程序

无论您是构建要在浏览器中运行的应用程序，还是命令行工具，Javascript库或本机移动应用程序，引导您的合约都是相同的，并且使用您部署的合约工件遵循相同的一般过程，无论应用程序如何你正在建设。

配置构建工具或应用程序时，您需要执行以下步骤：

1）将所有合约工件存入构建管道/应用程序。这包括目录中的所有`.json`文件`./build/contracts`。

2）`.json`通过[truffle合约](https://github.com/trufflesuite/truffle-contract)将这些合约工件转换为易于使用的合约抽象。

3）使用Web3提供程序提供这些合约抽象。在浏览器中，此提供程序可能来自[Metamask](https://metamask.io/)或[Mist](https://github.com/ethereum/mist)，但它也可能是您配置为指向[Infura](http://infura.io/)或任何其他以太坊客户端的自定义提供程序。

4）使用你的合约！

在Node中，这很容易做到。让我们看一个展示执行上述步骤的“最纯粹”方式的示例，因为它存在于任何构建过程或工具之外。

```
// Step 1: Get a contract into my application
var
 json
=
require
(
"./build/contracts/MyContract.json"
)
;
// Step 2: Turn that contract into an abstraction I can use
var
 contract
=
require
(
"truffle-contract"
)
;
var
 MyContract
=
contract
(
json
)
;
// Step 3: Provision the contract with a web3 provider

MyContract
.
setProvider
(
new
Web3
.
providers
.
HttpProvider
(
"http://127.0.0.1:8545"
)
)
;
// Step 4: Use the contract!

MyContract
.
deployed
(
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
someFunction
(
)
;
}
)
;
```

所有构建过程和合约引导都将遵循这种模式。设置自己的自定义构建过程时的关键是确保您正在使用所有合约工件并正确配置抽象。
