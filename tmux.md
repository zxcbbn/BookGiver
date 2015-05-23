#### 简介
Tmux（"Terminal Multiplexer"的简称）可以让我们在单个屏幕的灵活布局下开出很多终端，我们就可以协作地使用它们。

#### 安装
- mac
>brew install tmux

- ubuntu
>sudo apt-get install tmux

#### 使用
$ tmux   进入会话
> 需要先按下 Ctrl+B 才能执行其他的tmux命令

Ctrl+B + "c"，建立一个新的会话

当前所在的会话，在窗口下方标了星号

##### 多会话切换
1. Ctrl+B + 对应会话号

2. Ctrl+B + "w"，然后选择相应的会话号

3. Ctrl+B + "f"，回车，列出所有窗口和面板数量，选择相应的会话号
```
(0)  0: [tmux] [144x40] (3 panes)
(1)  1: bash [144x40] (1 panes)
(2)  2: bash [144x40] (1 panes)
```

##### 一个会话，多面板
- Ctrl+B 后在按下 Shift+%，将当前会话窗口竖直分割
- Ctrl+B 后在按下 Shift+“ ，将当前会话窗口水平分割
- Ctrl+B+方向键，切换面板

- Ctrl+B，并且不要松开Ctrl，使用方向键调整窗格大小

##### 退出
exit 退出当前 会话 & 面板

##### 会话的脱离和恢复
Ctrl+B 后接着按 "d" 。将脱离所有的会话返回原来的终端屏幕。
 
$ tmux attach  恢复会话

#### 帮助
Ctrl+B+ ? 来查看所有支持的命令

$ man tmux   查看手册，获得详细的使用命令

##### 修改快捷键
命令行下绑定和解绑快捷方式
```
$ tmux unbind C-b        #解绑ctrl+b的快捷键
$ tmux set -g prefix C-x    #绑定ctrl+x的快捷键
```
