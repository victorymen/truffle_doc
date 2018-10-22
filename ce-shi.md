# 测试你的合同

## 骨架

Truffle标配自动化测试框架，使您的合同测试变得轻而易举。此框架允许您以两种不同的方式编写简单且可管理的测试：

* 在
  [Javascript中](https://truffleframework.com/docs/getting_started/javascript-tests)
  ，用于运行来自外部世界的合同，就像您的应用程序一样。
* 在
  [Solidity中](https://truffleframework.com/docs/getting_started/solidity-tests)
  ，用于在先进的，裸露到金属的场景中执行合同。

两种类型的测试都有其优点和缺点。有关每一部分的讨论，请参阅接下来的两节。

## 地点

所有测试文件都应位于`./test`目录中。松露将只与下列文件扩展名的运行测试文件：`.js`，`.es`，`.es6`，和`.jsx`和`.sol`。所有其他文件都将被忽略。

## 命令

要运行所有测试，只需运行：

```
$ truffle test

```

或者，您可以指定要运行的特定文件的路径，例如，

```
$ truffle test ./path/to/test/file.js

```

## 洁净室环境

Truffle在运行测试文件时提供洁净室环境。在针对[Ganache](https://truffleframework.com/ganache)或Truffle Develop运行测试时，Truffle将使用高级快照功能来确保您的测试文件不会彼此共享状态。当与[go-ethereum](https://github.com/ethereum/go-ethereum)等其他Ethereum客户端运行时，Truffle将在每个测试文件的开头重新部署所有迁移，以确保您有一组新的合同可供测试。

## 速度和可靠性考虑

在运行自动化测试时，Ganache和Truffle Develop都比其他客户快得多。此外，它们还包含特殊功能，Truffle利用这些功能将测试运行时间提高了近90％。作为一般工作流程，我们建议您在正常开发和测试期间使用Ganache或Truffle Develop，然后在准备部署到实时或生产网络时，针对go-ethereum或其他官方Ethereum客户端运行测试一次。

  


