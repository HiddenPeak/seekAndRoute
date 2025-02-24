# 安装 xgrammar

``` bash
# make sure to start with a fresh environment
conda env remove -n xgrammar-venv
# create the conda environment with build dependency
conda create -n xgrammar-venv -c conda-forge \
    "cmake>=3.18" \
    git \
    python=3.11
# enter the build environment
conda activate xgrammar-venv
# install Python dependency
python3 -m pip install ninja pybind11 torch

pip install pre-commit

# 1. clone from GitHub
git clone --recursive https://github.com/mlc-ai/xgrammar.git && cd xgrammar
# 2. Install pre-commit hooks
pre-commit install
# 3. build XGrammar core and Python bindings
mkdir build && cd build
cmake .. -G Ninja
ninja
# 4. install the Python package
cd ../python
python3 -m pip install .
# 5. (optional) add the python directory to PATH
echo "export PATH=\"$(pwd):\$PATH\"" >> ~/.bashrc

export TORCH_CUDA_ARCH_LIST="8.7"
python -c "import xgrammar; print(xgrammar)"
```

