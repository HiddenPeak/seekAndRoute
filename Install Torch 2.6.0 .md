# 安装 Torch 2.6.0
## 拉取指定版本的torch
``` bash
git clone -b v2.6.0 --recursive https://github.com/pytorch/pytorch
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

# 安装其他编译过程中需要的库
sudo apt-get install libgflags-dev libprotobuf-dev protobuf-compiler libgoogle-glog-dev

# 只适用于Intel CPU的环境
# pip install mkl-static mkl-include

```

## 修改CMakeList.txt文件，增加如下内容：

``` cmake
# add OPENBLAS Path by user
set(OpenBLAS_INCLUDE_DIR /usr/include/aarch64-linux-gnu)
set(OpenBLAS_LIB /usr/lib/aarch64-linux-gnu)

# add Numa Path by user
set(Numa_INCLUDE_DIR /usr/include)
set(Numa_LIBRARIES /usr/lib/aarch64-linux-gnu)

# add cuDNN Path by user
set(CUDNN_LIBRARY_PATH /usr/lib/aarch64-linux-gnu)
set(CUDNN_INCLUDE_PATH /usr/include)

# add cuDSS Path by user
set(CUDSS_LIBRARY_PATH /usr/lib/aarch64-linux-gnu/libcudss/12)
set(CUDSS_INCLUDE_PATH /usr/include/libcudss/12)

# add CUSPARSELT Path by user
set(CUSPARSELT_LIBRARY_PATH /usr/local/cuda/lib64)
set(CUSPARSELT_INCLUDE_PATH /usr/local/cuda/include)

# fix shorthash about libnvrtc.so
set(CUDA_NVRTC_LIB "${CUDA_nvrtc_LIBRARY}" CACHE FILEPATH "")
if(CUDA_NVRTC_LIB AND NOT CUDA_NVRTC_SHORTHASH)
  find_package(Python COMPONENTS Interpreter)
  message(STATUS "Python_EXECUTABLE: ${Python_EXECUTABLE}")
  message(STATUS "CUDA_NVRTC_LIB: ${CUDA_NVRTC_LIB}")
  execute_process(
    COMMAND ${Python_EXECUTABLE} -c
    "import hashlib;hash=hashlib.sha256();hash.update(open('${CUDA_NVRTC_LIB}','rb').read());print(hash.hexdigest()[:8])"
    RESULT_VARIABLE _retval
    OUTPUT_VARIABLE CUDA_NVRTC_SHORTHASH)
  message(STATUS "execute_process result: ${_retval}")
  message(STATUS "execute_process output: ${CUDA_NVRTC_SHORTHASH}")
  if(NOT _retval EQUAL 0)
    message(WARNING "Failed to compute shorthash for libnvrtc.so")
    set(CUDA_NVRTC_SHORTHASH "XXXXXXXX")
  else()
    string(STRIP "${CUDA_NVRTC_SHORTHASH}" CUDA_NVRTC_SHORTHASH)
    message(STATUS "${CUDA_NVRTC_LIB} shorthash is ${CUDA_NVRTC_SHORTHASH}")
  endif()
endif()
```


## 配置环境变量
``` bash
# 启用新编译器New++ABI
export _GLIBCXX_USE_CXX11_ABI=1
# 设置环境变量
export USE_NCCL=1
export USE_SYSTEM_NCCL=0
export USE_DISTRIBUTED=1
export USE_QNNPACK=0
export USE_PYTORCH_QNNPACK=0
export TORCH_CUDA_ARCH_LIST="8.7"
export PYTORCH_BUILD_VERSION=2.6.0
export PYTORCH_BUILD_NUMBER=1
export USE_PRIORITIZED_TEXT_FOR_LD=1 

export CUDA_HOME=/usr/local/cuda-12.8
export CUDA_PATH=$CUDA_HOME

export MAGMA_INCLUDE_DIR=/usr/local/magma/include
export MAGMA_LIBRARIES=/usr/local/magma/lib/

export BLAS=OpenBLAS
export USE_CUDNN=1
export USE_CUSPARSELT=1
export USE_CUDSS=1
export USE_CUFILE=1
export USE_XPU=0
export USE_FBGEMM=0
export USE_MPI=0
export NCCL_ENABLE_SLIMMING=1

export CMAKE_PREFIX_PATH="${CONDA_PREFIX:-'$(dirname $(which conda))/../'}:${CMAKE_PREFIX_PATH}"
```

## 构建 wheel
``` bash
python setup.py bdist_wheel
```


## 其他需要解决的警告与错误

需要单独修改的区域汇总：

