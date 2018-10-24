# Building Qt   (*Not Tested!*)
>reference: http://www.tal.org/tutorials/building-qt-510-raspberry-pi-debian-stretch

### Build Qt for Raspberry Pi
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

Broadcom EGL library filename fix

    On Raspbian Stretch the OpenGL library files have been renamed (1, 2)so that they wouldn't conflict with Mesa installed ones. Unfortunately Qt configure script is still looking for the old names. There are a couple of solutions for this:
    Run rpi-update and install bleading edge firmware and kernel. Then you are done. (Author had done this and therefor hadn't noticed this issue, sorry about that)
    Patch the Qt configuration to look for the correct libraries. See QTBUG-62216 for details.
    Create symlink manually.

    In /opt/vc/lib run:

    sudo ln -s libbrcmEGL.so libEGL.so
    sudo ln -s libbrcmGLESv2.so libGLESv2.so
    sudo ln -s libbrcmOpenVG.so libOpenVG.so
    sudo ln -s libbrcmWFC.so libWFC.so

Create a shadow build directory outside of the Qt source tree
We will build Qt outside of the source tree, this way you can easily have different build version and easily also start over in case of any issues. You build location can be anywhere where there is enough space, for example an USB stick in case you are running out on your SD card. Remember to adjust any paths in the commands.

    mkdir build
    cd build

Configure the Qt build

For some odd reason Qt insists on being configured for cross-compiling, even when doing a native build. Fortunately we can work around it by specifying a couple of extra parameters to get everything detected properly.

You can choose to build a generic build that will work on all of the various Pi versions or specifc one that is optimized for your specific type of board. Choose one of:
Raspberry Pi ARM platform options Platform option 	Device
linux-rasp-pi-g++ 	ARMv6 compatible version, resulting binaries will run on all Raspberry Pi models. This is the default option and the only one tested to work!
linux-rasp-pi2-g++ 	ARMv7 optimized version, runs on Raspberry Pi 2 & 3
linux-rasp-pi3-g++ 	ARMv8 optimized version, runs on Raspberry Pi 3
linux-rasp-pi3-vc4-g++ 	ARMv8 optimized version, runs on Raspberry Pi 3. Using experimental VC4 KMS driver.

"This should allow accelerated EGL and OpenGL with eglfs via KMS/DRM/GBM (instead of the Broadcom-specific backend), under X11 with xcb."

Change the configure option linux-rasp-pi-g++ to any of the above to suit your needs.

Note that we are disabling the qtwebengine and mapboxgl as unfortunately compiling them requires much more memory than any current Pi can provide. If you need these components, then consider doing a cross-compilation or build on the ARM device with more memory available or you can try adding plenty of swap.

If you like, go hand ahead and use any suitable custom prefix instead of /opt/Qt5.10 provided in the example configure command.

Run configure with the following options:

PKG_CONFIG_LIBDIR=/usr/lib/arm-linux-gnueabihf/pkgconfig:/usr/share/pkgconfig \
PKG_CONFIG_SYSROOT_DIR=/ \
../qt-everywhere-src-5.10.1/configure -v -opengl es2 -eglfs -no-gtk \
-device linux-rasp-pi-g++ -device-option CROSS_COMPILE=/usr/bin/ \
-opensource -confirm-license -release -reduce-exports \
-force-pkg-config -no-kms -nomake examples -no-compile-examples -no-pch \
-skip qtwayland -skip qtwebengine -no-feature-geoservices_mapboxgl \
-qt-pcre -ssl -evdev -system-freetype -fontconfig -glib -prefix /opt/Qt5.10

Make sure that the configure script detects Raspberry Pi EGLFS, look for the following output:

QPA backends:
  DirectFB ............................... no
  EGLFS .................................. yes
  EGLFS details:
...
    EGLFS Rasberry Pi .................... yes

Now Qt should be configured properly with all features enabled that we need. If you need some of the optional features, make sure to check the configure result that they where properly detected.
Compile Qt

To compile run:

make

or if you are using any of the quad-core Pis, append the -j4 parameter to build in parallel. Make sure you have proper cooling in this case, the 3+ this should run fine without getting throtled.

The compilation time depends on the components being built, speed of your SD card, etc . You can expect about 2-3 hours on a Raspberry Pi 3 when built with parallel make (make -j4).

Note: It might happen that parallel build needs too much memory, in that case run with just one process or try -j2, -j3
Install the build

The compilation should finnish without any errors, if it does not, double check that you have all the dependecies installed and run configure correctly.

If all is well, install Qt by running

make install

You should now have Qt 5.10 installed in /opt/Qt5.10 ready for use. To configure your Qt project(s) to build with this version run qmake from the installation directory:

/opt/Qt5.10/bin/qmake

You can of course also add it to your PATH.
Choosing platform

As explained in the introduction, this build will default to using the eglfs platform, meaning that no windowing environment is required to run GUI applications. You can choose the platform binaries will run against by suppling the "-platform" paramter when running them.
Qt Platform option examples Platform 	Parameter
X11 	-platform xcb
VNC 	-platform vnc
WebGL 	-platform webgl
