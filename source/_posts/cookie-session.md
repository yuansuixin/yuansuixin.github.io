---
title: 会话技术cookie和session的区别和联系
date: 2018-02-12 20:37:06
tags:
---

# COOKIE 和 SESSION 有什么区别和联系

- 存储位置
 - cookie 保存在客户端中
 - session 保存在服务器端

- Session 是服务器保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中
- Cookie 是客户端保存用户信息的一个机制，用来记录用户的一些信息，也是实现Session的一个方式，用于存储session_id ,存储在用户的浏览器中

## 不要混淆 cookie 和 session 实现
- 本身 session 是一个抽象的概念，将user agent 和server 之前一对一的交互，抽象为’会话‘，进而衍生出’会话状态‘，也就是session的概念
- 而 cookie 是一个实际存在的东西，http 协议中定义在header中的字段，可以认为是 session 的后端无状态实现
- 现在的 session ，是为了绕开 cookie 的各种限制，通常借助与cookie本身和后端存储实现的，一种更高级的状态实现



- session、cookie、token会话技术

![会话](http://p693ase25.bkt.clouddn.com/Untitled-1-201833011500.png)






