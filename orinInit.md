# Jetson AGX Orin 初始化

## 1. 安装Conda

``` bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh  -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh

source ~/miniconda3/bin/activate

conda init --all


conda create -n tgi python=3.11
conda activate tgi

pip install pep517
```

## 2. 编译pytorch

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

## 3.编译flash-attn

``` bash
# 为适应TGI版本，vllm可以单独安装对应版本
git clone --branch v2.6.1 --recursive https://github.com/Dao-AILab/flash-attention.git
cd flash-attention

# modify setup.py 
 cc_flag.append("arch=compute_87,code=sm_87")
 
 python setup.py bdist_wheel
```

## 4.安装Protoc

 ``` bash
# 版本根据需求调整
PROTOC_ZIP=protoc-21.12-linux-aarch_64.zip
curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v21.12/$PROTOC_ZIP
sudo unzip -o $PROTOC_ZIP -d /usr/local bin/protoc
sudo unzip -o $PROTOC_ZIP -d /usr/local 'include/*'
rm -f $PROTOC_ZIP
 ```

## 5.安装Rust

``` bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## 6.安装TGI

```bash
sudo apt-get install libssl-dev gcc -y


git clone https://github.com/huggingface/text-generation-inference.git

cd text-generation-inference

BUILD_EXTENSIONS=True make install


```

### 1. 安装过程会报错修改repo中server中的Makefile-flash-att

``` makefile
#此处内容需要单独编译我们自己的版本

后续完善
```

### 2.llvm 和 triton 安装

```bash
git clone https://github.com/llvm/llvm-project.git
cd $HOME/llvm-project  # your clone of LLVM.

mkdir build
cd build

cmake -G Ninja -DCMAKE_BUILD_TYPE=Release -DLLVM_ENABLE_ASSERTIONS=ON ../llvm -DLLVM_ENABLE_PROJECTS="mlir;llvm" -DLLVM_TARGETS_TO_BUILD="host;NVPTX;AMDGPU"

ninja

# Modify as appropriate to point to your LLVM build.
export LLVM_BUILD_DIR=$HOME/ssd/setup/llvm-project/build


pip install pybind11

cd <triton install>
LLVM_INCLUDE_DIRS=$LLVM_BUILD_DIR/include \
  LLVM_LIBRARY_DIR=$LLVM_BUILD_DIR/lib \
  LLVM_SYSPATH=$LLVM_BUILD_DIR \
  pip install -e python

```

### 直接编译

``` bash
cd <triton install>/python

python setup.py bdist_wheel
```



### 3.kernels安装

``` bash
git clone https://github.com/danieldk/attention-kernels
git clone https://github.com/danieldk/moe-kernels.git
cd attention-kernels

 python setup.py bdist_wheel
 
```

