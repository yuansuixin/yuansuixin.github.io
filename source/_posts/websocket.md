---
title: WebSocket详细解析
date: 2018-04-10 21:23:24
tags:
---



### 你真的了解WebSocket吗？

  WebSocket协议是基于TCP的一种新的协议。WebSocket最初在HTML5规范中被引用为TCP连接，作为基于TCP的套接字API的占位符。它实现了浏览器与服务器全双工(full-duplex)通信。其本质是保持TCP连接，在浏览器和服务端通过Socket进行通信。

 本文将使用Python编写Socket服务端，一步一步分析请求过程！！！

### 1.启动服务端

- 启动服务器后，等待用户连接，然后进行收发数据

```
import socket
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind(('127.0.0.1', 8002))
sock.listen(5)
# 等待用户连接
conn, address = sock.accept()
```

### 2.客户端连接

- 当客户端向服务端发送连接请求时，不仅连接还会发送【握手】信息，并等待服务端响应，至此连接才创建成功！

```
<script type="text/javascript">
    var socket = new WebSocket("ws://127.0.0.1:8002/xxoo");
    ...
</script>
```

### 3.建立链接【握手】

```
import socket
 
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind(('127.0.0.1', 8002))
sock.listen(5)
# 获取客户端socket对象
conn, address = sock.accept()
# 获取客户端的【握手】信息
data = conn.recv(1024)
...
...
...
conn.send('响应【握手】信息')

```

请求和响应的【握手】信息需要遵循规则：

- 从请求【握手】信息中提取 Sec-WebSocket-Key
- 利用magic_string 和 Sec-WebSocket-Key 进行hmac1加密，再进行base64加密
- 将加密结果响应给客户端
#### 注：magic string为：258EAFA5-E914-47DA-95CA-C5AB0DC85B11

请求【握手】信息为：

```
GET /chatsocket HTTP/1.1
Host: 127.0.0.1:8002
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Origin: http://localhost:63342
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: mnwFxiOlctXFN/DeMt1Amg==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

提取Sec-WebSocket-Key值并加密：

```
import socket
import base64
import hashlib
 
def get_headers(data):
    """
    将请求头格式化成字典
    :param data:
    :return:
    """
    header_dict = {}
    data = str(data, encoding='utf-8')
 
    for i in data.split('\r\n'):
        print(i)
    header, body = data.split('\r\n\r\n', 1)
    header_list = header.split('\r\n')
    for i in range(0, len(header_list)):
        if i == 0:
            if len(header_list[i].split(' ')) == 3:
                header_dict['method'], header_dict['url'], header_dict['protocol'] = header_list[i].split(' ')
        else:
            k, v = header_list[i].split(':', 1)
            header_dict[k] = v.strip()
    return header_dict
 
 
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind(('127.0.0.1', 8002))
sock.listen(5)
 
conn, address = sock.accept()
data = conn.recv(1024)
headers = get_headers(data) # 提取请求头信息
# 对请求头中的sec-websocket-key进行加密
response_tpl = "HTTP/1.1 101 Switching Protocols\r\n" \
      "Upgrade:websocket\r\n" \
      "Connection: Upgrade\r\n" \
      "Sec-WebSocket-Accept: %s\r\n" \
      "WebSocket-Location: ws://%s%s\r\n\r\n"
magic_string = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11'
value = headers['Sec-WebSocket-Key'] + magic_string
ac = base64.b64encode(hashlib.sha1(value.encode('utf-8')).digest())
response_str = response_tpl % (ac.decode('utf-8'), headers['Host'], headers['url'])
# 响应【握手】信息
conn.send(bytes(response_str, encoding='utf-8'))
```

### 4.客户端和服务端收发数据

详细原理：[解包和封包原理](https://yuansuixin.github.io/2018/04/09/websocket-data/ "解包和封包原理")

客户端和服务端传输数据时，需要对数据进行【封包】和【解包】。客户端的JavaScript类库已经封装【封包】和【解包】过程，但Socket服务端需要手动实现。

第一步：获取客户端发送的数据【解包】

```
   info = conn.recv(8096)

    payload_len = info[1] & 127
    if payload_len == 126:
        extend_payload_len = info[2:4]
        mask = info[4:8]
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
复制代码
```
 第二步：向客户端发送数据【封包】

```
def send_msg(conn, msg_bytes):
    """
    WebSocket服务端向客户端发送消息
    :param conn: 客户端连接到服务器端的socket对象,即： conn,address = socket.accept()
    :param msg_bytes: 向客户端发送的字节
    :return: 
    """
    import struct

    token = b"\x81"
    length = len(msg_bytes)
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


[基于Python的简单示例](https://github.com/yuansuixin/WebSocket "基于Python的简单示例")

[基于Tornado框架的Web聊天室](https://github.com/yuansuixin/WebSocket-Tornado-Chat "基于Tornado框架的Web聊天室")