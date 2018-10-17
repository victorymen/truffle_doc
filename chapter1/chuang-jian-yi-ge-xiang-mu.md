# 创建项目

要使用大多数Truffle命令，您需要针对现有的Truffle项目运行它们。所以第一步是创建一个松露项目。

您可以创建一个裸项目模板，但对于那些刚开始使用的模板，您可以使用[Truffle Boxes](https://truffleframework.com/boxes)，它是示例应用程序和项目模板。我们将使用[MetaCoin](https://truffleframework.com/boxes/metacoin) box，它创建一个可以在帐户之间转移的令牌：

1. 为您的Truffle项目创建一个新目录：

   ```
   mkdir MetaCoin
   cd MetaCoin
   ```

2. 下载（“unbox”）MetaCoin框：

   ```
   truffle unbox metacoin
   ```

   **注意**：您可以使用该`truffle unbox <box-name>`命令下载任何其他truffle box。

   **注意**：要创建一个没有包含智能合约的空的truffle项目，请使用`truffle init`。

完成此操作后，您现在将拥有包含以下项目的项目结构：

* `contracts/`：[Solidity](https://truffleframework.com/docs/truffle/getting-started/interacting-with-your-contracts)合约目录
* `migrations/`：[可编写脚本的部署文件的目录](https://truffleframework.com/docs/truffle/getting-started/running-migrations#migration-files)
* `test/`：用于[测试应用程序和合同的](https://truffleframework.com/docs/truffle/testing/testing-your-contracts)测试文件的目录
* `truffle.js`：truffle[配置文件](https://truffleframework.com/docs/truffle/reference/configuration)



