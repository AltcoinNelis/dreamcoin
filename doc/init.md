Sample init scripts and service configuration for dreamcoind
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/dreamcoind.service:    systemd service unit configuration
    contrib/init/dreamcoind.openrc:     OpenRC compatible SysV style init script
    contrib/init/dreamcoind.openrcconf: OpenRC conf.d file
    contrib/init/dreamcoind.conf:       Upstart service configuration file
    contrib/init/dreamcoind.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "dreamcoincore" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes dreamcoind will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, dreamcoind requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, dreamcoind will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that dreamcoind and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If dreamcoind is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running dreamcoind without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/dreamcoin.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/dreamcoind`  
Configuration file:  `/etc/dreamcoincore/dreamcoin.conf`  
Data directory:      `/var/lib/dreamcoind`  
PID file:            `/var/run/dreamcoind/dreamcoind.pid` (OpenRC and Upstart) or `/var/lib/dreamcoind/dreamcoind.pid` (systemd)  
Lock file:           `/var/lock/subsys/dreamcoind` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the dreamcoincore user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
dreamcoincore user and group.  Access to dreamcoin-cli and other dreamcoind rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/dreamcoind`  
Configuration file:  `~/Library/Application Support/DreamcoinCore/dreamcoin.conf`  
Data directory:      `~/Library/Application Support/DreamcoinCore`
Lock file:           `~/Library/Application Support/DreamcoinCore/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start dreamcoind` and to enable for system startup run
`systemctl enable dreamcoind`

4b) OpenRC

Rename dreamcoind.openrc to dreamcoind and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/dreamcoind start` and configure it to run on startup with
`rc-update add dreamcoind`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop dreamcoind.conf in /etc/init.  Test by running `service dreamcoind start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy dreamcoind.init to /etc/init.d/dreamcoind. Test by running `service dreamcoind start`.

Using this script, you can adjust the path and flags to the dreamcoind program by
setting the DREAMD and FLAGS environment variables in the file
/etc/sysconfig/dreamcoind. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.dreamcoin.dreamcoind.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.dreamcoin.dreamcoind.plist`.

This Launch Agent will cause dreamcoind to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run dreamcoind as the current user.
You will need to modify org.dreamcoin.dreamcoind.plist if you intend to use it as a
Launch Daemon with a dedicated dreamcoincore user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
