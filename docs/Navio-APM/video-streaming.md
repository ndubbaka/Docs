#### Video Streaming with Navio

Streaming real-time video from a drone powered by a Raspberry Pi 2 has never been easier.  There is only only a handful of actions that you need to make to get a drone streaming real-time video to a remote PC, tablet, phone or whatnot.

#### Raspberry PI2

First things first. You need to install some packages on RPi2

```bash
sudo apt-get install gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-libav
```

After the installation has completed you can choose whatever platform you want to stream FPV to.

#### Ubuntu

If you're going to stream to a Ubuntu PC, install the same packages locally.

```bash
sudo apt-get install gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-libav
```

#### Android

Download and install QtGStreamerHUD:
[QtGStreamerHUD.apk](http://www.emlid.com/files/QtGStreamerHUD.apk). Find out your IP address in the Preferences. You'll need it in order to connect to the phone from your RPi2.  
Use our [our](http://docs.emlid.com/Navio-APM/installation-and-running/) tutorial to run APM using the IP you just found out.

Here's the app in action

![selfie](Navio-APM/img/NavioPlus-gstreamer-selfie.png)

Unfortunately, the cable length was not enough to make a selfie but at least we'd tried. 

#### Mac OS X

The simplest way is to use brew. To install it run the following in your Mac terminal:

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install gstreamer gst-libav gst-plugins-ugly gst-plugins-base gst-plugins-bad gst-plugins-good 
```

#### Windows

Download and install [gstreamer for Windows](http://gstreamer.freedesktop.org/data/pkg/windows/1.4.5/gstreamer-1.0-x86_64-1.4.5.msi).
 
#### Launching

Now everything is ready for streaming.

***On your computer***

```bash
gst-launch-1.0 -v udpsrc port=9000 caps='application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H264' ! \ 
    rtph264depay ! \
    avdec_h264 ! \
    videoconvert ! \
    autovideosink sync=f
```
From now on, your computer will be waiting for the input stream from Raspberry PI2. Once it gets a stream, you'll see the real-time video from your drone.

***On Raspberry***

```bash
raspivid -n -w 1280 -h 720 -b 1000000 -fps 15 -t 0 -o - | \
    gst-launch-1.0 -v \
    fdsrc !  \
    h264parse ! \
    rtph264pay config-interval=10 pt=96 ! \ 
    udpsink host=<remote_ip> port=9000
```
where <remote_ip> is the IP of the device you're streaming to.

Adjust bitrate with ***-b*** switch or ***-fps*** if your video lags behind. 

Feel free to ask on our [forum](http://community.emlid.com) if you stumble upon any problems. We're always there at your convenience.  
