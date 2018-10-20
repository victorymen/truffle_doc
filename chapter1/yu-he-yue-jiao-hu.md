# 与合约交互

## 介绍

如果您为了与合约进行交互而自己向以太坊网络写入原始请求，您很快就会意识到编写这些请求是笨重而繁琐的。同样，您可能会发现管理您所做的每个请求的状态都很_复杂_。幸运的是，Truffle为您处理这种复杂性，使您与合同的互动变得轻而易举。

## 读写数据

以太坊网络数据写入网络和读取数据的有着很大的区别，这种区别在编写应用程序的方式中起着重要作用。通常，写入数据称为交易**，**而读取数据称为调用。交易和调用的处理方式截然不同，具有以下特征。

### 交易

交易从根本上改变了网络的状态。交易可以像将Ether发送到另一个帐户一样简单，也可以像执行合约功能或向网络添加新合约一样复杂。交易的定义特征是它写入（或更改）数据。交易耗费以太运行，称为“天然气”，交易需要时间来处理。当您通过交易执行合约的功能时，您无法接收该函数的返回值，因为交易不会立即处理。通常，意图通过交易执行的函数不会返回值;他们将返回一个交易ID。总而言之，交易：

* 消耗gas（以太）
* 更改网络状态
* 不立即处理
* 不会公开返回值（只是一个事务ID）。

### 调用

另一方面，调用是非常不同的。可以使用调用在网络上执行代码，但不会永久更改任何数据。调用可以自由运行，它们的定义特征是它们可以读取数据。当您通过调用执行合约功能时，您将立即收到返回值。总之，调用：

* 免费（不用gas）
* 不会改变网络状态
* 立即处理
* 会暴露一个回报值（万岁！）

在事务和调用之间进行选择就像决定是要读取数据还是写入数据一样简单。

## 介绍抽象

