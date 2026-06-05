Sample init scripts and service configuration for th3d
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/th3d.service:    systemd service unit configuration
    contrib/init/th3d.openrc:     OpenRC compatible SysV style init script
    contrib/init/th3d.openrcconf: OpenRC conf.d file
    contrib/init/th3d.conf:       Upstart service configuration file
    contrib/init/th3d.init:       CentOS compatible SysV style init script

Service User
---------------------------------

All three Linux startup configurations assume the existence of a "raven" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes th3d will be set up for the current user.

Configuration
---------------------------------

At a bare minimum, th3d requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, th3d will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that th3d and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If th3d is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running th3d without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/th3.conf`.

Paths
---------------------------------

### Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/th3d`  
Configuration file:  `/etc/th3/th3.conf`  
Data directory:      `/var/lib/th3d`  
PID file:            `/var/run/th3d/th3d.pid` (OpenRC and Upstart) or `/var/lib/th3d/th3d.pid` (systemd)  
Lock file:           `/var/lock/subsys/th3d` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the raven user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
raven user and group.  Access to th3-cli and other th3d rpc clients
can then be controlled by group membership.

NOTE: When using the systemd .service file, the creation of the aforementioned
directories and the setting of their permissions is automatically handled by
systemd. Directories are given a permission of 710, giving the ravencoin group
access to files under it _if_ the files themselves give permission to the
ravencoin group to do so (e.g. when `-sysperms` is specified). This does not allow
for the listing of files under the directory.

NOTE: It is not currently possible to override `datadir` in
`/etc/th3/th3.conf` with the current systemd, OpenRC, and Upstart init
files out-of-the-box. This is because the command line options specified in the
init files take precedence over the configurations in
`/etc/th3/th3.conf`. However, some init systems have their own
configuration mechanisms that would allow for overriding the command line
options specified in the init files (e.g. setting `RAVEND_DATADIR` for
OpenRC).

### macOS

Binary:              `/usr/local/bin/th3d`  
Configuration file:  `~/Library/Application Support/TH3/th3.conf`  
Data directory:      `~/Library/Application Support/TH3`  
Lock file:           `~/Library/Application Support/TH3/.lock`  

Installing Service Configuration
-----------------------------------

### systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start th3d` and to enable for system startup run
`systemctl enable th3d`

### OpenRC

Rename th3d.openrc to th3d and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/th3d start` and configure it to run on startup with
`rc-update add th3d`

### Upstart (for Debian/Ubuntu based distributions)

Drop th3d.conf in /etc/init.  Test by running `service th3d start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

### CentOS

Copy th3d.init to /etc/init.d/th3d. Test by running `service th3d start`.

Using this script, you can adjust the path and flags to the th3d program by
setting the RAVEND and FLAGS environment variables in the file
/etc/sysconfig/th3d. You can also use the DAEMONOPTS environment variable here.

### Mac OS X

Copy org.th3.th3d.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.th3.th3d.plist`.

This Launch Agent will cause th3d to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run th3d as the current user.
You will need to modify org.th3.th3d.plist if you intend to use it as a
Launch Daemon with a dedicated raven user.

Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
