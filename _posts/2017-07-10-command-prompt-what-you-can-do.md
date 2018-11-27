---
layout: post
title: A Compilation of Command Prompt Tips, Tricks & Cool Things You Can Do
comments: true
categories: [linux, optimize, tips]
tags: [linux, optimize, tips]
---

### Stop any command mid-operation

Since you might be testing some commands... pressing Ctrl + C will cancel a command that you've already entered and allow you to begin typing another one, while typing cls will clear the current window of any commands that you've entered.

### Did you know there is a settings menu?

Right-click the title bar and open Properties for settings that include the ability to change your Command Prompt's font, layout and colors, as well as options such as Quick Edit mode, which will automatically paste text from your clipboard when you right click in the window.

Bonus: You can customize the text in the top bar by typing title followed by the text that you want (example: title My title was pasted with Quick Edit mode).

### Command Prompt keyboard shortcuts
Tapping the up and down arrows on your keyboard will cycle through commands that you've previously entered. More shortcuts:

Tab: When you're typing a folder path, tab will auto-complete and cycle through directories
`Ctrl + M:` Enables Mark Mode which lets you move the cursor in all directions with the arrow keys
`Ctrl + C or V:` As of Windows 10, you can copy and paste with control C and V like elsewhere around the OS
`Ctrl + F:` Likewise, control + F now lets you search for text in the Command Prompt
`Ctrl + Shift + Scroll` on your mouse: Increases or decreases the window transparency (+ and - keys work too)
`Alt + Enter:` Enables full-screen mode with no title bar displayed on top (F11 also works like elsewhere in Windows)

### Drag and drop folders to insert a directory path

Directory paths don't have to be typed in the first place. Aside from being able to copy/paste text and tab through directories, you can drag and drop a folder directly into the Command Prompt window to automatically insert the location.

### The function keys F1-F9 are also shortcuts

The function (F) keys on the top of your keyboard are also shortcuts that mostly re-enter previous commands without retyping them.

- F1: Tapping or holding this key will retype the command that you just entered letter by letter.
- F2: Copies the current command up to a specified character.
- F3: Completely retypes the previous line that you entered.
- F4: The reverse of F2 -- auto-deletes up to a specified character (your cursor must be in front of the text).
- F5: Retypes the previous command like F3 but lets you cycle back through many lines in your command history.
- F6: Inserts Ctrl+Z (^Z) in the Command Prompt, which is an end-of-file indication (text after this is ignored)
- F7: Opens a list of previously entered commands that you can select from.
- F8: Works like F5 but doesn't stop at the end of your command history, cycles back to the start.
- F9: Lets you retype a previous command by entering a number associated with the line.

### Windows Commands

1. Creates a symbolic link. (`mklink`)

#### Syntax

<pre>
	mklink [[/d] | [/h] | [/j]] <Link> <Target>
</pre>

#### Parameters

| Parameter | Description |
| /d | Creates a directory symbolic link. By default, mklink creates a file symbolic link. |
| /h | Creates a hard link instead of a symbolic link. |
| /j | Creates a Directory Junction. |
| <Link> | Specifies the name of the symbolic link that is being created. |
| <Target> | Specifies the path (relative or absolute) that the new symbolic link refers to. |
| /? | Displays help at the command prompt. |

#### Examples

To create a symbolic link named MyDocs from the root directory to the \Users\User1\Documents directory, type:

<pre>
	mklink /d \MyDocs \Users\User1\Documents
</pre>


### Enter multiple commands at once

Adding && between commands will let you enter multiple lines at once to be executed in succession. Example:

<pre>
	tasklist && netstat -b
</pre>


### See every process running and connected

Entering tasklist command will list all of the processes running on your machine along with details such as their process identifier and memory usage, while netstat -b will generate a list of all the processes with an established network connection.

### List every device driver on your PC

There are many ways to generate a list of drivers in the Command Prompt, here's one line that includes additional information such as the status of a device and the folder location/file name of the associated driver:

<pre>
	driverquery /FO list /v
</pre>

### Output results to text file or clipboard

You can save the output of a command such as tasklist or driverquery to a new text file by adding > along with a directory and file name. Example:

<pre>
	driverquery > C:\Users\TechSpot\Desktop\output.txt
</pre>


### Delete temporary files from your drive

Some of the temporary files on your drive can be deleted with the following commands (/q runs the operation without a confirmation prompt, /f ignores the read-only attribute and forces deletion, /s deletes contents from all sub-folders):

