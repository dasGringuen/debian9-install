# Debian 9 Stretch - installation notes

# NVIDIA drivers and CUDA

CUDA is needed in order to use Tensorflow and keras

The CUDA part is a Transcript of: https://www.youtube.com/watch?v=1Xu1zsAgaEg

## downgrade to gcc 4.9
- Downgrade gcc to 4.9. After CUDA is installed you can set it back to gcc 6
- To install just the drivers, the gcc 6 works. CUDA works only with gcc 4.9

### Install this packages
- TODO: Check the installation order again

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

Alternatively you can add Debian Jessie repositories to your `sources.list.d` and install it via `apt`:


```bash
sudo sh -c "echo deb http://http.us.debian.org/debian/ jessie main > /etc/apt/sources.list.d"
sudo apt-get update && sudo apt-get install gcc-4.9 g++-4.9 -y
```

After installation remove Debian Jessie repositories from your `sources.list.d` (or just comment line) and run:

```bash
sudo apt-get udpate
```

- Set gcc 4.9 as the default compiler for nvcc

We assume, that you have installed `/usr/local/cuda` symlink (via run installer or manually). Run following:

```bash
sudo ln -s /usr/bin/gcc-4.9 /usr/local/cuda/bin/gcc 
sudo ln -s /usr/bin/g++-4.9 /usr/local/cuda/bin/g++
```

If the above would not work for you, there is an option to nvcc `--compiler-bindir`, which can be used to point to an alternative compilers.

## Install nVidia drivers
```bash
# aptitude update
# aptitude -r install linux-headers-$(uname -r|sed 's,[^-]*-[^-]*-,,')
# apt install firmware-linux build-essential gcc-multilib
```
And follow:
https://linuxconfig.org/how-to-install-the-latest-nvidia-drivers-on-debian-9-stretch-linux

## CUDA

To use GPUs with compute capability (2.0 or 2.1) you should use CUDA version not greater than 8.*.

- Perl stuff from the video

```bash
sudo apt-get install libcupti-dev
./cuda_8.0.61_375.26_linux.run --tar mxvf
sudo cp InstallUtils.pm /usr/lib/x86_64-linug-gnu/perl
export $PERLLIB
```

- Then install CUDA (and patches if present)

```bash
sudo apt-get install libcupti-dev
sudo sh cuda_8.0.61_375.26_linux.run
sudo sh cuda_8.0.61.2_linux.run
```
Note: no need the --override thing if using gcc 4.9

### Cudnn
- Download cudnn 5.1 from the nvidia site
```bash
tar xvzf cudnn-8.0-linux-x64-v5.1-ga.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

### Test
Install TensorFlow and Keras

```bash
sudo pip3 install keras
sudo pip3 install --upgrade tensorflow-gpu
```
- To make it usable from the notebook and for everyuser.Put this in /etc/profile:
```bash
#export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/lib:/usr/lib/x86_64-linux-gnu
export CUDA_HOME=/usr/local/cuda
export PATH=$PATH:/usr/local/cuda/bin
```
And append to /etc/ld.so.conf:
```bash
/usr/local/cuda/lib64
/usr/local/lib
/usr/lib/x86_64-linux-gnu
```

Test the examples (compile them first)

```bash
~/NVIDIA_CUDA-8.0_Samples/bin/x86_64/linux/release/matrixMul

Test with keras

python3 -c "import keras"
```
