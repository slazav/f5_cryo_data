## Computer system for Fridge5

#### V.Zavjalov, 2023-11-13

The computer is installed on the 1st floor and connected to
university network. ip address: `148.88.189.202`, name:
`pya522000026.lancs.ac.uk`, internally domain name is set to `f5a`.

Install and start ssh server.

Firewall is controlled by `ufw` program, see `ufw status`.
Open ports: 22 (sshd)

Users:
  sla -- included to admin groups
  f5 -- For all Fridge5 users
 - add keys to my users
 - PasswordAuthentication no, PubkeyAuthentication yes (/etc/ssh/sshd_config)
 - ordered network card (PCIe, 1GBit) + 5m cable in Farnell

#### Installed packages
  openssh-server
  cifs-utils
  mc xterm xfig geeqie xpdf git
  tigervnc-standalone-server fvwm
  blt octave octave-dev

  make pkg-config gcc  g++ gfortran
  libmicrohttpd-dev  libcurl4-openssl-dev libjansson-dev
  tcl-dev tk-dev tcl-itcl4
  libdb-dev db-util libgsl-dev
  debhelper debmake

  missing: libgpib

Storage:
  /luna - Luna folder is mounted with ultservi credentials (see /etc/fstab)
  /data -- folder for local data, to be exported via nfs if needed

VNC:
  using tigervnc (Xvnc is not supported by Ubuntu)
  Set user mapping in /etc/tigervnc/vncserver.users
    :0 for f5 user
    :1 for sla user
  Set user config in ~/.vnc and passwords with vncpasswd program
  all with localhost=yes, f5 with SecurityTypes=None (open to any user on the localhost)
    ~/.vnc/Xtigervnc-session -- xsession
    ~/.fvwm/config -- fvwm config
  Start services (for both :0 and :1):
    systemctl enable tigervncserver@:0.service
    systemctl start tigervncserver@:0.service

  Connect from outside:
    - setup ssh connection to "f5a"
    - vncviewer :0 -MenuKey F12 -SecurityTypes None -via f5a -geometry 1920x1200 -Maximize

Locall programs
  I'm builing my programs in /home/sla/PROG
  - device2 -- libgpib missing
  - fit_res  -- OK
  - graphene -- OK
  - he3lib -- -fperm
  - pico_osc
  - tcl-device_role
  - tcl-exp
  - tcl-gpib
  - tcl-xblt

#### V.Zavjalov, 2023-11-14

Reconfiguring desktops
Installed packages: xdm tigervnc-viewer
Remove Packages: gdm3-

~/.xsession  -- session config
~/.fvwm/config -- fvwm config
~/.vnc/Xtigervnc-session -- session config for virtual desktop
~/.vnv/fvwm.conf -- fvwm config for for virtual desktop

Screens:
  :0 -- physical desktop
  :10 -- f5 virtual desktop
  :11 -- sla virtual desktop

My programs
  - build and install graphene
      https://github.com/slazav/graphene
  - build and install device2 (w/o gpib support),
      https://github.com/slazav/device2
  - build and install fit_res
      https://github.com/slazav/fit_res
  - tcl-device_role, tcl-xblt, tcl-exp

    start device_d server
    device configuration: /etc/device2/devices.cfg
    database device "db", databases are located in /data/DB, accessed as root

#### V.Zavjalov, 2023-11-15

Installed second network card, connected to internal network
set static IP (see /etc/netplan) 10.22.21.10/24

Install gpib+usb -> eth converter (Keysight E5810B)

lock-in D5 15

####  V.Zavjalov, 2023-11-16
install: nmap isc-dhcp-server libvisa-dev

Scan internal network: nmap -sn 10.22.21.*

10.22.21.2    00:1B:21:7B:3E:49 (Intel Corporate) -- windows computer
10.22.21.5    20:4E:7F:44:35:35 (Netgear)  -- bridge downstairs
10.22.21.6    30:46:9A:1C:B1:26 (Netgear)  -- bridge upstairs
10.22.21.101  00:30:D3:23:0E:12 (Agilent Technologies)
10.22.21.103  00:30:D3:18:0D:80 (Agilent Technologies)
10.22.21.105  00:30:D3:1C:01:8A (Agilent Technologies)
10.22.21.110  00:30:D3:1D:1D:32 (Agilent Technologies)

X Set udhcpd (/etc/udhcpd.conf, /etc/default/udhcpd)
/etc/dhcp/dhcpd.conf

also write hostnames in /etc/hosts
systemctl start isc-dhcp-server

####  V.Zavjalov, 2023-11-20,21

* vxi driver for device2 server

* install pico_osc package

* install he3lib package  (not a nice octave installation)

"pkg load he3lib" in ~/.octaverc

* set word selection chars:

"XTerm.VT100.charClass: 95:48,45:48,46:48" in .Xresources
"xrdb -merge ~/.Xresources" in .xsession and .vnc/Xtigervnc-session

####  V.Zavjalov, 2024-01-03

Add f5 user to admin groups:
`usermod -a -G adm,sudo f5`

