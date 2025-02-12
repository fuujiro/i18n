# Electron 版本管理

> 详细查看我们的版本控制策略和实现。

从版本 2.0.0, Electron 遵循 [ semver ](#semver)。以下命令将安装 Electron 最新稳定的版本:

```sh
npm install --save-dev electron
```

现有项目更新到最新的稳定版本:

```sh
npm install --save-dev electron@latest
```

## 版本1.x

小于 * 2.0*的Electron版本编号并不遵循 [semver](http://semver.org) 规范: major版本对应最终用户API的变更, minor版本更新对应Chromium的主版本更新, patch 版本更新会带来新功能和bug修复. 虽然方便开发人员合并功能，但却为面向客户端应用程序的开发人员带来了麻烦。 像Slack，Stride，Teams，Skype，VS Code，Atom和Desktop等主要应用程序的QA测试周期可能很长，稳定性是一个非常理想的结果。 尝试吸收错误修复时，采用新功能的风险很高。

以下是 1.x 策略的一个例子：

![](../images/versioning-sketch-0.png)

使用 `1.8.1`开发的应用程序无法吸收 `1.8.2 ` 的功能，或者通过反向移植修复和维护新的发行版，无法采用 `1.8.3`错误修复。

## 版本 2.0 和之后版本

下面列出的 1.x 策略有几个主要的变化。 每个更改都是为了满足开发人员/维护人员和应用程序开发人员的需求和优先级。

1. 严格使用 semver
2. 引入符合 semver 的 `-beta` 标签
3. 引入[常规提交消息](https://conventionalcommits.org/)
4. 明确定义的稳定分支
5. `master`分支没有版本信息，只有稳定分支会包含版本信息。

我们将详细介绍 git 分支是如何工作的，npm 标记是如何工作的，开发人员应该看到什么，以及如何能够支持更改。

# semver

从 2.0 开始，Electron 将遵循 semver。

下面是一个表格，明确地将变化的类型映射到它们对应的 semver 类别 (例如Major，Minor，Patch)。

| Major 版本增量          | Minor 版本增量           | Patch 版本增量         |
| ------------------- | -------------------- | ------------------ |
| Electron 突破性 API 变更 | Electron 无突破性 API 变更 | Electron bug 修复    |
| Node.js 重大版本更新      | Node.js 次要版本更新       | Node.js patch 版本更新 |
| Chromium 版本更新       |                      | 修复相关的 chromium 补丁  |

请注意，大多数 Chromium 更新将被视为突破性更新。 可以从新版被向后移植的修复可能会被挑选出来作为补丁修复现有版本。

# 稳定分支

稳定分支是与主控并行运行的分支，仅接受与安全性或稳定性有关的最优提交。 这些分支从不合并回主分支。

![](../images/versioning-sketch-1.png)

稳定分支始终是 **major** 或 **minor** 版本, 并按照以下模板命名`$MAJOR-$MINOR-x`.例如 `2-0-x`.

我们允许同时存在多个稳定分支，并且打算在任何时候至少支持两个并行支持安全修复。 ![](../images/versioning-sketch-2.png)

GitHub不支持旧线路，但是其他分组可以自行获取所有权和返回稳定性和安全修复。 我们不鼓励这样做，但是认识到它使得许多应用程序开发人员的生活更轻松。

# 测试版和 Bug 修复

开发人员想知道哪个版本可以 *安全* 使用。 即使是简单的功能也会使应用程序变得复杂。 同时，锁定到一个固定的版本是很危险的，因为你忽略了自你的版本以来可能出现的安全补丁和错误修复。 我们的目标是在 `package.json ` 中允许以下标准的 semver 范围:

- 使用 ` ~ 2.0. 0 ` 只接受您的 ` 2.0.0 ` 版本的稳定性或安全性相关的修复程序。
- 使用 ` ^ 2.0. 0 ` 可允许不破坏性的 * 合理稳定 * 功能以及安全性和 bug 修复。

第二点重要的是使用 `^` 的应用程序仍然能够期望合理的稳定性水平。 为了达到这个目的，semver允许一个 *pre-release 标识* 来表示一个特定的版本还不 *安全* 或 *稳定*.

无论你选择什么，你将定期不得不在 `package.json` 中打破版本，因为突破性变更是 Chromium 的一个常态。

过程如下:

1. 所有新的主要和次要的发布线首先使用一个测试系列，通过半排放前标签显示 `beta.N`例如： `2.0.0-beta.1`- 在第一次测试之后，随后的测试版必须符合以下所有条件： 
    1. 更改是落后的 API 兼容 (允许废弃)
    2. 实现我们稳定的时间表的危险必须是低的。
2. 如果允许更改需要在释放测试版之后进行，则使用并增加预放标签，例如`2.0.0-beta.2`。
3. 如果特定的beta版本*通常被认为*是稳定的，那么它将作为稳定版本被重新发布，只改变版本信息。例如.0。 例如 `2.0.0-beta.1`. 在第一个稳定之后，所有的变化都必须落后兼容的 bug 或安全修复。
4. 如果未来错误修复或安全补丁一旦发布稳定，它们将被应用，并且 *补丁* 版本被增量 ，例如 `2.0.1`。

特别地，上述步骤意味着：

1. 在测试周期的第3周前允许不打破的 API 更改非常好，即使这些变化有可能造成适度的副影响
2. 保存标记功能更改，否则不改变现有代码路径，在测试周期的大部分点里是可用的。用户可以在其应用中明确启用这些标记。
3. Admitting features of any sort after Week 3 in the beta cycle is 

对于每个主要和次要的颠覆，你都应该像以下示例一样进行操作：

```text
2.0.0-beta.1
2.0.0-beta.2
2.0.0-beta.3
2.0.0
2.0.1
2.0.2
```

图片中的生命周期示例:

- 将创建一个新的发布分支, 其中包括最新的一组功能。它被发布为 ` 2.0. 0-beta 1 `。 ![](../images/versioning-sketch-3.png)
- Bug 修复进入主分支，即反向移植（backport）到发布分支中。补丁程序运行，新的测试版发布为 ` 2.0. 0-beta 2 `。 ![](../images/versioning-sketch-4.png)
- 测试版被认为是 * 一般稳定 * 的, 它在 ` 2.0.0 ` 下作为非 beta 版本再次被发布。 ![](../images/versioning-sketch-5.png)
- 之后，释放一个零日漏洞，同时将修复应用于主分支（用以检测修复效果）。 我们将补丁程序反向移植到 `2-0-x `版本序列中，发布` 2.0.1 `。 ![](../images/versioning-sketch-6.png)

几个不同的 semver 范围将如何接收新版本的示例:

![](../images/versioning-sketch-7.png)

# 缺失的特性: alpha版本

我们的战略有几次权衡，我们现在认为这是适当的。 最重要的是, 新的主分支特性可能需要一段时间才能作为稳定版发布。 如果你想立即尝试一个新的特性, 你必须自己编译Electron 。

作为未来的考虑, 我们可以介绍以下一种或两种情况:

- 具有松散稳定性限制的 alpha 释放版; 例如, 当稳定通道在 * alpha * 中时, 允许接纳新特性

# 功能标志

功能标志是 Chromium 的一种常见的做法, 在网络开发生态系统中得到了很好的确立。 在 Electron 环境中, 功能标志或 ** 软分支 ** 必须具有以下属性:

- 是在运行时或生成时启用/禁用的。我们不支持请求作用域功能标志的概念
- 它完全细分新的和旧的代码路径; 重构旧代码以允许新功能 * 违反 * 功能标志内容
- 在合并功能后, 功能标志最终将被删除

# 提交语义

我们力求在更新和发布过程的各个层面提高清晰度。 从 ` 2.0.0 ` 开始, 我们将要求遵循 [ 常规提交 ](https://conventionalcommits.org/) 规范的拉请求, 可以概括如下:

- 会导致 semver **major** 版本改变的提交必须以`BREAKING CHANGE:`开头。
- 会导致 semver **minor** 版本改变的提交必须以 `feat:` 开头。
- 会导致 semver ** patch ** 版本改变的提交必须以 ` fix:` 开头。

- 我们允许合并提交，只要合并提交的消息符合上述消息格式。

- 只要pull request里包含有意义的总结性的版本语义消息，即使它其中的某些提交消息不包含版本语义前缀也是可以接受的

# 打了版本的 `主分支`

- The `master` 分支将始终在其 `package.json` 中包含 `0.0.0-dev`.
- Release 分支永远不会合并回 master 分支
- 发布分支 *在* 其`package.json ` 中包含正确的版本
- 只要一个 release 分支被切出来用于发布 major 版本, 主分支就必须跳到下一个 major 版本. 也就是说`master` 总会被视作下一个理论上要发布的版本的分支。