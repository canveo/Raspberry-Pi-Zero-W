# Building Qt 5.11 w/ Raspberry Pi3B+/Pi0W   
 _Not verified yet!_

>reference: https://wiki.qt.io/Native_Build_of_Qt5_on_a_Raspberry_Pi

>reference: http://www.tal.org/tutorials/building-qt-510-raspberry-pi-debian-stretch
 
Download the Qt 5.11.2 source archive

    wget http://download.qt.io/official_releases/qt/5.11/5.11.2/single/qt-everywhere-src-5.11.2.tar.xz
    tar xf qt-everywhere-src-5.11.2.tar.xz

Install required build dependencies

    apt-get update
    apt-get install build-essential libfontconfig1-dev libdbus-1-dev libfreetype6-dev libicu-dev libinput-dev    libxkbcommon-dev libsqlite3-dev libssl-dev libpng-dev libjpeg-dev libglib2.0-dev libraspberrypi-dev
    
Install optional development packages

    apt-get install bluez libbluetooth-dev
    apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-ugly gstreamer1.0-plugins-bad libgstreamer-plugins-bad1.0-dev gstreamer1.0-pulseaudio gstreamer1.0-tools gstreamer1.0-alsa
    apt-get install libasound2-dev
    apt-get install pulseaudio libpulse-dev

Support for various databases (PostgreSQL, MariaDB/MySQL) 	

    apt-get install libpq-dev libmariadbclient-dev
    
Printing support using CUPS

    apt-get install libcups2-dev
    
Wayland support

    apt-get install libwayland-dev
    
X11 support

    apt-get install libx11-dev libxcb1-dev libxkbcommon-x11-dev libx11-xcb-dev libxext-dev
    
Accessibility 	

    apt-get install libatspi-dev

### Broadcom EGL library filename fix
On Raspbian Stretch the OpenGL library files have been renamed so that they wouldn't conflict with Mesa installed ones. Unfortunately Qt configure script is still looking for the old names. There are a couple of solutions for this:
Run rpi-update and install bleading edge firmware and kernel. Then you are done. 
Patch the Qt configuration to look for the correct libraries. See QTBUG-62216 for details.
Create symlink manually.
In /opt/vc/lib run:

    sudo rpi-update

    sudo ln -s libbrcmEGL.so libEGL.so
    sudo ln -s libbrcmGLESv2.so libGLESv2.so
    sudo ln -s libbrcmOpenVG.so libOpenVG.so
    sudo ln -s libbrcmWFC.so libWFC.so

Create a shadow build directory outside of the Qt source tree

    mkdir build
    cd build

### Configure the Qt build
You can choose to build a generic build that will work on all of the various Pi versions or specifc one that is optimized for your specific type of board. Choose one of:

Raspberry Pi ARM platform options | Platform option 	| Device
--- | --- | ---  
linux-rasp-pi-g++ 	| ARMv6 compatible version| run on all Raspberry Pi models
linux-rasp-pi2-g++ 	| ARMv7 optimized version | runs on Raspberry Pi 2 & 3
linux-rasp-pi3-g++ 	| ARMv8 optimized version | runs on Raspberry Pi 3
linux-rasp-pi3-vc4-g++ 	| ARMv8 optimized version | runs on Raspberry Pi 3. Using experimental VC4 KMS driver.


### RPi0W Run configure with the following options:

    PKG_CONFIG_LIBDIR=/usr/lib/arm-linux-gnueabihf/pkgconfig:/usr/share/pkgconfig \
    PKG_CONFIG_SYSROOT_DIR=/ \
    ../qt-everywhere-src-5.11.2/configure -v -opengl es2 -eglfs -no-gtk \
    -device linux-rasp-pi-g++ -device-option CROSS_COMPILE=/usr/bin/ \
    -opensource -confirm-license -release -reduce-exports \
    -force-pkg-config -no-kms -nomake examples -no-compile-examples -no-pch \
    -skip qtwayland -skip qtwebengine -no-feature-geoservices_mapboxgl \
    -qt-pcre -ssl -evdev -system-freetype -fontconfig -glib -prefix /opt/Qt5.11
 ### RPi3B+ Run configure with the following options:

    PKG_CONFIG_LIBDIR=/usr/lib/arm-linux-gnueabihf/pkgconfig:/usr/share/pkgconfig \
    PKG_CONFIG_SYSROOT_DIR=/ \
    ../qt-everywhere-src-5.11.2/configure -v -opengl es2 -eglfs -no-gtk \
    -device linux-rasp-pi3-g++ -device-option CROSS_COMPILE=/usr/bin/ \
    -opensource -confirm-license -release -reduce-exports \
    -force-pkg-config -no-kms -nomake examples -no-compile-examples -no-pch \
    -skip qtwayland -skip qtwebengine -no-feature-geoservices_mapboxgl \
    -qt-pcre -ssl -evdev -system-freetype -fontconfig -glib -prefix /opt/Qt5.11   
    
Make sure that the configure script detects Raspberry Pi EGLFS, look for the following output:
QPA backends:

    DirectFB ............................... no
    EGLFS .................................. yes
    EGLFS details:
    ...
    EGLFS Rasberry Pi .................... yes
Now Qt should be configured properly.

### Compile Qt
To compile run:

    make

If all is well, install Qt by running

    make install

You should now have Qt 5.11 installed in /opt/Qt5.11 ready for use.  You can choose the platform binaries will run against by suppling the "-platform" paramter when running them.

Qt Platform option | Platform 	| Parameter
--- | --- | ---  
X11 	| -platform | xcb
VNC 	| -platform | vnc
WebGL 	| -platform | webgl


