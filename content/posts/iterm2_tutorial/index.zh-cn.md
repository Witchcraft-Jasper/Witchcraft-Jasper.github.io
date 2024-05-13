+++
title = 'Iterm2快捷键速查'
date = 2024-05-06T16:25:35+08:00
draft = false
tags = ["Mac", "查询"]
+++


## 1. 光标控制

| 快捷键    | 描述                       |
| --------- | -------------------------- |
| ⌃ + a     | 到行首                     |
| ⌃ + e     | 行末                       |
| ⌃ + f/b   | 前进后退，相当于左右方向键 |
| ⌃ + p     | 上一条命令，相当于方向键上 |
| ⌃ + r     | 搜索命令历史               |
| ⌃ + d     | 删除当前字符               |
| ⌃ + h     | 删除之前的字符             |
| ⌃ + w     | 删除光标前的单词           |
| ⌃ + k     | 删除到文本末尾             |
| ⌃ + t     | 交换光标处文本             |
| ⌘ + —/+/0 | 调整字体大小               |
| ⌘ + r     | 清屏                       |
| ⌃ + l     | 清屏（滚动到新的一屏）     |


## 2. 终端操作快捷键
### 2.1 窗口面板管理：一个窗口分割

| 快捷键                                   | 描述                        |
| ---------------------------------------- | --------------------------- |
| 垂直分割                                 | ⌘ + D                       |
| 水平分割                                 | ⇧ + ⌘ + D                   |
| 前一个面板                               | ⌘ + [ 或 ⌥ + ⌘ + 左右方向键 |
| 后一个面板                               | ⌘ + ]                       |
| 切换到上/下/左/右面板                    | ⌥ + ⌘ + 上下左右方向键      |
| 关闭panel                                | ⌘ + w                       |
| 最大化Tab中的pane，隐藏本Tab中的其他pane | ⌘+ ⇧ +enter                 |

### 2.2 新建Tab标签页

| 快捷键           | 描述                    |
| ---------------- | ----------------------- |
| 新建标签页       | ⌘ + T                   |
| 关闭标签页       | ⌘ + W                   |
| 前一个标签页     | ⌘ + 左方向键，⇧ + ⌘ + [ |
| 后一个标签页     | ⌘ + 右方向键，⇧ + ⌘ + ] |
| 进入标签页       | ⌘ + 标签页编号          |
| Expose 标签页    | ⌥ + ⌘ + E               |
| 快速定位光标位置 | ⌘ + /                   |



### 2.3 多个窗口操作

| 快捷键     | 描述         |
| ---------- | ------------ |
| 新建窗口   | ⌘ + N        |
| 关闭窗口   | ⌘ + w        |
| 前一个窗口 | ⌘ + `        |
| 后一个窗口 | ⇧ + ⌘ + `    |
| 进入窗口   | ⌥ + ⌘ + 编号 |

 

### 2.4 其他功能

- 支持自定义全局快捷键用于显示和隐藏iTerm2 Preference -> Keys －> Show/hide iTerm2 with a system-wide hotkey 打上勾之后
| 快捷键              | 描述                           |
| ------------------- | ------------------------------ |
| 进入和退出全屏      | ⌘ + Enter                      |
| 查看光标位置        | ⌘ + /                          |
| 命令自动补全        | ⌘ + ;（较少使用，Zsh补全更佳） |
| 开启/关闭背景半透明 | ⌘ + u                          |
| 清屏                | ⌘ + r                          |

#### 2.4.1 粘贴历史

使用⌘ + ⇧ + h 可以呼出粘贴历史，支持模糊检索。还可以设置将粘贴历史保存在磁盘上（Preferences -> General）

#### 2.4.2 即时回放 

使用⌘ + Opt + b 打开即时回放，按Esc退出。即时回放可以记录终端输出的状态，让你“穿越时间”查看终端内容。默认每个会话最多储存4MB的内容，可以在设置中更改（Preferences -> Genernal -> Instant Replay）。

### 2.5 自定义快捷键设置

#### 2.5.1设置光标按照单词快速移动 

iTerm2之后，发现⌥+←和⌥+→这两组快捷键并不能实现光标按照单词快速移动，

在Mac自带的终端中是可以使用这两个快捷键的，经过查找和测试发现，需要重新配置相应的映射。iTerm2中将这两组快捷键用作了其他的功能。

- 打开iTerm2的Preferences设置
- 选择相应的Profile（默认为Default），选择“Keys”选项卡，然后可以在Key Mappings看到⌥+←和⌥+→这两组快捷键用作了其他功能，这里我们只需要重新绑定新的映射即可
- 找到⌥+← ， 双击就会弹出一个编辑框，选择Action为“Send Escape Sequence”, 然后在 Esc+选项中输入`b`
- 找到⌥+→， 双击就会弹出一个编辑框，选择Action为“Send Escape Sequence”, 然后在 Esc+选项中输入`f`

修改映射之后，再重新启动iTerm2就可以使用⌥+←和⌥+→来实现光标按单词快速移动了，非常方便。如果不想使用⌥+←和⌥+→这两个组合键的话，也可以自行添加新的Shortcut即可。

## 3. 个性化的设置

### 3.1 设置无分栏边框

preference->appearance：

取消：Show per-pane title bar with split panes

勾选：Hide scrollbars

### 3.2 终端显示边框

preference->appearance：

勾选：Show border around window

### 3.2 显示、隐藏终端快捷键

Preferences -> Keys -> Hotkey:

勾选： Show/hide iTerm2 with a system-wide hotkey

然后在下面的Hotkey里面填上F12，

这个F12可能被系统占用了，这修改系统的快捷键，或更换别的快捷键。

### 3.3 鼠标移动切换窗口

当一个终端打开了多个窗口，可以设置鼠标移动窗口时获取焦点

Preferences -> Pointer:

勾选：Focus follows mouse

### 3.4 设置光标形状

Preferences -> Profiles -> Profile Name -> Text -> Cursor

选择一个自己喜欢的样式

### 3.5 新窗口打开的路径跟当前终端的路径一直

Preferences -> Profiles -> General -> Working Directory ->  reuse previous session's directory



### 3.6 新窗口在电脑获取焦点的窗口生成

Preferences -> Profiles -> General -> Window -> Settings for New Windows -> Screen 

选择：No Preference

或者选择：Screen with Cursor（在鼠标的窗口）

### 3.7 设置开机启动不显示界面

1、打开系统设置(System Perferences) -> 用户与群组(User & groups) -> 登陆项(Login Items)， 点击加号，然后去应用程序里面找到Iterm2添加到启动项里面。
2、启动iterm2程序，打开一个窗口，然后手动点击关闭按钮，确保电脑右上角苹果图标右边是iTerm2,然后
按【⌘】+【⇧】+【S】，保存名为No Window
3、打开一个iterm2终端界面，然后按`cmd + ,`打开iterm的设置界面，选择`Arrangements`，点击刚才保存的`No Window`， 然后点击`Set Default`。
4、在iterm的设置界面选择`General`, 在`Startup`中选择`Open Default Window Arrangement`。


## 参考资料

[iterm2使用快捷键](https://www.jianshu.com/p/da7728a8a4d7)

[关于iTerm2 你不知道的一些事](https://www.jianshu.com/p/3436bcb17a03)

[Mac下iTerm2光标按照单词快速移动设置](https://blog.csdn.net/skyyws/article/details/78480132?locationNum=4&fps=1)

[打造有情怀的 iTerm2 终端](https://www.jianshu.com/p/83c38271b09c)


