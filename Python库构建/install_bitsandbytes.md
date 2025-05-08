## 编译bitsandbytes

``` bash
git clone https://github.com/bitsandbytes-foundation/bitsandbytes.git && cd bitsandbytes/
cmake -DCOMPUTE_BACKEND=cuda -S .
make
pip install -e .   # `-e` for "editable" install, when developing BNB (otherwise leave that out)
```

## 确认是否成功复制
``` bash
# 进入编译目录
cd ~/build4vllm/bitsandbytes
find . -name "*.so"
```

## 确认torch是否工作正常
``` python
import torch
print(torch.cuda.is_available())  # 应该返回 True
print(torch.__version__)
print(torch.version.cuda)  # 查看使用的 CUDA 版本
```

注意：由于 bitsandbytes 依赖 torch ，但 torch 安装时有可能因为其他依赖导致 bitsandbytes 安装，所以当torch安装后，需要确认库安装情况。

## 测试 Adam8bit 是否正常工作
``` python
import torch
import bitsandbytes as bnb

print(dir(bnb))  # 应该能看到 'optim', 'nn', 'cextension' 等模块

model = torch.nn.Linear(1024, 1024).cuda()
adam = bnb.optim.Adam8bit(model.parameters(), min_8bit_size=16384)
print(adam)
print("Device:", next(model.parameters()).device)

print("基于CPU测试")
model = torch.nn.Linear(1024, 1024)
adam = bnb.optim.Adam8bit(model.parameters(), min_8bit_size=16384)
print(adam)
print("Device:", next(model.parameters()).device)
```

