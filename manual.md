# Manual

All i've done.

## Default configuration of raspberry

login: 
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

login:
username pi 
password: the one you have set in step 2

1. ping google.ch # must not fail now
1. sudo apt-get update

## Set fix ip of raspberry on fritzbox

1. Go to Wireless > Radio Internet
1. choose your raspberrypi (default name is raspberrypi) and edit
1. set a preferred ip (I left the default)
1. select `Always assign this network device the same IPv4 address`
1. Ok

## Check soundcard

1. arecord -l # should show usb soundcard card-`<CARD_NUMBER_OF_USB_CARD>` remeber `<CARD_NUMBER_OF_USB_CARD>` for asound file
1. sudo nano /etc/asound.conf # example in repo

## Install streaming software

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

## Configuration

1. cd $HOME
1. nano darkice.cfg # example in repo
1. nano darkice.sh # example in repo
1. sudo chmod +x darkice.sh # makes file executable
1. sudo service icecast2 start # as icecast is a service it starts automatically on reboot
1. crontab -e
1. @reboot sleep 10 && sudo $HOME/darkice.sh

You should now be able to call the following website and you should see a mountpoint to a mp3 file (default called raspi.mp3):
http://<FIX_IP_OF_RASPBERRY>:8000

If the sound of the turntable got a bit louder I heard a lot of background noise. To remove this consume [next section](#remove-hiss-on-stream)

## Remove hiss on stream

1. amixer controls 
1. search auto gain controll (looks like this: numid=<AUTO_GAIN_CONTROLL_NUMID>,iface=MIXER,name='Auto Gain Control')
1. amixer cset numid=<AUTO_GAIN_CONTROLL_NUMID> 0
