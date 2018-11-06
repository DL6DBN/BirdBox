#BirdBox#

My version of a RaspberryPi Bird Box

On the Raspberry Pi Homepage I found a tutorial about a [bird box](https://www.raspberrypi.org/learning/infrared-bird-box/) watching camera. As I had a [Pi NoIR camera](https://www.raspberrypi.org/learning/infrared-bird-box/components/camera-noir/) and a [Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) at hand for no other purposes, I started this project in mid February 2017. I hoped this won't be to late for some birds to find a home in the box.

Here I'll describe the several steps that led me to the final box...

## Ignite ##

[The ignition from the Raspberry Pi Homepage](https://www.raspberrypi.org/learning/infrared-bird-box/)

## Case ##

As the Box is (of course) located outside, and the Pi too, a weather-proofed case was needed. I found the

- [BOPLA Euromas II ET-215 (120x80x57mm³)](https://www.reichelt.de/Kunststoffgehaeuse-BOPLA/BOPLA-ET-215/3/index.html?ACTION=3&LA=5700&ARTICLE=5729&GROUPID=7712&artnr=BOPLA+ET-215)

To fit all components (Pi, wiring, WiFi and microphone connectors) into the case a quick (and dirty) hack was done, as you can see in the pictures. Thanks to an adapting plate (see below) the case suits well to the Pi.

![](Images4ReadMe/case-plate.jpg)

But the WiFi antenna and the USB connector stick out. The only solid connection to the outer world is a four lines shielded wire.

- 2 lines are for 5 V power
- 1 line is Serial TX for data from the Pi
- 1 line is Serial RX for data to the Pi
- Shield is GND

The serial lines were chosen to get access to the hidden Raspberry Pi, if wireless access fails.

The only disadvantage of the small case was: the microUSB supply plug didn't fit (even a small angled one, but I knew that before). So I soldered two small wires to the bottom side of the PI's PCB at the socket pads.

In the end all openings (USB, wirings for camera, LEDs and supply voltage) were sealed with silicone for outside purposes and the case screwed onto the bird box.

## WiFi ##

In the beginning I asked myself to use either a RPi3 with WiFi onboard or an older RPi2 with an external WLAN stick. After a few measurements of the WiFi range I decided to order a new WLAN USB stick with a **real** antenna to use it with the RPi2.

- [Edimax EDI EW-7612UAN](https://www.reichelt.de/WLAN-Adapter/EDI-EW-7612UAN/3/index.html?ACTION=3&LA=5700&ARTICLE=104399&GROUPID=5839&artnr=EDI+EW-7612UAN)

## LEDs ##

It's dark inside the box. Two Infrared (IR) LEDs are used to illuminate what's happening with the birds. I sanded down the LEDs a bit to get a more diffuse light. And they are not directly pointing to the birds but to the walls of the bird box.

- [LD 271 Infrared Diode, 950 nm, 25°](https://www.reichelt.de/Fotodioden-etc-/LD-271/3/index.html?ACTION=3&LA=5700&ARTICLE=10199&GROUPID=3045&artnr=LD+271)

I think it is a good idea not to power the IR-LEDs all the time. There may be times no birds are living in the box or the whole streaming/pi/camera stuff is down due to reasons<sup>TM</sup>. So I added a NPN-transistor to the two LEDs in line, to be triggered by a GPIO of the Pi.

![](Images4ReadMe/IR-LED_Schaltplan.png)

## Microphone ##

From internet sources about streaming out of a bird box I found they all were using the video stream only. What about sound? To get the option of experimenting with and later adding sound from the birds inside the box, I mounted a small USB ready microphone into the box too.

As the Pi has no sound/mic input, I bought one of this kind: 

- [USB Lavalier Microphone](https://www.amazon.de/Seacue-Omnidirektionaler-Kondensator-Interviews-Netzwerksingen/dp/B071171DBP/ref=sr_1_6?s=ce-de&ie=UTF8&qid=1540189333&sr=1-6&keywords=lavalier+mikrofon+usb)

So far, I have no idea, if and how it'll work. But the ALSA recording tool that comes with the Raspian distro indicated different sound levels from the mic though nothing useful was recorded yet.

## Assembly ##

As I hadn't have much time for a proper assembly after business hours and family needs, it became really a rather quick and dirty hack. See the following pictures...

###The completed case###

![](Images4ReadMe/case-breakthrough.jpg)

The breakthrough for the camera cable and the LED supply

![](Images4ReadMe/case-pi-fits.jpg)

The Raspberry Pi fits

![](Images4ReadMe/case-usb.jpg)

USB dongles (WiFi, microphone) sticking out

![](Images4ReadMe/pi-on-the-box.jpg)

Wired RPi in the case on the bird box

###The box electrics###

![](Images4ReadMe/box-plate.jpg)

Pi NoIR camera, IR LEDs and microphone on a single board for the bird box's ceiling

![](Images4ReadMe/box-electrics.jpg)

Electronics mounted in the bird box

![](Images4ReadMe/wired-box.jpg)

Wires (mic, LEDs, cam) sticking out of the box.

![](Images4ReadMe/box-ready.jpg)

Hardware finished!

## Software ##

### It streams ... ###

![](Images4ReadMe/streaming.jpg)

### ... under construction! ###

For some tests I used streaming to YouTube with following command, which basics I found in [Part 2 of the Bird Box Worksheets](https://www.raspberrypi.org/learning/infrared-bird-box/worksheet2/).

`raspivid -o - -t 0 -w 480 -h 360 -vf -hf -sa -100 -br 60 -rot 270 -fps 25 -b 400000 -g 75 | ffmpeg -re -ar 44100 -ac 2 -acodec pcm_s16le -f s16le -ac 2 -i /dev/zero -f h264 -i - -vcodec copy -acodec aac -ab 128k -g 75 -strict experimental -f flv rtmp://a.rtmp.youtube.com/live2/<access-key-here>`

Major changes are

`-w 480 -h 360 -vf -hf -sa -100 -br 60 -rot 270 -fps 25 -b 400000 -g 75`

where I reduced the video **w**idth and **h**eight to 480 by 360 pixels, **v**ertically and **h**orizontally **f**lipped the image, reduced the colour **sa**turation to get a greyscaled picture, enhanced **br**ightness, **ro**tated the image by 270 degrees for correct orientation, fixed the **f**rames **p**er **s**econd to 25, limited the **b**andwidth to 400 kbps, and told the codec to transmit a full image every 75 frames (**g** 75/25 fps = 3 s).

## Going Live ##

I really didn't notice the entry of birds. Today (mid June 2017) my son came in and shouted "Dad, turn the bird cam on". And yes there were some just hatched titmice with one of their parents in the box.

![](Images4ReadMe/BirdCamTitmice.jpg)

When bandwidth and power constraints allow streaming, the system will be turned on.<br>
Actual links to the streams will be published via my [Twitter account](https://twitter.com/DL6DBN) with HashTag #BirdCamDL6DBN.

## Archive ##

Find a playlist of streamed videos [here](https://www.youtube.com/playlist?list=PLy5ecgafiimP4Lyqy01Moq5EMXKOon5B8).

## Upgrade 2018 ##

After two bird seasons I thought it might be interesting to monitor temperature and humidity inside the bird box. So I added a combined sensor [DHT22](https://www.adafruit.com/product/385) into the box.

![](Images4ReadMe/box-temphumid.jpg)

![](Images4ReadMe/case-temphumid.jpg)

## Licensing ##

Please respect all licenses.

The initial tutorial is provided for free by the [Raspberry Pi Foundation](https://www.raspberrypi.org/) under a [Creative Commons](https://www.raspberrypi.org/creative-commons/) licence. Find more at [raspberrypi.org/resources](https://www.raspberrypi.org/resources/) and [github.com/raspberrypilearning](https://github.com/raspberrypilearning).

All my additions to the project or own creations for the project are for use under the<br> **Beer-Ware License** (Revision 42.fs): "*dl6dbn@googlemail.com* made this stuff. As long as you retain this notice you can do whatever you want with this stuff. If we meet some day, and you think this stuff is worth it, you can buy me a beer (or another drink I like) in return."<br>*Frank Sperber (DL6DBN)* with thanks to [PHK](https://people.freebsd.org/~phk/) for writing down this beautiful kind of license.

*as of: October 2018*
