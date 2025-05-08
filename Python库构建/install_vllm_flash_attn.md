## 拉取对应版本 -b 版本号
``` bash
git clone --recursive https://github.com/vllm-project/flash-attention.git
cd flash-attention
```

## 编辑 CMakeList.txt
``` cmake
# Supported NVIDIA architectures.
set(CUDA_SUPPORTED_ARCHS "8.0;8.6;8.7;8.9;9.0")


set(TORCH_SUPPORTED_VERSION_CUDA "2.7.0")

cuda_archs_loose_intersection(FA2_ARCHS "8.0;8.7;9.0;10.0;10.1;12.0" "${CUDA_ARCHS}")
```

## 编译
``` bash
export TORCH_CUDA_ARCH_LIST="8.6;8.7"
python setup.py bdist_wheel
```

