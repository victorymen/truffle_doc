# truffle box

## 发布一个truffle box

要创建一个truffle box，你需要做一些事情：

* 创建一个GitHub repository

* 配置文件
* 可选：box的列表里的小图像和大图像

配置文件和图像属于repository的顶级目录。有了这些文件，你的repository在GitHub上unbox命令将是：`truffle unbox {USER_NAME || ORG_NAME}/{REPO_NAME}`如果您从头开始，我们已经创建了一个[蓝图框，](https://truffleframework.com/boxes/blueprint)其中包含具有常用值的配置文件，以及小图像和大图像的模板版本。如果您正在使用现有项目，请[在此处下载文件](https://truffleframework.com/files/truffle-box-essentials.zip)并将其提取到项目中。

## 配置文件

所有truffle box都包含配置文件`truffle-box.json`。该文件有三个属性：`ignore`，`commands`，和`hooks`。

### `ignore`（数组）

你想让Truffle在unbox时忽略的一系列文件或相对路径。常见文件包括`readme.md`或`.gitignore`。

```
"ignore": [
  "README.md",
  ".gitignore"
]
```

### `commands`（object）

键/值对的对象分别是描述符和控制台命令。一旦您的box成功unbox，这些配对将显示给用户。将这些视为快速说明。

例如，请考虑下面的对象。我们已经为用户提供了编译，迁移和测试智能合约所需的一切，以及开发前端的命令。

```
"commands": {
  "Compile": "truffle compile",
  "Migrate": "truffle migrate",
  "Test contracts": "truffle test",
  "Test dapp": "npm test",
  "Run dev server": "npm run start",
  "Build for production": "npm run build"
}
```

### `hooks`（object）

包含要在未装箱后执行的控制台命令的对象。由于我们在Node.js中工作，因此最常见的是`npm install`。

```
"hooks": {
  "post-unpack": "npm install"
}
```

## 图片

大盒子图像是512px正方形，每边有32px的填充。

![](https://truffleframework.com/img/boxes/box-img-lg-template.png "大盒子图象模板")

小盒子图像是735px x 100px。横幅左侧有32px的填充，并且它的最小尺寸（在模板中描述为“Min Banner Width”）为290px，包括左边的填充。当窗口变窄时，横幅固定在左侧并从右侧裁剪。

![](https://truffleframework.com/img/boxes/box-img-sm-template.png "小盒子图象模板")

如果框中没有提供图像，我们默认使用包含Truffle徽标的横幅：

![](https://truffleframework.com/img/boxes/loading-thumb.png "默认大图")

![](https://truffleframework.com/img/boxes/loading-banner.png "默认小图像")

## Truffle网站上市

在被列入Truffle网站之前，所有盒子都经过筛选过程以确保与Truffle的兼容性。要开始预筛选流程，[请向我们发送一封电子邮件，](mailto:info@trufflesuite.com)其中包含您所需的包装箱名称和简要说明，以及其GitHub回购链接：[info@trufflesuite.com](mailto:info@trufflesuite.com)。

