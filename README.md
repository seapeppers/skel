# skel

![Project Image](skel-banner.png)

## Install
0. clone the bluez repo
```
git clone https://github.com/pauloborges/bluez/tree/master
```
0. install prerequisites for bluez
```
sudo apt-get update && sudo apt-get install gcc libglib2.0-dev dbus dbus-x11 udev libdbus-1-dev libdbus-glib-1-dev libudev-dev libical-dev libreadline-dev libjson-c-dev
```
0. install latest version of `readline`, you should be able to find with `apt-cache search readline | grep readline`, example:
```
sudo apt-get install libreadline8 libreadline8-dev
```
0. apply patches to bluez
```
TODO
```
0. configure bluez and then install
```
cd bluez && \
  ./configure --prefix=/usr --mandir=/usr/share/man --sysconfdir=/etc --localstatedir=/var && \
  make && make install
```
