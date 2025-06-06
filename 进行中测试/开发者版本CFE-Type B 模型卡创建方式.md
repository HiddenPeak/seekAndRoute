# 开发者版本CFE-Type B 模型卡创建方式

## 操作流程

1. 模型卡分区
2. 模型卡格式化
3. 配置模型卡标签
4. 创建模型卡目录结构
5. 复制模型
6. 创建自动拉起脚本
7. 重启测试

## 1. 模型卡分区（全新卡）

开发者版本仅支持 ext4 分区，商业发行版支持（NTFS、exfat、ext4格式）

``` bash
# 创建分区 （1.输入 g 创建 GPT 分区表；2.输入 n ,创建新分区，完成后 输入 w 保存并退出）
sudo fdisk /dev/nvme0n1
```

## 2. 模型卡格式化

``` bash
# 命令格式化为 ext4 （假设新分区是 nvme0n1p1）
sudo mkfs.ext4 /dev/nvme0n1p1
```

## 3. 配置模型卡标签

``` bash
# 若选择ext4格式磁盘
# 配置标签
sudo e2label /dev/nvme0n1p1 rm01cfe
# 验证标签
sudo blkid /dev/nvme0n1p1

# 测试配置
sudo mount -a
# 查看挂载情况
lsblk
# 将cfe目录所有设置为 rm01 用户
sudo chown -R rm01:rm01 cfe
# 确认设置情况
ls -l
```

## 4.1 创建模型卡目录结构

``` bash
# 结构如下:
# app (商业发行版可用)
mkdir -p /home/rm01/cfe/app
# autoModel（自动拉起脚本）
mkdir -p /home/rm01/cfe/autoModel
# model（模型）
mkdir -p /home/rm01/cfe/model
```

## 4.2 自动拉起脚本目录结构：

``` bash
# 商业发行版目录：
# - llm（开发者版本不可用）
mkdir -p /home/rm01/cfe/autoModel/llm
mkdir -p /home/rm01/cfe/model/llm
# - embedding（开发者版本不可用）
mkdir -p /home/rm01/cfe/autoModel/embedding
mkdir -p /home/rm01/cfe/model/embedding
# - reranker（开发者版本不可用）
mkdir -p /home/rm01/cfe/autoModel/reranker
mkdir -p /home/rm01/cfe/model/reranker
# 自动脚本 (仅开发者版本可用)：
# - run_llm.sh （大语言模型拉起脚本）
# - run_embedding_model.sh（TEI嵌入模型拉起脚本）
# - run_reranker_model.sh（TEI重排序模型拉起脚本）
# 开发者版本日志：
# - run_llm.log
# - service_error.log
# - service_output.log
```

## 4.3 自动拉起脚本参考1：大语言模型

``` bash
cd /home/rm01/cfe/autoModel
nano run_llm.sh
```

文件内容如下：

``` bash
#!/bin/bash

# 日志文件
LOGFILE="/home/rm01/cfe/autoModel/run_llm.log"

# 检查 /home/rm01/cfe 是否存在（即 NVMe 是否已挂载）
if [ ! -d "/home/rm01/cfe" ]; then
    echo "NVMe not mounted: /home/rm01/cfe does not exist. Exiting." >> "$LOGFILE"
    exit 0
fi

# 写入启动时间
echo "Starting autorun.sh at $(date)" >> "$LOGFILE"

# 初始化 conda 环境
export PATH="/home/rm01/miniconda3/bin:$PATH"
source /home/rm01/miniconda3/etc/profile.d/conda.sh
conda activate vllm085p1 >> "$LOGFILE" 2>&1
if [ $? -ne 0 ]; then
    echo "Failed to activate conda environment 'vllm085p1'" >> "$LOGFILE"
    exit 1
fi
export TORCH_CUDA_ARCH_LIST=8.7
# 启动模型服务 (根据模型调整)
echo "Starting vLLM server at $(date)" >> "$LOGFILE"
vllm serve "/home/rm01/cfe/autoModel/llm" --gpu-memory-utilization=0.75 --served-model-name "RM01LLM" >> "$LOGFILE" 2>&1

# 检查是否失败
if [ $? -ne 0 ]; then
    echo "Failed to start vLLM server at $(date)" >> "$LOGFILE"
else
    echo "vLLM server started successfully at $(date)" >> "$LOGFILE"
fi
```

