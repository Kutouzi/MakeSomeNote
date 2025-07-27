## linux运维笔记--多会话

### screen

screen是一种多会话技术，可以做到中断会话不杀掉正在运行的进程，这对ssh进行远程连接的会话非常有用

用的时候需要

```bash
/*开启一个screen会话，之后进入screen下的一个bash子会话*/
screen
/*起别名*/
screen -S <会话名>
```

使用ctrl+a+d中断会话，可以随时返回对话

如需要返回则

```bash
/*显示所有中断的会话*/
screen -ls
/*进入中断会话*/
screen -r <会话id/会话名>
```

如果需要结束某会话

```
/*结束指定会话*/
screen -S <会话id/会话名> -X quit
```

### tmux

比较新的一个多会话

地址在[tmux](https://github.com/tmux/tmux/)也可以自己下

安装

```bash
sudo yum install -y tmux
```

命令使用

```bash
tmux                # 新建会话
tmux new -s myname  # 新建名为 myname 的会话
tmux ls             # 列出会话
tmux attach -t myname  # 连接到名为 myname 的会话
tmux kill-session -t myname  # 删除名为 myname 的会话
tmux detach         # 从当前会话分离（回到普通 shell）
```

快捷键，使用是先按Ctrl+b，然后再输入&这个字符，分两步完成不要一直按Ctrl+b

```bash
Ctrl+b c           # 新建窗口
Ctrl+b w           # 查看所有窗口
Ctrl+b ,           # 重命名窗口
Ctrl+b d		   # detach当前会话
Ctrl+b &           # 删除当前会话
Ctrl+b x           # 删除当前会话
Ctrl+b n/p         # 下一个/上一个窗口
Ctrl+b 0~9         # 切到指定编号窗口
```

