# QtPi4
Build steps for building qt 5.15 for raspberry pi 4B

Archive:
http://download.qt.io/archive/qt/5.15/5.15.0/single/qt-everywhere-src-5.15.0.tar.xz

## global preparation on host

```
sudo apt install autoconf automake libtool flex bison gperf pkg-config gdb-multiarch
sudo apt install gcc-9-aarch64-linux-gnu
sudo apt install g++-9-aarch64-linux-gnu
```
### symlinks for compiler

```
ln -s /usr/bin/aarch64-linux-gnu-g++-9 /usr/bin/aarch64-linux-gnu-g++
ln -s /usr/bin/aarch64-linux-gnu-gcc-9 /usr/bin/aarch64-linux-gnu-gcc
```

#Cross-compile with Ubuntu-server

Installed image:
https://ubuntu.com/download/raspberry-pi/thank-you?version=20.04&architecture=arm64+raspi

Linux ubuntu 5.4.0-1012-raspi #12-Ubuntu SMP Wed May 27 04:08:35 UTC 2020 aarch64 aarch64 aarch64 GNU/Linux

##prepare device

###update system and install gnome
```
sudo apt-get update
sudo apt-get dist-upgrade
sudo reboot

sudo apt install ubuntu-desktop
```

(incase of display issues, e.g. resolution, black-border)
vi /boot/firmware/usercfg.txt
Add the following lines:
```
dtoverlay=vc4-fkms-v3d
disable_overscan=1
```
-> reboot

###update system and install gnome
uncomment deb-src in /etc/apt/sources.list
```
sudo apt update
sudo apt-get build-dep qtbase-opensource-src
```
sudo mkdir -p /opt/qt/5.15.0
sudo chown -R 1000:1000 /opt/qt

## prepare host for compilation
mkdir -p rootfs/usr/include & mkdir -p rootfs/usr/lib & mkdir -p  rootfs/opt/qt/5.15.0

```
rsync -avz ubuntu@yourpi:/lib rootfs/
rsync -avz ubuntu@yourpi:/usr/include rootfs/usr/
rsync -avz ubuntu@yourpi:/usr/lib rootfs/usr/
```

### python symlink script
./sysroot-relativelinks.py rootfs

## configure and build
mkdir build & cd build

```
../../../qt-everywhere-src-5.15.0/configure -release -opengl es2 -eglfs -device linux-rasp-pi4b-v3d-g++ -device-option CROSS_COMPILE=/usr/bin/aarch64-linux-gnu- -sysroot /path/to/rootfs -prefix /opt/qt/5.15.0 -opensource -confirm-license -skip qtscript -skip qtwebengine -nomake tests -nomake examples -make libs -pkg-config -no-use-gold-linker -v -recheck
```

##build

```
make -j4
make install
```

## sync qt folder to device

```
rsync -avz rootfs/opt/qt/5.15.0 ubuntu@yourpi:/opt/qt/
```



#Cross-compile with Raspberry Pi OS 64-bit

Installed image:  
https://downloads.raspberrypi.org/raspios_arm64/images/raspios_arm64-2020-05-28/2020-05-27-raspios-buster-arm64.zip  

Linux raspberrypi 5.4.42-v8+ #1319 SMP PREEMPT Wed May 20 14:18:56 BST 2020 aarch64 GNU/Linux

### prepare device
- uncomment deb-src in /etc/apt/sources.list

```
sudo apt-get update
sudo apt-get dist-upgrade
sudo rpi-update
sudo reboot
```  
sudo apt-get build-dep qtbase-opensource-src
sudo apt-get install libts-dev libbluetooth-dev bluez-tools gstreamer1.0-plugins* libgstreamer-plugins-base1.0-dev libopenal-dev pulseaudio


### prepare host
mkdir -p rootfs/usr/include & mkdir -p rootfs/usr/lib & mkdir -p  rootfs/opt/qt/5.15.0

```
rsync -avz pi@yourpi:/lib rootfs/
rsync -avz pi@yourpi:/usr/include rootfs/usr/
rsync -avz pi@yourpi:/usr/lib rootfs/usr/
rsync -avz pi@yourpi:/opt/vc rootfs/opt/
```

python symlink script  

### configure and build
../../../qt-everywhere-src-5.15.0/configure -release -opengl es2 -eglfs -device linux-rasp-pi4b-v3d-g++ -device-option CROSS_COMPILE=/usr/bin/aarch64-linux-gnu- -sysroot /opt/raspberry/pi/pi-os86-202005/rootfs  -prefix /opt/qt/5.15.0 -opensource -confirm-license -skip qtscript -skip qtwebengine -nomake tests -nomake examples -make libs -pkg-config -no-use-gold-linker -v -recheck