## 4.4 自动拉起脚本参考2：嵌入模型

``` bash
cd /home/rm01/cfe/autoModel
nano run_embedding_model.sh
```

文件内容如下：

``` bash
#!/bin/bash
exec /home/rm01/.cargo/bin/text-embeddings-router --model-id /home/rm01/cfe/autoModel/embedding/ --port 58080 --hostname 0.0.0.0 --prometheus-port 58180
```

## 4.5 自动拉起脚本参考2：嵌入模型

``` bash
cd /home/rm01/cfe/autoModel
nano run_reranker_model.sh
```

文件内容如下：

``` bash
#!/bin/bash
exec /home/rm01/.cargo/bin/text-embeddings-router --model-id /home/rm01/cfe/autoModel/reranker/ --port 58081 --hostname 0.0.0.0 --prometheus-port 58181
```

## 4.6 配置脚本可执行

``` bash
cd /home/rm01/cfe/autoModel
chmod +x *.sh
ls -l
```

如果配置成功将出现如下提示：

``` bash
drwxrwxr-x 2 rm01 rm01 4096  6月  7 02:20 embedding
drwxrwxr-x 2 rm01 rm01 4096  6月  7 02:20 llm
drwxrwxr-x 2 rm01 rm01 4096  6月  7 02:20 reranker
-rwxrwxr-x 1 rm01 rm01  165  6月  7 02:28 run_embedding_model.sh
-rwxrwxr-x 1 rm01 rm01 1060  6月  7 02:25 run_llm.sh
-rwxrwxr-x 1 rm01 rm01  164  6月  7 02:29 run_reranker_model.sh
```

## 5. 复制或下载模型

使用 scp 复制

``` bash
# 复制 LLM（from LP Mu 10.10.99.99）
scp -r /media/rm01-5/rm01cfe/Qwen2.5-1.5B-Instruct-AWQ/ rm01@10.10.99.98:/home/rm01/cfe/model/llm

# 复制 embedding
scp -r /media/rm01-5/rm01cfe/model/embedding/gte_Qwen2-1.5B-instruct/ rm01@10.10.99.98:/home/rm01/cf
e/model/embedding

# 复制 reranker
scp -r /media/rm01-5/rm01cfe/model/reranker/gte-reranker-modernbert-base/ rm01@10.10.99.98:/home/rm01/cfe/model/reranker
```

保证模型文件夹中直接包含模型文件，而不是文件夹。

当 AGX 设备链接到工厂量产设备时，具有网络访问能力，可以直接下载模型，举例如下：

```bash
# 下载llm模型
cd /home/rm01/cfe/autoModel/llm
modelscope download --model swift/Qwen3-30B-A3B-AWQ --local_dir .
# 下载embedding
cd /home/rm01/cfe/autoModel/embedding
modelscope download --model Qwen/Qwen3-Embedding-0.6B --local_dir .
# 下载reranker
cd /home/rm01/cfe/autoModel/reranker
modelscope download --model Qwen/Qwen3-Reranker-0.6B --local_dir .
```

支持的模型类型如下：
`bert`, `xlm-roberta`, `camembert`, `roberta`, `distilbert`, `nomic_bert`, `mistral`, `gte`, `new`, `qwen2`, `mpnet`, `modernbert`

## 6.创建自动拉起脚本

### run_llm.sh

