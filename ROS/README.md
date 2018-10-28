## ROS Kinetic installation on Raspberry Pi Zero/Raspberry Pi Zero W

Reference:https://github.com/nomumu/Kinetic4RPiZero/

    unzip rpi-zerow-kinetic_X.X.X-X_armhf.zip
    sudo gdebi rpi-zerow-kinetic_X.X.X-X_armhf.deb
    sudo /opt/ros/kinetic/initialize.sh


ros_kinetic_install_raspizero.bash

    sudo apt-get update
    sudo apt-get install -y build-essential gdebi
    mkdir -p ~/tmp
    wget https://github.com/nomumu/Kinetic4RPiZero/releases/download/v_2017-10-15/rpi-zerow-kinetic_1.0.0-1_armhf.zip
    unzip rpi-zerow-kinetic_1.0.0-1_armhf.zip
    sudo gdebi rpi-zerow-kinetic_1.0.0-1_armhf.deb
    sudo /opt/ros/kinetic/initialize.sh

Setup

    sudo rosdep init
    rosdep update
    echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
    source ~/.bashrc

    roscore
