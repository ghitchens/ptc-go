# ptc-go - SCS PACTOR modems driver for the Pat Winlink-client

(If you came here through the Pat Wiki, please read the full text!)

## What is this?

The code in this repository is a plug-in (you may also call it a
"driver") to support PACTOR modems manufacted by SCS in the
[Pat Winlink-client](http://getpat.io/). It does this by communicating
with the PACTOR modems using the WA8DED hostmode, enabling full binary transparency.

Note that this is **work in progress**, things may or may not work
properly. Use it at your own risk. The code is not feature complete yet. It should
be usable enough to send Winlink messages through a PACTOR (or PACKET)
channel using a supported PACTOR modem, though.

The code in this repository is independently developed from Pat
itself, although in close collaboration. Please do not bother Martin, LA5NTA, with 
questions concerning this driver. Instead, look [further down](https://github.com/harenber/ptc-go/blob/master/README.md#seeking-help).

Author: Torsten Harenberg, DL1THM

## How to use it

### setup

#### Compiling


The PACTOR support is beta and not included in the standard distribution of [Pat](http://getpat.io) yet. 
You need to build a version of Pat manually, however it is not difficult to do so. The Pat version with PACTOR support is
available as a seperate branch in the source tree. This [build script](https://github.com/la5nta/pat/blob/master/make.bash) will pull all dependencies, including this driver. So you do not need to bother with this repository.

Some very basic knowledge of using the [Go programming language](https://golang.org/) is helpful. But you do not need to write own code!

If you haven't done yet, [download](https://golang.org/dl/) and [install](https://golang.org/doc/install) Go. Get familiar with [the workspace](https://golang.org/doc/code.html#Workspaces). 

Now go to your GO src directory and issue

```
git clone -b feature/ptc-support https://github.com/la5nta/pat github.com/la5nta/pat
cd github.com/la5nta/pat
./make.bash libax25
./make.bash
```

That will create the binary into the current directory, you may want to move it to a directory in your PATH.

#### setup

Once you have successfully compiled Pat as described above, [configure it](https://github.com/la5nta/pat/wiki/The-command-line-interface#configure). Afterwards you should add an entry to your $HOME/.wl2k/config.json file like this:

```json
	"ptc": {
	"path": "/dev/ttyUSB0",
	"rig": "",
	"custom_init_script": "/home/pi/ptcinit.txt"
	},
```

Path is the tty to your SCS modem. The example here is from Linux and I haven't tested this on any other platform yet.
This __custom_init_script__ parameter is completely optional and may
contain commands which are send to the modem before switching it to
WA8DED-mode. It is generally not needed! Examples of commands which
could be useful are setting the audio level or any command needed to
select the right frequency, if you steer your radio through the
modem. Please check the modem's manual for details. You **do not**
need to set basic parameters like your callsign with this
feature. This is handled by the code.

### connect

To connect to the remote station PJ2A using the defaults set in your
config.json file

```
pat connect ptc:///PJ2A
```

To overwrite one or the other default in the config.json, you may use

```
pat connect "ptc:///PJ2A?host=/dev/ttyUSB0&baud=57600"
```

A successful connect looks like this:

```
pi@pi2 ~/gopackages/src/github.com/la5nta/pat $ ./pat connect ptc:///PJ2A
2018/04/22 13:16:21 Connecting to PJ2A (ptc)...
2018/04/22 13:16:23 Connected to PJ2A ()
PJ2A - Linux RMS Gateway 2.4.0 Oct 24 2017 (FK52nd)

Welcome to the PJ2A Winlink 2000 RMS Gateway. VERONA Radio Club, Curacao, Dutch Caribbean
 
INFO: Host Name sandiego.winlink.org, Port 8772
Connected
[WL2K-5.0-B2FWIHJM$]
;PQ: ABCDEFGH
CMS via PJ2A >
>FF
;PM: DL1THM P7TVSXXASKRJ 987 SERVICE@winlink.org //WL2K User Notice
FC EM P7TVSXXASKRJ 1873 987 0
F> DE
1 proposal(s) received
Accepting P7TVSXXASKRJ
Receiving [//WL2K User Notice] [offset 0]
//WL2K User Notice: 100%
>FF
FQ
```

#### using Packet radio

(for advanced users only)

You can change the PTC to use PACKET radio instead of PACTOR by moving
the PACTOR channel away from the default channel 4 (Channel is a data
stream inside the WA8DED protocol). The easiest way to do that is to
add a

```
PTCH 1
```

to the script which is called by `custom_init_script`. 

## Supported hardware

The code has tested against the PTC-II and -III series of the SCS PTC
modems. It should work with USB, serial and Bluetooth connections. The
P4 Dragon seems to use a non-standard baudrate on the serial line,
which is not supported by the underlying Go package. And I do not own one of these modems. So for the time
being, these new modems are unfortunately **not** supported, 
although there has been some [effort](https://github.com/harenber/ptc-go/tree/feature/p4-dragon) from Martin, LA4NTA, to get those modems running as well. If you
think you can contribute, please feel free to comment on [issue #3](https://github.com/harenber/ptc-go/issues/3). 
## What is missing

There are a lot of features that would be nice to have and which are
still under development. Most notably these are:

* listen mode. At the moment, the driver can only call remote station but cannot accept connections. 
* P4 Dragon support. (see [this issue](https://github.com/harenber/ptc-go/issues/3) for details). As I don't own such a device, feedback is more than welcome.

If you feel you are able to contribute, you are more than
welcome. Please comment on the appropreate issues.

Furthermore, tests on non-Linux systems would be appreciated.

## Debugging

Setting an environment variable `ptc_debug` will enable some more
debug messages. This feature will become more verbose in the future
and will probably include the data transferred from and to the PACTOR modem.

## Seeking help

The best place to ask for help is the
[Pat Google Group](https://groups.google.com/forum/#!forum/pat-users). 

That is true even if you have questions concerning this driver, I (DL1THM) monitor the Pat Google Group as well and will answer there. 

Otherwise, feel free to open issues to this repository if you find bugs not reported yet.

## Acknowledments

First I wish to thank Martin Hebnes Pedersen, LA5NTA, for developing
Pat, his patience and for his helpful code reviews. Further thanks to my good friend
Brett Ruiz, PJ2BR, for providing a second station for beta-testing. And thanks to @blockmurder for
his patches.
