# 安装 Torch 2.6.0
## 拉取指定版本的torch
``` bash
git clone -b 2.6.0 --recursive http://github.com/pytorch/pytorch
cd pytorch
```

## 创建构建环境 （via conda）
``` bash
conda create -n build4Torch python=3.12
conda activate build4Torch
```

## 安装构建依赖
``` bash
pip3 install -r requirements.txt
pip3 install scikit-build
pip3 install ninja cmake

# **在系统安装LAPACK和 BLAS 库**
sudo apt-get install liblapack-dev libblas-dev

# **安装 Numa** 
sudo apt-get install libnuma-dev

# 安装 MPI
sudo apt-get install openmpi-bin libopenmpi-dev

# 只适用于Intel CPU的环境
# pip install mkl-static mkl-include

```

## 配置环境变量
``` bash
# 启用新编译器New++ABI
export _GLIBCXX_USE_CXX11_ABI=1
# 设置环境变量
export USE_NCCL=1
export USE_SYSTEM_NCCL=0        # if you do not use system nccl, nvidia-smi may do not work
export USE_DISTRIBUTED=1                    # set 0 if you want to disable OpenMPI backend
export USE_QNNPACK=0
export USE_PYTORCH_QNNPACK=0
export TORCH_CUDA_ARCH_LIST="8.7"
export PYTORCH_BUILD_VERSION=2.6.0  # without the leading 'v', e.g. 2.3.0 for PyTorch v2.3.0
export PYTORCH_BUILD_NUMBER=1
export USE_PRIORITIZED_TEXT_FOR_LD=1 

export CUDA_HOME=/usr/local/cuda-12.8
export CUDA_PATH=$CUDA_HOME
export BLAS=OpenBLAS
export OPENBLAS_INCLUDE_DIR=/usr/include
export OPENBLAS_LIBRARY_DIR=/usr/lib/aarch64-linux-gnu

export MAGMA_INCLUDE_DIR=/usr/local/magma/include
export MAGMA_LIBRARIES=/usr/local/magma/lib/

export USE_CUDNN=1
export USE_CUSPARSELT=1
export USE_CUDSS=1
export USE_CUFILE=1
export USE_XPU=0

python setup.py bdist_wheel
```

## 构建 wheel
``` bash
python setup.py bdist_wheel
```
