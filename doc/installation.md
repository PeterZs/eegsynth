# Software installation instructions

The EEGsynth software is developed with Linux and OS X in mind. In principle it should also work on Windows, but we don't actively work on that platform.

For Linux we are specifically targetting [Raspbian](http://www.raspbian.org), which is a free Debian-based operating system optimized for the [Raspberry Pi](https://www.raspberrypi.org) hardware. This allows to build a dedicated EEGsynth on the low-cost Raspberry Pi platform.

## General instructions

### Install EEGsynth from gitub
```
git clone https://github.com/eegsynth/eegsynth.git
```

There are some dependencies from FieldTrip, which you can download and install using

```
cd eegsynth/bin
install.sh
```

## Installation instructions for Raspbian

Use "raspi-config" to configure the correct keyboard, time-zone, to extend the partition on the SD card and to disable the automatic start of the graphical interface upon boot.
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install screen
sudo apt-get install vim
sudo apt-get install git
```

### Reconfigure the keyboard

The default keyboard layout is UK-English, which is probably not what you have.

```
sudo dpkg-reconfigure console-data
sudo dpkg-reconfigure keyboard-configuration
```

### Install the web interface

EEGsynth includes a web interface that allows you to start and stop modules and to edit the patch configuration. The installation of this is detailed in the [README](interface/README.md) document for the interface.

### Install redis

This is used for inter-process communication between modules.

```
sudo apt-get install redis-server
```

Redis is automatically started on Raspberry Pi. If you look for running processes, you should see

```
pi@hackpi:/etc/redis $ ps aux | grep redis
redis      434  0.4  2.0  29332  2436 ?        Ssl  10:40   0:14 /usr/bin/redis-server 127.0.0.1:6379       
```


If you want to connect between different computers, you should edit /etc/redis/redis.conf and specify that it should bind to all network interfaces rather than only 127.0.0.1 (default). Edit the configuration

```
sudo nano /etc/redis/redis.conf
```

and comment out the line "bind 127.0.0.1" like this:

```
# By default Redis listens for connections from all the network interfaces
# available on the server. It is possible to listen to just one or multiple
# interfaces using the "bind" configuration directive, followed by one or
# more IP addresses.
#
# Examples:
#
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1                  ## COMMENTED OUT FOR EEGSYNTH
```

After changing the configuration file, you can kill the server, which will then restart with the correct configuration:

```
pi@raspberry:/etc/redis $ ps aux | grep redis
sudo kill -9 <ID>
```

And you should see that it restarts and binds to all interfaces:

```
pi@raspberry:/etc/redis $ ps aux | grep redis
redis     2840  0.0  2.2  29332  2684 ?        Ssl  11:35   0:00 /usr/bin/redis-server *:6379               
```

The redis command line interface is an useful tool for monitoring and debugging the redis server:

```
redis-cli monitor
```

### Install portmidi

This is used for MIDI communication.

```
sudo apt-get install libportmidi-dev
```


### Install python modules

```
wget https://bootstrap.pypa.io/ez_setup.py -O - | sudo python
sudo easy_install pip

sudo pip install redis
sudo pip install mido
sudo pip install pyserial
sudo pip install pyosc
sudo pip install numpy
```

### Install audio

This is only needed for the software synthesizer module, which runs fine on OS X but which still has issues on the Raspberry Pi.

```
sudo apt-get install python-pyaudio
sudo apt-get install jackd
```

The following might actually not be needed.

```
sudo apt-get install alsa-utils
sudo apt-get install mpg321
sudo apt-get install lame
```

### Fix audio problems on Raspberry Pi

The following are some attempts to get the software synthesizer module working nicely with the audio interface of the Raspberry Pi.

See https://dbader.org/blog/crackle-free-audio-on-the-raspberry-pi-with-mpd-and-pulseaudio

```
sudo apt-get install pulseaudio
sudo apt-get install mpd
sudo apt-get install mpc
```

This did not solve it, I uninstalled them again.

```
sudo apt-get install libasound2-dev
sudo apt-get install python-dev
wget https://pypi.python.org/packages/source/p/pyalsaaudio/pyalsaaudio-0.8.2.tar.gz
tar xvzf pyalsaaudio-0.8.2.tar.gz
cd pyalsaaudio-0.8.2
python setup.py build
sudo python setup.py install
```

### Switch the Launch Control XL to low-power mode

To use the Launch Control XL connected directly (without powered USB hub) with the Raspberry Pi you must first switch it to low-power mode. To do this hold down both the *User* and *Factory Template* buttons and insert the USB cable. Release the buttons and press *Record Arm*. Finally press the right arrow button.


## Installation instructions for OS-X

### Install MIDO for Python

MIDO is a MIDI package that is based on portaudio.

```
sudo pip install --upgrade pip
sudo pip install mido
sudo port selfupdate
export DYLD_LIBRARY_PATH=$DYLD_LIBRARY_PATH:/opt/local/lib
```

```
sudo apt-get install libportmidi0
sudo apt-get install libportmidi-dev
```
