# Debian 9 Stretch - installation notes

## Install nVidia drivers
source: https://www.youtube.com/watch?v=1Xu1zsAgaEg

source: https://linuxconfig.org/how-to-install-the-latest-nvidia-drivers-on-debian-9-stretch-linux

### downgrade to gcc 4.9
- Downgrade gcc to 4.9, after CUDA is installed you can set it back to gcc 6

#### Install this packages
- Check the order again

```bash
package=cpp-4.9_4.9.2-10_amd64.deb 
wget http://ftp.us.debian.org/debian/pool/main/g/gcc-4.9/$package

sudo dpkg -i $package

package=g++-4.9_4.9.2-10_amd64.deb 
wget http://ftp.us.debian.org/debian/pool/main/g/gcc-4.9/$package

package=gcc-4.9_4.9.2-10_amd64.deb  
wget http://ftp.us.debian.org/debian/pool/main/g/gcc-4.9/$package

package=gcc-4.9-base_4.9.2-10_amd64.deb
wget http://ftp.us.debian.org/debian/pool/main/g/gcc-4.9/$package

package=libasan1_4.9.2-10_amd64.deb 
wget http://ftp.us.debian.org/debian/pool/main/c/cloog/libcloog-isl-dev_0.18.2-1+b2_amd64.deb
sudo dpkg -i libcloog-isl-dev_0.18.2-1+b2_amd64.deb

package=libgcc-4.9-dev_4.9.2-10_amd64.deb   
wget http://ftp.us.debian.org/debian/pool/main/i/isl/libisl10_0.12.2-2_amd64.deb
sudo dpkg -i libisl10_0.12.2-2_amd64.deb

package=libstdc++-4.9-dev_4.9.2-10_amd64.deb
wget http://ftp.us.debian.org/debian/pool/main/g/gcc-4.9/$package

wget http://ftp.us.debian.org/debian/pool/main/c/cloog/libcloog-isl4_0.18.2-1+b2_amd64.deb
sudo dpkg -i  libcloog-isl4_0.18.2-1+b2_amd64.deb
```



```bash
sudo unlink /usr/bin/gcc
sudo ln -s /usr/bin/gcc-4.9 /usr/bin/gcc
gcc --version
sudo unlink /usr/bin/g++
sudo ln -s /usr/bin/g++-4.9 /usr/bin/g++
```

```bash
# aptitude update
# aptitude -r install linux-headers-$(uname -r|sed 's,[^-]*-[^-]*-,,')
# apt install firmware-linux build-essential gcc-multilib
```

CUDA
=======
sudo apt-get install libcupti-dev
 
Note: the gcc trick must be done...
sudo sh cuda_8.0.61_375.26_linux.run




### Cudnn
- Download cudnn 5.1 from nvidia
```bash
tar xvzf cudnn-8.0-linux-x64-v5.1-ga.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

### Test
```bash
sudo pip3 install keras
sudo pip3 install --upgrade tensorflow-gpu

put this in /etc/profile . after
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/lib:/usr/lib/x86_64-linux-gnu
export CUDA_HOME=/usr/local/cuda
export PATH=$PATH:/usr/local/cuda/bin

~/NVIDIA_CUDA-8.0_Samples/bin/x86_64/linux/release/matrixMul

python3 -c "import keras"
```
### Set gcc 6 as default again
```bash
sudo unlink /usr/bin/gcc
sudo ln -s /usr/bin/gcc-6 /usr/bin/gcc
gcc --version
sudo unlink /usr/bin/g++
sudo ln -s /usr/bin/g++-6 /usr/bin/g++
```

