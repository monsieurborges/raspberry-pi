# Installing OpenCV

![OpenCV](../assets/opencv.png)

## Installing OpenCV using Pip

Pip is the easiest way to install OpenCV.

[Dave Jones](https://github.com/waveform80) (creator of the Picamera Python module) and [Ben Nuttall](https://github.com/bennuttall) (Raspberry Pi community) run [piwheels.org](https://www.piwheels.org), a Python package repository providing pre-compiled binaries packages for the Raspberry Pi.

There are four OpenCV packages that are pip-installable on the PyPI repository:

* [opencv-python](https://pypi.org/project/opencv-python/)

    This repository contains just the main modules of the OpenCV library.

* [opencv-contrib-python](https://pypi.org/project/opencv-contrib-python/)

    This repository contains both the main modules along with the contrib modules.

* [opencv-python-headless](https://pypi.org/project/opencv-python-headless/)

    Same as opencv-python but no GUI functionality.

* [opencv-contrib-python-headless](https://pypi.org/project/opencv-contrib-python-headless/)

    Same as opencv-contrib-python but no GUI functionality.

> If you need non-free algorithms you need to compile and install OpenCV from source.

### 1. Install dependencies

```bash
sudo apt-get update && sudo apt-get upgrade
```

```bash
sudo apt-get install \
    libhdf5-dev libhdf5-serial-dev libhdf5-100 \
    libqtgui4 libqtwebkit4 libqt4-test python3-pyqt5 \
    libatlas-base-dev \
    libjasper-dev
```

### 2. Create a virtual environment

```bash
mkvirtualenv pipcv4 -p python3
```

### 3. Install OpenCV

```bash
# Activate Python environment
workon pipcv4

# Install numpy
pip install numpy

# Install OpenCV with contrib
pip install opencv-contrib-python
```

## Installing OpenCV from source

### 1. Install dependencies

Let's update the system:

```bash
sudo apt-get update && sudo apt-get upgrade
```

Install developer tools including [CMake](https://cmake.org):

```bash
sudo apt-get install build-essential cmake unzip pkg-config
```

Install a selection of image and video libraries:

```bash
sudo apt-get install libjpeg-dev libpng-dev libtiff-dev \
  libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
  libxvidcore-dev libx264-dev
```

Install GTK, a GUI backend (only if you have a Raspberry Pi with Desktop):

```bash
sudo apt-get install libgtk-3-dev
sudo apt-get install libcanberra-gtk*
```

Install two packages for numerical optimizations:

```bash
sudo apt-get install libatlas-base-dev gfortran
```

Install the Python 3 development headers:

```bash
sudo apt-get install python3-dev
```

Create and activate a Python environment:

```bash
mkvirtualenv cv4
```

And finally, install numpy:

```bash
pip install numpy
```

### 2. Downloading OpenCV files

Copy and paste the following commands:

```bash
OPENCV_VERSION="4.1.0"
OPENCV_URL="https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.tar.gz"
OPENCV_CONTRIB_URL="https://github.com/opencv/opencv_contrib/archive/${OPENCV_VERSION}.tar.gz"
```

```bash
cd ${HOME}

curl -L ${OPENCV_URL} -o "opencv.tar.gz"
curl -L ${OPENCV_CONTRIB_URL} -o "opencv_contrib.tar.gz"

tar -xzf opencv.tar.gz
tar -xzf opencv_contrib.tar.gz

mkdir -p opencv-${OPENCV_VERSION}/build
cd opencv-${OPENCV_VERSION}/build
```

### 3. Configuring OpenCV installation

```bash
# Configure OpenCV via CMake
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES_PATH="../../opencv_contrib-${OPENCV_VERSION}/modules" \
    -D ENABLE_NEON=ON \
    -D ENABLE_VFPV3=ON \
    -D BUILD_TESTS=OFF \
    -D OPENCV_ENABLE_NONFREE=ON \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D BUILD_EXAMPLES=OFF ..
```

Check if the interpreter points to the correct Python 3 binary, and if numpy points to the NumPy package installed inside the virtual environment.

* `-D OPENCV_ENABLE_NONFREE=ON`  flag ensures that you'll have access to SIFT/SURF and other non-free algorithms.
* [NEON](https://developer.arm.com/architectures/instruction-sets/simd-isas/neon) is an optimization architecture extension for ARM processors designed specifically for faster video processing, image processing, speech recognition, and machine learning.
* [VFPv3](https://developer.arm.com/architectures/instruction-sets/floating-point) is the name for ARM's Vector Floating Poing (VFP) version 3.

### 4. Increase the Raspberry Pi SWAP

Before the compile process, it is recommended to increase the swap space. More swap space enables to compile LibRealSense with all cores of the Raspberry Pi without having problems due to full memory.

Edit and restart the swap service:

```bash
sudo sed -i 's/CONF_SWAPSIZE=100/CONF_SWAPSIZE=2048/g' /etc/
dphys-swapfile
```

```bash
sudo /etc/init.d/dphys-swapfile stop
```

```bash
sudo /etc/init.d/dphys-swapfile start
```

### 5. Make & make install

```bash
make -j4
```

```bash
sudo make -j4 install
```

```bash
sudo ldconfig
```

### 6. Reset the the Raspberry Pi SWAP

> Remember to go back and reset the swap size:

```bash
sudo sed -i 's/CONF_SWAPSIZE=2048/CONF_SWAPSIZE=100/g' /etc/dphys-swapfile
```

```bash
sudo /etc/init.d/dphys-swapfile stop
sudo /etc/init.d/dphys-swapfile start
```

We enabled a large swap only to compile LibRealSense. It is not prudent to keep a large swap size for a long time. Increasing swap size is a great way to burn out your SD card. Flash-based storage have limited number of writes you can perform until the card is essentially unable to keep the recorded data.

### 7. Sym-link OpenCV to your virtual environment

Copy and paste the following commands on your terminal:

```bash
# Get the OpenCV library path
opencv_lib="$(ls /usr/local/lib/python3.*/site-packages/cv2/python-3.*/cv2.*.so)"

# Activate cv4 Python environment
workon cv4

# Get the site-packages path of the virtual environment
site_packages="$(python3 -c "import distutils.sysconfig as s; print(s.get_python_lib())")"

# Create a link to OpenCV library
ln -s ${opencv_lib} ${site_packages}
```

### 8. Test installation with Python

```bash
# Check if the OpenCV library is well configured
python -c "import cv2;  print(cv2.__version__)"
# The output should be 4.1.0
```

## References

[1] Adrian Rosebrock. [OpenCV Tutorials, Resources, and Guides](https://www.pyimagesearch.com/opencv-tutorials-resources-guides/). PyImageSearch.

[2] Adrian Rosebrock. [Install OpenCV 4 on your Raspberry Pi](https://www.pyimagesearch.com/2018/09/26/install-opencv-4-on-your-raspberry-pi/). PyImageSearch, September 26, 2018.

[3] Adrian Rosebrock. [Pip install opencv](https://www.pyimagesearch.com/2018/09/19/pip-install-opencv/). PyImageSearch. September 19, 2018.

[4] Ben Nuttall. [New opencv builds](https://blog.piwheels.org/new-opencv-builds/). Piwheels. September 27, 2018.

[5] Sagi Zeevi. [Build a faster OpenCV for Raspberry Pi3](https://www.theimpossiblecode.com/blog/build-faster-opencv-raspberry-pi3/). October 30, 2017.

[6] Scott Robinson. [Python Virtual Environments: A Primer](https://realpython.com/python-virtual-environments-a-primer/). Real Python. December 01, 2018.

[7] Shane Pfaffly. [How to change Raspberry Pi's Swapfile Size on Raspbian](https://www.bitpi.co/2015/02/11/how-to-change-raspberry-pis-swapfile-size-on-rasbian/). BitPi.co . February 11, 2015.
