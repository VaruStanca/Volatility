# Volatility


How to Install Volatility on Linux
Volatility is a powerful tool used for analyzing memory dumps on Linux, Mac, and Windows systems. On Linux and Mac systems, one has to build profiles separately, and notably, they must match the memory system profile (building a Ubuntu 18.04.3 profile to analyze a Ubuntu 18.04.4 system will not work). This article will go over all the dependencies that need to be downloaded as well as how to build a profile.

Dependencies
Note that information is taken from Volatility's Github. Many Volatility plugins will not work with the following packages.

Prerequisites for Dependencies
Run the following:

$ sudo apt-get install build-essential autoconf dwarfdump git subversion pcregrep libpcre++-dev python-dev -y
Install Distorm
Do not use pip install distorm3. It will not build correctly. Instead, download the source tar from here. After unzipping it, cd into the the directory and run:

$ python setup.py build
$ python setup.py build install
Install Yara
Do not use pip install yara-python. I made this mistake, and Volatility was not able to detect Yara. Make sure to go to the main website, and download the source tar. Run:

$ tar -zxf yara-4.0.1.tar.gz
$ cd yara-4.0.1
$ ./bootstrap.sh
Install some dependencies:

$ sudo apt-get install automake libtool make gcc pkg-config
Continue:

$ ./configure
$ make
$ sudo make install
Check to see if it installed properly:

$ make check
Install PyCrypto
Download the latest source from here and cd into the directory.

$ python setup.py build
$ sudo python setup.py build install
Install Volatility
Finally, clone from Volatility's Github repo and install:

$ git clone https://github.com/volatilityfoundation/volatility.git
$ cd volatility
$ sudo python setup.py build install
Create module.dwarf:

$ cd /tools/linux
$ make
Make a zip containing module.dwarf and the exact profile of your Linux distro:

$ cd ../../../
$ sudo zip $(lsb_release -i -s)_$(uname -r)_profile.zip ./volatility/tools/linux/module.dwarf /boot/System.map-$(uname -r)
Copy the zip file into the Volatility plugin path:

$ cp *name*.zip ./volatility/volatility/plugins/overlays/linux
Test if installation is complete and profile is configured:

$ cd volatility
$ python vol.py --info | grep $(lsb_release -i -s)
Generating a Memory Sample with LiME
LiME is a memory acquisition tool made specifically for Linux devices.

Dependencies
$ sudo apt install linux-headers-4.9.0-8-amd64
$ sudo apt install build-essential
Download and Compile
Install the latest version:

$ git clone https://github.com/504ensicsLabs/LiME
Compile:

$ cd LiME/src/
$ make
A file named lime-5.3.0-62-generic.ko is created.

Taking Memory Image
Use insmod to load the compiled LKM. Also, format=lime and timeout=0 are imp ortant for analysis via Volatility.

$ sudo insmod lime-5.3.0-62-generic.ko "path=/home/dump1.mem format=lime timeout=0"
