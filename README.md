# QtPi4
Build steps for building qt for raspberry pi 4B

#Cross-compile

## install on host
sudo apt install autoconf automake libtool flex
sudo apt install bison
sudo apt install gperf
sudo apt install pkg-config
sudo apt install gdb-multiarch
sudo apt install gcc-9-aarch64-linux-gnu
sudo apt install g++-9-aarch64-linux-gnu

## configure and build
../../qt-everywhere-src-5.15.0/configure -release -opengl es2 -eglfs -device linux-rasp-pi4b-v3d-g++ -device-option CROSS_COMPILE=/usr/bin/aarch64-linux-gnu- -sysroot /opt/raspberry/pi/rootfs -prefix /opt/qt -opensource -confirm-license -skip qtscript -skip qtwebengine -nomake tests -nomake examples -make libs -pkg-config -no-use-gold-linker -v -recheck


## ubuntu-server 20.04 with gnome

/boot/firmware/syscfg.txt (or usercfg.txt):
Add the following line: dtoverlay=vc4-fkms-v3d

vi /boot/firmware/usercfg.txt
Add the following line: disable_overscan=1
