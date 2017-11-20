# Docs

https://github.com/NVIDIA/DIGITS/tree/digits-6.0

https://github.com/NVIDIA/caffe/blob/caffe-0.15/docs/installation.md

https://github.com/NVIDIA/DIGITS/blob/digits-6.0/docs/Configuration.md

# Install Ubuntu 16.04



# Install Nvidia driver

Update the PCI hardware database

```
$ sudo update-pciids
```

Disable "nouveau" and prepare for nvidia driver install

```
$ sudo echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf
$ sudo update-initramfs -u && sudo reboot
```

Check "nouveau" by this command if there is no list display :

```
$ lsmod |grep nouveau
```

Install nvidia driver with deb packge

```
$ sudo apt-cache search nvidia |grep 375    ##check new driver(but not beta) to install
$ sudo apt-get intall nvidia-375
$ sudo reboot   	  ##need reboot to let nvidia driver work and can check it with next command
$ nvidia-smi   	  ##if it worked,will report all your CUDA-capable devices in the system
```

# Install cuda toolkit 8

Download form https://developer.nvidia.com/cuda-downloads, there is 4 installer type for ubuntu16.04,at here,choose "deb(network)".

```
$ wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
$ sudo dpkg -i cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
$ sudo apt-get update
$ sudo apt-get install cuda
```

# Install cudnn 5.1

Download form https://developer.nvidia.com/rdp/cudnn-download， 

```
$ tar -zxvf cudnn-8.0-linux-x64-v5.1.tgz
$ cd cuda
$ sudo cp lib64/* /usr/local/cuda/lib64/
$ sudo cp include/cudnn.h /usr/local/cuda/include
```

Fix error :

```
$ cd /usr/local/cuda/lib64/   
$ sudo rm -rf libcudnn.so libcudnn.so.5   
$ sudo ln -s libcudnn.so.5.1.5 libcudnn.so.5   
$ sudo ln -s libcudnn.so.5 libcudnn.so
```

# Build Protobuf 3

Dependencies

```
$ sudo apt-get install autoconf automake libtool curl make g++ git python-dev python-setuptools unzip
```

Download Source

DIGITS is currently compatiable with Protobuf 3.2.x

```
$ export PROTOBUF_ROOT=~/protobuf
$ git clone https://github.com/google/protobuf.git $PROTOBUF_ROOT -b '3.2.x'
```

Building Protobuf

```
$ cd $PROTOBUF_ROOT
$ ./autogen.sh
$ ./configure
$ make "-j$(nproc)"
$ sudo make install
$ sudo ldconfig
$ cd python
$ sudo python setup.py install --cpp_implementation
```

# Build Caffe 0.15

Dependencies packages

```
$ sudo apt-get install --no-install-recommends build-essential cmake git gfortran libatlas-base-dev libboost-filesystem-dev libboost-python-dev libboost-system-dev libboost-thread-dev libgflags-dev libgoogle-glog-dev libhdf5-serial-dev libleveldb-dev liblmdb-dev libopencv-dev libsnappy-dev python-all-dev python-dev python-h5py python-matplotlib python-numpy python-opencv python-pil python-pip python-pydot python-scipy python-skimage python-sklearn
$ sudo apt-get install --no-install-recommends libboost-all-dev
$ sudo apt-get install libprotobuf-dev protobuf-compiler libnccl-dev libnccl1=1.2.3-1+cuda8.0
```

Set environment variable

```
$ sudo vim /etc/profile
export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
export CAFFE_ROOT=/home/ubuntu/caffe${CAFFE_ROOT:+${CAFFE_ROOT}}
$ soure /etc/profile
```

Dwonload source

```
$ export CAFFE_ROOT=~/caffe
$ git clone https://github.com/NVIDIA/caffe.git $CAFFE_ROOT -b 'caffe-0.15'
```

Install Python packages

```
$ sudo pip install -r $CAFFE_ROOT/python/requirements.txt
```

If you hit some errors about missing imports, then use this command to install the packages in order

```
$ cat $CAFFE_ROOT/python/requirements.txt | xargs -n1 sudo pip install
```

Build Caffe 

```
$ cd $CAFFE_ROOT
$ mkdir build
$ cd build
$ cmake -DUSE_NCCL=ON -DUSE_CUDNN=ON ..
$ make -j"$(nproc)"
$ make install
```

??????? BUG?

/home/ubuntu/caffe/build/install/bin/caffe --version

# Build Digits6

Prerequisites

```
# For Ubuntu 16.04
$ CUDA_REPO_PKG=http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
$ ML_REPO_PKG=http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb

# Install repo packages
$ wget "$CUDA_REPO_PKG" -O /tmp/cuda-repo.deb && sudo dpkg -i /tmp/cuda-repo.deb && rm -f /tmp/cuda-repo.deb
$ wget "$ML_REPO_PKG" -O /tmp/ml-repo.deb && sudo dpkg -i /tmp/ml-repo.deb && rm -f /tmp/ml-repo.deb

# Download new list of packages
$ sudo apt-get update
```

Dependencies packages

```
$ sudo apt-get install --no-install-recommends git graphviz python-dev python-flask python-flaskext.wtf python-gevent python-h5py python-numpy python-pil python-pip python-scipy python-tk
```

Download source

```
$ DIGITS_ROOT=~/digits
$ git clone https://github.com/NVIDIA/DIGITS.git $DIGITS_ROOT -b 'digits-6.0'
```

Install python packages

```
$ sudo pip install -r $DIGITS_ROOT/requirements.txt
```

Starting the server

```
$ ./digits-devserver
```

# Set Environment Variables for Digits

| Variable                  | Example value | Description                              |
| ------------------------- | ------------- | ---------------------------------------- |
| `DIGITS_JOBS_DIR`         | ~/digits-jobs | Location where job files are stored. Default is `$DIGITS_ROOT/digits/jobs`. |
| `DIGITS_LOGFILE_FILENAME` | ~/digits.log  | File for saving log messages. Default is `$DIGITS_ROOT/digits/digits.log`. |

# Set auto-start

```
$ sudo vim /etc/rc.loacl
su - ubuntu -c /home/ubuntu/digits/digits-devserver
```
