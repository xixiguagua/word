
假设ping www.baidu.com 首先我们需要dns协议，将网址转为IP地址，

其次ping的原理是使用ICMP的回响机制，所以必然用到了网络层的ICMP协议，

最后在到达对方局域网的时候需要使用RARP查找mac地址，在发送主机不知道自己IP的时候也会用到ARP协议，

所以综上ping的过程中用到了dns协议、ICMP协议、ARP协议、RARP协议。
