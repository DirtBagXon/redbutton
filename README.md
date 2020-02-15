RedButton MHEG Engine
=====================


The RedButton service may be discontinued in the near future due to BBC cutbacks.

This code may serve as an archive, and to allow investigation into the MHEG digital interactive television \
service via compilers and decompilers.

https://en.wikipedia.org/wiki/BBC_Red_Button

Below is taken from: http://redbutton.sourceforge.net/

I appears development has been discontinued by its original author: Simon Kilvington

RedButton allows you to use the interactive MHEG applications broadcast with DVB digital TV services. MHEG replaces the text only services available with analogue TV.
RedButton consists of two parts. The first, rb-download, allows MHEG data to be downloaded from a DVB service. The second, rb-browser, allows the downloaded MHEG applications to be displayed. Both rb-download and rb-browser can be run on the same host, or rb-download can be run on a host with a DVB card and rb-browser can be run on a remote frontend without a DVB card.

The rb-author package contains tools for working with MHEG applications. If you want to write your own MHEG applications, you can use the mhegc compiler to generate the ASN1 format files rb-browser expects. If you want to see how other peoples applications work, you can use the mhegd tool to convert ASN1 format files into the text form.

The source is released under the GPL and can be downloaded here.

Just un-tar them and type 'make'.


**rb-download**

Usage:

    rb-download [-v] [-a <adapter>] [-b <base_dir>] [-t <timeout>] [-l <listen_addr>] [-f <channels_file>] [-c <carousel_id>] [<service_id>]

Download the DVB Object Carousel for the given channel onto the local hard disc.
If no directory is given with -b, files will be stored under the current directory. Note that <base_dir> must already exist and be writeable.

The default timeout is 10 seconds. If no DSM-CC data is found after this time, it is assumed none is being broadcast.

The DVB devices used will be:

    /dev/dvb/adapter0/frontend0
    /dev/dvb/adapter0/demux0
    /dev/dvb/adapter0/dvr0

use the -a option to change the adapter number (eg "-a 1" will use `/dev/dvb/adapter1/frontend0` etc).\
rb-download needs a "channels.conf" file which gives tuning parameters for service_id's. A `channels.conf` file can be generated by the "scan" utility in the dvb-apps package at www.linuxtv.org. For example:

    scan ./uk-Malvern > ~/.tzap/channels.conf

If not specified with -f, rb-download will search for (in the case of DVB-T cards):

    ~/.tzap/channels.conf
    /etc/channels.conf


For DVB-S cards it will look in `~/.szap/`, for DVB-C cards it will look in `~/.czap/` and for ATSC cards it will look in the `~/.azap/` directory.

rb-download listens on the network for commands from a remote rb-browser. The default IP to listen on is 0.0.0.0 (ie all interfaces), the default TCP port is 10101. The -l option changes the default IP and port. `<listen_addr>` should be given in the form "host:port", where host defaults to 0.0.0.0 and port defaults to 10101. Eg, to listen on a different port, do "-l 8080", to only listen on the loop back, do "-l 127.0.0.1" or on a different port too, do "-l 127.0.0.1:8080".

The `<carousel_id>` will normally be read from the PMT, but you can use the -c option to explicitly choose a carousel.

-v is verbose/debug mode. Use -vv for even more mind-numbing tedium.

If no` <service_id>` is given, a list of possible channels (and their `<service_id>`) is printed. These will be the channels available on the MUX your DVB card is currently tuned to. Use dvbtune or equivalent to tune your card. eg:

    dvbtune -f 722166667 && rb-download

The file structure stored under `<base_dir>` will be:

    ./services/<service_id>

this is a symlink to the root of the carousel for the given service.
The actual carousel files and directories are stored under:

    ./carousels/<PID>/<CID>/

where <PID> is the PID the carousel was downloaded from and <CID> is the Carousel ID.

    ./cache/

Temporary cache of DVB tables to stop us having to wait for them to be retransmitted everytime we need them.
Just leave it running and any updated files will be downloaded as they are broadcast.

*Notes*

I've compiled and run it under `Gentoo` and `Mandr{ake,iva}` Linux on x86, but it should work on any (big- or little-endian) Linux distro.
It only implements a subset of the whole DSM-CC specs. However, it is enough to download everything that is currently being broadcast on the 'Freeview' DVB-T channels in the UK.

Once the data is downloaded, you can use rb-browser (see below) to view it.

**rb-browser**

Usage:

    rb-browser [-v] [-f] [-d] [-o <video_output_method>] [-k <keymap_config_file>] [-t <timeout>] [-n <network_id>] [-r] [<service_gateway>]

Display the MHEG apps downloaded with rb-download.
The -r option means use a remote backend (rb-download running on another host). If -r is specified, then <service_gateway> should be the host[:port] that rb-download is running on.

The default backend is "-r 127.0.0.1" ie rb-download running on the same machine as rb-browser.

Eg, on a single host, do this:

    rb-download 4165 &
    rb-browser

To run the frontend on a different host, do this on the backend:

    rb-download 4165 &

