Minimum X Packages Requirements to Work with X11 Forward in SSH
--------

### The minimum packages needed for X-Windows to work with SSH are:

    xorg-x11-xauth
    xorg-x11-fonts-*
    xorg-x11-utils

### Install the X11 Packages in Server:

    yum install xorg-x11-xauth xorg-x11-fonts-* xorg-x11-utils -y

### Enable the following in the sshd_config file:

    X11Forwarding yes

### Connect to SSH using the -XY parameters:

    $ ssh root@192.168.0.1 -XY

### Check $DISPLAY is ok

    $ echo $DISPLAY