合约抽象是从Javascript与以太坊合同交互的基础和黄油。简而言之，合同抽象是一种包装代码，可以轻松地与您的合同进行交互，从而让您忘记在引擎盖下执行的许多引擎和齿轮。松露通过[松露合同](https://github.com/trufflesuite/truffle-contract)模块使用自己的合同抽象，这是下面描述的合同抽象。

然而，为了理解合同抽象的有用性，我们首先需要谈谈合同。我们将通过Truffle Boxes使用您可以使用的MetaCoin合约`truffle unbox metacoin`。

```
pragma solidity ^0.4.2;

import "./ConvertLib.sol";

// This is just a simple example of a coin-like contract.
// It is not standards compatible and cannot be expected to talk to other
// coin/token contracts. If you want to create a standards-compliant
// token, see: https://github.com/ConsenSys/Tokens. Cheers!

contract MetaCoin {
    mapping (address => uint) balances;

    event Transfer(address indexed _from, address indexed _to, uint256 _value);

    function MetaCoin() {
        balances[tx.origin] = 10000;
    }

    function sendCoin(address receiver, uint amount) returns(bool sufficient) {
        if (balances[msg.sender] < amount) return false;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        Transfer(msg.sender, receiver, amount);
        return true;
    }

    function getBalanceInEth(address addr) returns(uint){
        return ConvertLib.convert(getBalance(addr),2);
    }

    function getBalance(address addr) returns(uint) {
        return balances[addr];
    }
}
```

本合同有三种方法除了构造函数（`sendCoin`，`getBalanceInEth`，和`getBalance`）。所有这三种方法都可以作为事务或调用来执行。

现在让我们看看`MetaCoin`Truffle为我们提供的Javascript对象，如[Truffle控制台中提供的](https://truffleframework.com/docs/truffle/getting-started/using-truffle-develop-and-the-console)：

```
// Print the deployed version of MetaCoin.
// Note that getting the deployed version requires a promise, hence the .then.
MetaCoin.deployed().then(function(instance) {
  console.log(instance);
});

// outputs:
//
// Contract
// - address: "0xa9f441a487754e6b27ba044a5a8eb2eec77f6b92"
// - allEvents: ()
// - getBalance: ()
// - getBalanceInEth: ()
// - sendCoin: ()
// ...
```

请注意，抽象包含与合同中完全相同的函数。它还包含一个指向MetaCoin合同部署版本的地址。

## 执行合同职能

使用抽象，您可以轻松地在以太坊网络上执行合同功能。

### 进行交易

我们可以执行MetaCoin合约上的三个函数。如果您分析它们中的每一个，您将看到这`sendCoin`是唯一一个旨在对网络进行更改的功能。目标`sendCoin`是从一个帐户“发送”一些META币到下一个帐户，这些变化应该持续存在。

在打电话时`sendCoin`，我们会将其作为交易执行。在下面的示例中，我们将以一种持续更改网络的方式从一个帐户向另一个帐户发送10META币：

```
var account_one = "0x1234..."; // an address
var account_two = "0xabcd..."; // another address

var meta;
MetaCoin.deployed().then(function(instance) {
  meta = instance;
  return meta.sendCoin(account_two, 10, {from: account_one});
}).then(function(result) {
  // If this callback is called, the transaction was successfully processed.
  alert("Transaction successful!")
}).catch(function(e) {
  // There was an error! Handle it.
})
```

以上代码有一些有趣的事情：

* 我们
  `sendCoin`
  直接
  调用了抽象
  函数。
  这将导致默认情况下的事务（即写入数据）而不是调用。
* 当事务成功时，在处理事务之前不会触发回调函数。
  这使生活变得简单，这意味着您不必自己检查交易状态。
* 我们将一个对象作为第三个参数传递给
  `sendCoin`
  。
  请注意，
  `sendCoin`
  我们的Solidity合同中
  的
  函数没有第三个参数。
  您在上面看到的是一个特殊对象，它始终可以作为最后一个参数传递给函数，该函数允许您编辑有关事务的特定详细信息。
  在这里，我们设置了
  `from`
  确保此交易来自
  的
  地址
  `account_one`
  。

### 打个电话

继续使用MetaCoin，请注意该`getBalance`功能是从网络读取数据的理想选择。它不需要进行任何更改，因为它只返回传递给它的地址的MetaCoin余额。我们来试一试：

```
var account_one = "0x1234..."; // an address

var meta;
MetaCoin.deployed().then(function(instance) {
  meta = instance;
  return meta.getBalance.call(account_one, {from: account_one});
}).then(function(balance) {
  // If this callback is called, the call was successfully executed.
  // Note that this returns immediately without any waiting.
  // Let's print the return value.
  console.log(balance.toNumber());
}).catch(function(e) {
  // There was an error! Handle it.
})
```

这里有趣的是：

* 我们必须
  `.call()`
  明确地
  执行该
  功能，让以太坊网络知道我们不打算坚持任何改变。
* 我们在成功时收到了返回值而不是事务ID。
  请注意，由于以太坊网络可以处理非常大的数字，我们会得到一个
  [BigNumber](https://github.com/MikeMcl/bignumber.js/)
  对象，然后我们将其转换为数字。

**警告：**我们将返回值转换为数字，因为在此示例中数字很小。但是，如果您尝试转换大于Javascript支持的最大整数的BigNumber，您可能会遇到错误或意外行为。

### 抓住事件

您的合同可以触发您可以捕获的事件，以便更深入地了解您的合同正在做什么。处理事件的最简单方法是处理触发事件的事务的结果对象，如下所示：

```
var account_one = "0x1234..."; // an address
var account_two = "0xabcd..."; // another address

var meta;
MetaCoin.deployed().then(function(instance) {
  meta = instance;  
  return meta.sendCoin(account_two, 10, {from: account_one});
}).then(function(result) {
  // result is an object with the following values:
  //
  // result.tx      => transaction hash, string
  // result.logs    => array of decoded events that were triggered within this transaction
  // result.receipt => transaction receipt object, which includes gas used

  // We can loop through result.logs to see if we triggered the Transfer event.
  for (var i = 0; i < result.logs.length; i++) {
    var log = result.logs[i];

    if (log.event == "Transfer") {
      // We found the event!
      break;
    }
  }
}).catch(function(err) {
  // There was an error! Handle it.
});
```

### 处理交易结果

当您进行交易时，您将获得一个`result`对象，该对象为您提供有关交易的大量信息。具体来说，您将获得以下内容：

* `result.tx`
  _（字符串）_
  * 事务哈希
* `result.logs`
  _（数组）_
  * 解码事件（日志）
* `result.receipt`
  _（对象）_
  * 交易收据

欲了解更多信息，请参阅[README](https://github.com/trufflesuite/truffle-contract)中`truffle-contract`的项目。

### 添加新合约到网络

在上述所有情况中，我们一直在使用已经部署的合同抽象。我们可以使用以下`.new()`功能将我们自己的版本部署到网络：

```
MetaCoin.new().then(function(instance) {
  // Print the new address
  console.log(instance.address);
}).catch(function(err) {
  // There was an error! Handle it.
});
```

### 在特定地址使用合约

如果您已经拥有合同的地址，则可以创建新的抽象来表示该地址的合同。

```
var instance = MetaCoin.at("0x1234...");
```

### 将以太发送到合约中

您可能只想将Ether直接发送给合同，或触发合同的[后备功能](http://solidity.readthedocs.io/en/develop/contracts.html#fallback-function)。您可以使用以下两个选项之一来执行此操作。

选项1：通过直接向合同发送交易`instance.sendTransaction()`。这与所有可用的合同实例函数一样被保证，并且具有与`web3.eth.sendTransaction`没有回调相同的API。`to`如果未指定，将自动为您填写该值。

```
instance.sendTransaction({...}).then(function(result) {
  // Same transaction result object as above.
});
```

选项2：还可以直接发送以太网的简写：

```
instance.send(web3.toWei(1, "ether")).then(function(result) {
  // Same result object as above.
});
```

## 进一步阅读

Truffle提供的合同抽象包含大量公用事业，可以轻松地与您的合同进行交互。查看[松露合同](https://github.com/trufflesuite/truffle-contract)文档，了解提示，技巧和见解。

