# 编写外部脚本

通常，您可能希望运行与合同交互的外部脚本。Truffle提供了一种简单的方法，可以根据您所需的网络引导您的合同，并根据您的[项目配置](https://truffleframework.com/docs/advanced/configuration)自动连接到您的以太坊客户端。

## 命令

要运行外部脚本，请执行以下操作：

```
$ truffle exec 
```

为了使外部脚本能够正确运行，Truffle希望它们能够导出一个函数，该函数将单个参数作为回调：

```
module.exports = function(callback) {
  // perform actions
}
```

只要在脚本完成时调用回调，您就可以在此脚本中执行任何操作。回调接受错误作为其第一个也是唯一的参数。如果提供了错误，则执行将暂停，并且进程将返回非零退出代码。

