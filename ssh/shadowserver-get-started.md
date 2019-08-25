# 配置sssserver

## 购买主机

各种平台都可以购买，不过我们要的是梯子，所以只能购买墙外了的。当然距离最近时延迟越低，所以我选🇭🇰。

## 购买公网IP

需要公网地址客户端的请求才能到达云主机。根据情况购买。我是白嫖公司的，所以100M按流量收费。一个字：爽！

## 创建sshkey

在平台上下载sshkey，并绑定主机，通过 ssh 登录主机。`ssh -i pathofsshkey root@ip`。

## 下载 ssh 程序

[`pip install shadowsocks`](https://cloud.tencent.com/developer/article/1159683)

## 配置 ssh 服务器

vi  /etc/shadowsocks.json

```
{
    "server":"0.0.0.0",
    "server_port":9000, // whatever you want 
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"rc4-md5"
}
```

## 开启服务并导出日志

`ssserver -c '上一步你保存的配置文件' -d --log-file LOG_FILE`

ssserver --help 自己看吧

## 配置防火墙

现在主流的云资源提供商，都会提供防火墙功能，所以需要在云平台上找到云主机的防火墙，并开放我们配置是的端口。根据上文就是9000。

## 坑

当然这东西就是容易出现奇奇怪怪的问题。我有遇到了配置完没有生效的问题。
当遇到问题的时候，第一步永远是看日志。上面的log-file地址中就能拿到日志。我在日志中看到了unkownhost的报错。很明显直接定位DNS问题。/etc/resolv.conf 配置这个文件后，restart一遍之后搞定。




