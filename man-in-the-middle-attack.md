# Man-in-the-middle attack

### 事情的起因

2018-09-10星期一，一上班发现iTerm2连不上开发的服务器了，顿时觉得很奇怪，自己最近并没有改过pem文件，然后把这件事情告诉了老大，老大就开始在server端排查原因。先贴出具体报错的截图。

![](https://blog-private.oss-cn-shanghai.aliyuncs.com/20200104172240.png)

通过报错能很明显的看出是提示存在man-in-the-middle攻击的风险，会出现这个错误的原因是第一次SSH连接服务器的时候客户端会存下服务端的信息（存在~/.ssh/known_hosts的文件），如果客户端和服务端校验失败就会出现上面截图的错误。那么问题来了，为什么我们用SSH连接服务器时会出现上述问题呢？

整个过程时这样的：（1）远程主机收到用户的登录请求，把自己的公钥发给用户。（2）用户使用这个公钥，将登录密码加密后，发送回来。（3）远程主机用自己的私钥，解密登录密码，如果密码正确就允许用户登录。

现在就来解释下我们为什么连不上服务器，原因是我们的服务器做过一次扩容，扩容后客户端连接服务器的时候发现服务器与之前的不一样，就警告用户有可能被劫持了，所以就直接拒绝连接，我们的解决方法就是执行`ssh-keygen -R remote_ip`这样本地就会更新~/.ssh/known_host文件，这样就可以直接连接了。

其实这里我有个疑问，那理论上第一次连接服务器也可能被劫持呀，不过话又说回来，SSH对于这种被劫持也只能尽可能的去避免吧。

### 什么是Man-in-the-Middle(MITM) attack?

中间人攻击(Wiki解释)：在密码学和计算机安全领域中，是指攻击者与通讯的两端分别创建独立的联系，并交换其所收到的数据，使通讯的两端认为他们正在通过一个私密的连接与对方直接对话，但事实上整个会话都被攻击者完全控制。在中间人攻击中，攻击者可以拦截通讯双方的通话并插入新的内容。

中间人攻击是一种“间接”的入侵攻击



