# MobileNet-SSDLite-RealSense-TF

MobileNetv2-SSDLite(Tensorflow/MobileNetv2SSDLite) + RealSense D435 + Tensorflow + without Neural Compute Stick(NCS)

## Environment

1. Install changyuheng's [dotfiles](https://github.com/changyuheng/dotfiles).

2. Upgrade `pip` to version 19.0+ (required by TensorFlow).

    ```
    sudo pip3 install --upgrade pip
    ```

3. Install the newest `TensorFlow`.

    ```
    sudo apt install --upgrade python3-widgetsnbextension python3-testresources
    sudo pip install --upgrade tensorflow
    ```

4. Install `OpenCV` version 3.4.

    ```
    sudo pip install --ignore-installed pyzmq
    sudo pip install --upgrade pillow lxml jupyter matplotlib cython
    sudo apt install --upgrade build-essential cmake pkg-config
    sudo apt install --upgrade libjpeg-dev libpng-dev libtiff-dev
    sudo apt install --upgrade libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libxvidcore-dev x264 libx264-dev libdc1394-22-dev libxine2 v4l-utils
    cd /usr/include/linux
    sudo ln -s -f ../libv4l1-videodev.h videodev.h
    cd -
    sudo apt install --upgrade libopenblas-dev gfortran
    sudo apt install --upgrade libtbb-dev
    sudo apt install --upgrade python3-dev
    sudo pip install --upgrade numpy
    sudo apt install --upgrade "gstreamer1.0*"
    sudo apt install ubuntu-restricted-extras
    sudo apt install --upgrade libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
    sudo apt install --upgrade libavresample-dev
    sudo apt install --upgrade libselinux-dev
    sudo apt install --upgrade libgtk-3-dev libgtkglext1 libgtkglext1-dev qt5-default
    sudo apt install --upgrade libatlas-base-dev libfaac-dev libmp3lame-dev libtheora-dev
    sudo apt install --upgrade libvorbis-dev  libopencore-amrnb-dev libopencore-amrwb-dev
    sudo add-apt-repository "deb http://security.ubuntu.com/ubuntu xenial-security main"
    sudo apt update
    sudo apt install --upgrade libjasper1 libjasper-dev
    sudo apt install --upgrade libprotobuf-dev protobuf-compiler
    sudo apt install --upgrade libgoogle-glog-dev libgflags-dev
    sudo apt install --upgrade libgphoto2-dev libeigen3-dev libhdf5-dev doxygen
    sudo pip install --upgrade wheel numpy scipy matplotlib scikit-image scikit-learn ipython dlib
    sudo apt install --upgrade python-opengl
    sudo pip install --upgrade pyopengl
    sudo pip install --upgrade pyopengl_accelerate
    ```

    Reboot.

    ```
    take ~/workspace
    git clone git@github.com:opencv/opencv_contrib.git opencv_contrib-3.4
    cd opencv_contrib-3.4
    git checkout 3.4
    cd ..
    git clone git@github.com:opencv/opencv.git opencv-3.4
    cd opencv-3.4
    git checkout 3.4
    take build
    ```

    ```
    cmake -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-3.4/modules \
        -D BUILD_EXAMPLES=OFF \
        -D PYTHON_DEFAULT_EXECUTABLE=$(which python3) \
        -D INSTALL_C_EXAMPLES=ON \
        -D INSTALL_PYTHON_EXAMPLES=ON \
        -D BUILD_opencv_python2=ON \
        -D BUILD_opencv_python3=ON \
        -D WITH_GTK=ON \
        -D WITH_OPENGL=ON \
        -D WITH_QT=ON \
        -D WITH_TBB=ON \
        -D WITH_V4L=ON \
        ..
    make -j $(($(nproc) * 2))
    sudo make install
    sudo ldconfig
    ```

5. Install `Intel® RealSense™ SDK 2.0`

    ```
    sudo apt install --upgrade libssl-dev libusb-1.0-0-dev
    sudo apt install --upgrade libglfw3-dev libgl1-mesa-dev libglu1-mesa-dev at
    ```

    ```
    cd ~/workspace
    git clone https://github.com/IntelRealSense/librealsense.git
    cd librealsense
    ./scripts/setup_udev_rules.sh
    ./scripts/patch-realsense-ubuntu-lts.sh
    export OpenCV_DIR=~/workspace/opencv-3.4/build
    take bulid
    ```

    ```
    cmake \
        -DBUILD_EXAMPLES=true \
        -DBUILD_PYTHON_BINDINGS=bool:true \
        -DPYTHON_EXECUTABLE=$(which python3) \
        ../
    make -j $(($(nproc) * 2))
    sudo make install
    ```

    ```
    cd ../wrappers/opencv
    take build
    cmake ..
    ```

    vi `../latency-tool/CMakeLists.txt`

    ```
    -target_link_libraries(rs-latency-tool ${DEPENDENCIES})
    +target_link_libraries(rs-latency-tool ${DEPENDENCIES} pthread)
    ```

    ```
    make -j $(($(nproc) * 2))
    sudo make install
    ```

6. Set up TensorFlow models

    ```
    cd ~/workspace
    git clone --recurse-submodules https://github.com/tensorflow/models.git
    export PYTHONPATH=$PYTHONPATH:~/workspace/tensorflow/models/research:~/workspace/tensorflow/models/research/object_detection
    cd models/research
    protoc object_detection/protos/*.proto --python_out=.
    cd object_detection
    wget http://download.tensorflow.org/models/object_detection/ssdlite_mobilenet_v2_coco_2018_05_09.tar.gz
    tar -xzvf ssdlite_mobilenet_v2_coco_2018_05_09.tar.gz
    ```

7. Set up MobileNet-SSD

    ```
    sudo pip install --upgrade pyrealsense2
    cd ~/workspace
    git clone git@github.com:changyuheng/MobileNet-SSDLite-RealSense-TF.git
    cd MobileNet-SSDLite-RealSense-TF
    ```

## Usage

```
export PYTHONPATH=$PYTHONPATH:~/workspace/tensorflow/models/research:~/workspace/tensorflow/models/research/object_detection
python3 MobileNetSSDwithRealSenseTF.py
```