- Delete temporary user files: del /q /f /s %temp%\*
- Delete temporary system files (requires admin rights): del /s /q C:\Windows\temp\*
- ...Or run them together: del /q /f /s %temp%\* && del /s /q C:\Windows\temp\*

If that didn't empty enough storage, here are more temp folder locations, and we recently covered a bunch of ways to free up space on Windows, including Command Prompt methods to launch an advanced Disk Cleanup tool and another to disable hibernation by deleting the feature's system file (hiberfil.sys).

### Open Windows' on-screen keyboard

Entering osk into a Command Prompt opens Windows' on-screen keyboard which lets you click keys with your mouse instead of typing them.

### Shutdown your PC at a certain time

The "shutdown" command can be used with a range of switches to shutdown, reboot and more, including the ability to force applications closed, to display a message on shutdown, and to specify the number of seconds you'd like before the operation happens. Example: shutdown -s -t 3600 would shutdown your PC in one hour.


### Create a Wi-Fi hotspot & Find your Wi-Fi password

If your network adapter supports the feature, you can configure your PC to be a wireless hotspot from the Command Prompt.

How to check if your hardware is capable: Enter netsh wlan show drivers and look for the line that reads "Hosted network support: Yes."

From there, you can enable the hotspot with this command: `netsh wlan set hostednetwork mode=allow ssid=YOURSSID key=YOURPASSWORD`

Also enter this line to enable the hotspot: netsh wlan start hostednetwork (stop works too) and you can check the status of your new hotspot by entering netsh wlan show hostednetwork.

