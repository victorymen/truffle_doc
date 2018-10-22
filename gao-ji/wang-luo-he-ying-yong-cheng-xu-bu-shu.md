# 网络和应用程序部署

即使是最小的项目也会在至少两个区块链节点上进行交互：一个在开发人员的机器上，如[Ganache](https://truffleframework.com/ganache)或Truffle Develop，另一个代表开发人员最终将部署其应用程序的网络（例如主要的公共以太网网络或例如，私人财团网络。Truffle提供了一个系统，用于管理每个网络的编译和部署工件，并以简化最终应用程序部署的方式实现。

## 组态

有关更多信息，请参阅“[配置”](https://truffleframework.com/docs/truffle/reference/configuration#networks)部分

## 指定网络

大多数Truffle命令的行为将根据指定的网络而有所不同，并将使用该网络的合同和配置。您可以使用该`--network`选项指定网络，如下所示：

```
$ truffle migrate --network live
```

在此示例中，Truffle将在“实时”网络上运行您的迁移，如果像[示例](https://truffleframework.com/docs/truffle/reference/configuration#networks)一样配置[，](https://truffleframework.com/docs/truffle/reference/configuration#networks)则与公共以太坊区块链相关联。

## 构建工件

如[编译合同](https://truffleframework.com/docs/truffle/getting-started/compiling-contracts)部分所述，构建工件`./build/contracts`作为`.json`文件存储在目录中。当您编译合同或使用特定网络运行迁移时，Truffle将更新这些`.json`文件，使其包含与该网络相关的信息。当稍后使用这些工件时 - 例如通过[松露合同](https://github.com/trufflesuite/truffle-contract)在您的前端或应用程序中- 它们将自动检测以太坊客户端连接到哪个网络并相应地使用正确的合同工件。

## 应用部署

由于合同工件在运行时会自动检测到网络，这意味着您只需要部署_一次_应用程序或前端。当您运行应用程序时，正在运行的以太坊客户端将确定使用哪些工件，这将使您的应用程序非常灵活。例如，如果您要将Web应用程序部署到[http://mydapp.io](http://mydapp.io/)，您可以使用您喜欢的钱包浏览器（如MetaMask或Mist）导航到该地址，并且无论以太坊如何，您的dapp都能正常工作钱包浏览器连接到的网络。如果钱包浏览器已连接到实时网络，则您的dapp将使用您在实时网络上部署的合同。如果在Ropsten上，将使用您部署到Ropsten的合同。

  


