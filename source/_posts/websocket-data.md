---
title: WebSocket传输数据服务端的封包解包原理
date: 2018-04-09 22:31:40
tags:
---


# WebSocket传输数据服务端的封包解包原理

  客户端和服务端传输数据时，需要对数据进行【封包】和【解包】。客户端的JavaScript类库已经封装【封包】和【解包】过程，但Socket服务端需要手动实现。

> 获取客户端发送的数据【解包】

- 获取到的客户端的数据不能直接拿到，需要通过位运算，里面的东西都有代指的，第几位代表什么，
- 规则
  ![Untitled-1-201849223212](http://p693ase25.bkt.clouddn.com/Untitled-1-201849223212.png)
    - 以客户端发送的hello为例，服务端接收到``info="b'\x88\x82\x08j\xe8\x00\x0b\x83'"``
    - 服务端拿到的都是字节，那么info[0]就是第一个字节，也就是八位
    - 通过规则图可以知道，我们要计算出来opcode的值
    - 我们就需要计算出来第一个字节的后四位的值，那么怎么才能拿到后四位的值呢，就是和1111进行与运算，也就是和15进行与计算``opcode=info[0] & 15``,那么这个值最大为15
    - 我们需要计算出图中的fin，向右移7位就可以得到，同理得到其他的值
    - opcode及之前的fin等这两个字节是用来判断发送的内容是否发送完毕，如果没发完继续收，等全部内容发完，再统一的解码
    - 同样的道理我们要计算出payload_len,payload_len需要得到第二个字节的八位，那么和11111111进行与运算可以得到``payload_len=info[1]&127``,那么这个值最大为127
    - payload_len 的长度决定了它（数据头）再往后会占多少
        - 小于126，数据头到payload_len就结束了
        - 等于126，数据头再往后延伸2个字节，头部总共占4个字节
        - 大于126，数据头再往后延伸8个字节，头部总共占10个字节
    - 数据头部是数据部分，数据部分的前4个字节都是加密的masking key，我们需要解密才能拿到真正的数据


- 代码部分：

```
    info = conn.recv(8096)

    payload_len = info[1] & 127
    if payload_len == 126:
        #数据头部延伸的长度
        extend_payload_len = info[2:4]
        #加密的4个字节
        mask = info[4:8]
        #数据
        decoded = info[8:]
    elif payload_len == 127:
        extend_payload_len = info[2:10]
        mask = info[10:14]
        decoded = info[14:]
    else:
        extend_payload_len = None
        mask = info[2:6]
        decoded = info[6:]

    bytes_list = bytearray()
    for i in range(len(decoded)):
        chunk = decoded[i] ^ mask[i % 4]
        bytes_list.append(chunk)
    body = str(bytes_list, encoding='utf-8')
    print(body)

```



- 规则文档

```
The MASK bit simply tells whether the message is encoded. Messages from the client must be masked, so your server should expect this to be 1\. (In fact, [section 5.1 of the spec](http://tools.ietf.org/html/rfc6455#section-5.1) says that your server must disconnect from a client if that client sends an unmasked message.) When sending a frame back to the client, do not mask it and do not set the mask bit. We'll explain masking later. _Note: You have to mask messages even when using a secure socket._RSV1-3 can be ignored, they are for extensions.

The opcode field defines how to interpret the payload data: 0x0 for continuation, `0x1` for text (which is always encoded in UTF-8), `0x2` for binary, and other so-called "control codes" that will be discussed later. In this version of WebSockets, `0x3` to `0x7` and `0xB` to `0xF` have no meaning.

The FIN bit tells whether this is the last message in a series. If it's 0, then the server will keep listening for more parts of the message; otherwise, the server should consider the message delivered. More on this later.

**Decoding Payload Length**

To read the payload data, you must know when to stop reading. That's why the payload length is important to know. Unfortunately, this is somewhat complicated. To read it, follow these steps:

1.  Read bits 9-15 (inclusive) and interpret that as an unsigned integer. If it's 125 or less, then that's the length; you're **done**. If it's 126, go to step 2\. If it's 127, go to step 3.
2.  Read the next 16 bits and interpret those as an unsigned integer. You're **done**.
3.  Read the next 64 bits and interpret those as an unsigned integer (The most significant bit MUST be 0). You're **done**.

**Reading and Unmasking the Data**

If the MASK bit was set (and it should be, for client-to-server messages), read the next 4 octets (32 bits); this is the masking key. Once the payload length and masking key is decoded, you can go ahead and read that number of bytes from the socket. Let's call the data **ENCODED**, and the key **MASK**. To get **DECODED**, loop through the octets (bytes a.k.a. characters for text data) of **ENCODED** and XOR the octet with the (i modulo 4)th octet of MASK. In pseudo-code (that happens to be valid JavaScript):

var DECODED = "";
for (var i = 0; i < ENCODED.length; i++) {
    DECODED[i] = ENCODED[i] ^ MASK[i % 4];
}

Now you can figure out what **DECODED** means depending on your application.
```

- 解密
    - 使用masking key ，从后面一个一个的拿数据，然后一个一个的解
    - 注意：如果有中文，直接解码为字节，然后全部的字节统一转化成字符串，否则使用一个一个解码放入字符中容易乱码

> 向客户端发送数据【封包】

```
#msg_bytes这个是给用户返回的字节
def send_msg(conn, msg_bytes):
    """
    WebSocket服务端向客户端发送消息
    :param conn: 客户端连接到服务器端的socket对象,即： conn,address = socket.accept()
    :param msg_bytes: 向客户端发送的字节
    :return: 
    """
    # 封二进制包使用
    import struct

    token = b"\x81"
    length = len(msg_bytes)
    #打包规则
    if length < 126:
        token += struct.pack("B", length)
    elif length <= 0xFFFF:
        token += struct.pack("!BH", 126, length)
    else:
        token += struct.pack("!BQ", 127, length)

    msg = token + msg_bytes
    conn.send(msg)
    return True
    
```



## 知识点补充
- 位运算，右移动  >>
    - 10010001
    - 右移4:00001001  前面补0
    - 左移4：100100010000  后面补0
- 异或运算，向异取1
    - 都是1： 0
    - 0,1： 1
    - 0,0:  0
- 与运算
    - 一个字节八位，如果要后四位的值那么就设计一个值和原值进行与运算结果还是后四位
    - 0 0 0 0 0 0 0 0 要取后四位的值，只需要和00001111进行与运算，前面的四位为0000忽略掉，即可得到后四位还是0000









