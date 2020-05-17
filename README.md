The sole purpose of this repository is to provide a 32-bit Linux binary of dosbox-x. Its main repository only provides a 64-bit one and that is packed in .rpm format. Uses of Debian-derived systems can however download that, open it with an archive manager and extract the binary.

My binary is compiled without the AV codec. As far as I am aware this is only needed for playing audio CDs, MP3s and the like. Compiling it in adds 100MBs to the binary so really doesn’t seem worth it.  For all that it is 32-bit it can be used on a 64-bit system if you run the command `sudo dpkg --add-architecture i386` before installing it or have done so in the past.


# Know Bugs
KEYB causes slight distortion of display and if using the UK keyboard layout you can't type a # or ~.


# Building dosbox-x
Dosbox-x is poorly documented — not surprising as the people involved with it concentrate their efforts on the software itself. It took me months to fathom how to build it successfully so here’s what I learnt.

Firstly you need the stuff to build anything. On Debian/Ubuntu you can get this by installing `build-essential`. Then you need dosbox-x’s build dependencies. These are installed by:—

	sudo apt-get install automake libncurses-dev nasm libsdl-net1.2-dev libpcap-dev libfluidsynth-dev libavformat-* libswscale-* libavcodec-* libfreetype6-dev

You also need `ffmpeg` and `libavdevice58` if you want to compile in the audio codec but the Stretch versions are too old. If you really want them, download, build and install the latest ffmpeg from: https://ffmpeg.org/releases/ffmpeg-4.2.2.tar.bz2 That is an easy build but takes ages. Open `ffmpeg-4.2.2.tar.bz2` and extract the files to a directory of your choosing then open a terminal and cd into it. Then type:—

	./configure 
	make 
	sudo make install

Now go to https://github.com/joncampbell123/dosbox-x and click the green *Clone or download* button and choose *Download ZIP*. When that’s arrived open it and extract the files to a directory of your choosing. Open a terminal and cd into it.

To build the full version of dosbox-x type:—

	./build

To build it without the AV codec it’s:—

	./build-no-avcodec

There are other ./build-whatever scripts available for different situations.

A good while later that should finish and you’ll have a binary called dosbox-x in the `/src` sub-directory. You can test that by typing:—

	src/dosbox-x

If all is well you can move that to wherever you like, though the usual place is `usr/bin.`


# Configuration

Settings are stored in the file `~/.dosbox/dosbox-x.conf`.  This repository contains the reference version which gives details of ALL the possible settings, though you’re unlikely to need most of them. For instance the configuration file I actually use only consists of:—

```
[sdl]
output=opengl
mapperfile=~/.dosbox/mapper-0.83.2.map

[dosbox]
captures=~/capture

[parallel]
parallel1=file

[dos]
keyboardlayout=uk

[autoexec]
# Lines in this section will be run at startup.
# You can put your MOUNT lines here.

mount c: ~/.dos
c:
```

There is a Wiki for dosbox-x at https://github.com/Wengier/dosbox-x-wiki/wiki but it is primarily aimed at Windows users, though a lot of the information in it applies to whatever system it's running on.


# Printing

DOSBox-X has a built-in virtual dot-matrix printer but the developers seem to have every little interest in it and it’s far from good.  It was only from early May 2020 that it even worked on Linux and even now it won't print to a real printer, only produce an image of what would have come out of one.   The other problem is that they provide NO instructions on its use.  It was taken from DOSBox-Daum so its Documentation applies but that’s far from clear.

Firstly it needs some fonts to be able to do anything.  These are `courier.ttf`, `roman.ttf`, `sansserif.ttf`, `ocra.ttf` and `script.ttf` and must be in the directory `~/.dosbox/FONTS` — note the capital letters.  It would appear that the only one that is essential is `courier.ttf`.  It doesn’t matter what the fonts actually are providing they have those names.  For instance if you prefer FreeMono to Courier you can copy that to `~/.dosbox/FONTS` and rename the copy `courier.ttf` or use links. eg `ln ~/dosbox/FONTS/courier.ttf  /path/to/FreeMono.ttf`.

To use it you need to set `parallel1=printer` in the `[parallel]` section of the .conf file.

My suggested version of the `[printer]` section is:—

```
[printer]
printer     = true
dpi         = 360
width       = 82
height      = 117
printoutput = png
multipage   = false
docpath     = /home/<USER>/capture
timeout     = 1000
```

The defaults are for printing on American ‘Letter’ paper (8½” x 11”) so I’ve changed it to A4 (8¼” x 11¾”).  Besides that I have changed the `timeout` from `0`.  If that’s left it will only ‘print’ when it receives a form feed and not all software sends one.  Now it if no data comes after one second it assumes that's all there is.

In `docpath`, `<USER>` needs changing to your user name.  Another 'quirk' is that `~/` won't work; it has to be given in full.

According to the reference .conf file if `printoutput=printer` it will print to your actual printer but this doesn't work on Linux.

My preferred method of printing is by setting `parellel1=file` and then printing that using `lpr`. That works fine if it’s plain text or PCL, such as is the case if the DOS program is set to print to an HP Laserjet printer. 

Printing this way can be automated by use of the two scripts `dbx-print` and `CapturePrint`.  dbx-print calls CapturePrint, which actually does the printing,  then loads dosbox-x and when you quit that switches off the printing mechanism.  Normal dosbox-x parameters can be added after dbx-print.  **NOTE:  BOTH THESE SCRIPTS ARE LIKELY TO NEED AMENDING TO SUIT YOUR SET-UP**.  As they stand they assume that dosbox-x sends files it has captured from LPT1 to `~/capture`, that the CapturePrint script is in `~/.dosbox` and that the dosbox-x binary is somewhere in your `$PATH`.  You will also need to have installed `inotify-tools`.

Whichever method is used there’s a lot of messing about to set it up.


