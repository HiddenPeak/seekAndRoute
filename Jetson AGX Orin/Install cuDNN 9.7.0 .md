# cuDNN 9.7.0 安装
Jetpack 6.2 默认安装版本为 9.3.0.75
``` bash
wget https://developer.download.nvidia.com/compute/cudnn/9.7.0/local_installers/cudnn-local-tegra-repo-ubuntu2204-9.7.0_1.0-1_arm64.deb
sudo dpkg -i cudnn-local-tegra-repo-ubuntu2204-9.7.0_1.0-1_arm64.deb
sudo cp /var/cudnn-local-tegra-repo-ubuntu2204-9.7.0/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cudnn
```

## 安装 cudnn-cuda-12 包
``` bash
sudo apt-get -y install cudnn-cuda-12
```

