# 安装 cuDSS

``` bash
wget https://developer.download.nvidia.com/compute/cudss/0.4.0/local_installers/cudss-local-tegra-repo-ubuntu2204-0.4.0_0.4.0-1_arm64.deb
sudo dpkg -i cudss-local-tegra-repo-ubuntu2204-0.4.0_0.4.0-1_arm64.deb
sudo cp /var/cudss-local-tegra-repo-ubuntu2204-0.4.0/cudss-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cudss
```

