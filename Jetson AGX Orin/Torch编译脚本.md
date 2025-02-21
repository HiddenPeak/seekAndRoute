# Torch编译脚本

``` bash
# 开发者调试 PyTorch 核心代码时使用，能更容易追踪问题，但会显著增加编译时间和生成文件大小。
export DEBUG=1
# 需要既有性能又能调试的构建，适合性能分析或生产环境下的问题排查。
export REL_WITH_DEB_INFO=1
# 指定编译时的最大并行任务数, 12+1 或者 12*1.5
export MAX_JOBS=18
# 如果不需要 GPU 支持或系统没有 CUDA，可以禁用以加快编译
export USE_CUDA=1
# 在 CUDA 12 上可能会因内存不足（OOM）而失败，默认禁用。
export DEBUG_CUDA=1
# 启用或禁用 cuDNN 支持
export USE_CUDNN=1
# 启用或禁用 cuSPARSELt 支持（用于稀疏线性代数）
export USE_CUSPARSELT=1
# 启用或禁用 cuDSS 支持（CUDA 直接稀疏求解器）
export USE_CUDSS=1
# 启用或禁用 cuFile 支持（用于 GPU 直接存储访问），需要高效 I/O 操作时启用
export USE_CUFILE=0
# 启用或禁用 FBGEMM（Facebook General Matrix Multiply）支持
export USE_FBGEMM=0
# 启用或禁用 Kineto 库，需要分析 PyTorch 性能时启用
export USE_KINETO=0
# 启用或禁用 NumPy 支持
export USE_NUMPY=0
# 启用或禁用测试构建
export BUILD_TEST=0
# 启用或禁用 MKL-DNN（Intel 的深度学习优化库）
export USE_MKLDNN=0
# 在 ARM 架构上启用 MKL-DNN 的 Compute Library 后端，必须同时启用 USE_MKLDNN，在 ARM 设备上优化性能。
export USE_MKLDNN_ACL=1
# 指定 MKL-DNN 的线程模式，可选 TBB 或 OMP（默认）。根据系统线程库选择合适的并行后端
export MKLDNN_CPU_RUNTIME=OMP
# 启用或禁用 NNPACK（神经网络推理优化库）
export USE_NNPACK=0
```

