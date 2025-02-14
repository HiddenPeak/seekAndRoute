# 本地部署AI应用开发工作流（Ubuntu or macOS）

## 一、模型推理框架与管理平台

在开发阶段可以使用Xinference作为模型推理的管理平台

### 1.1 Xinference的docker部署

``` bash
docker run \
  -v /home/yourUser/.xinference:/root/.xinference \
  -e XINFERENCE_MODEL_SRC=modelscope \
  -v /home/yourUser/.cache/huggingface:/root/.cache/huggingface \
  -v /home/yourUser/.cache/modelscope:/root/.cache/modelscope \
  -p 9997:9997 \
  --gpus all \
  xprobe/xinference:v0.16.2 \
  xinference-local -H 0.0.0.0
```

在国内使用加上了一个环境变量方便从魔搭获取模型，对于离线环境可以先下载好模型。

启用了 --gpus all 需要有cuda支持，如果使用其他GPU则修改对应的参数

v0.16.2是Xinference的版本，根据实际部署的版本调整

-H 参数用以配制绑定的ip地址，配置成0.0.0.0，将可以匹配能访问本机的所有ip地址

-p 参数映射了9997端口

yourUser则是～文件夹的名称，也就是你的用户名

### 1.2 访问模型推理管理平台服务

使用浏览器访问

``` url
http://yourIP:9997
```

出现管理界面使用选中需要加载的模型或者已经下载好的模型（会标注cached）

#### 特别说明

1. 模型分为大语言模型、嵌入模型、重排模型、图像模型、音频模型、视频模型、自定义模型
2. 其中嵌入模型和重排模型可以根据需要载入多个
3. 在同一时间，大语言模型、图像模型、音频模型、视频模型只能加载其中一个模型。
4. 需要先拉起嵌入模型和重排模型，再拉起大语言模型，否则无法正确加载RAG应用中的需要用到的全部模型

## 二、 AI工作流无代码开发平台

为了方便演示以dify为例

### 2.1 Dify部署

``` bash
git clone https://github.com/langgenius/dify.git

cd dify
cd docker
cp .env.example .env
docker compose up -d
```

.env文件是dify的配置文件，常规应用可以直接配置该文件中的参数，如果不需要调整端口号则可以直接按照以上命令执行。

提前部署Dify好了的离线环境，则可以使用

``` bash
docker ps
```

查看当前Dify运行的容器和端口

可以使用

``` bash
docker compose logs -f
```

查看实时的日志

### 2.2 Dify的使用

Dify完成启动后约1分钟，打开浏览器访问

``` url
http://yourIP:yourPort
```

默认端口是80，但有时为了方便调试会考虑使用40800端口以避让本机的80端口

首次启动会要求初始化管理员账户，输入邮箱（不会发邮件）、用户名、密码即可，选择需要的语言。

### 2.3 Dify的模型服务配置

在界面的右上角点击用户名，选择菜单中的“设置”，在模型供应商中选择Xinference

![provider-icon](https://dify.app.aoseo.com:50443/console/api/workspaces/current/model-providers/xinference/icon_large/zh_Hans)

添加在第一步中已经拉起的大语言模型、嵌入模型、重排模型

模型名称和模型U-id可以在Xinference的管理界面中获取

完成以上设置之后可以刷新页面，在模型供应商中选择系统模型设置，为dify分配默认模型。

## 三、开始创建Dify工作流

### 3.1 使用探索示例

选择探索菜单项，将出现大量各类工作流示例，包含工作流程编排和各种组件的使用。

但是这些工作流都做得很差，非常适合用来了解工作流程编排组件，但不适合直接使用。

### 3.2 了解对话机器人和工作流程

在初期适合使用对话机器人的基础编排和工作流编排，并创建知识库与之关联查看RAG的效果。

在创建知识库的过程中评估不同的方式的召回效果。

在开发阶段建议使用对话机器人的工作流编排替代工作流编排，这样可以方便的使用对话输出了解运行情况。

### 3.3 使用Agent与工具

智能体（Agent）是配合工具使用的，Dify默认的工具有不少是需要调用外部并进行授权的。

在脱机环境下可以考虑执行编写工具
