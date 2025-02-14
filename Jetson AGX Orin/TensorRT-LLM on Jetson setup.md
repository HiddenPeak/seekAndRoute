# TensorRT-LLM on Jetson setup

## 1. 创建conda环境



## 2. 创建TensorRT软链接

``` bash
sudo ln -s /usr/lib/python3.10/dist-packages/tensorrt* ~/miniconda3/envs/trt-llm/lib/python3.10/site-packages/
```

## 3. 安装必要组件

``` bash
sudo apt upgrade gcc g++
```

https://github.com/NVIDIA/TensorRT-LLM/blob/v0.12.0-jetson/README4Jetson.md

参考该页面

