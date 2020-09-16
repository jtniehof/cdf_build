# CDF tool related makefiles

This repository contains Makefiles to simplify the build and install
of tools for using NASA [CDF](https://cdf.gsfc.nasa.gov/) files.

They support the sorts of things you'd expect from an autotools-style
Makefile, e.g. specifying a prefix on build and a different one on install,
as well as helping get some of the Java pathing right. JDK is required.

You may need to change the `JAVA_HOME` before building; you can either
set the environment variable, e.g.:
`JAVA_HOME=/usr/lib/jvm/java make`
or edit the Makefile. On Ubuntu there's usually a reasonable one in
`/usr/lib/jvm/default-java` and on RedHat derivatives, it's
`/usr/lib/jvm/java`. Note this for the full JDK not just JRE.
(`sudo yum install java-1.7.0-openjdk-devel` on RedHat.)

Notable targets are:

`download`: download the source

`dist`: create a full tarball including the downloaded source

`install`: install, as usual

`distclean`: clean up everyting, including the downloaded source.

A simple `make && make install` will do the trick in most cases.

Edit `versions.mk` to match the current version (this is what will
be downloaded.)

There are two versions: one using [Environment
Modules](http://modules.sourceforge.net/) and one not. I highly
recommend using modules, but the setup in here relies on a particular
set of helpers that I haven't set up for the public yet...hopefully
they'll be helpful for ideas. Having two directories here is of
course sub-optimal and at some point maybe the modules will be just
an option...

This is all pretty Linux-specific.

Default install location is /usr/local. On RedHat and related
distributions you'll probably need to add /usr/local/lib to
`ld.so.conf.d`; the scripts don't define LD_LIBRARY_PATH or otherwise
alter the search path (they do set CDF_BASE, so you don't have to call
the definitions scripts just to run them.)

## CDF

Main NASA CDF library.

Installed scripts:

`cdftools.sh`: Java-based graphical CDF tools.

## skeleton editor

Skeleton editor and ISTP compliance checker. Install the CDF library first;
if a PREFIX or LIBDIR are specified, they must be the same for CDF library
and the skeleton editor.

Note the Makefile on this isn't quite perfect: you need to explicitly run
`make` then `make install` (just doing `make install` doesn't do the
download/build properly; sorry.)

Installed scripts are:

`istpcheck.sh`: command-line ISTP compliance checker

`skteditor.sh`: Skeleton editor GUI
