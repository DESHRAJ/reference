Installing Caffe on Ubuntu 14.04 the right way
-----------------------------------------------

What is Caffe? Why it is used ?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Caffe is a deep learning framework made with expression, speed, and modularity in mind. It is developed by the Berkeley Vision and Learning Center (BVLC) and by community contributors.

Before beginning the installation, I would like to tell you that its a great pain in ass installing the Caffe. 

Installation: 
~~~~~~~~~~~~~
First, you need to install the general dependencies using the following command:

`` sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev libgflags-dev libgoogle-glog-dev liblmdb-dev protobuf-compiler ``


After installing these, you need to install three more dependencies:
    CUDA is required for GPU mode. Installing CUDA is optional but it is recommended for better performance. 
    To install CUDA, you can visit the link https://developer.nvidia.com/cuda-downloads  and then you can download CUDA for Ubuntu 14.04. Start the download and go get some coffee since it is quite large and will take time to download. After successful download, install it. 
    BLAS : Install ATLAS by sudo apt-get install libatlas-base-dev or install OpenBLAS or MKL for better CPU performance.
    BOOST : BOOST C++ library can be downloaded and installed through Sourceforge.
    OpenCV 2.4 or above: For installing OpenCV, follow this link.  
    Python: If you use the default python, then you will need to sudo apt-get install python-dev package.

Compile Caffe:
~~~~~~~~~~~~~~
Now as you have installed the prerequisites on your system, so now you need to edit the config file (Makefile.config). Create the config file by copying the contents of Makefile.config.example file to Makefile.config using the following command:

``cp Makefile.config.example  Makefile.config``

After this, you need to change the configurations in Makefile.config file. Change the config according to the following conditions:
    For cuDNN acceleration, you should uncomment the USE_CUDNN := 1 switch in Makefile.config.
    For CPU-only Caffe, uncomment CPU_ONLY := 1 in Makefile.config.

For the lazy ones, here is the complete the configuration of my Makefile.

``## Refer to http://caffe.berkeleyvision.org/installation.html
# Contributions simplifying and improving our build system are welcome!

# cuDNN acceleration switch (uncomment to build with cuDNN).
USE_CUDNN := 1

# CPU-only switch (uncomment to build without GPU support).
#CPU_ONLY := 1

# To customize your choice of compiler, uncomment and set the following.
# N.B. the default for Linux is g++ and the default for OSX is clang++
# CUSTOM_CXX := g++

# CUDA directory contains bin/ and lib/ directories that we need.
CUDA_DIR := /usr/local/cuda
# On Ubuntu 14.04, if cuda tools are installed via
# "sudo apt-get install nvidia-cuda-toolkit" then use this instead:
CUDA_DIR := /usr

# CUDA architecture setting: going with all of them.
# For CUDA < 6.0, comment the *_50 lines for compatibility.
CUDA_ARCH := -gencode arch=compute_20,code=sm_20 \
		-gencode arch=compute_20,code=sm_21 \
		-gencode arch=compute_30,code=sm_30 \
		-gencode arch=compute_35,code=sm_35 \
		-gencode arch=compute_50,code=sm_50 \
		-gencode arch=compute_50,code=compute_50

# BLAS choice:
# atlas for ATLAS (default)
# mkl for MKL
# open for OpenBlas
BLAS := atlas
# Custom (MKL/ATLAS/OpenBLAS) include and lib directories.
# Leave commented to accept the defaults for your choice of BLAS
# (which should work)!
# BLAS_INCLUDE := /path/to/your/blas
# BLAS_LIB := /path/to/your/blas

# Homebrew puts openblas in a directory that is not on the standard search path
# BLAS_INCLUDE := $(shell brew --prefix openblas)/include
# BLAS_LIB := $(shell brew --prefix openblas)/lib

# This is required only if you will compile the matlab interface.
# MATLAB directory should contain the mex binary in /bin.
# MATLAB_DIR := /usr/local
# MATLAB_DIR := /Applications/MATLAB_R2012b.app

# NOTE: this is required only if you will compile the python interface.
# We need to be able to find Python.h and numpy/arrayobject.h.
PYTHON_INCLUDE := /usr/include/python2.7 \
		/usr/lib/python2.7/dist-packages/numpy/core/include
# Anaconda Python distribution is quite popular. Include path:
# Verify anaconda location, sometimes it's in root.
# ANACONDA_HOME := $(HOME)/anaconda
# PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
		# $(ANACONDA_HOME)/include/python2.7 \
		# $(ANACONDA_HOME)/lib/python2.7/site-packages/numpy/core/include \

# We need to be able to find libpythonX.X.so or .dylib.
PYTHON_LIB := /usr/lib
# PYTHON_LIB := $(ANACONDA_HOME)/lib

# Homebrew installs numpy in a non standard path (keg only)
# PYTHON_INCLUDE += $(dir $(shell python -c 'import numpy.core; print(numpy.core.__file__)'))/include
# PYTHON_LIB += $(shell brew --prefix numpy)/lib

# Uncomment to support layers written in Python (will link against Python libs)
# WITH_PYTHON_LAYER := 1

# Whatever else you find you need goes here.
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib

# If Homebrew is installed at a non standard location (for example your home directory) and you use it for general dependencies
# INCLUDE_DIRS += $(shell brew --prefix)/include
# LIBRARY_DIRS += $(shell brew --prefix)/lib

# Uncomment to use `pkg-config` to specify OpenCV library paths.
# (Usually not necessary -- OpenCV libraries are normally installed in one of the above $LIBRARY_DIRS.)
# USE_PKG_CONFIG := 1

BUILD_DIR := build
DISTRIBUTE_DIR := distribute

# Uncomment for debugging. Does not work on OSX due to https://github.com/BVLC/caffe/issues/171
# DEBUG := 1

# The ID of the GPU that 'make runtest' will use to run unit tests.
TEST_GPUID := 0

# enable pretty build (comment to see full commands)
Q ?= @
``

After making successful changes in the configuration file, you need to run the following commands:

``make all
make test
make runtest
``
(Before running next two compilation commands, you need to make sure that you have set PYTHON and MATLAB path in Makefile.config)
To compile the PYTHON wrappers, you need to run the command,

``make pycaffe``

To compile the MATLAB wrappers, you need to run the command ,

``make metacaffe``

Finally, if you have reached here, then you have installed Caffe on your System successfully. I am sure that after the successful installation of Caffe, you will feel like Heaven ;)    

Errors that may occur during Installation: 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    nvcc is not found during the ``make all`` command. To solve this error, install the whole nvidia-cuda-toolkit using the following command:
``sudo apt-get install nvidia-cuda-toolkit``

    cudnn.hpp does not exist:  To fix this error, you need to place the cudnn.hpp file that resides in the tar file( for eg. cudnn-6.5-linux-x64-v2 in my case) you just downloaded. Copy the file cudnn.hpp to ./caffe/include/ directory with appropriate permissions(644) . After placing the file there, then run the compilation commands again.
This issue was also also reported on github Caffe repository at https://github.com/BVLC/caffe/issues/1325. 

Now you are all done. Now, you can start playing with examples like Training LeNet on MNIST. 

End-Note:
~~~~~~~~~
    Hope this page helps reduce some effort required to install Caffe on your machines. To view our bigger attempts at reducing this overload, checkout the CloudCV organisation page.

    The BVLC Caffe issues and the caffe-users google-group are excellent sources to get your queries solved and debug the errors you face.

    I have taken references from a lot of sources I found online and one can easily find them through the sources cited in above bullet. This wiki is for educational purposes only.