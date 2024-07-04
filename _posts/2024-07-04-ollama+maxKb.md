---
title: "ollama+maxKb installation on windows"
date: 2024-07-02 12:00:00 +0800
categories: [llm,ollama]
tags: [ollama, maxKB]
---

### install ollama
1. download ollama windows from [ollama url](https://ollama.com/download)
2. install it, and then choose one model to install, I choose the `ollama run qwen:0.5b` model.
3. run llama, to make sure that it can be accessed by maxKB you should type `ollama serve`
4. as docker is running in wsl environment, you cannot access the ollama by localhost, so you should use the ip address of your windows system to access it. to do that you need to config ollama to bind the local address, which can be set as below:


![alt text](/assets/images/ollama_host_env_mosai.png)

### install maxKB

the easiest way to install is use docker-desktop on windows, but you have to ensure that wsl is enabled on your windows system.

```docker
docker run -d --name=maxkb -p 8080:8080 -v ~/.maxkb:/var/lib/postgresql/data cr2.fit2cloud.com/1panel/maxkb

# 用户名: admin
# 密码: MaxKB@123..
```

### add qwen model in maxKb
please note that currently maxKb only support ollama models, so you need to choose ollama to add the qwen model in maxKb.

![alt text](/assets/images/ollama_qwen_list.png)

the api domain should be the ip address of your windows system, and the port should be 11434.
as an example the api key may look like this: `http://192.168.104.80:11434`

the api key doen't matter, you can set it as `1234567890`.

if everything is ok, you should be able to see the qwen model in maxKb.