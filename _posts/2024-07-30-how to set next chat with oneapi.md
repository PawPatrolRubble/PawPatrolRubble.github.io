---
title: "how to set next chat with oneapi"
date: 2024-07-31 12:00:00 +0800
categories: [oneapi,nextchat]
tags: [nextchat]
---

### 如何设置

如下图所示
![alt text](/assets/images/set_in_chat_next.png)

最主要的是设置好接口地址及Oneapi里生成的Key.如果出现异常，可以在浏览器的控制台看一下是不正确的请求地址。

如下面的为正确的地址，
`http://localhost:3000/v1/chat/completions`

以上设置好以后便可以使用nextchat了。

notes:
- 自定义模型名的值：`qwen-plus,qwen-max,qwen-turbo`一定要和你的模型名一致，因为这个参数会在post里传递给oneapi

