# OpenCV test

Starting with an Orange PI one with the Armbian 5.90 Xenial image

https://dl.armbian.com/_old/orangepione/archive/Armbian_5.90_Orangepione_Ubuntu_xenial_default_3.4.113_desktop.7z

## Install and build

I had a problem with running out of memory while compiling the Python 3 stuff. To fix this I created a low priority swap file (by default Armbian uses zram swap devices with priority 5 - it is not advisable to use sdram as swap as it wears it out so I set the priority to 0)

```
sudo touch /var/swap
sudo chmod 0600 /var/swap
dd if=/dev/zero of=/var/swap bs=1024 count=524288
sudo mkswap -f /var/swap
sudo swapon -p 0 /var/swap
```

You can list the active swap devices with

```
swapon -s
```

Then I started with the OpenCV build

See https://docs.opencv.org/4.3.0/d7/d9f/tutorial_linux_install.html

Prerequisites:

```
# compiler
sudo apt-get install build-essential

# required
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev

# optional (do this for python build below)
sudo apt-get install python-dev python3-dev python-numpy python3-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
```

Download source from https://opencv.org/releases/

eg:

```
wget https://github.com/opencv/opencv/archive/4.3.0.zip
```

Unzip the source:

```
unzip 4.3.0.zip
```

Change to the source directory and create a build directory

```
cd opencv-4.3.0
mkdir build
cd build
```

Configure the build (including python 3.5 and generating pkg-config info)

```
cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local -DPYTHON3_EXECUTABLE=/usr/bin/python3 -DPYTHON_INCLUDE_DIR=/usr/include/python3.5m -DPYTHON_INCLUDE_DIR2=/usr/include/arm-linux-gnueabihf/python3.5m -DPYTHON_LIBRARY=/usr/lib/arm-linux-gnueabihf/libpython3.5m.so -DPYTHON3_NUMPY_INCLUDE_DIRS=/usr/lib/python3/dist-packages/numpy/core/include/ -DOPENCV_GENERATE_PKGCONFIG=ON ..
```

Then build with 7 parallel jobs (This failed the first time so I restarted without parallel jobs and it seemed to continue)

```
make -j7
```
