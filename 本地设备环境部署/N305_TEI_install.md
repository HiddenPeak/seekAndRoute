# N305 TEI 安装脚本

``` bash
# 安装基础依赖
sudo apt update
sudo apt install -y gpg-agent wget
sudo apt install libssl-dev
sudo apt install pkg-config
# 安装 cc
sudo apt update
sudo apt install build-essential
# 验证安装
cc --version

# 安装mkl
wget -O- https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB | gpg --dearmor | sudo tee /usr/share/keyrings/oneapi-archive-keyring.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/oneapi-archive-keyring.gpg] https://apt.repos.intel.com/oneapi all main" | sudo tee /etc/apt/sources.list.d/oneAPI.list
sudo apt update
sudo apt install intel-oneapi-mkl
sudo apt install intel-oneapi-mkl-devel

# 添加MKL环境变量 （具体路径需要根据实际调整）
echo 'export LD_LIBRARY_PATH=/opt/intel/oneapi/compiler/2025.1/lib:/opt/intel/oneapi/mkl/2025.1/lib/intel64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc

# 安装 Cargo
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
cargo update
rustup update
rustc --version
cargo install --path router -F mkl
```

