# 编译合约

## 地点

所有合约都位于项目的`contracts/`目录中。由于合同是用[Solidity](https://solidity.readthedocs.io/en/develop/)编写的，所有包含合同的文件的文件扩展名都是`.sol`。Associated Solidity[库](http://solidity.readthedocs.org/en/latest/contracts.html#libraries)也有`.sol`扩展名。

使用一个空的的Truffle[项目](https://truffleframework.com/docs/truffle/quickstart)（通过创建`truffle init`），您将获得一个`Migrations.sol`有助于部署过程的文件。如果您使用的是truffle box，那么您将拥有多个文件。

## 命令

要编译Truffle项目，请切换到项目所在目录的根目录，然后在终端中键入以下内容：

```
truffle compile
```

首次运行时，将编译所有合同。在后续运行中，Truffle将仅编译自上次编译以来已更改的合约。如果您要覆盖重新编译，请使用`truffle compile --all`命令。

## 构建工件

编译的工件将放在`build/contracts/`相对于项目根目录的目录中。（如果该目录不存在，将创建该目录。）

这些工件是Truffle内部工作的组成部分，它们在成功部署应用程序中起着重要作用。**您不应编辑这些文件，**因为它们将被合约编译和部署覆盖。

## 依赖

您可以使用Solidity的[import](http://solidity.readthedocs.org/en/latest/layout-of-source-files.html#importing-other-source-files)命令声明合约依赖项。Truffle将以正确的顺序编译合同，并确保将所有依赖项发送给编译器。可以通过两种方式指定依赖关系：

### 通过文件名导入依赖项

要从单独的文件导入合同，请将以下代码添加到Solidity源文件中：

```
import "./AnotherContract.sol";

```

这将使所有合同都在`AnotherContract.sol`可用范围内。这里，`AnotherContract.sol`是相对于正在编写的当前合同的路径。

请注意，Solidity也允许其他导入语法。有关详细信息，请参阅Solidity[导入文档](http://solidity.readthedocs.org/en/latest/layout-of-source-files.html#importing-other-source-files)。

### 从外部包导入合同

Truffle支持通过[EthPM](https://truffleframework.com/docs/truffle/getting-started/package-management-via-ethpm)和[NPM](https://truffleframework.com/docs/truffle/getting-started/package-management-via-npm)安装的依赖项。要从依赖项导入合同，请使用以下语法

```
import "somepackage/SomeContract.sol";

```

这里，`somepackage`表示通过EthPM或NPM安装的包，表示该包提供的Solidity`SomeContract.sol`源文件。

请注意，在搜索从NPM安装的软件包之前，Truffle将首先从EthPM搜索已安装的软件包，因此在极少数情况下会出现命名冲突，将使用通过EthPM安装的软件包。

有关如何使用Truffle软件包管理功能的更多信息，请参阅Truffle[EthPM](https://truffleframework.com/docs/truffle/getting-started/package-management-via-ethpm)和[NPM](https://truffleframework.com/docs/truffle/getting-started/package-management-via-npm)文档。

