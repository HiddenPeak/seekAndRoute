# 安装 NVTX 3

## Python 语言库
``` bash
# Get NVTX using Conda 暂时不支持 CUDA 12.8
conda install -c conda-forge nvtx
# Or Get NVTX using PIP
python3 -m pip install nvtx
```

## C/C++ 语言库
- Get NVTX from [GitHub](https://github.com/NVIDIA/NVTX)
- Get NVTX with NVIDIA Developer Tools
- Get NVTX with the CUDA Toolkit
- Get NVTX using CMake Package Manager (CPM)

## 说明
vLLM启动过程中会加载NVTX3
