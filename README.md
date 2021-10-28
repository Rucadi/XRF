# XRF
XRF is a small utility written for the Xbox One System OS. It's written to allow the retrieval of console information, experimentation and to take advantage of Win32 functionality.

### Prerequisites
- Visual Studio 2015/2017
- Windows 10 SDK (Preferably latest)
- Xbox One Devkit

### Deployment
After compiling, you can utilize a post-build event to transfer the executable via Network Share. Executable is required to be on the console before execution, obviously.

### Usage
Printing basic console information
```
XRF cinfo
```


### Credit:
- emoose
- tuxuser
- gligli



https://gbatemp.net/threads/info-xbox-one-getting-somewhat-started.517582/
#1
NOTE: This is not an exploit or breakthrough of any sort. It's simply taking advantage of provided debugging features in developer mode! This is for any one who may be curious and want to reverse engineer the Xbox One.

This is also mainly provided for anyone who wants to just have a go at reversing the system. There's a lot to utilize with the public features anyway.

Prerequisites:
- Must be in developer-mode (obviously)
- Have some form of SSH/telnet client. (PuTTy, etc)
- At least have Visual Studio 2015 or 2017

To get started without putting up with developing UWP applications we can instead utilize the open SSH connection provided by the console. This is only available in developer mode, just in case you get any ideas.

If you're using Windows and will be using standard command prompt for telnet then make sure you enable it first!

Spoiler
1. Control Panel -> Programs -> Turn Windows features on or off"
2. Tick "Telnet client"
3. Done

First open up whatever client you have for SSH, in this instance PuTTy, and connect using your console IP and default port.
There'll be a pop-up. Just hit yes.

Now it will ask for login details. Make sure you have Dev Home opened and hit "Show Visual Studio Pin". Keep note of this pin but also remember it will change after a small period of time!

Username: DevToolsUser
Password: The Visual Studio pin provided in Dev Home.

If all goes successfully then you can either stick with it or intialise telnet. Run the following command in order to do so (ignore quotes):

Spoiler
"devtoolslauncher LaunchForProfiling telnetd "cmd.exe 24""
Now you can connect over!

Spoiler
Open command prompt on Windows and run: telnet [consoleip] 24

(Example: telnet 192.168.1.5 24)

The telnet session will be running under the VSProfilingAccount privileges which is the same as what the VS debugger runs under when building UWP apps.

Keep in mind that there is not too much of a difference at this stage. It just allows a tiny bit more flexability.

Basic file system exploration:

Spoiler
You can do this by accessing the Xbox Device Portal on your computer and going to File Explorer tab. There will
be an option near the top right that is called Browse. Using this will show you credentials that can be used
to access the developer scratch. We can use the developer scratch to store our junctions to navigate throughout the mounted drives.

Using telnet or SSH, go to D:\DevelopmentFiles.
- >D:
- >cd DevelopmentFiles
- >mkdir Links

And run the following:
- >mklink /J "Links\System" C:\

If the result is successful then double check:
- >cd links\system
- >dir

If it gives you a directory listing then there you go!

You can get easier access by opening File Explorer on Windows and typing the following into the file path bar: \\[consoleip

It will prompt for login details. If you open the device portal and go to File Explorer tab then on right side hit browse; you will be given details to use. Once in then you can access most but not all volumes.

(Refer to "Mount points" to find out more)

So what now? Well, I'm going to provide a small "template" which you can use in order to write a standard "Win32" application. The only difference is that it will run on the Xbox One.

(Requires Windows 10 SDK compatible with Xbox One and probably Visual Studio 2017, at least 2015.)
XRF: Attached below.
Place anywhere on the console and run "xrf cinfo" for a basic spit of console info.

Additional information:

Spoiler
Basic introduction:

Spoiler
The Xbox One currently runs 3 separate operating systems with each prioritised with their own purpose.

These are known as:
• Host OS
• System OS
• Game OS

System and Game OS both reside in their own partition:
• Shared Resource Access - Runs apps and renders the UI experience.
• Exclusive Resource Access - Runs games and has more priority with resources.

These operations are stored in an Xbox Virtual Disk (XVD) with a small bootloader, currently assumed based on previous data dumps, that contains the kernel, HAL and other important system files. These get stored in the
User Data section of each.

• host.xvd | ExtHost.xvd
• System.xvd
• era.xvd

System and Host are stored in both the flash and on the console hard drive. The Game OS XVD is stored with each
packaged game that is released for the Xbox One. Although this requires another look; it appears that when a user
launches a game, System then initiates a call that mounts the package to the ERA partition which then boots into the Game OS before finally mounting and starting the game.
Mount points:

Spoiler
Within the SRA Partition, the following are mounted to each drive letter:
\\.\C:\ -> System.xvd
\\.\D:\ -> USB (typically for retail) (Development scratch for dev-mode)
\\.\J:\ -> SystemTools.xvd (dev-mode only)
\\.\L:\ -> en-%s (languages)
\\.\M:\ -> SystemMisc.xvd
\\.\P:\ -> Page file
\\.\S:\ -> Settings.xvd | Settings-devkit.xvd
\\.\T:\ -> Temp.xvd (or whatever)
\\.\U:\ -> user.xvd / user-devkit.xvd
\\.\X:\ -> SystemAux.xvd
\\.\Y:\ -> SystemAuxF.xvd
