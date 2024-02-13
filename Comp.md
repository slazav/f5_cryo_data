## Computer system for Fridge5

V.Zavjalov, 2024-01-10

### General network configuration

A the moment computer is installed on the 1st floor and connected to
university network. ip address: `148.88.189.202`, name:
`pya522000026.lancs.ac.uk`, internally domain name is set to `f5a`.

Internal network is connected via second ethernet card.
See `/etc/netplan`.

DHCP server is working on the internal network:
 - Service: `systemctl (start|stop|restart|status) isc-dhcp-server`
 - Configuration: `/etc/dhc~cpd.conf`
 - Also note aliases for network devices in `/etc/hosts`

Firewall is controlled by `ufw` program, see `ufw status`.
Open ports:
 - 22 (sshd)

SSH server is configured with `PasswordAuthentication no`,
`PubkeyAuthentication yes` (see `/etc/ssh/sshd_config`).
If you want access to f5 user, a public key should be added to
`/home/f5/.ssh/authorized_keys`.
(Now there is access for Theo, Tineke, Slava, ultservi user on Fridge5
windows computer).

### Users

sla, f5 -- both included to admin groups, can do `sudo` (user password is
needed).

### Storage

- `/luna` - Luna folder is mounted with ultservi credentials (see `/etc/fstab`)
- `/data` -- folder for local data, to be exported via nfs if needed
- `/data/DB` -- databases (writable only by root user)

### Backup

Cron configuration, running programs regularly: `/etc/crontab`.
Actual scripts are in `/data/CRON/`.

`/data/CRON/sync_luna` -- sync data to luna. Currently databases
(`/data/DB` folder) is syncronized with luna every hour.

### Desktops

It is possible to have a few phisical desktops (attached to the monitor)
and a few virtual ones. Each virtual desktop belongs to one user,
phisical desctops can switch users. Any desktop can be shared via vnc
(fully, or view-only).

For example in Fridge4 there are two phisical desktops on the table
computer (for me, screensaver+password protected, and for group use), and
a virtual desktop on the rack computer which can be shared (all
measurements are running there).

At the moment Fridge5 computer has one physical desktop `:0` (any user can log in)
and a virtual desktop for f5 user `:10`.

Physical desktop `:0`:
- Display manager: `xdm`
- Session configuration (what to run): `/home/f5/.xsession`
- Window manager: `fvwm`, configuration: `/home/f5/.fvwm/config`
 (useful to add programs to menu, to apply configuration use `restart` in the menu).

Virtual desktop `:10`:
- Using `tigervnc`
- Session configuration (what to run): `/home/.vnc/Xtigervnc-session`
- Window manager: `fvwm`, configuration: `/home/f5/.vnc/fvwm.conf`
- Virtual desktop can be open from the physical desktop using `VNC: Measurements` in `fvwm` menu.
- All users who have access to the computer have access to this desktop, no passwords.
- Access from outside can be arranged via ssh tunnel:
    - setup ssh connection to "f5a"
    - vncviewer :0 -MenuKey F12 -SecurityTypes None -via f5a -geometry 1920x1200 -Maximize
- TODO: add instruction for windows users

Some low-level configuration:
User mapping (virtual desktop -> user):
  `/etc/tigervnc/vncserver.users`)

Start/stop/enable/disable/status for desktop `:10`:
  `systemctl (start|stop|enable|disable|status) tigervncserver@:10.service`

SSH tunnel:

  `ssh f5a -L 5910:localhost:5910`


### Local programs

#### Graphene -- the database.
`https://github.com/slazav/graphene`

#### Device2 -- server for accessing databases and devices
`https://github.com/slazav/device2`

At the moment installed without gpib support (libgpib is not supported in
Ubuntu, but I can try to add it).

Works with vxi devices (Keysight gpib2eth converter), usbtmc devices
(Keysight things connected via USB), lxi devices, some serial devices
(leak detectors etc.), Graphene database, Pico oscilloscope driver...

Configuration: `/etc/device2`
List of devices: `/etc/device2/devices.cfg`
Client program `device_c`
TCL library: `Device2`

Example:
```
# device_c ask gen101 "*IDN?"
Agilent Technologies,33521B,MY52701054,2.09-1.19-2.00-52-00
```

#### fit_res -- a simple tool for fitting linear resonances
`https://github.com/slazav/fit_res`

#### tcl-xblt -- some useful tcl/tk libraries from ROTA group
`https://github.com/slazav/tcl-xblt`

#### tcl-device_role -- second layer of device drivers
`https://github.com/slazav/tcl-device_role`

#### tcl-exp -- interfaces for measurement programs
`https://github.com/slazav/tcl-exp`

#### pico_osc -- driver for Pico oscilloscopes and ADC
`https://github.com/slazav/pico_osc`

#### he3lib -- he3 library
`http://slazav.github.io/he3lib/index.htm`
`https://github.com/slazav/he3lib`

Works via command line:
```
$ he3 he3_tc 0:10:30
# he3_tc -- T_c [mK] vs P [bar], Greywall-86 scale
# P he3_tc
0.000000e+00 9.293837e-01
1.000000e+01 1.827985e+00
2.000000e+01 2.239204e+00
3.000000e+01 2.438364e+00
```

and in Octave:
```
octave> he3_tc(0:10:30) 
ans =
   0.9294   1.8280   2.2392   2.4384
```

### Devices
See `/etc/device2/devices.cfg`

* db -- databases in `/data/DB`
* gen_a3
* gen_a5

### Databases

* comments -- text database for comments

