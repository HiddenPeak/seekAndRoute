# Ipex-llm on 4 Arc A770 setup

## Prepare Driver and OneAPI

1. Only support client driver for Arc 770, install **latest** version.
2. OneAPI 2024.2 

``` bash
source /opt/intel/oneapi/2024.2/setvars.sh
```

Show as blow

``` bash
:: initializing oneAPI environment ...
   -bash: BASH_VERSION = 5.1.16(1)-release
   args: Using "$@" for setvars.sh arguments: 
:: advisor -- latest
:: ccl -- latest
:: compiler -- latest
:: dal -- latest
:: debugger -- latest
:: dev-utilities -- latest
:: dnnl -- latest
:: dpcpp-ct -- latest
:: dpl -- latest
:: ipp -- latest
:: ippcp -- latest
:: mkl -- latest
:: mpi -- latest
:: tbb -- latest
:: vtune -- latest
:: oneAPI environment initialized ::
```

Test it, now.

``` bash
sycl-ls
```

### if OK, you can see them

``` bash
[opencl:cpu][opencl:0] Intel(R) OpenCL, 12th Gen Intel(R) Core(TM) i9-12900 OpenCL 3.0 (Build 0) [2024.18.7.0.11_160000]
[opencl:fpga][opencl:1] Intel(R) FPGA Emulation Platform for OpenCL(TM), Intel(R) FPGA Emulation Device OpenCL 1.2  [2024.17.3.0.08_160000]
[opencl:gpu][opencl:2] Intel(R) OpenCL Graphics, Intel(R) Arc(TM) A770 Graphics OpenCL 3.0 NEO  [24.39.31294]
[opencl:gpu][opencl:3] Intel(R) OpenCL Graphics, Intel(R) Arc(TM) A770 Graphics OpenCL 3.0 NEO  [24.39.31294]
[opencl:gpu][opencl:4] Intel(R) OpenCL Graphics, Intel(R) Arc(TM) A770 Graphics OpenCL 3.0 NEO  [24.39.31294]
[opencl:gpu][opencl:5] Intel(R) OpenCL Graphics, Intel(R) Arc(TM) A770 Graphics OpenCL 3.0 NEO  [24.39.31294]
[opencl:gpu][opencl:6] Intel(R) OpenCL Graphics, Intel(R) UHD Graphics 770 OpenCL 3.0 NEO  [24.39.31294]
[level_zero:gpu][level_zero:0] Intel(R) Level-Zero, Intel(R) Arc(TM) A770 Graphics 1.3 [1.3.29735]
[level_zero:gpu][level_zero:1] Intel(R) Level-Zero, Intel(R) Arc(TM) A770 Graphics 1.3 [1.3.29735]
[level_zero:gpu][level_zero:2] Intel(R) Level-Zero, Intel(R) Arc(TM) A770 Graphics 1.3 [1.3.29735]
[level_zero:gpu][level_zero:3] Intel(R) Level-Zero, Intel(R) Arc(TM) A770 Graphics 1.3 [1.3.29735]
[level_zero:gpu][level_zero:4] Intel(R) Level-Zero, Intel(R) UHD Graphics 770 1.3 [1.3.29735]
```

## create conda env

``` bash
# create conda env
conda create -n tomEnv python=3.11
# activate ENV
conda activate tomEnv
```

## install ipex 2.1.4

``` bash
# install ipex 2.1.4
python -m pip install torch==2.1.0.post3 torchvision==0.16.0.post3 torchaudio==2.1.0.post3 intel-extension-for-pytorch==2.1.40+xpu oneccl_bind_pt==2.1.400+xpu --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/cn/
python -m pip install numpy==1.26.4
```

## Sanity Test

``` bash
export OCL_ICD_VENDORS=/etc/OpenCL/vendors

python -c "import torch; import intel_extension_for_pytorch as ipex; print(torch.__version__); print(ipex.__version__); [print(f'[{i}]: {torch.xpu.get_device_properties(i)}') for i in range(torch.xpu.device_count())];"
```

## If it's OK

``` bash
2.1.0.post3+cxx11.abi
2.1.40+xpu
[0]: _DeviceProperties(name='Intel(R) Arc(TM) A770 Graphics', platform_name='Intel(R) Level-Zero', dev_type='gpu', driver_version='1.3.29735', has_fp64=0, total_memory=15473MB, max_compute_units=512, gpu_eu_count=512)
[1]: _DeviceProperties(name='Intel(R) Arc(TM) A770 Graphics', platform_name='Intel(R) Level-Zero', dev_type='gpu', driver_version='1.3.29735', has_fp64=0, total_memory=15473MB, max_compute_units=512, gpu_eu_count=512)
[2]: _DeviceProperties(name='Intel(R) Arc(TM) A770 Graphics', platform_name='Intel(R) Level-Zero', dev_type='gpu', driver_version='1.3.29735', has_fp64=0, total_memory=15473MB, max_compute_units=512, gpu_eu_count=512)
[3]: _DeviceProperties(name='Intel(R) Arc(TM) A770 Graphics', platform_name='Intel(R) Level-Zero', dev_type='gpu', driver_version='1.3.29735', has_fp64=0, total_memory=15473MB, max_compute_units=512, gpu_eu_count=512)
[4]: _DeviceProperties(name='Intel(R) UHD Graphics 770', platform_name='Intel(R) Level-Zero', dev_type='gpu', driver_version='1.3.29735', has_fp64=0, total_memory=59586MB, max_compute_units=32, gpu_eu_count=32)
```

