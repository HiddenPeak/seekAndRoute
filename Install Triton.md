# Triton 安装

## 创建用于构建的虚拟环境
``` bash
# 创建conda环境
creat conda -n build4Triton python=3.10
```
可根据需要指定python的版本
当前triton支持python 3.9到3.13

## 拉取triton
``` bash
# 可使用 -b 指定版本
git clone https://github.com/triton-lang/triton.git
cd triton
```

## 安装构建用依赖
``` bash
pip install ninja cmake wheel pybind11 # build-time dependencies
```

## 可编辑模式构建
``` bash
pip install -e python
```
构建完成后已经处于被安装的状态

## 生成wheel
``` bash
cd python
python3 setup.py bdist_wheel
```
直接构建wheel会导致报错
生成后的文件位于dist

## 启用 Blackwell 架构支持 （Thor AGX）
环境要求：
- CUDA 12.8
- PyTorch 2.6.0
- 如果需要torchvision, torchtext, torchaudio则需要单独从源代码编译

完成上述操作后，再执行本文中从源代码构建Triton，并安装。
