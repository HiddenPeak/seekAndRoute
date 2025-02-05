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

## 安装到默认位置
``` bash
# 创建目录
sudo mkdir -p /usr/local/magma/include
sudo mkdir -p /usr/local/magma/lib
sudo mkdir -p /usr/local/magma/lib/pkgconfig
# 复制到默认位置
sudo cp build/target/include/* /usr/local/magma/include/
sudo cp build/target/lib/*.so /usr/local/magma/lib/
sudo cp build/target/lib/pkgconfig/*.pc /usr/local/magma/lib/pkgconfig/
```

## 测试 [官方参考](https://icl.utk.edu/projectsfiles/magma/doxygen/run__tests_8py.html)
``` bash
# 拷贝测试脚本到已编译的测试程序目录
cd testing
cp *.py ../build/testing/
# 回到项目目录
cd ..
# 进入已编译的测试程序目录
cd build/testing/
# 运行测试脚本
./run_tests.py --lu --precision s --small --interactive
```

### 部分测试结果如下
``` bash
****************************************************************************************************
./testing_sgetrf --version 2 -c2 -n 1:20:1 -n 30 -n 31 -n 32 -n 33 -n 34 -n 62 -n 63 -n 64 -n 65 -n 66 -n 94 -n 95 -n 96 -n 97 -n 98 -n 126 -n 127 -n 128 -n 129 -n 130 -n 254 -n 255 -n 256 -n 257 -n 258
****************************************************************************************************
% MAGMA 2.9.0 svn 32-bit magma_int_t, 64-bit pointer.
% Compiled for CUDA architectures 87
% CUDA runtime 12060, driver 12060. OpenMP threads 12.
% device 0: Orin, 1300.0 MHz clock, 62840.8 MiB memory, capability 8.7
% Thu Feb  6 00:26:10 2025
% Usage: ./testing_sgetrf [options] [-h|--help]

% ngpu 1, version 2
%   M     N   CPU Gflop/s (sec)   GPU Gflop/s (sec)   |Ax-b|/(N*|A|*|x|)
%========================================================================
    1     1     ---   (  ---  )      0.00 (   0.00)   0.00e+00   ok
    2     2     ---   (  ---  )      0.00 (   0.00)   3.62e-08   ok
    3     3     ---   (  ---  )      0.01 (   0.00)   6.67e-09   ok
    4     4     ---   (  ---  )      0.04 (   0.00)   5.26e-09   ok
    5     5     ---   (  ---  )      0.08 (   0.00)   9.54e-09   ok
    6     6     ---   (  ---  )      0.07 (   0.00)   4.74e-09   ok
    7     7     ---   (  ---  )      0.11 (   0.00)   1.56e-09   ok
    8     8     ---   (  ---  )      0.17 (   0.00)   1.77e-09   ok
    9     9     ---   (  ---  )      0.24 (   0.00)   2.97e-09   ok
   10    10     ---   (  ---  )      0.29 (   0.00)   2.90e-09   ok
   11    11     ---   (  ---  )      0.29 (   0.00)   1.68e-09   ok
   12    12     ---   (  ---  )      0.57 (   0.00)   6.54e-10   ok
   13    13     ---   (  ---  )      0.45 (   0.00)   1.23e-09   ok
   14    14     ---   (  ---  )      0.61 (   0.00)   1.49e-09   ok
   15    15     ---   (  ---  )      0.53 (   0.00)   1.32e-09   ok
   16    16     ---   (  ---  )      0.84 (   0.00)   5.67e-10   ok
   17    17     ---   (  ---  )      0.78 (   0.00)   3.65e-10   ok
   18    18     ---   (  ---  )      0.98 (   0.00)   2.88e-09   ok
   19    19     ---   (  ---  )      0.88 (   0.00)   1.39e-09   ok
   20    20     ---   (  ---  )      1.03 (   0.00)   1.05e-09   ok
   30    30     ---   (  ---  )      1.76 (   0.00)   3.90e-10   ok
   31    31     ---   (  ---  )      1.77 (   0.00)   5.81e-10   ok
   32    32     ---   (  ---  )      1.66 (   0.00)   6.03e-10   ok
   33    33     ---   (  ---  )      2.66 (   0.00)   2.53e-10   ok
   34    34     ---   (  ---  )      2.34 (   0.00)   7.75e-10   ok
   62    62     ---   (  ---  )      5.83 (   0.00)   1.71e-10   ok
   63    63     ---   (  ---  )      5.66 (   0.00)   2.75e-10   ok
   64    64     ---   (  ---  )      4.42 (   0.00)   2.59e-10   ok
   65    65     ---   (  ---  )      5.84 (   0.00)   2.40e-10   ok
   66    66     ---   (  ---  )      7.04 (   0.00)   1.57e-10   ok
   94    94     ---   (  ---  )      3.95 (   0.00)   4.24e-10   ok
   95    95     ---   (  ---  )      6.52 (   0.00)   7.55e-11   ok
   96    96     ---   (  ---  )      5.79 (   0.00)   1.30e-10   ok
   97    97     ---   (  ---  )      6.24 (   0.00)   1.78e-10   ok
   98    98     ---   (  ---  )      6.17 (   0.00)   1.05e-10   ok
  126   126     ---   (  ---  )      6.02 (   0.00)   1.87e-10   ok
  127   127     ---   (  ---  )      5.93 (   0.00)   1.55e-11   ok
  128   128     ---   (  ---  )      5.63 (   0.00)   2.74e-10   ok
  129   129     ---   (  ---  )      2.88 (   0.00)   1.02e-10   ok
  130   130     ---   (  ---  )      5.60 (   0.00)   2.67e-10   ok
  254   254     ---   (  ---  )     12.38 (   0.00)   9.85e-11   ok
  255   255     ---   (  ---  )     22.63 (   0.00)   9.61e-11   ok
  256   256     ---   (  ---  )     19.67 (   0.00)   6.29e-12   ok
  257   257     ---   (  ---  )     21.33 (   0.00)   1.03e-10   ok
  258   258     ---   (  ---  )     21.21 (   0.00)   2.81e-11   ok
  ok
[enter to continue; M to make and re-run]

****************************************************************************************************
./testing_sgetrf --version 3 -c2 -n 1:20:1 -n 30 -n 31 -n 32 -n 33 -n 34 -n 62 -n 63 -n 64 -n 65 -n 66 -n 94 -n 95 -n 96 -n 97 -n 98 -n 126 -n 127 -n 128 -n 129 -n 130 -n 254 -n 255 -n 256 -n 257 -n 258
****************************************************************************************************
% MAGMA 2.9.0 svn 32-bit magma_int_t, 64-bit pointer.
% Compiled for CUDA architectures 87
% CUDA runtime 12060, driver 12060. OpenMP threads 12.
% device 0: Orin, 1300.0 MHz clock, 62840.8 MiB memory, capability 8.7
% Thu Feb  6 00:26:13 2025
% Usage: ./testing_sgetrf [options] [-h|--help]

% ngpu 1, version 3
%   M     N   CPU Gflop/s (sec)   GPU Gflop/s (sec)   |Ax-b|/(N*|A|*|x|)
%========================================================================
    1     1     ---   (  ---  )      0.00 (   0.00)   0.00e+00   ok
    2     2     ---   (  ---  )      0.00 (   0.00)   3.62e-08   ok
    3     3     ---   (  ---  )      0.01 (   0.00)   6.67e-09   ok
    4     4     ---   (  ---  )      0.02 (   0.00)   5.26e-09   ok
    5     5     ---   (  ---  )      0.03 (   0.00)   9.54e-09   ok
    6     6     ---   (  ---  )      0.07 (   0.00)   4.74e-09   ok
    7     7     ---   (  ---  )      0.07 (   0.00)   1.56e-09   ok
    8     8     ---   (  ---  )      0.17 (   0.00)   1.77e-09   ok
    9     9     ---   (  ---  )      0.21 (   0.00)   2.97e-09   ok
   10    10     ---   (  ---  )      0.33 (   0.00)   2.90e-09   ok
   11    11     ---   (  ---  )      0.27 (   0.00)   1.68e-09   ok
   12    12     ---   (  ---  )      0.35 (   0.00)   6.54e-10   ok
   13    13     ---   (  ---  )      0.49 (   0.00)   1.23e-09   ok
   14    14     ---   (  ---  )      0.43 (   0.00)   1.49e-09   ok
   15    15     ---   (  ---  )      0.43 (   0.00)   1.32e-09   ok
   16    16     ---   (  ---  )      0.65 (   0.00)   5.67e-10   ok
   17    17     ---   (  ---  )      1.01 (   0.00)   3.65e-10   ok
   18    18     ---   (  ---  )      0.92 (   0.00)   2.88e-09   ok
   19    19     ---   (  ---  )      0.88 (   0.00)   1.39e-09   ok
   20    20     ---   (  ---  )      1.27 (   0.00)   1.05e-09   ok
   30    30     ---   (  ---  )      2.17 (   0.00)   3.90e-10   ok
   31    31     ---   (  ---  )      2.14 (   0.00)   5.81e-10   ok
   32    32     ---   (  ---  )      2.13 (   0.00)   6.03e-10   ok
   33    33     ---   (  ---  )      3.39 (   0.00)   2.53e-10   ok
   34    34     ---   (  ---  )      3.71 (   0.00)   7.75e-10   ok
   62    62     ---   (  ---  )      6.04 (   0.00)   1.71e-10   ok
   63    63     ---   (  ---  )      6.12 (   0.00)   2.75e-10   ok
   64    64     ---   (  ---  )      4.31 (   0.00)   2.59e-10   ok
   65    65     ---   (  ---  )      6.49 (   0.00)   2.40e-10   ok
   66    66     ---   (  ---  )      6.57 (   0.00)   1.57e-10   ok
   94    94     ---   (  ---  )      3.52 (   0.00)   4.24e-10   ok
   95    95     ---   (  ---  )      6.82 (   0.00)   7.55e-11   ok
   96    96     ---   (  ---  )      6.11 (   0.00)   1.30e-10   ok
   97    97     ---   (  ---  )      6.79 (   0.00)   1.78e-10   ok
   98    98     ---   (  ---  )      0.73 (   0.00)   1.05e-10   ok
  126   126     ---   (  ---  )      7.44 (   0.00)   1.87e-10   ok
  127   127     ---   (  ---  )      1.62 (   0.00)   1.55e-11   ok
  128   128     ---   (  ---  )      6.78 (   0.00)   2.74e-10   ok
  129   129     ---   (  ---  )      1.05 (   0.00)   1.02e-10   ok
  130   130     ---   (  ---  )      7.20 (   0.00)   2.67e-10   ok
  254   254     ---   (  ---  )     15.92 (   0.00)   9.85e-11   ok
  255   255     ---   (  ---  )     15.86 (   0.00)   1.25e-10   ok
  256   256     ---   (  ---  )     15.45 (   0.00)   6.31e-12   ok
  257   257     ---   (  ---  )     15.96 (   0.00)   7.40e-11   ok
  258   258     ---   (  ---  )     16.47 (   0.00)   6.00e-12   ok
  ok
[enter to continue; M to make and re-run]

****************************************************************************************************
summary
****************************************************************************************************
  361 tests in 15 commands passed
  192 tests failed accuracy test
    2 errors detected (crashes, CUDA errors, etc.)
routines with failures:
    testing_sgesv -c
    testing_sgesv_gpu -c
    testing_sgesv_rbt -c
    testing_sgetf2_gpu -c
    testing_sgetrf --version 1 -c2
    testing_sgetrf_gpu --version 1 -c2
    testing_sgetrf_gpu --version 3 -c2
    testing_sgetrf_gpu --version 4 -c2
    testing_sgetrf_gpu --version 5 -c2
    testing_sgetrf_mgpu --ngpu 2 -c2
    testing_sgetri_gpu --version 2 -c
    testing_sgetri_gpu -c

```
