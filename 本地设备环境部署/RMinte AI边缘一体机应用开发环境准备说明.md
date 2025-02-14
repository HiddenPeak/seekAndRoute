# RMinte AI边缘一体机应用开发环境准备说明

## 一、硬件准备

1. RM01开发板（暂缺）
2. RMinte开发用工作站或服务器最低配置要求
   1. Intel 12代CPU或更新 （或五代至强）
   2. 64GB以上RAM （推荐192GB或更多）
   3. RTX A6000 with NVLink （或 RTX 6000 Ada）—— 96GB显存

## 二、开发环境准备

1. RM01开发板（已经预置）

2. 手动部署

   1. 安装Ubuntu 22.04或更新版本系统

   2. 安装驱动https://www.nvidia.com/en-us/drivers/

      1. 生产环境使用的稳定版本
      2. 最新的实验版本
      3. 最新修复漏洞的beta版本

   3. 如果需要使用最新稳定版本的cuda编译器的话需要使用beta版本的驱动

      ```bash
      nvidia-smi
      ```

      终端显示如下：

      ```bash
      Sun Dec  8 18:53:44 2024       
      +-----------------------------------------------------------------------------------------+
      | NVIDIA-SMI 560.35.05              Driver Version: 560.35.05      CUDA Version: 12.6     |
      |-----------------------------------------+------------------------+----------------------+
      | GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
      | Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
      |                                         |                        |               MIG M. |
      |=========================================+========================+======================|
      |   0  NVIDIA RTX A6000               Off |   00000000:01:00.0 Off |                  Off |
      | 30%   36C    P8             19W /  300W |     258MiB /  49140MiB |      0%      Default |
      |                                         |                        |                  N/A |
      +-----------------------------------------+------------------------+----------------------+
      |   1  NVIDIA RTX A6000               Off |   00000000:02:00.0 Off |                  Off |
      | 30%   34C    P8             22W /  300W |      15MiB /  49140MiB |      0%      Default |
      |                                         |                        |                  N/A |
      +-----------------------------------------+------------------------+----------------------+
                                                                                               
      +-----------------------------------------------------------------------------------------+
      | Processes:                                                                              |
      |  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
      |        ID   ID                                                               Usage      |
      |=========================================================================================|
      |    0   N/A  N/A      2279      G   /usr/lib/xorg/Xorg                             64MiB |
      |    0   N/A  N/A      2854      G   /usr/bin/gnome-shell                          154MiB |
      |    1   N/A  N/A      2279      G   /usr/lib/xorg/Xorg                              4MiB |
      +-----------------------------------------------------------------------------------------+
      ```

3. 安装Cuda-toolkit

   1. 添加包管理器源或者下载本地安装包
   2. 安装cuda选择需要的版本

   https://developer.nvidia.com/cuda-toolkit

   安装最新版本的cuda之后，需要手动配置环境变量以保证系统能够正常调用

   ```bash
   # 添加环境变量 注意版本号要一致
   export PATH=/usr/local/cuda-12.6/bin${PATH:+:${PATH}}
   # 查看版本是否匹配
   nvcc -V
   ```

   

   终端显示如下：

   ```bash
   nvcc: NVIDIA (R) Cuda compiler driver
   Copyright (c) 2005-2024 NVIDIA Corporation
   Built on Tue_Oct_29_23:50:19_PDT_2024
   Cuda compilation tools, release 12.6, V12.6.85
   Build cuda_12.6.r12.6/compiler.35059454_0
   ```

   

   版本符合要求就可以添加环境变量.bashrc中

   ```bash
   sudo nano ~/.bashrc
   ```

4. 安装NCCL

   https://docs.nvidia.com/deeplearning/nccl/install-guide/index.html

   ```bash
   wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
   sudo dpkg -i cuda-keyring_1.1-1_all.deb
   sudo apt-get update
   ```

   

   添加安装源后：

   ```bash
   sudo apt install libnccl2=2.23.4-1+cuda12.6 libnccl-dev=2.23.4-1+cuda12.6
   ```

   

   根据操作系统和版本选择下载或网络安装libnccl2 libnccl-dev即可。

   如果考虑安装NVIDIA HPC SDK则可以不需要如上单独安装

   ```bash
   # NVIDIA HPC SDK Install
   curl https://developer.download.nvidia.com/hpc-sdk/ubuntu/DEB-GPG-KEY-NVIDIA-HPC-SDK | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-hpcsdk-archive-keyring.gpg
   echo 'deb [signed-by=/usr/share/keyrings/nvidia-hpcsdk-archive-keyring.gpg] https://developer.download.nvidia.com/hpc-sdk/ubuntu/amd64 /' | sudo tee /etc/apt/sources.list.d/nvhpc.list
   sudo apt-get update -y
   sudo apt-get install -y nvhpc-24-11
   ```

   

   安装完成后可以使用NCCL-test对卡间通讯进行测试

   https://github.com/NVIDIA/nccl-tests

5. 安装conda

   https://docs.anaconda.com/miniconda/install/#quick-command-line-install

   可以使用如下命令行安装

   ```bash
   mkdir -p ~/miniconda3
   wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
   bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
   rm ~/miniconda3/miniconda.sh
   ```

   完成后激活环境变量

   ```bash
   source ~/miniconda3/bin/activate
   ```

   初始化conda，打开所有shell时自动激活

   ```bash
   conda init --all
   ```

6. 安装Xinference

​	https://inference.readthedocs.io/zh-cn/latest/getting_started/installation.html

​	根据以上页面提示安装后可以拉起嵌入模型、重排模型后，再拉起大语言模型

7. Dify安装

   参考以下链接

   https://docs.dify.ai/zh-hans/getting-started/install-self-hosted/docker-compose

   完成后配置模型供应商：

   Xinference

   如果使用RM01开发板则使用 RMinte 作为模型服务供应商

8. 配置模型下载加速

   安装modelscope库

   ```bash
   pip install modelscope
   ```

   使用命令行下载到指定文件夹

   ```bash
   modelscope download --model OpenGVLab/InternVL2_5-78B --local_dir ./dir
   ```

   如果不采用--local_dir参数则会直接下载到modelscope的默认文件夹，由于需要统一管理，所以要求指定文件夹。

   