``` bash
CMake Warning at caffe2/CMakeLists.txt:1476 (target_link_libraries):
  Target "torch_cpu" requests linking to directory
  "/usr/lib/aarch64-linux-gnu".  Targets may link only to libraries.  CMake
  is dropping the item.


CMake Warning at caffe2/CMakeLists.txt:1476 (target_link_libraries):
  Target "torch_cpu" requests linking to directory
  "/usr/lib/aarch64-linux-gnu".  Targets may link only to libraries.  CMake
  is dropping the item.


CMake Warning at cmake/public/cuda.cmake:249 (target_link_libraries):
  Target "torch_cuda" requests linking to directory "/usr/local/cuda/lib64".
  Targets may link only to libraries.  CMake is dropping the item.
Call Stack (most recent call first):
  cmake/Dependencies.cmake:44 (include)
  CMakeLists.txt:896 (include)


CMake Warning at cmake/public/cuda.cmake:265 (target_link_libraries):
  Target "torch_cuda" requests linking to directory
  "/usr/lib/aarch64-linux-gnu/libcudss/12".  Targets may link only to
  libraries.  CMake is dropping the item.
Call Stack (most recent call first):
  cmake/Dependencies.cmake:44 (include)
  CMakeLists.txt:896 (include)


CMake Warning at cmake/public/cuda.cmake:233 (target_link_libraries):
  Target "torch_cuda" requests linking to directory
  "/usr/lib/aarch64-linux-gnu".  Targets may link only to libraries.  CMake
  is dropping the item.
Call Stack (most recent call first):
  cmake/Dependencies.cmake:44 (include)
  CMakeLists.txt:896 (include)


CMake Warning at cmake/public/cuda.cmake:233 (target_link_libraries):
  Target "cuda_cudnn_test" requests linking to directory
  "/usr/lib/aarch64-linux-gnu".  Targets may link only to libraries.  CMake
  is dropping the item.
Call Stack (most recent call first):
  cmake/Dependencies.cmake:44 (include)
  CMakeLists.txt:896 (include)


CMake Warning at cmake/public/cuda.cmake:233 (target_link_libraries):
  Target "torch_python" requests linking to directory
  "/usr/lib/aarch64-linux-gnu".  Targets may link only to libraries.  CMake
  is dropping the item.
Call Stack (most recent call first):
  cmake/Dependencies.cmake:44 (include)
  CMakeLists.txt:896 (include)


CMake Warning at cmake/public/cuda.cmake:249 (target_link_libraries):
  Target "torch_python" requests linking to directory
  "/usr/local/cuda/lib64".  Targets may link only to libraries.  CMake is
  dropping the item.
Call Stack (most recent call first):
  cmake/Dependencies.cmake:44 (include)
  CMakeLists.txt:896 (include)
  
  CMake Warning at /home/rm01/setup/develop/pytorch/build/CMakeFiles/CMakeScratch/TryCompile-BeI8oE/CMakeLists.txt:28 (target_link_libraries):
  Target "cmTC_b0ac7" requests linking to directory
  "/usr/lib/aarch64-linux-gnu".  Targets may link only to libraries.  CMake
  is dropping the item.
  
  CMake Warning at /home/rm01/setup/develop/pytorch/build/CMakeFiles/CMakeScratch/TryCompile-1oKRNd/CMakeLists.txt:28 (target_link_libraries):
  Target "cmTC_03118" requests linking to directory
  "/usr/lib/aarch64-linux-gnu".  Targets may link only to libraries.  CMake
  is dropping the item.
```

## 拉取指定版本的torchvision
``` bash
git clone -b v0.20.1 --recursive https://github.com/pytorch/vision
cd vision
```

## 配置环境变量
``` bash
# 启用CUDA
export CUDA_HOME=/usr/local/cuda-12.8
export CUDA_PATH=$CUDA_HOME
export WITH_CUDA=ON

# 安装视频处理后端
conda install -c conda-forge 'ffmpeg<4.3'
```

## 构建 wheel
``` bash
python setup.py bdist_wheel
```

## 拉取指定版本的torch
``` bash
git clone -b v2.5.1 --recursive https://github.com/pytorch/audio.git
cd pytorch

export CUDA_HOME=/usr/local/cuda-12.8
export CUDA_PATH=$CUDA_HOME
export USE_CUDA=ON
python setup.py bdist_wheel
```

## torch编译后安装 [参考](https://pytorch.org/FBGEMM/fbgemm_gpu-development/InstallationInstructions.html)

```bash
pip install fbgemm-gpu
```
