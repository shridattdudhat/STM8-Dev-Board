# Manual Installation

The recommanded way of installation is now the [automatic
installation](board-manager-install.md) using the Arduino Board Manager.

The manual installation described here is only useful if you prefer a
Makefile based build over using the Arduino IDE or if you would like to help
with the development of Sduino.

In any case it is still possible to use the IDE.

For all systems start with this:

- Clone or download and extract the
  [repository](https://github.com/tenbaht/sduino) to a convenient location.
- Download the lastest [SDCC snapshot
  build](http://sdcc.sourceforge.net/snap.php) for your system. Support for
  the STM8 improves significantly from version to version. Do not use the
  older version of SDCC that might be included in your distribution (Version
  3.6 is definitely too old).


## Linux

#### Sduino core

Link the sduino folder of the downloaded repository to your local Arduino
hardware folder (usually in `~/sketchbook` or `~/Arduino`). Create the
hardware folder, if not already present:

```bash
	cd ~/Arduino
	mkdir hardware	# only if not already present
	ln -s [the-extracted-repository]/sduino hardware
```

#### SDCC

Extract the downloaded [SDCC archive](http://sdcc.sourceforge.net/snap.php)
under `/opt`:

```bash
	sudo mkdir /opt
	sudo tar xvjf ~/Downloads/sdcc-snapshot* -C /opt
```

Create a link to SDCC from the tools directory of the sduino repository
(required for IDE builds):

```bash
	cd ~/Arduino/hardware/sduino/tools
	ln -s /opt/sdcc .
```

#### Flashtools

`stm8flash` and `stm8gal` are both included as precompiled binaries in this
tools directory. Create a link `linux` to the linux tools directory that
matches your system and copy the binaries to a convient place in your path,
e.g. `/usr/local/bin`:

```bash
	ln -s linux64 linux
	cp -av linux/* /usr/local/bin
```


`make` and libusb-1.0 (needed by stm8flash) are part of the standard
repository for any Linux distribution. Example for Debian-type systems (like
Ubuntu, Mint, Elementary etc.):

```bash
	sudo apt install make libusb-1.0-0
```

Finally, add a udev rule in order to access the USB port for your flash
tool. Save this as root in in `/etc/udev/rules.d/99-stlink.rules`:

```text
	# ST-Link/V2 programming adapter

	# ST-Link V1, if using a STM8S discovery board
	# important: It needs a special entry in /etc/modprobe.d
	SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3744", \
	    MODE:="0666", \
	    SYMLINK+="stlinkv1_%n"

	# ST-Link/V2, the china adapter with the green plastic housing
	SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", \
	    MODE:="0666", \
	    SYMLINK+="stlinkv2_%n"

	# ST-Link/V2.1, the new Nucleo STM8 boards
	SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", \
	    MODE:="0666", \
	    SYMLINK+="stlinkv2-1_%n"
```

Using the STM8S-Discovery board as a flash programmer requires a [special
modprobe configuration
entry](../hardware/stm8sdiscovery#usage-with-linux-and-stm8flash).




## MacOS

Quite similar to the Linux install. [Install Homebrew](https://brew.sh/) if
you haven't done it yet, than install make and unpack the sdcc snapshot:

```bash
	brew make
	mkdir /opt
	tar xvjf sdcc-snapshot* -C /opt
```

Adopt the other steps from the Linux install. The arduino hardware folder is
in your Documents folder at `/Users/<username>/Documents/Arduino/hardware`.

Not sure about stm8flash, I didn't try it. Have a look at the [stm8flash
repository](https://github.com/vdudouyt/stm8flash). It would be great if
someone could summarize the procedure and send it to me.







## Windows

Same idea again, but additionally we need to install a minimal command line
environment with all the tools needed by make and the Makefile. Let's start
with that:


### Installing a command line environment

This can be any of these options (in order of complexity):

- busybox and make
- Msys2
- MinGW
- Cygwin

The makefile `sduino.mk` uses busybox by default when running on Windows. To
change that, set these environment variables accordingly:

	SHELL=bash
	.SHELLFLAGS=-c

Alternativly, it is possible to include these lines in the project `Makefile`
or edit `sduino.mk` directly (lines 68, 69).



#### Install busybox and make

This is by far the easiest way to get going. busybox already comes with
sduino in the `hardware/sduino/tools/win/` folder. A statically linked copy
of make-4.2 can be found in the
[windowstools](https://github.com/tenbaht/sduino-windowstools) repository.

Just copy these two binaries into a place somewhere in your `%PATH%`.



#### Install Msys2

Msys2/mingw is more capable than busybox and even comes with a proper
package manager. Install this if you plan on using the command line tools
for more than just running sdunio compilations.

mingw is a full (cross-) compilation environment and it uses msys2, which is
just a collection of basic UNIX tools. msys2 is enough for using sduino.

- Download and run the installer from [http://www.msys2.org](http://www.msys2.org)
- Install make: `pacman -S make`
- define the environment variable `HOME` in the windows settings as
  `%USERPROFILE%`
- close and re-open the terminal window

Now we can start working. 
(Or read the excellent and more comprehensive installation guide on https://github.com/orlp/dev-on-windows/wiki/Installing-GCC--&-MSYS2
)


#### Cygwin

cygwin is similar to msys2, but even more complete in the sense that it
tries to emulate a full POSIX environment. It is really more like a virtual
machine than just a set of libraries.

Almost anything that compiles for UNIX can be compiled for cygwin with
minimal effort. The downside is that the resulting binaries are linked
against cygwin specific libraries and that they don't mix very well with
Windows native applications. It is possible to do it, but it might require
some extra effort.

Install this if you need full POSIX compliance. (e.g. compiling
stm8flash with support for espstlink, that requires the POSIX termios
library).



#### Install the core files and SDCC

Thanks to the running bash the remaining steps are the same as for Linux,
the only difference is your Arduino folder in `Documents/Arduino`.


#### ST-Link, stm8flash

If not already done, install the ST-Link driver from the ST website for
hardware access.

You are not bound to `stm8flash`, you can use the regular flash tool by ST
if you prefer.

`stm8flash` and `libusb-1.0.dll` are both included as precompiled binaries
in the tools directory. Copy or link them to a directory in your path:

```text
	cd hardware/sduino/tools/win
	cp -av stm8flash.exe libusb-1.0.dll /bin
```

#### Limitations

Keep the [known problems using
Windows](limitations#known-problems-using-windows) in mind.


## Additional suggestions for Windows users

### Get a better console

There is no good reason to keep suffering from the stock Windows terminal
window. Installing e.g.
[console2](https://sourceforge.net/projects/console/) will make your life on
the command line so much more enjoyable.


### Installation shortcut

Instead of installing mingw and sdcc from scratch you can

* manually unpack the latest board manager tools archive
  [sduino-tools_mingw32*.tar.bz2](https://github.com/tenbaht/sduino/releases)
  into the hardware/sduino/tools folder
* set `c:\sdcc` as a link to the sdcc folder
* Add `tools/win` and `c:\sdcc\bin` to your `%PATH%

As a workaround for unpacking a tar.bz2 file (again, Windows is lacking even
the most basic tools...) you could use the board manager and copy the needed
files out of Appdata/Arduino/packages/sduino. But mind the slightly
different directory layout and don't forget to remove the package again
after you are done copying. Otherwise your are going to use different
versions of the sduino core for IDE and Makefile builds. Confusion would be
guaranteed!


### Using MinGW instead of msys2

I used to recommand using MinGW, because it is more compact than msys2. But
installation is a little more complicated. If you are like me and prefer
minimal solutions over big packages:

1. Check the
   [MinGW Installation Notes](http://www.mingw.org/wiki/Getting_Started)
2. Download and run
   [mingw-get-setup.exe](https://sourceforge.net/projects/mingw/files/Installer/)
   You can safely deactivate the graphical option.
3. Add `;c:\mingw\bin;c:\mingw\msys\1.0\bin` to the end of your `%PATH%`
4. Open a command line and install msys-base: `mingw-get install msys-base`
5. Now `ls` or `make` should work.
6. Start a bash: `bash`
7. For now you might want to set the `HOME` variable: `export
   HOME=/c/Users/<yourusername>`. In the long run this goes in `.bashrc`.


## Additional general tweaks

The full SDCC archive includes support for many CPUs, but you only need the
stm8. You can safely delete all the files related to the PIC CPUs in the
`pic*` and `non-free` directories. That safes more than 90% of the used disk
space and leaves only 20MB out of 240MB.
