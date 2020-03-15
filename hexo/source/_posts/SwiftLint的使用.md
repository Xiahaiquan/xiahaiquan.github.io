---
title: SwiftLint的使用
date: 2019-09-10 16:05:01
tags: 第三方的使用
---

# 简介

- 为了促进 iOS 项目小组各成员之间高效的合作。 使整个软件开发工作可以协调有序地进行。 iOS项目采用 Swift 语言开发时，项目可以使用 SwiftLint 第三方工具规范代码风格。
- [SwiftLint](https://github.com/realm/SwiftLint)是 [Realm](https://realm.io/) 推出的一款 Swift 代码规范检查工具, SwiftLint 基于 [Github 公布的 Swift 代码规范](https://github.com/Artwalk/swift-style-guide/blob/master/README_CN.md)进行代码检查。
<!-- more -->

# 安装

## 使用 Homebrew 安装

全局安装，一次安装，所有项目可用。

- 在终端输入以前命令进行安装：`brew install swiftlint`

![屏幕快照 2019-09-10 14.41.28](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUserSwiftLint/1-2.png)

- 验证安装成功: `swiftlint help`

![屏幕快照 2019-09-10 14.42.07](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUserSwiftLint/1-3.png)



## 使用Cocoapods安装

- 在Podfile文件中添加如下代码：`pod 'SwiftLint' `
- cd到项目根目录下： `Pod install`
- 安装成后打开 XX.xcworkspace 文件即可。



# 用法

在Xcode中写入Shell脚本

![RUNOOB 图片](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUserSwiftLint/1-1.png "位置指引")

1. 使用Homebrew安装后要执行脚本代码：

```shell
if which swiftlint >/dev/null; then
swiftlint
else
echo "warning: SwiftLint not installed, download from https://github.com/realm/SwiftLint"
fi
```



2. 使用Cocoapods安装后要执行的脚本代码

```shell
"${PODS_ROOT}/SwiftLint/swiftlint"
```

# 配置

## 创建配置文件

cd到项目根目录下,  在终端输入： `touch .swiftlint.yml` 创建配置文件。执行完该命令后,  在文件夹中可能找不到该yml格式文件, 那是因为文件被隐藏了。使用 command + shift + . 显示（隐藏）文件。

```tex
disabled_rules: # 执行时排除掉的规则
- colon #冒号
- comma #逗号
- control_statement #控制声明
opt_in_rules: # 一些规则仅仅是可选的
- empty_count
- missing_docs
# 可以通过执行如下指令来查找所有可用的规则:
# swiftlint rules
included: # 执行 linting 时包含的路径。如果出现这个 `--path` 会被忽略。
- Source # 要确保项目中有这个文件夹
excluded: # 执行 linting 时忽略的路径。 优先级比 `included` 更高。
- Carthage
- Pods
- Source/ExcludedFolder
- Source/ExcludedFile.swift

# 可配置的规则可以通过这个配置文件来自定义
# 二进制规则可以设置他们的严格程度
force_cast: warning # 隐式
force_try:
severity: warning # 显式
# 同时有警告和错误等级的规则，可以只设置它的警告等级
# 隐式
line_length: 110
# 可以通过一个数组同时进行隐式设置
type_body_length:
- 300 # warning
- 400 # error
# 或者也可以同时进行显式设置
file_length:
warning: 500
error: 1200
# 命名规则可以设置最小长度和最大程度的警告/错误
# 此外它们也可以设置排除在外的名字
type_name:
min_length: 4 # 只是警告
max_length: # 警告和错误
warning: 40
error: 50
excluded: iPhone # 排除某个名字
identifier_name:
min_length: # 只有最小长度
error: 4 # 只有错误
excluded: # 排除某些名字
- id
- URL
- GlobalAPIKey
reporter: "xcode" # 报告类型 (xcode, json, csv, checkstyle, junit, html, emoji)

```

这是官方给出的配置。把它复制到项目的配置文件中，可能会报如下错误：

![屏幕快照 2019-09-10 15.15.13](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUserSwiftLint/1-4.png)

报错原因是：项目中不存在Source文件夹，在配置文件中删除- Source再command + b就不会有错误了。

## 在代码中关闭某个规则

- 可以通过在一个源文件中定义一个如下格式的注释来关闭某个规则：

`// swiftlint:disable <rule>`

在该文件结束之前或者在定义如下格式的匹配注释之前，这条规则都会被禁用：

`// swiftlint:enable <rule>`

例如新建项目AppDelegate中默认的方法名长度都超过SwiftLint官方给出的方法长度。

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
// Override point for customization after application launch.
return true
}
```

对这部分代码关闭长度限制前提示：行数不应超过110个字符：当前为145个字符（line_length）

![屏幕快照 2019-09-10 15.34.05](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUserSwiftLint/1-5.png)

对这部分代码关闭长度限制后无提示

![屏幕快照 2019-09-10 15.35.15](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/HowToUserSwiftLint/1-6.png)

- 若要对文件中关闭所有的规则，可使用以下代码对：

  `// swiftlint:disable all`

  `// swiftlint:enable all`

- 可以通过添加 :previous, :this 或者 :next 来使关闭或者打开某条规则的命令分别应用于前一行，当前或者后一行代码。

```swift
// swiftlint:disable:next force_cast
let noWarning = NSNumber() as! Int
let hasWarning = NSNumber() as! Int
let noWarning2 = NSNumber() as! Int // swiftlint:disable:this force_cast
let noWarning3 = NSNumber() as! Int
// swiftlint:disable:previous force_cast
```

## 忽略引入的第三方库

- 忽略CocoaPods导入的第三方库，excluded 配置项用来设置忽略代码规范检查的路径，可以指定整个文件夹,比如如果你的项目使用 Carthage 管理第三方库的话，可以将 Carthage 目录添加到忽略列表：

```
excluded:
- Pods
- Carthage
```

- 指定精确路径下的文件，通过 - xxxx 的形式列在下面就可以了

```excluded:
excluded: # 执行 linting 时忽略的路径。 优先级比 `included` 更高。
- Source/ExcludedFolder
- Source/ExcludedFile.swift
```

# 自动更正（慎用）

`SwiftLint`可以自动修正某些错误，磁盘上的文件会被一个修正后的版本覆盖。

确保在对文件执行 `swiftlint autocorrect`之前有对它们做过备份，否则的话有可能导致重要数据的丢失。

因为在执行自动更正只是更正代码规则，无法检测逻辑，在更正的时候会有可能代码逻辑，带来更多问题 ！

# 其他

经测试，当项目中有OC代码时，SwiftLint对其无约束规范的提示。