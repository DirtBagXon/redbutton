
Red Button
==========

    DVB-S

    https://en.wikipedia.org/wiki/List_of_free-to-air_channels_at_28%C2%B0E

    w_scan -f s -c GB -s S28E2 -X > ~/.szap/channels.conf

    OR

    w_scan -f s -c GB -s S28E2 -x > Astra-28.2E
    scan -a1 Astra-28.2E > ~/.szap/channels.conf


    DVB-T

    https://ukfree.tv/transmitters/tv/Crystal

    scan -a4 uk-CrystalPalace > ~/.tzap/channels.conf

    T 490000000 8MHz 2/3 NONE QAM64 8k 1/32 NONE # London .
    T 514000000 8MHz 2/3 NONE QAM64 8k 1/32 NONE # London .
    T 545833330 8MHz 2/3 NONE QAM256 AUTO AUTO AUTO # London .
    T 482000000 8MHz 3/4 NONE QAM64 8k 1/32 NONE # London .
    T 506000000 8MHz 3/4 NONE QAM64 8k 1/32 NONE # London .
    T 529833330 8MHz 3/4 NONE QAM64 8k 1/32 NONE # London .
    T 537833000 8MHz 3/4 NONE QPSK 8k 1/32 NONE # London .
    T 570000000 8MHz 2/3 NONE QAM256 AUTO AUTO NONE # London .
    T 586000000 8MHz 3/4 NONE QAM256 AUTO AUTO AUTO # London .

----------------------------------------------------------------------

### Racing UK ('freq' from channel.conf [second field])

    dvbtune -c 4 -f 506000000 -i | less

### Racing UK (pid taken from 'dvbtune' output)

    <service id="15032" ca="0">
    <description tag="0x48" type="12" provider_name="" service_name="Racing UK" />
    <stream type="11" pid="6441">
    <stream_id id="190" />
    <descriptor tag="0x13" data="000000be00" text="....." />
    <descriptor tag="0x66" data="010601010000" text="......" />
    </stream>
    </service>

    dvbsnoop -adapter 4 -s ts 6441 <-pid
    dvbsnoop -adapter 4 -s sec 6441 - *both require tuning via 'rb-download'


----------------------------------------------------------------------

### Racing UK ('service_id' from channel.conf [last field] or dvbtune -i )

    rb-download -a 4 -f .tzap/channels.conf -b /opt/mheg/ 15032

### BBC services
    rb-download -a 0 -f .szap/channels.conf -b /opt/mheg/ 6450  <- DVB-S R1

    rb-download -a 4 -f .tzap/channels.conf -b /opt/mheg/ 6720  <- RADIO 1
    rb-download -a 4 -f .tzap/channels.conf -b /opt/mheg/ 6784  <- RADIO 2
    rb-download -a 4 -f .tzap/channels.conf -b /opt/mheg/ 6848  <- RADIO 3
    rb-download -a 4 -f .tzap/channels.conf -b /opt/mheg/ 6284  <- Local Radio

### Audio unsupported (use -d in rb-browser)
    rb-download -a 2 -f .tzap/channels.conf -b /opt/mheg/ 4164  <- BBC ONE
    rb-download -a 3 -f .tzap/channels.conf -b /opt/mheg/ 4287  <- BBC TWO
    rb-download -a 4 -f .tzap/channels.conf -b /opt/mheg/ 4416  <- BBC RB

----------------------------------------------------------------------

### Carousel Files

    cd /opt/mheg/services
    ls -al

----------------------------------------------------------------------

### mhegc/mhegd

    cd /opt/mheg/services/<service id>
    mhegd -o arty a
    grep TransitionTo *arty
                        :TransitionTo ( ( '/b.asn' 0 ) )
                        :TransitionTo ( ( '/c.asn' 0 ) )

    mhegd -o barty b.asn
    mhegd -o carty c.asn

    less arty
    less barty
    less carty

----------------------------------------------------------------------

### Remote connect using rb-browser

    telnet x.x.x.x 10101
    rb-browser -r x.x.x.x

    rb-browser -d -r x.x.x.x   <- No audio/video stream
    rb-browser -vvv -r x.x.x.x   <- Verbose debug

If -r is not specified, rb-download is running on the same machine and <service_gateway> should be an entry in the services directory. This is really only useful for debugging, or running MHEG apps you have written yourself.
The -d flag disables all audio and video output.
Use -o to choose a video output method if the default is not supported/too slow on your graphics card. 'rb-browser -o' will give a list of available video output methods.
The -v flag enables verbose/debug mode.
The -t flag determines how long (in seconds) it will poll for missing files before passing an error to the MHEG application. The default is 10 seconds.
Use the -k option to load a keymap (keymap config files can be generated with the rb-keymap command). The default keys to navigate around the apps are:

    Key		Function
    Cursor Keys	Up/Down/Left/Right
    0 - 9		0 - 9
    r		Red
    g		Green
    b		Blue
    y		Yellow
    Return		OK/Select
    Escape		Cancel
    t		Text


----------------------------------------------------------------------

