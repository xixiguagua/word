TCP的报文头有一个长度可变的选项部分，也就是说TCP的报文头的长度是不固定的，

无论是TCP建立连接还是释放连接都是有两部分组成的，客户端和服务器端，
客户端是主动打开建立连接的一方，主动关闭客户端和服务器端都可以。

建立连接：客户端最初的状态是CLOSED状态，发送建立连接的请求报文段，请求报文段中SYN为1，seq（序列号）假设为x，发送完成之后客户端的状态由CLOSED转为SYN_SENT状态，服务器端最初状态是CLOSED状态，接着转为LISTEN状态。

服务器端由CLOSED状态转为LISTEN状态是由服务器自己决定的，并不是说，服务器端在客户端发送请求报文段的时候同步转为LISTEN状态，实际上服务器端一般要先将CLOSED状态转为LISTEN状态。

服务器端收到客户端的请求报文段之后，会发送一个确认报文段，同时服务器的状态也有LISTEN转为SYN_RCVD状态，确认报文段的ACK为1，SYN为1，seq假设为y，ack（序列号确认号）为x+1，这里的ack是指服务器端期望客户端发送的下一个序列号值，等到客户端接收到确认报文段之后，状态就成了ESTABLISHED状态，并发送确认报文段，ACK为1，seq为x+1，ack为y+1；

客户端的状态转换为：CLOSED->SYN_SENT->ESTABLISHED->FIN_WAIT1->FIN_WAIT2->TIME_WAIT->CLOSED;

服务器端的状态转换：CLOSED->LISTEN->SYN_RCVD->ESTABLISHED->CLOSED_WAIT->LAST_ACK->CLOSED;

释放连接：客户端发送释放连接报文段，其中FIN为1，seq假设为u，客户端也由ESTABLISHED转为FIN_WAIT1;
服务器端接受到释放连接报文段之后发送确认释放连接报文段，ACK为1，seq假设为v，ack为u+1，状态由ESTABLISHED转为CLOSED_WAIT状态；
客户端接收到确认释放连接报文段之后状态转为FIN_WAIT2；
接着服务器端发送释放连接报文段，FIN为1，ACK为1，seq假设为w，ack为u+1，状态转为LAST_ACK，客户端接收到之后转为TIME_WAIT；
过2MSL后转为CLOSED状态，服务器端接受到最后一个确认后转为CLOSED。

建立连接需要三次握手的原因：
防止失效链接请求建立连接：
假设客户端发送一个建立连接请求，但是这个请求在网络中产生了很长时间的滞留，客户端就重新发送了一个建立连接的请求，然后正常建立连接，最后都释放连接了，第一个建立连接的请求才到，这个就是失效的报文请求，如果两次握手即建立连接，那么失效的请求就建立了连接。

客户端需要等待2MSL的原因，
防止最后一个确认报文段服务器端收不到；

保证不会出现失效连接请求；