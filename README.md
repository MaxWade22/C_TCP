# C-TCP-用C语言实现TCP单进程服务器和客户端

一，环境配置

Linux：Ubuntu 22.04

gcc：11.3.0

# 一，单进程服务器和单进程客户端

## 1，内容

1，操作人员在命令行窗口输入一行字符并回车后，客户端进程立刻从命令行(本质即stdin）读取数据，并将该行信息发送给服务器。
2，服务器收到该行信息后，会将该信息原封不动的返回给客户端，即所谓消息回声(Message Echo)。

3，客户端收到服务器返回的消息回声后，将其打印输出至屏幕(本质即stdout ) 。
4，客户端在从命令行收到EXIT指令后退出。
5，在启动1个客户端连接上服务器开展交互时，再启动另一个客户端连接服务器。

## 2，流程

![img](https://img-blog.csdnimg.cn/15e0bec3b99a4ca5af751802b508f04c.png)

## 3，编译说明

首先编译服务端（server.c)为可执行文件server.o

```shell
gcc -o server.o server.c
```

初始化服务端(127.0.0.1为ip地址，12345为端口号，8888为验证码)

```shell
 ./server.o 127.0.0.1 12345 8888 
```

编译客户端(client.c)为可执行文件client.o

```shell
gcc -o client.o client.c
```

客户端连接至服务器

```shell
./client.o 127.0.0.1 12345
```

## 4,输入输出样例

客户端

```
[cli] server[127.0.0.1:12345] is connected!
hello,world!
[ECH_RQT]hello,world!
[ECH_REP](8888)hello,world!
this is Max speaking.
[ECH_RQT]this is Max speaking.
[ECH_REP](8888)this is Max speaking.
inkqonciorwemciojurtbvlanjkx
[ECH_RQT]inkqonciorwemciojurtbvlanjkx
[ECH_REP](8888)inkqonciorwemciojurtbvlanjkx
EXIT
[cli]listenfd is closed!
[cli]server is to return! 
```

服务端

```
[cli] server[127.0.0.1:12345] is connected!
hello,world!
[ECH_RQT]hello,world!
[ECH_REP](8888)hello,world!
this is Max speaking.
[ECH_RQT]this is Max speaking.
[ECH_REP](8888)this is Max speaking.
inkqonciorwemciojurtbvlanjkx
[ECH_RQT]inkqonciorwemciojurtbvlanjkx
[ECH_REP](8888)inkqonciorwemciojurtbvlanjkx
EXIT
[cli]listenfd is closed!
[cli]server is to return! 
```



# 二，单进程服务器和多进程客户端

## 1,内容

编写TCP多进程循环服务器程序与单进程客户端程序，实现以下主体功能:。客户端启动连接服务器之后，进入命令行交互模式。

- 操作人员在命令行窗口输入一行字符并回车后，客户端进程立刻从命令行(本质即stdin)）读取数据，并将该行信息发送给服务器。
- 服务器收到该行信息后，会将该信息原封不动的返回给客户端，即所谓消息回声(Message Echo)。客户端收到服务器返回的消息回声后，将其打印输出至屏幕(本质即stdout )。
- 客户端在从命令行收到EXIT指令后退出。
- 若服务器启动时设定Established Queue的长度，即listen()第二个参数backlog为2，则最多可以有2个客户端同时连上服务器并开展交互，此时，再启动另一个客户端连接服务器，观察体验是什么现象，并尝试分析现象背后的底层逻辑。
  

## 2，编译说明

首先编译服务端（server.c)为可执行文件server.o

```shell
gcc -o server.o server.c
```

初始化服务端(127.0.0.1为ip地址，12345为端口号，8888为验证码)

```shell
 ./server.o 127.0.0.1 12345 8888 
```

编译客户端(client.c)为可执行文件client.o

```shell
gcc -o client.o client.c
```

实例客户端1连接至服务器

```shell
./client.o 127.0.0.1 12345 8888
```

实例客户端2连接至服务器

```shell
./client.o 127.0.0.1 12345 8888
```





## 3,输入输出样例

服务端

```
[srv](480)[srv_sa](127.0.0.1:12345)[vcd](8888) Server has initialize!
[srv](480) [cli_sa][127.0.0.1:39034] Client is accepted!
[chd](562)[ppid](480) Child process is created!
[srv](480) [cli_sa][127.0.0.1:39036] Client is accepted!
[chd](628)[ppid](480) Child process is created!
[chd](562)[cid](8888)[ECH_RQT] hello,this is client1!
[chd](628)[cid](8888)[ECH_RQT] hello,this is client2!
[chd](562)[cid](8888)[ECH_RQT] I love coding!
[chd](628)[cid](8888)[ECH_RQT] I don't love coding!
[chd](628)[ppid](480)[cli_sa](127.0.0.1:39036) Client is closed!
[chd](628)[ppid](480) connfd is closed!
[chd](628)[ppid](480) Child process is to return!
[chd](562)[ppid](480)[cli_sa](127.0.0.1:39034) Client is closed!
[chd](562)[ppid](480) connfd is closed!
[chd](562)[ppid](480) Child process is to return!
```

客户端1

```
[cli](561)[srv_sa](127.0.0.1:12345)Server is connected!
hello,this is client1!
[cli](561)[cid](8888)[ECH_RQT] hello,this is client1!
[cli](561)[vcd](8888)[ECH_REP] hello,this is client1!
I love coding!
[cli](561)[cid](8888)[ECH_RQT] I love coding!
[cli](561)[vcd](8888)[ECH_REP] I love coding!
EXIT
[cli](561)[cid](8888)[ECH_RQT] EXIT
[cli](561) connf is closed!
[cli](561) Client is to return! 
```

客户端2

```
[cli](561)[srv_sa](127.0.0.1:12345)Server is connected!
hello,this is client1!
[cli](561)[cid](8888)[ECH_RQT] hello,this is client1!
[cli](561)[vcd](8888)[ECH_REP] hello,this is client1!
I love coding!
[cli](561)[cid](8888)[ECH_RQT] I love coding!
[cli](561)[vcd](8888)[ECH_REP] I love coding!
EXIT
[cli](561)[cid](8888)[ECH_RQT] EXIT
[cli](561) connf is closed!
[cli](561) Client is to return! 
```