``` bash
#!/bin/bash

# 日志文件
LOGFILE="/home/rm01/cfe/autorun.log"

# 检查 /home/rm01/cfe 是否存在（即 NVMe 是否已挂载）
if [ ! -d "/home/rm01/cfe" ]; then
    echo "NVMe not mounted: /home/rm01/cfe does not exist. Exiting." | tee -a "$LOGFILE" | logger -t run_llm
    exit 0
fi

# 写入启动时间
echo "Starting autorun.sh at $(date)" | tee -a "$LOGFILE" | logger -t run_llm

# 初始化 conda 环境
export PATH="/home/rm01/miniconda3/bin:$PATH"
source /home/rm01/miniconda3/etc/profile.d/conda.sh
conda activate vllm085p1 2>&1 | tee -a "$LOGFILE" | logger -t run_llm
if [ $? -ne 0 ]; then
    echo "Failed to activate conda environment 'vllm085p1'" | tee -a "$LOGFILE" | logger -t run_llm
    exit 1
fi
export TORCH_CUDA_ARCH_LIST=8.7

# 启动模型服务 (根据模型调整),建议端口调整为 58000
echo "Starting vLLM server at $(date)" | tee -a "$LOGFILE" | logger -t run_llm
vllm serve "/home/rm01/cfe/autoModel/llm" --gpu-memory-utilization=0.75 --served-model-name "RM01LLM" 2>&1 | tee -a "$LOGFILE" | logger -t run_llm

# 检查是否失败
if [ $? -ne 0 ]; then
    echo "Failed to start vLLM server at $(date)" | tee -a "$LOGFILE" | logger -t run_llm
else
    echo "vLLM server started successfully at $(date)" | tee -a "$LOGFILE" | logger -t run_llm
fi
```

### run_embedding_model.sh

``` bash
#!/bin/bash

# 日志文件
LOGFILE="/home/rm01/cfe/autoModel/run_embedding_model.log"

# 日志函数：同时写入文件和发送到 Syslog
log_message() {
    local message="$1"
    echo "$(date '+%Y-%m-%d %H:%M:%S') $message" >> "$LOGFILE"
    logger -t "embedding_model" "$message"
}

# 检查 /home/rm01/cfe 是否存在（即 NVMe 是否已挂载）
if [ ! -d "/home/rm01/cfe" ]; then
    log_message "NVMe not mounted: /home/rm01/cfe does not exist. Exiting."
    exit 0
fi

# 写入启动时间
log_message "Starting run_embedding_model.sh at $(date)"

# 启动嵌入模型服务
log_message "Starting text-embeddings-router at $(date)"
/home/rm01/.cargo/bin/text-embeddings-router --model-id /home/rm01/cfe/autoModel/embedding/ --port 58080 --hostname 0.0.0.0 --prometheus-port 58180 2>&1 | while IFS= read -r line; do log_message "text-embeddings-router: $line"; done &

# 检查是否失败
if [ ${PIPESTATUS[0]} -ne 0 ]; then
    log_message "Failed to start text-embeddings-router at $(date)"
else
    log_message "text-embeddings-router started successfully at $(date)"
fi
```

### run_reranker_model.sh

``` bash
#!/bin/bash

# 日志文件
LOGFILE="/home/rm01/cfe/autoModel/run_reranker_model.log"

# 日志函数：同时写入文件和发送到 Syslog
log_message() {
    local message="$1"
    echo "$(date '+%Y-%m-%d %H:%M:%S') $message" >> "$LOGFILE"
    logger -t "reranker_model" "$message"
}

# 检查 /home/rm01/cfe 是否存在（即 NVMe 是否已挂载）
if [ ! -d "/home/rm01/cfe" ]; then
    log_message "NVMe not mounted: /home/rm01/cfe does not exist. Exiting."
    exit 0
fi

# 写入启动时间
log_message "Starting run_reranker_model.sh at $(date)"

# 启动重排序模型服务
log_message "Starting text-embeddings-router at $(date)"
/home/rm01/.cargo/bin/text-embeddings-router --model-id /home/rm01/cfe/autoModel/reranker/ --port 58081 --hostname 0.0.0.0 --prometheus-port 58181 2>&1 | while IFS= read -r line; do log_message "text-embeddings-router: $line"; done &

# 检查是否失败
if [ ${PIPESTATUS[0]} -ne 0 ]; then
    log_message "Failed to start text-embeddings-router at $(date)"
else
    log_message "text-embeddings-router started successfully at $(date)"
fi
```

## 7. 重启测试

``` bash
# 查看所有模型拉起日志
journalctl -t run_llm -t embedding_model -t reranker_model -f

# 查看 vllm 启动日志
journalctl -t run_llm -f
# 查看 embedding_model 启动日志
journalctl -t embedding_model -f
# 查看 reranker_model 启动日志
journalctl -t reranker_model -f
```
