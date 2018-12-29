# turntable-as-stream
List of commands I executed on my raspberry pi to stream my turntable locally

All files which get touched with nano can be found in this repo.

## Required hardware

As I am new to turntables I have no idea if I spent too much money on single components. The sound is awsome for me so I'm happy with it.

* Turntable (e.g. Sony PS-T33)
* Preamplifier (e.g. Pro-Ject Phono Box) 
* AUX to RCA cable
* USB Sound card (e.g. Delock USB Sound Adapter 7.1 )
* Raspberry Pi (I use raspbian stretch lite as operating system)
* Radio streaming app where you can add custom urls (e.g. TuneIn)

## Default configuration of raspberry

first login: 
user: pi
password raspberry

1. sudo passwd root # change password of root
1. passwd # change password of pi-user
1. sudo raspi-config
   1. Localisation Options
      1. Change local > `de_CH.UTF-8 UTF-8`
      1. Change timezone > my city
      1. Change keyboard layout > `my keyboard layout`
   1. Network
      1. Network Options (Wifi is turned off by defaul  t because no country is set)
      1. set country
      1. set wifi credentials
   1. Interfacing Options
      1. SSH > enable
1. sudo reboot now

You can now connect via ssh. 

Login with username pi and and the password you have set in step 2

1. ping google.ch # should not fail now
1. sudo apt-get update

## Set fix ip of raspberry on fritzbox

1. Go to Wireless > Radio Internet
1. choose your raspberrypi (default name is raspberrypi) and edit
1. set a preferred ip (I left the default)
1. select `Always assign this network device the same IPv4 address`
1. Ok

## check soundcard

1. arecord -l # should show usb soundcard card-`<CARD_NUMBER_OF_USB_CARD>` remeber `<CARD_NUMBER_OF_USB_CARD>` for asound file
1. sudo nano /etc/asound.conf # example in repo

## install streaming software

1. cd /tmp
1. echo "deb-src http://archive.raspbian.org/raspbian/ jessie main contrib non-free rpi" | sudo tee --append /etc/apt/sources.list
1. sudo apt-get update
1. sudo apt-get install libmp3lame-dev
1. apt-get source darkice
cd darkice-*
1. ./configure  --prefix=/usr --sysconfdir=/usr/share/doc/darkice/examples --with-lame --with-lame-prefix=/usr/lib/arm-linux-gnueabihf --with-alsa --with-alsa-prefix=/usr/lib/arm-linux-gnueabihf
1. make
1. sudo make install
1. sudo apt-get install icecast2

## configuration

1. cd $HOME
1. nano darkice.cfg # example in repo
1. nano darkice.sh # example in repo
1. sudo chmod +x darkice.sh # makes file executable
1. sudo service icecast2 start # as icecast is a service it starts automatically on reboot
1. crontab -e
1. @reboot sleep 10 && sudo $HOME/darkice.sh

You should now be able to call the following website and you should see a mountpoint to a mp3 file (default called raspi.mp3):
http://<FIX_IP_OF_RASPBERRY>:8000

## remove hiss on stream

1. amixer controls 
1. search auto gain controll (looks like this: numid=<AUTO_GAIN_CONTROLL_NUMID>,iface=MIXER,name='Auto Gain Control')
1. amixer cset numid=<AUTO_GAIN_CONTROLL_NUMID> 0

## (Not needed) install lame by your own

1. wget https://sourceforge.net/projects/lame/files/lame/3.100/lame-3.100.tar.gz # check for newer versions first!
1. tar xfz lame-3.100.tar.gz
1. cd lame-3.100 
1. sudo ./configure --with-fileio=lame --without-vorbis --disable-gtktest --enable-expopt=full --prefix=/usr
1. sudo make
1. sudo make install

## source of inspiration

Most important articels i've read. Thanks to all those authors. :)

* https://www.instructables.com/id/Add-Aux-to-Sonos-Using-Raspberry-Pi/ Idea of how to realise this project
* https://gist.github.com/vees/b52fcf0cccde403472a4058761aa3b50 how to install current darkice for mp3
* https://raspberrypi.stackexchange.com/questions/19705/usb-card-as-my-default-audio-device how to remove hiss