and run this on the frontend:

    rb-browser -r 10.0.0.1

where 10.0.0.1 is the IP or hostname of the backend.

If -r is not specified, rb-download is running on the same machine and `<service_gateway>` should be an entry in the services directory. This is really only useful for debugging, or running MHEG apps you have written yourself.

Eg, to run an MHEG app you have previously downloaded and saved, do this:

    rb-browser path/to/saved/services/4165

Although this will not give you any video or audio as this is streamed from rb-download. Retuning will also probably not work unless you've also saved the services directory for the channel you want to retune to.
For local backends, -n allows you to specify a Network ID that will be used to resolve `rec://svc/def`. By default, the Network ID is left blank in `rec://svc/def`. If you don't know what this means, you can probably just ignore the -n flag!

It will display the app in a window, use -f for full screen mode.

The -d flag disables all audio and video output.

Use -o to choose a video output method if the default is not supported/too slow on your graphics card. 'rb-browser -o' will give a list of available video output methods.

The -v flag enables verbose/debug mode.

The -t flag determines how long (in seconds) it will poll for missing files before passing an error to the MHEG application. The default is 10 seconds.

Use the -k option to load a keymap (keymap config files can be generated with the rb-keymap command). The default keys to navigate around the apps are:

    Key	Function
    Cursor Keys	Up/Down/Left/Right
    0 - 9	0 - 9
    r	Red
    g	Green
    b	Blue
    y	Yellow
    Return	OK/Select
    Escape	Cancel
    t	Text
    e	EPG (NZ Profile only)

*Notes*

You will need the "expat" XML parsing library and the xsltproc XML stylesheet processor that comes with "libxslt" to compile it. On Gentoo, you can just:
emerge expat libxslt
If the compile fails with errors about missing .c files, expat is not installed. If the compile fails with errors about function names ending in "_dup" being missing, libxslt is not installed.
To run it you need an X server that supports the Xrender extension and you need to have libpng, freetype2 and ffmpeg installed.

It does not implement the whole MHEG spec. However, it seems to be enough to view everything that is currently being broadcast on the 'Freeview' channels in the UK. If it comes across something that is not yet implemented it will print out a message on the console. Let us know if you find anything it can't do.

It does not currently use any acceleration that your graphics card may provide so it'll drop video frames in full screen mode unless you have a very fast processor (or your full screen X resolution is set to 720x576 pixels).

It will display apps that conform to the UK MHEG Profile (available from www.dtg.org.uk). Thanks to some patches and info from Steve at OpenMedia in NZ, it should also be able to download apps conforming to the NZ MHEG spec and display them.

When it starts you will probably need to press 'r' (for BBC) or 't' (for ITV/C4) to activate any MHEG app that may be waiting for you to do something.

The default font for the UK MHEG Profile is Tiresias Screenfont which was developed with the RNIB to be readable on TV screens. You can download this font as part of the DigiTV iTuner package from Nebula Electronics. Extract the file called tt7268m_802.ttf and add it to your X Windows fonts. The easiest way is to install DigiTV iTuner on a Windows box and then copy the `C:\Windows\Fonts\tt7268m_802.ttf` file onto your Linux box. To install new X fonts in KDE, open the Control Centre application, go to the System Administration section and select Font Installer.

Alternatively, if you know someone in Australia, ABC is currently broadcasting `Tiresias.pfr` as part of their TV guide application. You can download it with rb-download. The Australian applications are MHP, not MHEG, so you should probably look at www.openmhp.org for something to display them.

If the Tiresias Screenfont is not available, rb-browser first tries to use the FreeSans font instead. If this is also not available, rb-browser uses whatever scalable font Xft returns for "sans". Note that these fonts will have different metrics and so may not fit into the spaces in the MHEG apps designed for Tiresias.

**mhegc**

Usage:

     mhegc [-vv] [-o <output_file>] [<input_file>]

Will convert the `<input_file>` (or stdin if no filename is given) from the MHEG text form into the ASN1 form. The DER encoded ASN1 object will be stored in the given output file (or in a file named "a" if no -o option is given).
The -v flag enables debug mode and will print details about the ASN1 object it creates. Use -vv for even more info.

The redbutton-author package contains a "Hello World" example from http://www.digvid.info/mheg5/hello_world.php. To compile the Hello World example, do this:

    mhegc -o helloworld/startup helloworld/startup.txt
    mhegc -o helloworld/hello.mhg helloworld/hello.mhg.txt

Then to run it:

    rb-browser helloworld/


**mhegd**

Usage:

    mhegd [-vv] [-o <output_file>] <input_file>

Will convert the `<input_file>` from the ASN1 form to the MHEG text format. The text will be written to the given output file (or to stdout if no -o option is given).
The -v flag enables debug mode and will print details about the ASN1 object as it is decoded. Use -vv for even more info.


![redbutton](https://raw.githubusercontent.com/DirtBagXon/redbutton/master/images/redbutton.png)
![redbutton](https://raw.githubusercontent.com/DirtBagXon/redbutton/master/images/redbutton2.png)