You may also have to navigate through some GUI menus to enable connection sharing (we couldn't find a way to do this from the Command Prompt): Go to Network and Sharing > Change adapter settings (Control Panel\Network and Internet\Network Connections) and you should see the network adapters connected to your PC.

Right click the device that is connected to the Internet (not your new hotspot if that's listed) and open Properties. In the Sharing tab, check the box to "Allow other network users to connect through this computer's Internet connection."

Among other netsh commands, the feature can show your Wi-Fi password by entering netsh WLAN show profile name=YOURPROFILE key=clear (look under Security settings > Key Content).

This line can delete a network profile you're no longer using: netsh WLAN delete profile name=YOURPROFILE

### Quick fix commands for connection issues

Trouble with your connection? ipconfig has some go-to commands to resolve the issue:

- ipconfig /release (Releases your currently issued local IP address)
- ipconfig /renew (Requests a new local IP address)
- ipconfig /flushdns (Resets the contents of the DNS client resolver cache)

For persistent issues with your connection, there are some more elaborate commands that are worth trying. Note that you may have to reconfigure your network adapter etc. if you have manual settings, particularly when resetting TCP/IP and the Windows Firewall.

These commands require a reboot.

- netsh winsock reset (Resets the winsock catalog to its default state, removing layered service providers `-- netsh winsock show catalog` lists these items)
- netsh int ip reset all (Removes and reinstalls TCP/IP, rewriting a couple of associated registry keys)
- netsh advfirewall reset (Restores the Windows Firewall to default settings, deleting previously configured rules etc.)
- nbtstat -r (Purges the contents of the NetBIOS name cache and then reloads the #PRE-tagged entries from the Lmhosts file)
- nbtstat -rr (Releases and then refreshes NetBIOS names for the local computer that is registered with WINS servers)


### Quick fix commands for boot issues

Are you greeted with a message that reads "reboot and select proper boot device?" or "NTLDR is missing" when you turn on your PC? The method below has worked for us to repair a Windows 8 machine and should be valid for other editions of Windows.

Note that these lines must be entered in a Command Prompt that is opened in a recovery environment from outside of your operating system, such as from a bootable USB drive with Windows 10 installation media.

- bootrec /fixmbr
- bootrec /fixboot
- bootrec /scanos
- bootrec /rebuildbcd

Although that sequence has worked for us in the past, we tested this on a virtual machine by deleting the system reserved partition (where boot information is stored) and trying to restore the bootable state by entering those lines. Unfortunately, access was denied for bootrec /fixboot and we couldn't find a workaround, so Windows still couldn't load.

However, these bcdboot and bootsect commands worked to get Windows booting again, even though we just reformatted the system partition:

- `bcdboot C:\Windows` (Recreates the system partition files with those copied from C:\Windows. While this format works from within Windows, see the note below about running this command from a recovery console outside of Windows).
- `bootsect /nt60 SYS /mbr` (Rewrites master boot code on the system partition and updates your master boot record)

*Note: If you are trying to run bcdboot C:\Windows from a recovery console outside of your Windows installation (such as from the Windows 10 installation media), C:\Windows is probably D:\Windows (your operating system) while C:\ is probably your system partition (the boot info). Entering diskpart and then list vol will display drive volumes so you know what's what (type exit to close diskpart). To run the bcdboot operation from a recovery environment, you want something like this command: bcdboot D:\Windows /s C:\ *


### Footnote...

Master Boot Record - The first sector on the whole drive and contains a boot loader as well as information about partitions on the drive.

Volume Boot Record (aka Partition Boot Sector/System Partition in MS terminology) - The first sector on a partition containing information to load an operation system.


### Quick fix commands for repairing Windows

Still can't boot into Windows? Experiencing bluescreens or some other bug? Windows has built-in tools to scan for and replace corrupt system files:

- sfc /scannow (from inside Windows)
- sfc /scannow /offbootdir=D:\ /offwindir=D:\Windows (from outside Windows, such as from a recovery drive)

A separate DISM command can scan the Windows component store for corrupt files and automatically repair them:

- DISM /Online /Cleanup-Image /RestoreHealth (from inside Windows)
- DISM /Image:D:\ /Cleanup-Image /RestoreHealth (from outside Windows)


### Encrypt, compress or backup files

Files and folders can be encrypted or compressed and then unencrypted and decompressed with quick individual commands for each operation -- make sure you change the target destination:

`Encrypt` a file or folder: cipher /e C:\Users\TechSpot\Desktop\Folder (here are more cipher commands -- /h applies a hidden attribute)

`Decrypt` that file or folder: cipher /d C:\Users\TechSpot\Desktop\Folder

`Compress` a file or folder: compact /c /s:C:\Users\TechSpot\Desktop\Folder

`Decompress` a file or folder: compact /u /s:C:\Users\TechSpot\Desktop\Folder

You can create semi-incremental file backups using robocopy, though this is more involved than the previous commands.

There's a great Super User thread discussing the subject where variants of the same command are recommended for having robocopy do basic file backups, copying files from one location to another -- but only when changes have been made.

Here's the most suggested command (see the link for an explanation on the options):

<pre>
	robocopy C:\source M:\destination /MIR /FFT /R:3 /W:10 /Z /NP /NDL
</pre>

### Generate reports about your system

These reports are surprisingly detailed and may help in understanding issues involving your system's power consumption or wireless connectivity.

`powercfg /energy` (Generates a report about the energy efficiency of your system -- the location of the report is mentioned after the command finishes)

`powercfg /batteryreport` (Generates a report with specifications for your battery as well as stats and graphs about the history of your battery usage)

`netsh wlan show wlanreport` (Generates a report with specs for your wireless hardware along with information about your connection history)


### Watch an ASCII version of Star Wars Episode IV

A variety of old school telnet services are still available, including the ability to watch Star Wars Episode IV.

Enter telnet towel.blinkenlights.nl into a Command Prompt for the movie to start (there's no sound).

Note: If the command isn't recognized, you might have to enable telnet. Search Start or the Control Panel for "Turn Windows features on or off" and check the box next to Telnet (click Ok...).

More places you can go via telnet:

`telnet towel.blinkenlights.nl 666` (a "bastard operator from hell" excuse generator)

`telnet telnet.wmflabs.org` (Wikimedia)

`telnet rainmaker.wunderground.com` (Weather Underground)

`telnet aardmud.org 4000` (Aardwolf)

`telnet achaea.com 23` (Achaea)

`telnet ateraan.com 4002` (New Worlds - Ateraan)

`telnet avalon-rpg.com 23` (Avalon: The Legend Lives)

`telnet batmud.bat.org 23` (BatMUD)

`telnet eclipse.cs.pdx.edu 7680` (New Moon)

`telnet forgottenkingdoms.org 4000` (Forgotten Kingdoms)

`telnet freechess.org 5000` (Free Chess)

`telnet igormud.org 1701` (IgorMUD)

`telnet lord.stabs.org 23` (Legend of the Red Dragon)

`telnet mush.shelteringcolorado.com 2601` (Sheltering Sky: Colorado by Night)

`telnet zombiemud.org 23` (Zombie MUD)


## References
1. [https://www.techspot.com/guides/1724-command-prompt-what-you-can-do/](https://www.techspot.com/guides/1724-command-prompt-what-you-can-do/)