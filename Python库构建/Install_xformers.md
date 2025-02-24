# 安装 xformers 

``` bash
git clone -b v0.0.28.post3 --recursive https://github.com/facebookresearch/xformers.git
cd xformers

# pip install build
pip install -r requirements.txt

export TORCH_CUDA_ARCH_LIST="8.0;8.6;8.7"
export CUDA_HOME=/usr/local/cuda-12.8
export CUDA_PATH=$CUDA_HOME

# 修改setup.py 文件，在支持的默认架构列表中增加 8.7

python setup.py bdist_wheel

```

