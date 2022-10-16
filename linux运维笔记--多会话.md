## linux运维笔记--多会话

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

