# embedded_development_Qt_course_aux


## Section 2 / Lecture 3

#### WiFi/SSH access
----------

wpa_supplicant.conf

    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    country=<Insert 2 letter ISO 3166-1 country code here>
    
    network={
     ssid="<Name of your wireless LAN>"
     psk="<Password for your wireless LAN>"
    }


## Section 2 / Lecture 4

#### Touch screen setup
----------

    sudo rm -rf LCD-show
    git clone https://github.com/goodtft/LCD-show.git
    chmod -R 755 LCD-show
    cd LCD-show/ 

and then: 
   

     sudo ./LCD5-show

source: http://www.lcdwiki.com/How_to_install_the_LCD_driver

#### Packages installation


----------

    sudo apt-get install default-libmysqlclient-dev dh-exec firebird-dev freetds-dev libasound2-dev libatspi2.0-dev libcups2-dev libdbus-1-dev libdouble-conversion-dev libfontconfig1-dev libgbm-dev libgl1-mesa-dev libgl-dev libgles2-mesa-dev libglib2.0-dev libglu1-mesa-dev libglu-dev libgtk-3-dev libharfbuzz-dev libicu-dev libinput-dev libjpeg-dev libmtdev-dev libpcre2-dev libpq-dev libproxy-dev libpulse-dev libsqlite3-dev libssl-dev libudev-dev libvulkan-dev libx11-dev libx11-xcb-dev libxcb-icccm4-dev libxcb-image0-dev libxcb-keysyms1-dev libxcb-randr0-dev libxcb-render-util0-dev libxcb-render0-dev libxcb-shape0-dev libxcb-shm0-dev libxcb-sync-dev libxcb-xfixes0-dev libxcb-xinerama0-dev libxcb-xkb-dev libxcb1-dev libxext-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev libxrender-dev pkg-kde-tools unixodbc-dev qttools5-dev-tools


#### Qt base packages installation
----------

##### Install Qt packages
    deb http://raspbian.mirror.uk.sargasso.net/raspbian/ buster main contrib non-free rpi
	
Available servers: here https://www.raspbian.org/RaspbianMirrors/

- sudo apt-get update
- sudo apt-get build-dep qt4-x11
- sudo apt-get build-dep libqt5gui5
- sudo apt-get install libudev-dev libinput-dev libts-dev libxcb-xinerama0-dev libxcb-xinerama0
- sudo apt install devscripts
- sudo mk-build-deps libqt5gui5
- sudo dpkg -i ./qtbase-opensource-src-build-deps_5.11.3+dfsg1-1+rpi1+deb10u3_armhf.deb
- apt install libnss3 libnss3-dev

#### Symlinks
- sudo mv /usr/lib/arm-linux-gnueabihf/libEGL.so.1.0.0 /usr/lib/arm-linux-gnueabihf/libEGL.so.1.0.0_backup
- sudo mv /usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.0.0 /usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.0.0_backup
- sudo ln -s /opt/vc/lib/libEGL.so /usr/lib/arm-linux-gnueabihf/libEGL.so.1.0.0
- sudo ln -s /opt/vc/lib/libGLESv2.so /usr/lib/arm-linux-gnueabihf/libGLESv2.so.2.0.0
- sudo ln -s /opt/vc/lib/libbrcmEGL.so /opt/vc/lib/libEGL.so
- sudo ln -s /opt/vc/lib/libbrcmGLESv2.so /opt/vc/lib/libGLESv2.so
- sudo ln -s /opt/vc/lib/libEGL.so /opt/vc/lib/libEGL.so.1
- sudo ln -s /opt/vc/lib/libGLESv2.so /opt/vc/lib/libGLESv2.so.2


## Section 2 / Lecture 6

    mkdir rpi0
    mkdir -p ~/rpi0/toolchain/src
    mkdir -p ~/rpi0/sysroot
    mkdir -p ~/rpi0/qt5


    rsync -av pi@192.168.x.x:/lib sysroot
    rsync -av pi@192.168.x.x:/usr/include sysroot/usr
    rsync -av pi@192.168.x.x:/usr/lib sysroot/usr
	rsync -av pi@192.168.x.x:/opt/vc sysroot/opt

	
    wget https://raw.githubusercontent.com/Kukkimonsuta/rpi-buildqt/master/scripts/utils/sysroot-relativelinks.py
    chmod +x sysroot-relativelinks.py
    sudo ./sysroot-relativelinks.py sysroot

