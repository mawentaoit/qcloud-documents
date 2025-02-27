## 操作场景

TBaaS 提供了智能合约 IDE 可视化编辑器，方便用户一站式编写及调试智能合约。本文档指导用户使用智能合约 IDE。
>? 智能合约 IDE 可视化编辑器目前仅支持使用 Go 语言。

## 前提条件

已登录 [TBaaS 控制台](https://console.cloud.tencent.com/tbaas)。

## 操作步骤

### 打开在线编辑器

1. 在左侧导航栏中，选择 “区块链网络”，进入 “区块链网络” 页面。
2. 选择待编辑或调试智能合约的 “区块链网络”，进入 “区块链网络” 信息页面。
3. 选择 “合约管理” 页签，单击【在线编辑器】。如下图所示：
![](https://main.qcloudimg.com/raw/b96ad84421906cef57d85290a14d0580.png)

### 编译代码

1. 在 “在线编辑器” 页面中，根据实际需求，编辑代码。
2. 单击【检查代码】，检查编辑的代码是否正确。
 - 是，执行下一步。
 - 否，根据实际检查结果进行处理。
3. 在右侧 “使用指引” 中，单击【编译】，查看编译结果。如下图所示：
![](https://main.qcloudimg.com/raw/792dd69c8f7df25b466bd48e581a6728.png)

### 实例化智能合约

1. 在右侧 “使用指引” 的 “调用参数” 中，输入调用初始化参数，单击【调用】，初始化智能合约。如下图所示：
例如，初始化合约，a、b 账户分别初始金额100、200，则在 “调用参数” 中输入 ["init","a","100","b","200"]，单击【调用】即可在运行框中查看初始化结果。
![](https://main.qcloudimg.com/raw/3ebe4431e1c286d1ba1caf35583b53dd.png)
2. 在右侧 “使用指引” 的 “调用参数” 中，根据调用相关智能合约的处理逻辑，输入调用参数，单击【调用】，实现业务调用。如下图所示：
例如，调用合约，a 账户转账金额10至 b 账户，则在 “调用参数” 中输入 ["invoke","a","b","10"]，单击【调用】即可在运行框中查看调用结果。
![](https://main.qcloudimg.com/raw/a7ca870d25b6d138d53de24a4d97c293.png)
3. 在右侧 “使用指引” 中，输入 “查询参数”，单击【查询】，查询链上结果。如下图所示：
![](https://main.qcloudimg.com/raw/6cd756019016f40a4e22c944e879326c.png)


