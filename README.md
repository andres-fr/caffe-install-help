The plan to successfully install Caffe (and pycaffe) on Ubuntu16 is to:
 1. Install OpenCV with the TIFF libraries explicitly included (usually by compiling it, NOT using `apt` or similar)
 2. Get the `caffe` repo, install required dependencies and make some small changes on the `make` files
 3. Set up the OS environment to make caffe globally accessible. Done!

The contents of this repo should help to speed up this process, although it is not straightforward.
The opencv compiled build dir was split into parts below 100MB using `gzip -c opencv_compiled_builddir.zip | split -b 99000000 - compressed.gz` and can be uncompressed using `cat compressed.gz* | zcat > test.zip`

`zip opencv_compiled_builddir.zip --out opencv_compiled_builddir_part.zip -s 90m`. To restore it, use

1. Compile and install OpenCV WITH TIFF SUPPORT: This repo has a compiled version for ubuntu 16. To do it over again (see https://docs.opencv.org/trunk/d7/d9f/tutorial_linux_install.html): 
   1. `sudo apt-get install build-essential` (compiler)
   2. `sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev` (required)
   3. `sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev` (optional)
   4. `git clone https://github.com/opencv/opencv.git`
   5. `mkdir build && cd build`
   6. `cmake WITH_TIFF=ON ..` or without root permissions: `cmake WITH_TIFF=ON -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/home/rodriguez/opencv_install ..`
   7. `make && sudo make install` without root permission simply `make install`. (this takes a very long time, for Ubuntu16 you can clone from my GitHub already built, and `make`will take *less* time, still notably more than a couple of minutes. In that case you may need to remove the `_CMakeCache.txt`file before the `cmake`)


2. `git clone https://github.com/BVLC/caffe.git`
   1.  `sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler`
   2. `sudo apt-get install --no-install-recommends libboost-all-dev`
   3. `sudo apt-get install -y libatlas-base-dev`
   4. `sudo apt-get install -y libgflags-dev libgoogle-glog-dev liblmdb-dev`
   5. `cp Makefile.config.example Makefile.config`
   6. In `Makefile.config`, if using CPU only, uncomment the line `CPU_ONLY := 1`
   7. In `Makefile.config`, find the line and change to `INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/`
   8. In `Makefile`, find the line and change to `LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_serial_hl hdf5_serial`
   9. `sudo apt-get install python-numpy`
   10. `make all -j 4 && make pycaffe -j 4 && make test && make runtest && && make distribute`
   11. add corresponding line to your `.bashrc` (mine is `export PYTHONPATH=${HOME}/git-work/caffe/python:$PYTHONPATH`)


Test it: open a python console and type `import caffe` and then `help(caffe)`



