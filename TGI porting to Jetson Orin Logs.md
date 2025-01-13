# TGI porting to Jetson Orin Logs

## 配置conda环境

``` bas
conda create -n tgi4orin python=3.11

conda activate tgi4orin
```



## 安装Torch

``` bash
sudo apt-get install python3-pip cmake libopenblas-dev libopenmpi-dev
sudo pip3 install -U jetson-stats

git clone --branch v2.5.1 --recursive http://github.com/pytorch/pytorch

cd pytorch

pip3 install -r requirements.txt
pip3 install scikit-build
pip3 install ninja

export USE_NCCL=1
export USE_DISTRIBUTED=1                    # set 0 if you want to disable OpenMPI backend
export USE_QNNPACK=0
export USE_PYTORCH_QNNPACK=0
export TORCH_CUDA_ARCH_LIST="8.7"
export PYTORCH_BUILD_VERSION=2.5.1  # without the leading 'v', e.g. 2.3.0 for PyTorch v2.3.0
export PYTORCH_BUILD_NUMBER=1
export USE_PRIORITIZED_TEXT_FOR_LD=1

python setup.py bdist_wheel
```

## 编译

``` bash
BUILD_EXTENSIONS=True make install
```

1. **build-flash-attention-v2-cuda** 编译出错

``` bash
# 为适应TGI版本，vllm可以单独安装对应版本
git clone --branch v2.6.1 --recursive https://github.com/Dao-AILab/flash-attention.git
cd flash-attention

# modify setup.py 
 cc_flag.append("arch=compute_87,code=sm_87")
 
 python setup.py bdist_wheel
```

2. 安装triton

``` bash

```

3. build-flash-attention编译出错

``` bash
git clone https://github.com/HazyResearch/flash-attention.git

cd flash-attention
git fetch 
git checkout 3a9bfd076f98746c73362328958dbc68d145fbec

# 修改setup.py 文件添加8.7架构支持

# 提示CUDA_HOME找不到，在.bashrc中增加
export CUDA_HOME=/usr/local/cuda-12.6

# 编译flash-attention
python setup.py bdist_wheel
# 编译layer_norm
cd csrc/layer_norm && python setup.py bdist_wheel
# 编译rotary
cd ../rotary && python setup.py bdist_wheel


```

4. 修改自编译文件链接(./server/pyproject.toml)



``` bash
# 单独安装 bitsandbytes
pip install bitsandbytes==0.41.3
```

修改Makefile文件

``` makefile
install-cuda: install-server install-flash-attention-v2-cuda install-flash-attention
	pip install -e ".[attention,marlin,moe]"
	pip install nvidia-nccl-cu12==2.23.4
```





``` toml
bitsandbytes = { version = "0.43.3", optional = true }


attention-kernels = [
  { url = "https://github.com/danieldk/attention-kernels/releases/download/v0.1.1/attention_kernels-0.1.1+cu123torch2.4-cp39-cp39-linux_x86_64.whl", python = "~3.9", optional = true },
  { url = "https://github.com/danieldk/attention-kernels/releases/download/v0.1.1/attention_kernels-0.1.1+cu123torch2.4-cp310-cp310-linux_x86_64.whl", python = "~3.10", optional = true },
  { url = "https://github.com/danieldk/attention-kernels/releases/download/v0.1.1/attention_kernels-0.1.1+cu123torch2.4-cp311-cp311-linux_x86_64.whl", python = "~3.11", optional = true },
  { url = "https://github.com/danieldk/attention-kernels/releases/download/v0.1.1/attention_kernels-0.1.1+cu123torch2.4-cp312-cp312-linux_x86_64.whl", python = "~3.12", optional = true },
]
marlin-kernels = [
  { url = "https://github.com/danieldk/marlin-kernels/releases/download/v0.3.7/marlin_kernels-0.3.7+cu123torch2.4-cp39-cp39-linux_x86_64.whl", python = "~3.9", optional = true },
  { url = "https://github.com/danieldk/marlin-kernels/releases/download/v0.3.7/marlin_kernels-0.3.7+cu123torch2.4-cp310-cp310-linux_x86_64.whl", python = "~3.10", optional = true },
  { url = "https://github.com/danieldk/marlin-kernels/releases/download/v0.3.7/marlin_kernels-0.3.7+cu123torch2.4-cp311-cp311-linux_x86_64.whl", python = "~3.11", optional = true },
  { url = "https://github.com/danieldk/marlin-kernels/releases/download/v0.3.7/marlin_kernels-0.3.7+cu123torch2.4-cp312-cp312-linux_x86_64.whl", python = "~3.12", optional = true },
]
moe-kernels = [
  { url = "https://github.com/danieldk/moe-kernels/releases/download/v0.7.0/moe_kernels-0.7.0+cu123torch2.4-cp39-cp39-linux_x86_64.whl", python = "~3.9", optional = true },
  { url = "https://github.com/danieldk/moe-kernels/releases/download/v0.7.0/moe_kernels-0.7.0+cu123torch2.4-cp310-cp310-linux_x86_64.whl", python = "~3.10", optional = true },
  { url = "https://github.com/danieldk/moe-kernels/releases/download/v0.7.0/moe_kernels-0.7.0+cu123torch2.4-cp311-cp311-linux_x86_64.whl", python = "~3.11", optional = true },
  { url = "https://github.com/danieldk/moe-kernels/releases/download/v0.7.0/moe_kernels-0.7.0+cu123torch2.4-cp312-cp312-linux_x86_64.whl", python = "~3.12", optional = true },
]
```

