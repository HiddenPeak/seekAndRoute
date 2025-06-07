# N305 TEI 安装脚本

``` bash
# 安装基础依赖
sudo apt update
sudo apt install -y gpg-agent wget
sudo apt install libssl-dev
sudo apt install pkg-config
sudo apt-get install protobuf-compiler
sudo apt-get install gcc -y
sudo apt-get install libonnxruntime-dev
# 安装 cc
sudo apt install build-essential
# 验证安装
cc --version

# 安装mkl（可选）
wget -O- https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB | gpg --dearmor | sudo tee /usr/share/keyrings/oneapi-archive-keyring.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/oneapi-archive-keyring.gpg] https://apt.repos.intel.com/oneapi all main" | sudo tee /etc/apt/sources.list.d/oneAPI.list
sudo apt update
sudo apt install intel-oneapi-mkl
sudo apt install intel-oneapi-mkl-devel
# 添加MKL环境变量 （具体路径需要根据实际调整）
echo 'export LD_LIBRARY_PATH=/opt/intel/oneapi/compiler/2025.1/lib:/opt/intel/oneapi/mkl/2025.1/lib/intel64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc

# 安装 OpenBLAS （可选）
sudo apt install libopenblas-dev

# 安装 Cargo
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
cargo update
rustup update
rustc --version
# 
cargo install --path router -F mkl
# MKL无法正常使用时，考虑使用ORT
cargo install --path router -F ort
# 编译
cargo build --no-default-features --features "candle,http,static-linking" --release

# 验证安装
text-embeddings-router --version
# 查看运行参数
text-embeddings-router --help

# 启动嵌入模型
text-embeddings-router --model-id /home/rm01/models/embedding/bce_v1 --port 58180 --hostname 0.0.0.0 --prometheus-port 9180

curl 127.0.0.1:58180/embed     -X POST     -d '{"inputs":"What is Deep Learning?"}'     -H 'Content-Type: application/json'
# 启动重排序模型
text-embeddings-router --model-id /home/rm01/models/reranker/bce_v1 --port 58181 --hostname 0.0.0.0 --prometheus-port 9181

curl 127.0.0.1:58181/rerank     -X POST     -d '{"query": "What is Deep Learning?", "texts": ["Deep Learning is not...", "Deep learning is..."]}'     -H 'Content-Type: application/json'

```

## Jetson 安装测试脚本

``` bash
sudo apt-get install protobuf-compiler
conda install -c conda-forge onnxruntime

export ORT_LIB_LOCATION=$(conda env list | grep base | awk '{print $2}')/lib
export LD_LIBRARY_PATH=$ORT_LIB_LOCATION:$LD_LIBRARY_PATH
export PKG_CONFIG_PATH=$ORT_LIB_LOCATION/pkgconfig:$PKG_CONFIG_PATH

# 安装 Cargo
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# 启用环境变量
. "$HOME/.cargo/env"
# 拉取
git clone https://github.com/huggingface/text-embeddings-inference.git
cd text-embeddings-inference
# 修改兼容性文件 (参考hiddenpeak版本)
nano backends/candle/src/compute_cap.rs
# 检查依赖树
cargo tree | grep cudarc
# 编译 
cargo install --path router -F candle-cuda -F http --features candle-cuda
# 高性能构建 （支持 static-linking）
cargo build --no-default-features --features "candle-cuda,http,static-linking" --release
```

``` bash
# 服务拉起测试
text-embeddings-router --model-id /home/rm01/cfe/models/embed/ --port 58180 --hostname 0.0.0.0 --prometheus-port 9180

http://10.10.99.98:58180/

text-embeddings-router --model-id /home/rm01/cfe/models/reranker/ --port 58181 --hostname 0.0.0.0 --prometheus-port 9181

http://10.10.99.98:58181
```



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

## Text Embeddings Inference On Orin

``` bash
# 安装系统依赖 参考N305安装脚本的基础依赖
sudo apt update
sudo apt install -y gpg-agent wget
sudo apt install libssl-dev
sudo apt install pkg-config
sudo apt-get install protobuf-compiler
# 检查 PATH 环境变量 查看是否包含 cuda 的 bin 路径
echo $PATH
# 安装 Rust 使用默认安装，确保所有下载正确，并安装完成再进入下一步
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# 启用环境变量
. "$HOME/.cargo/env"
# 拉取TEI
git clone https://github.com/huggingface/text-embeddings-inference.git
cd text-embeddings-inference
# 修改文件 参考https://github.com/huggingface/text-embeddings-inference/pull/467/files
nano backends/candle/src/compute_cap.rs
#  在.compile_protos(&["../proto/tei.proto"], &["../proto"])?;之前，添加.protoc_arg("--experimental_allow_proto3_optional")
nano router/build.rs
# 安装  -F http 或者 -F grpc 选其一
cargo install --path router -F candle-cuda -F http -F dynamic-linking --no-default-features
cargo install --path router -F candle-cuda -F grpc -F dynamic-linking --no-default-features
```

