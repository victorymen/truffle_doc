# 使用BUILD PIPELINE

Truffle 1.0和2.0标配了一个主要面向Web应用程序的默认构建系统（这里，术语“构建”意味着将代码工件转换为HTML，Javascript和CSS）。该构建系统已被引入其[自己的模块，](https://github.com/trufflesuite/truffle-default-builder/tree/master)以使Truffle可用于各种应用程序并可扩展。

Truffle可以配置为与任何构建系统紧密集成。要配置自定义[生成](https://truffleframework.com/docs/advanced/build_processes)系统，请参阅“[高级生成过程”](https://truffleframework.com/docs/advanced/build_processes)部分以获取更多详细信息。

## 命令

要在配置构建系统时构建应用程序，请运行：

```
$ truffle build
```

请注意，如果在`build`未先配置自定义生成过程的情况下尝试运行该命令，则会收到错误。

