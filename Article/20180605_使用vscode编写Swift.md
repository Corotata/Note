title: 使用vscode编写Swift
date: 2018年 6月 5日 星期二 11时35分07秒 CST
tags: 
---

由于Playground的使用体验是越来越差了，经常菊花常在，所以有时为了写个Demo，特别是无UI的，总要创建个App工程，将代码写在ViewController来运行的作法实现难受，后面发现了VSCode可以用来写Swift代码，也就来尝试一下。

####安装swiftenv

[swiftenv](https://swiftenv.fuller.li/en/latest/)是Swift版本的管理器，可以从他官方指南中安装，这边使用其中的一种方式brew安装：

##### 安装：

```bash
brew install kylef/formulae/swiftenv

echo 'if which swiftenv > /dev/null; then eval "$(swiftenv init -)"; fi' >> ~/.bash_profile
echo 'if which swiftenv > /dev/null; then eval "$(swiftenv init -)"; fi' >> ~/.zshrc
echo 'if which swiftenv > /dev/null; status --is-interactive; and source (swiftenv init -|psub); end' >> ~/.config/fish/config.fish

```

##### 删除：

```bash
rm -fr ~/.swiftenv
```

安装完后，你可以使用，你可以使用*swiftenv versions*查看当前版本使用的swift版本，以下是我电脑的版本

```bash
swiftenv versions

----------- 输出 -----------
* system
  4.1
```

使用*swiftenv --version*,可以查看当前swiftenv的版本号，更多使用可以通过*swiftenv --help*进行查看，这边就暂不展开来讲了。

```bash
swiftenv --version

----------- 输出 -----------
swiftenv 1.3.0
```

#### 安装sourcekite

sourcekite 是[Swift Development Environment](https://github.com/jinmingjian/sde)用来和 Swift 的 SourceKit 打交道的，可以为 VSCode 提供 Swift 的语法分析。

以下为安装方式：


```
git clone https://github.com/jinmingjian/sourcekite.git

cd sourcekite

* [ ] swift build -Xswiftc -framework -Xswiftc sourcekitd -Xswiftc -F -Xswiftc /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib -Xlinker -rpath -Xlinker  /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib
```


