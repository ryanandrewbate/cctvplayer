#cctvPlayer
Simple system for playing out a live RTSP feed full screen on a Raspberry Pi using OMXplayer.

*Disclaimer: This code is a bit poo, don't judge me. It works well enough for its intended purpose.*

##Pi Setup

Run `sudo raspi-config` or edit contig.txt using `sudo nano /boot/config.txt`

and set up the following:

- Enable as large a video split as possible (depends on model).
- Disable overscan.
- Force HDMI hotplug (assuming you're intending to use HDMI).
- Boot to console, no auto login.


##Installation

Modify `streams.sh` to your appropriate RTSP stream parameters.

If you are using `screen` in the `streams.sh` file (for instance, to display multiple streams simultaneously), install it:

```
sudo apt-get update
sudo apt-get install screen
```

Create the scripts in or copy the scripts to the /etc/init.d folder:

```
sudo nano /etc/init.d/cctv.sh
sudo nano /etc/init.d/cctv15.sh
sudo nano /etc/init.d/streams.sh
```
Make scripts executable:

```
sudo chmod +x /etc/init.d/cctv.sh
sudo chmod +x /etc/init.d/cctv15.sh
sudo chmod +x /etc/init.d/streams.sh
```

Add standard 60 second script to start on boot:


```
sudo update-rc.d cctv.sh defaults
```

This ensures that the stream has started up before crontab jumps in and starts checking and generally gets stuff on the screen as quick as possible.

Edit ``/etc/crontab` and add to end of file:

```
* * * * * pi bash /etc/init.d/cctv.sh
*/15 * * * * pi bash /etc/init.d/cctv15.sh
```

to run `cctv.sh` every 1 minute and `cctv15.sh` every 15 minutes.

Reboot!

###Disabling Screensaver

Successfully disables screensaver in XDE.

Add the following to `/etc/X11/xorg.conf`:
```
Section "ServerFlags"
        Option          "BlankTime"     "0"
        Option          "StandbyTime"   "0"
        Option          "SuspendTime"   "0"
        Option          "OffTime"       "0"
EndSection
```

And add the following to `/etc/xdg/lxsession/LXDE-pi/autostart`:
```
@xset s noblank
@xset s off
@xset -dpms
```


For fixing vertical lines add --avdict rtsp_transport:tcp to the omxplayer string!
