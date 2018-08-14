 ##User Datagram Protocol##
>UDP(User Datagram Protocol)
>相对较少的协议机制，面向事物的特性。没有对于消息对于传递和重复有协议层上的保障。在消息发出后不保存消息的状态
>简单到[RFC 768](https://tools.ietf.org/html/rfc768)总共只有两页不到的内容介绍

###Segment structure###
>UDP报文结构

```
                  0      7 8     15 16    23 24    31
                 +--------+--------+--------+--------+
                 |     Source      |   Destination   |
                 |      Port       |      Port       |
                 +--------+--------+--------+--------+
                 |                 |                 |
                 |     Length      |    Checksum     |
                 +--------+--------+--------+--------+
                 |
                 |          data octets ...
                 +---------------- ...

                      User Datagram Header Format
```

**Source Port**(16bit)
标识了消息发起者的端口

**Destination Port**(16bit)
标识了消息目标的端口

**Length**(16bit)
包括TCP头和数据在内的字节数

**CheckSum**(16bit)
校验和，用于对UDP头部和数据进行差错检测。在IPv4中为可选，IPv6为强制检测。


###CheckSum Computation###
>校验和计算
计算校验和是使用一个特殊的头信息包含IP头部、UDP头部、UDP数据以及数据填充在内的所有16bit的"一的补数"（**[Ones' complement](https://en.wikipedia.org/wiki/Ones%27_complement)**）。这个特殊的头部仅仅用于计算校验和而已，并不是UDP传输信息使用的真实的头部结构。在末尾填充0已满足以两个字节为单位用于计算补数。

**IPv4 Pseudo Header**
>IPv4使用这个头部结构用于计算检验和

```
                  0      7 8     15 16    23 24    31
                 +--------+--------+--------+--------+
                 |          source address           |
                 +--------+--------+--------+--------+
                 |        destination address        |
                 +--------+--------+--------+--------+
                 |  zero  |protocol|   UDP length    |
                 +--------+--------+--------+--------+
				 |   source port   | destination port|
				 +--------+--------+--------+--------+
                 |      length     |     checksum    |
				 +--------+--------+--------+--------+
				 |                data               |+
                 +--------+--------+--------+--------+
```

UDP 校验和计算对于IPv4来说是可选的，如果选择不适用校验和则Checksum应该被置位0。
protocal为UDP在[IP协议族](https://en.wikipedia.org/wiki/List_of_IP_protocol_numbers)中的编号17
UDP Length为UDP Header和Data的长度。

**IPv6 Pseudo Header**
>IPv6使用这个头部结构用于计算检验和

```
                  0      7 8     15 16    23 24    31
                 +--------+--------+--------+--------+
                 |       source ipv6  address        |[128bit]
                 +--------+--------+--------+--------+
                 |     destination ipv6 address      |[128bit]
                 +--------+--------+--------+--------+
                 |             UDP length            |[32bit]
                 +--------+--------+--------+--------+
                 |                          |  Next  |
                 |           Zeros          | Header |[32bit]
                 +--------+--------+--------+--------+
				 |   source port   | destination port|[32bit]
				 +--------+--------+--------+--------+
                 |      length     |     checksum    |[32bit]
				 +--------+--------+--------+--------+
				 |                data               |+
                 +--------+--------+--------+--------+
```

IPv6必须使用检验和。