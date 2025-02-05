# 安装 magma

[MAGMA网站](https://developer.nvidia.com/magma)
[MAGMA Github](https://github.com/icl-utk-edu/magma/)

## 从源代码构建
``` bash
git clone https://github.com/icl-utk-edu/magma.git
cd magma
```

## 安装 Fortran 编译器
``` bash
sudo apt-get install gfortran
```

## 配置编译参数
``` bash
echo -e "GPU_TARGET = sm_87\nBACKEND = cuda\nFORT = false" > make.inc
cmake -DGPU_TARGET=sm_80 -DCMAKE_CUDA_FLAGS="-DADD_ -O3 -fPIC -DNDEBUG -I/usr/local/cuda-12.8/include -fopenmp -Wall -Wno-unused-function"   -DCMAKE_C_FLAGS="-DADD_" -DCMAKE_Fortran_FLAGS="-DADD_"  -DCMAKE_CUDA_COMPILER="/usr/local/cuda-12.8/bin/nvcc" -DCMAKE_INSTALL_PREFIX=build/target -DCMAKE_CUDA_ARCHITECTURES=87-real . -Bbuild
```

## 编译
``` bash
cmake --build build -j $(nproc) --target install
```
