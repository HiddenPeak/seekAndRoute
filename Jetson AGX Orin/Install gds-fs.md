# 编译gds-nvidia-fs

``` bash
git clone https://github.com/NVIDIA/gds-nvidia-fs.git
cd gds-nvidia-fs/src
export CONFIG_MOFED_VERSION=$(ofed_info -s | cut -d '-' -f 2)
sudo make
sudo insmod nvidia-fs.ko


```
