# QT5.11.2 -Raspbian-Stretch

>reference[QT5.x-raspbian-stretch]: https://github.com/MarkusIppy/QT5.x-raspbian-stretch

After booting up your new Raspbian, follw the steps below :

Increase Rpi's swap size. As root, edit the file /etc/dphys-swapfile and modify the variable CONF_SWAPSIZE
```
  CONF_SWAPSIZE=2048
```
Run dphys-swapfile setup which will create and initialize the file.
```
  $ sudo swapon -s
  $ sudo dphys-swapfile swapoff
  $ sudo dphys-swapfile swapon
  $ sudo swapon -s
```
After increasing the swap size,
```
  $ sudo raspi-config
```
Set GPU to 256 and enable ssh (ssh is optional) 
```
  $ sudo apt-get update
  $ sudo chmod +x Pi3buildQT5.11.2.sh
```
```
  $./Pi3buildQT5.11.2.sh
```

```
Configure summary:

Building on: linux-g++ (arm, CPU features: <none>)
Building for: devices/linux-rasp-pi3-g++ (arm, CPU features: neon)
Configuration: cross_compile use_gold_linker enable_new_dtags largefile neon precompile_header shared rpath release c++11 c++14 c++1z concurrent dbus reduce_exports stl
Build options:
  Mode ................................... release
  Optimize release build for size ........ no
  Building shared libraries .............. yes
  Using C++ standard ..................... C++14
  Using ccache ........................... no
  Using gold linker ...................... yes
  Using new DTAGS ........................ yes
  Using precompiled headers .............. yes
  Using LTCG ............................. no
  Target compiler supports:
    NEON ................................. yes
  Build parts ............................ libs
Qt modules and options:
  Qt Concurrent .......................... yes
  Qt D-Bus ............................... yes
  Qt D-Bus directly linked to libdbus .... yes
  Qt Gui ................................. yes
  Qt Network ............................. yes
  Qt Sql ................................. yes
  Qt Testlib ............................. yes
  Qt Widgets ............................. yes
  Qt Xml ................................. yes
Support enabled for:
  Using pkg-config ....................... yes
  QML debugging .......................... yes
  udev ................................... yes
  Using system zlib ...................... yes
Qt Core:
  DoubleConversion ....................... yes
    Using system DoubleConversion ........ no
  GLib ................................... yes
  iconv .................................. no
  ICU .................................... yes
  Logging backends:
    journald ............................. no
    syslog ............................... no
    slog2 ................................ no
  Using system PCRE2 ..................... no
Qt Network:
  getaddrinfo() .......................... yes
  getifaddrs() ........................... yes
  IPv6 ifname ............................ yes
  libproxy ............................... no
  OpenSSL ................................ yes
    Qt directly linked to OpenSSL ........ no
  SCTP ................................... no
  Use system proxies ..................... yes
Qt Gui:
  Accessibility .......................... yes
  FreeType ............................... yes
    Using system FreeType ................ yes
  HarfBuzz ............................... yes
    Using system HarfBuzz ................ no
  Fontconfig ............................. yes
  Image formats:
    GIF .................................. yes
    ICO .................................. yes
    JPEG ................................. yes
      Using system libjpeg ............... yes
    PNG .................................. yes
      Using system libpng ................ yes
  EGL .................................... yes
  OpenVG ................................. no
  OpenGL:
    Desktop OpenGL ....................... no
    OpenGL ES 2.0 ........................ yes
    OpenGL ES 3.0 ........................ no
    OpenGL ES 3.1 ........................ no
  Session Management ..................... yes
  ... 
  
```


