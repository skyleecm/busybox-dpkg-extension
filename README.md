# busybox-dpkg-extension


I have made some modifications to the busybox dpkg.c and dpkg-deb.c , this is based on the [Debian busybox](https://packages.debian.org/jessie/busybox) source version 1:1.22.0-9

For dpkg.c ,

1. to run preinst install and postinst configure, instead of preinst and postinst without parameters. This is because the packages scripts may fail if called without any parameters. This would at least support the most basic scenario which is to install a new package.
2. if postinst configure fails, set the package status to be half-configured, instead of aborting the installation without any status.

For ref: see [Debian Package maintainer scripts and installation procedure](https://www.debian.org/doc/debian-policy/ch-maintainerscripts.html) and [Debian wiki - MaintainerScripts](https://wiki.debian.org/MaintainerScripts)

For dpkg-deb.c ,

1. add dpkg-deb -R option , which extracts packages filesystem tree to directory and the control information files into a DEBIAN subdirectory.

----

Further, I use a shell script, dpkg to add the following dpkg options:

1. dpkg -L package-name , to list installed files for package-name.
2. dpkg -s (or --status) package-name , to display status info of package-name.
3. dpkg -S filename-search-pattern , to search for a filename from installed packages.
4. a non-standard -D1 to turn on logging to /var/log/idpkg.log for the dpkg commands, and -D0 to turn off logging and remove the idpkg.log

The shell script should be placed at /usr/bin/dpkg , the dpkg default location, so that it can handle the additional options, and then it will simply call the busybox dpkg link at /bin/dpkg to handle the other options.

For this arrangement to work, the PATH env variable should list /usr/bin before /bin so that /usr/bin/dpkg should be executed when using dpkg without any path.

