### The history of systemd

When systemd was introduced to Linux, it faced the initial opposition of many die-hard Linux developers. After all, there were plenty of solutions to manage services and the early implementation has quite a few bugs so that *Linus Torvalds* himself in 2014 refused to merge code from the systems developer into the Linux kernel.

But after a rocky start, it was introduced with the *Jessie* release to the __Raspberry Pi__ community in 2018. It has since convinced users and developers with its reliability and flexibility and has become the new standard for managing services and running scripts.

### The placement of systemd files

Using systemd requires the creation of a small configuration file which is placed into the
`/etc/systemd/system/`

> Often times, you will read instructions where you are told to put this file into the `/lib/systemd/system/` directory but this is bad practise. Don’t do it.

The difference between these two file locations is as follows:
Files that come in packages downloaded from a distribution repository go into `/usr/lib/systemd/`.

> Local modifications (e.g. python scripts) that are manually placed by the user for ad-hoc software installations that are not in the form of a package go into `/etc/systemd/system/`.

### The configuration logic of systemd

To create a new service file in systemd, type

```
sudo nano /etc/systemd/system/name-of-your-service.service
```

By using this command, the file is already placed in the right directory, so you don’t have to navigate through the maze of Linux directories.

The command is the same if your want to later change your service file. If it doesn’t exist, it creates the file. If it already exists, it opens it.

Every systemd file has three sections called

```
[Unit]
[Service]
[Install]
```

### The Unit section

The Unit section provides basic information about the service and what conditions precedent (e.g. network connectivity) need to be met before a service is started.

This will be shown in the services overview, just call it as you like.

```
Description=My custom service
```

In this case, the service will be run after the user accounts are available to the system during the boot process.

```
After=multi-user.target
```

Or if you want it to wait until the desktop appears.

```
After=graphical.target
```

If your script requires network connectivity, use

```
Requires=network.target
```

A resulting typical Unit section looks like this:

```
[Unit]
Description=This is my great service
After=multi-user.target
```

### The Service section

The Service section provides instructions on how to control the service.

```
Type=idle
```

The ‘Type’ option of ‘idle’ tells systemd to wait until all other services have completed. This waits until the boot process has been fully completed.

```
User=pi
```

The User defines which user the program will run under.

```
ExecStart=/usr/bin/python3 /home/pi/your-script.py
```

These are the commands and arguments executed when the service starts. 

> Note that you must give the full path of both the program you are running (Python3) and the script.

Restart specifies what should happen in the event of a service ending either intentionally or by crashing.

There are four often used options:

```
Restart=always # always restart
Restart=no # the service will not be restarted. This is the default.
Restart=on-success # Restart only when the service process exits cleanly (exit code 0)
Restart=on-failure # Restart only when the service process does not exit cleanly (node-zero exit code)
```

This is followed by

```
RestartSec=60
```

which specifies the time in seconds to sleep before restarting a service.

A resulting typical Service section to start a Python script looks like this:

```
[Service]
Type=idle

User=pi
ExecStart=/usr/bin/python3 /home/pi/your-python-script.py

Restart=always
RestartSec=60
```

### The Install section

The Install section provides instructions on how systemd triggers the custom service if enabled with `systemctl enable`. This is mostly used for starting the custom service on boot.
A resulting typical Install section looks like this:

```
[Install]
WantedBy=multi-user.target
```

### Example of starting a Python script
There are plenty of Python script examples on this blog, which you can activate with systemd.

#### Create a new service

So create a new file with
```
sudo nano /etc/systemd/system/name-of-your-service.service
```
This typical setup works for the Python scripts on this blog. Just copy & paste

```
[Unit]
Description=name-of-your-service Service
After=multi-user.target

[Service]
Type=idle
User=pi
ExecStart=/usr/bin/python3 /home/pi/your-Python-script.py
Restart=always

[Install]
WantedBy=multi-user.target
```

### Change file permissions
Change the file permissions to make it readable by all by typing

```
sudo chmod 644 /etc/systemd/system/name-of-your-service.service
```

### Inform the system
As the last step, you need to tell the system that you have added this file and want to enable this service so that it starts at boot.

```
sudo systemctl daemon-reload
sudo systemctl enable name-of-your-service.service
```

> Reboot your Pi, and you are all set!

### If you need to make edits
If at any point you need to edit the script, just call it up again with

```
sudo nano /etc/systemd/system/name-of-your-service.service
```

edit and save it.

> As long as you don’t change the file name of the `.service` file, there is no need to invoke the systemctl commands again.

> However, after deleting a file, you should reload the systemd process with

```
sudo systemctl daemon-reload
```

## systemd commands

You already used the `sudo systemctl reload` and `sudo systemctl enable` command above, but let’s take a look at a few other ones that can be useful for debugging.

#### Starting a service manually

```
sudo systemctl start name-of-your-service.service
```
#### Stopping a service
```
sudo systemctl stop name-of-your-service.service
```
#### Restarting a service
```
sudo systemctl reload name-of-your-service.service
```

#### Enabling a service to start at boot
To tell systemd to start a service automatically at boot, you must enable it. That’s what we did in the previous chapter.

To start a service at boot, use the enable command:

```
sudo systemctl enable name-of-your-service.service
```
Disabling a service to start at boot
And the same syntax to disable a service at boot
```
sudo systemctl disable name-of-your-service.service
```

> Note that enabling a service does not start right away. Should you want to start the service and enable it at boot, you will have to issue both the start and enable commands.

#### Check if a service is active
```
sudo systemctl status name-of-your-service.service
```

#### Check if a service is enabled
To check if a service has been enabled to start at boot

```
sudo systemctl is-enabled name-of-your-service.service
```
#### List all active services
To see a list of all active services

```
systemctl list-units --type=service
```
#### Using systemd for interval triggered scripts
Often crontab -e is used to launch a script based on a fixed interval, e.g., every hour.

This systemd example will launch the script `say-the-time.py` every hour:

```
[Unit]
Description=This script will start launch
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/bin/python3 /home/pi/say-the-time.py

Restart=always
RestartSec=3600

[Install]
WantedBy=multi-user.target
```

> Note that this launches the script 3600 seconds after the previous call has been completed, not on the hour of the clock.

