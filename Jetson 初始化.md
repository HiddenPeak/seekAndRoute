# Jetson 初始化

## 配置最大功率

``` bash
sudo nvpmodel -m 0
sudo jetson_clocks
```

## 安装

``` bash
sudo -H pip3 install jetson-stats
sudo systemctl restart jtop.service

# 运行
jtop
```

## 锁定工作频率

``` bash
sudo jetson_clocks
```

## 更新Ubuntu 及安装必要系统组件

``` bash
sudo apt update

sudo apt-get install libglfw3-dev libc6-dev nano tmux  python3-pip libopenblas-dev libatlas-base-dev liblapack-dev libf2c2-dev gfortran

sudo apt upgrade
```

## 安装CUDA Toolkit 12.8

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/arm64/cuda-ubuntu2204.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.8.0/local_installers/cuda-tegra-repo-ubuntu2204-12-8-local_12.8.0-1_arm64.deb
sudo dpkg -i cuda-tegra-repo-ubuntu2204-12-8-local_12.8.0-1_arm64.deb
sudo cp /var/cuda-tegra-repo-ubuntu2204-12-8-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-8 cuda-compat-12-8 cuda  cuda-12.8


wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/sbsa/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt install libnccl2=2.25.1-1+cuda12.8 libnccl-dev=2.25.1-1+cuda12.8
```

nvidia-gds包含GPU的调试工具套件(可选)

### 根据选择调整

``` bash
# 配置环境变量支持12.8
export PATH=/usr/local/cuda-12.8/bin${PATH:+:${PATH}}

# 配置LD环境变量支持12.8
export LD_LIBRARY_PATH=/usr/local/cuda-12.8/lib64
```

若需要永久调整可以考虑修改~/.bashrc文件中对应位置

### 根据选择调整12.6

``` bash
# 配置环境变量支持12.8
export PATH=/usr/local/cuda-12.6/bin${PATH:+:${PATH}}

# 配置LD环境变量支持12.8
export LD_LIBRARY_PATH=/usr/local/cuda-12.6/lib64
```

若需要永久调整可以考虑修改~/.bashrc文件中对应位置

## 安装Conda

``` bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh  -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh

source ~/miniconda3/bin/activate

conda init --all
```

## cuda example 编译测试

``` bash
conda create -n test python python=3.12
conda activate test

pip install pycuda
pip install numpy
pip install numba

python3 -m pip install --upgrade setuptools pip wheel
python3 -m pip install nvidia-pyindex
python3 -m pip install \
nvidia-cuda-runtime-cu12 \
nvidia-cuda-cccl-cu12 \
nvidia-cuda-cupti-cu12 \
nvidia-cuda-nvcc-cu12 \
nvidia-cuda-nvrtc-cu12 \
nvidia-cublas-cu12 \
nvidia-cuda-sanitizer-api-cu12 \
nvidia-cufft-cu12 \
nvidia-curand-cu12 \
nvidia-cusolver-cu12 \
nvidia-cusparse-cu12 \
nvidia-npp-cu12 \
nvidia-nvfatbin-cu12 \
nvidia-nvjitlink-cu12 \
nvidia-nvjpeg-cu12 \
nvidia-nvml-dev-cu12 \
nvidia-nvtx-cu12
```

nvidia-cuda-opencl-cu12 无法直接安装

## 在conda中安装cuda Toolkit 组件

``` bash
conda install cuda -c nvidia
```

安装之前版本cuda 组件

``` bash
conda install cuda -c nvidia/label/cuda-12.6.0
```

## 安装cuSPARSELt 0.7.0

``` bash
wget https://developer.download.nvidia.com/compute/cusparselt/0.7.0/local_installers/cusparselt-local-tegra-repo-ubuntu2204-0.7.0_1.0-1_arm64.deb
sudo dpkg -i cusparselt-local-tegra-repo-ubuntu2204-0.7.0_1.0-1_arm64.deb
sudo cp /var/cusparselt-local-tegra-repo-ubuntu2204-0.7.0/cusparselt-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install libcusparselt0 libcusparselt-dev
```

## 编译magma pytorch的依赖

``` bash
git clone https://github.com/icl-utk-edu/magma.git
cd magma


echo -e "GPU_TARGET = sm_87\nBACKEND = cuda\nFORT = false" > make.inc
make generate
cmake -DGPU_TARGET=sm_80 -DCMAKE_CUDA_FLAGS="-DADD_ -O3 -fPIC -DNDEBUG -I/usr/local/cuda-12.8/include -fopenmp -Wall -Wno-unused-function"   -DCMAKE_C_FLAGS="-DADD_" -DCMAKE_Fortran_FLAGS="-DADD_"  -DCMAKE_CUDA_COMPILER="$CUDA_DIR/bin/nvcc" -DCMAKE_INSTALL_PREFIX=build/target -DCMAKE_CUDA_ARCHITECTURES=87-real . -Bbuild


cmake --build build -j $(nproc) --target install

export FC=gfortran
export CUDADIR=/usr/local/cuda-12.8
export CFLAGS="-O3 -fPIC -DNDEBUG -DADD_ -Wall -fopenmp -std=c99"
export CXXFLAGS="-O3 -fPIC -DNDEBUG -DADD_ -Wall -fopenmp -std=c++14 -I/usr/local/cuda-12.8/include"
export FFLAGS="-O3 -fPIC -DNDEBUG -DADD_ -Wall -Wno-unused-dummy-argument"
export F90FLAGS="-O3 -fPIC -DNDEBUG -DADD_ -Wall -Wno-unused-dummy-argument -x f95-cpp-input"
GPU_TARGET=Ampere make
```



## 编译pytorch

``` bash
# 获取repo
git clone --recursive https://github.com/pytorch/pytorch
cd pytorch

# if you are updating an existing checkout
git submodule sync
git submodule update --init --recursive

pip install cmake ninja
# Run this command from the PyTorch directory after cloning the source code using the “Get the PyTorch Source“ section below
pip install -r requirements.txt


```