> List of default packages in Buster:
> https://n8henrie.com/2019/08/list-of-default-packages-on-raspbian-buster-lite/

    wget https://ftpmirror.gnu.org/binutils/binutils-2.31.tar.bz2
    wget https://ftpmirror.gnu.org/gcc/gcc-8.3.0/gcc-8.3.0.tar.gz
    tar xf gcc-8.3.0.tar.gz
	tar xf binutils-2.31.tar.bz2
   
    export WORKSPACE_ROOT=~/rpi0/toolchain
    export PREFIX=${WORKSPACE_ROOT}/toolchain
    export TARGET=arm-linux-gnueabihf
    export SYSROOT=${WORKSPACE_ROOT}/../sysroot
    export PATH=${PREFIX}/bin:$PATH
    mkdir -p ${PREFIX}
    cd ${WORKSPACE_ROOT}

    mkdir -p build/binutils
    pushd build/binutils
    ../../src/binutils-2.31/configure --target=**${**TARGET**}** --prefix=**${**PREFIX**}**--with-arch=armv6 --with-fpu=vfp --with-float=hard --disable-multilib
    make -j4
    make install
    popd

    cd ~/rpi0/toolchain/sources/gcc-8.3.0
    contrib/download_prerequisites
    mkdir -p ../../build/gcc2
    pushd build/gcc2
    ../../src/gcc-8.3.0/configure --prefix=**${**PREFIX**}** --target=**${**TARGET**}** --with-sysroot=**${**SYSROOT**}** --enable-languages=c,c++ --disable-multilib --enable-multiarch --with-arch=armv6 --with-fpu=vfp --with-float=hard
    make -j4
    make install
    popd


## Section 3 / Lecture 7
#### Cross Compiling Qt
----------

    git clone git://code.qt.io/qt/qt5.git
    git checkout 5.x
    git submodule update --init --recursive
    mkdir -p ~/rpi0/qt5/build5.x && cd ~/rpi0/qt5/build5.x


    sudo ../qt5/configure -release -opengl es2 -device linux-rasp-pi-g++ -device-option CROSS_COMPILE=/home/user/rpi0/toolchain/toolchain/bin/arm-linux-gnueabihf- -opensource -confirm-license -nomake tests -no-pch -eglfs -xcb -skip qtwayland -skip qtwinextras -skip qt3d -skip qtandroidextras -skip qtcharts -skip qtdatavis3d -skip qtmacextras -skip qtpurchasing -skip qtquick3d -skip qtscript -skip qtpim -skip qtcanvas3d -skip qtqa -skip qtquicktimeline -skip qtrepotools -skip qtwebchannel -skip qtwebengine -skip qtwebglplugin -skip qtdocgallery -skip qtactiveqt -skip qtdoc -make libs -sysroot /home/user/rpi0/sysroot -prefix /usr/local/qt/5.15.0 -extprefix /opt/qt/5.15.0/raspbian/sysroot -hostprefix /opt/qt/5.15.0/raspbian -v 2>&1 | tee ../configure.log

    make -j4
    sudo mkdir -p /opt/qt/5.x
    sudo chown ubuntu:ubuntu /opt/qt
    sudo make install -j4 2>&1 | tee /home/user/rpi0/qt/install.log

## Section 3 / Lecture 8
#### Copy Qt files over to RPi [Deploy]

> Create /usr/local/qt/5.x dir in RPi
> mkdir -p /usr/local/qt/5.x

    sudo rsync -av /opt/qt/5.x/raspbian/sysroot/ root@192.168.x.x:/usr/local/qt/5.x
    
    sudo su
    echo /usr/local/qt/5.x/lib > /etc/ld.so.conf.d/00-qt.5.x.conf
    ldconfig
    exit
    sudo reboot

## Section 4 / Lecture 11
> https://ftp.gnu.org/gnu/gdb/gdb-8.2.1.tar.xz

## Section 5 / Lecture 12

    sudo systemctl edit --force --full my_app.service
    
    [Unit]
    Description=My App Service
    Wants=network-online.target
    After=network-online.target
    
    [Service]
    Type=simple
    User=pi
    WorkingDirectory=/home/pi/myapp
    ExecStart=/home/pi/myapp/bin/myapp
    
    [Install]
    WantedBy=multi-user.target
    
    sudo systemctl enable my_app.service

> ***Alternatively a service can be also created with  
> sudo nano my_app.service  
> and once edited, could be placed in the system and activated withe the following commands:  
> sudo cp my_app.service /lib/systemd/system/.  
> sudo systemctl daemon-reload  
> sudo systemctl enable my_app.service  
> sudo reboot  

## Section 5 / Lecture 13

> * In /boot/config.txt add:
> disable_splash=1  
> * In /boot/cmdline.txt add:  
> logo.nologo consoleblank=0 loglevel=1 quiet vt.global_cursor_default=0  
>*  and replace console=tty1 with:
>  console=tty3

    
    sudo systemctl disable getty@tty1
    sudo systemctl edit --force --full splash_screen.service
    
    [Unit]
    Description=Splash Screen Service
    DefaultDependencies=no
    After=local-fs.target
    Before=network-online.target
    
    [Service]
    WorkingDirectory=/home/pi/splash
    ExecStart=/usr/local/qt/5.x/bin/qmlscene /home/pi/splash/SplashScreen.qml
    StandardInput=tty
    StandardOutput=tty
    
    [Install]
    WantedBy=sysinit.target
    
    sudo systemctl enable splash_screen.service
