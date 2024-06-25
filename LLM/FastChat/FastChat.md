---
title: FastChat
date created: 2024-06-24 13:55
date modified: 2024-06-24 13:55
dg-publish: true
---

# 什么是Fastchat
按我的目//前的理解来看FastChat最大的功能是方便我们部署大模型，所有方法都风住哪个好了只要通过命令就可以得到一个可以对话，可以评估质量的web页面

# 怎么使用
首先要下载LLM
``` bash
# 开启大文件下载
git lfs install
# 下载 ChatGLM2-6B
git clone https://huggingface.co/THUDM/chatglm2-6b
# 下载 Llama2-7B-chat-hf
git clone https://huggingface.co/meta-llama/Llama-2-7b-chat-hf
```
## FastChat 命令行部署
FastChat 提供了命令行的方式来部署 LLM，命令行的方式比较简单，可以在命令行和 LLM 进行问答交互，我们先看下 FastChat 与 ChatGLM2 的命令行交互：

``` bash
$ python -m fastchat.serve.cli --model-path /你的下载路径/chatglm2-6b
问: 你好
答: 你好！我是人工智能助手 ChatGLM2-6B，很高兴见到你，欢迎问我任何问题。
```
再看看 FastChat 与 Llama2 的命令行交互：

``` bash
$ python -m fastchat.serve.cli --model-path /你的下载路径/Llam2-2-7n-chat-hf
[INST]: who r u
[/INST]: I'm just an AI, I don't have personal information or a physical identity like a human. I'm here to help answer your questions and provide information to the best of my ability. Is there something specific you would like to know or talk about?
```
可以看到我们主要使用了 FastChat 的fastchat.serve.cli命令，然后加上参数--model-path，参数的值就是我们下载的 LLM 具体路径。

## FastChat API 部署
部署 API 服务分三步，第一步是启动控制器服务，启动命令如下所示：

```bash
python -m fastchat.serve.controller --host 0.0.0.0
```
这次使用的是 FastChat 的fastchat.serve.controller命令，--host参数是设置服务的主机地址，这里设置为0.0.0.0，表示可以通过任何地址访问，如果不设置 host 的话在 AutoDL 环境上会报错，服务启动后默认端口是 21001。如果想查看该命令的更多信息可以执行python -m fastchat.serve.controller --help命令。

第二步是启动 Model Worker 服务，启动命令如下所示：

``` bash
python -m fastchat.serve.model_worker --model-path /你的下载路径/chatglm2-6b(或者是Llam2-2-7n-chat-hf) --host 0.0.0.0
```

使用 FastChat 的fastchat.serve.model_worker命令来启动服务，通过--model-path参数来指定 LLM 的路径，服务启动后默认端口是 21002，可以通过--port参数来修改端口，如果想查看该命令的更多信息可以执行python -m fastchat.serve.model_worker --help命令。

第三步是启动 RESTFul API 服务，启动命令如下所示：

python -m fastchat.serve.openai_api_server --host 0.0.0.0
1
服务启动后，默认端口是 8000，可以通过--port参数来修改端口，在浏览器中访问服务的这个路径http://127.0.0.1/docs可以查看接口信息，这个服务就是我们最终要用的 LLM API 服务，它的接口跟 OpenAI 的接口是兼容的，不仅可以推理，还可以进行 Embedding 操作

## FastChat WebUI 部署
FastChat 还提供了 WebUI 界面，我们可以通过浏览器来访问 WebUI 服务来进行问答交互，在启动 WebUI 服务之前，请确保已经启动好了上面的控制器服务和 Model Work 服务，启动 WebUI 服务的命令如下所示：
``` bash
python -m fastchat.serve.gradio_web_server --host 0.0.0.0
```
服务默认端口是 7860，可以通过--port参数来修改端口，还可以通过添加--share参数来开启 Gradio 的共享模式，这样就可以通过外网访问 WebUI 服务了


参考资料
[用通俗易懂的方式讲解大模型：使用 FastChat 部署 LLM 的体验太爽了](https://blog.csdn.net/2301_78285120/article/details/135302417)