``` bash
python -m ipex_llm.vllm.xpu.entrypoints.openai.api_server \
  --served-model-name "Qwen2.5" \
  --port 8000 \
  --model "Qwen/Qwen2.5-0.5B-Instruct" \
  --trust-remote-code \
  --gpu-memory-utilization 0.75 \
  --device xpu \
  --dtype float16 \
  --enforce-eager \
  --load-in-low-bit sym_int4 \
  --max-model-len 4096 \
  --max-num-batched-tokens 10240 \
  --max-num-seqs 12 \
  --tensor-parallel-size 1 \
  --disable-async-output-proc
```

python ./transformers_low_bit_pipeline.py --repo-id-or-model-path decapoda-research/llama-7b-hf --low-bit sym_int5 --save-path ./llama-7b-sym_int5

**~/rm05/ipex-llm/python/llm/example/CPU/HF-Transformers-AutoModels/Save-Load**



basekit_root=/opt/intel/oneapi

``` bash
source /opt/intel/oneapi/setvars.sh --force
source /opt/intel/oneapi/ccl/latest/env/vars.sh --force


python ./transformers_low_bit_pipeline.py --repo-id-or-model-path /media/a5770/3CDC40AFDC40656C/models/Qwen2.5-72B-Instruct --low-bit asym_int4 --save-path /media/a5770/3CDC40AFDC40656C/models/Qwen2.5-72B-Instruct-asym-int4


```



source /opt/intel/oneapi/setvars.sh **--force**

source /opt/intel/oneapi/ccl/latest/**env**/vars.sh **--force**

/media/a5770/3CDC40AFDC40656C/models

**Qwen2.5-32B-Instruct** **Qwen2.5-72B-AWQ** Qwen2.5-72B-Instruct **Qwen2.5-7B-Instruct**



``` bash
#/bin/bash
export DOCKER_IMAGE=intelanalytics/ipex-llm-inference-cpp-xpu:latest
export CONTAINER_NAME=ollama-ipex-llm
sudo docker run -itd \
        --net=host \
        --device=/dev/dri \
        -v /home/a5770/rm05/models:/models \
        -e no_proxy=localhost,127.0.0.1 \
        --memory="128G" \
        --name=$CONTAINER_NAME \
        -e bench_model="mistral-7b-v0.1.Q4_0.gguf" \
        -e DEVICE=Arc \
        --shm-size="64g" \
        $DOCKER_IMAGE
```



``` bash
export OLLAMA_FLASH_ATTENTION=1
export OLLAMA_INTEL_GPU=1
export OLLAMA_HOST=0.0.0.0:11434
export OLLAMA_MODELS="/home/a5770/rm05/models/ollama/models/"
export OLLAMA_SCHED_SPREAD=1
export OLLAMA_FLASH_ATTENTION=1
export OLLAMA_NUM_GPU=999
export OLLAMA_KEEP_ALIVE=-1
export no_proxy=localhost,127.0.0.1
export ZES_ENABLE_SYSMAN=1
source /opt/intel/oneapi/setvars.sh
export SYCL_CACHE_PERSISTENT=1
# [optional] under most circumstances, the following environment variable may improve performance, but sometimes this may also cause performance degradation
export SYCL_PI_LEVEL_ZERO_USE_IMMEDIATE_COMMANDLISTS=2
# [optional] if you want to run on single GPU, use below command to limit GPU may improve performance
export ONEAPI_DEVICE_SELECTOR=level_zero:0,1,2,3

./ollama serve
```



``` bash
export CCL_WORKER_COUNT=4
export FI_PROVIDER=shm
export CCL_ATL_TRANSPORT=ofi
export CCL_ZE_IPC_EXCHANGE=sockets
export CCL_ATL_SHM=1

# export CCL_LOG_LEVEL=trace 

export USE_XETLA=ON
export SYCL_PI_LEVEL_ZERO_USE_IMMEDIATE_COMMANDLISTS=2
export TORCH_LLM_ALLREDUCE=0
 
source /opt/intel/1ccl-wks/setvars.sh

python -m ipex_llm.vllm.xpu.entrypoints.openai.api_server \
  --served-model-name $served_model_name \
  --port 8000 \
  --model $model \
  --trust-remote-code \
  --block-size 8 \
  --gpu-memory-utilization 0.95 \
  --device xpu \
  --dtype auto \
  --enforce-eager \
  --load-in-low-bit sym_int4 \
  --max-model-len 1860 \
  --max-num-batched-tokens 2048 \
  --max-num-seqs 12 \
  --tensor-parallel-size 2 \
  --disable-async-output-proc \
  --distributed-executor-backend ray
  
  
  
 mpirun -n 2 -ppn 1 ccl_latency -b gpu -i 20 -f 1024 -t 67108864 -e 1
mpirun -n 2 -ppn 1 ccl_bw -b gpu -i 20 -f 1024 -t 67108864 -e 1
```

