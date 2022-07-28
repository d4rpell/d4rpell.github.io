---
title: How to bypass Windows 10 Login Panel
published: true
---

*Hi, I'm going to show you how to bypass the Login Panel of Windows 10 💻*

## Access to the Recovery Panel

Whe need to introduce on the Recovery Panel, for this whe need to shutdown our windows.

After that when our pc show the Windows logo, whe need push the botton of power again.

With that we makes that the pc run in the mode of *"preparing automatic repair"*.

Windows ask us if we want to shutdown the pc or going to the Advanced Options, we select the advanced options.

I show a image that say what do you need to select on this options:

<img src="https://i.ibb.co/MpJ7jSB/Table.png">

After that we have a panel that say, Select a system image backup, we click on continue.

Now on advanced and select search for a system image on the network.

<img src="https://i.ibb.co/VSntF4Q/Search-on-the-system.png">

## File Explorer Path

Now, we go to this path of the system *C:\\Windows\\System32\\*.

## Changing names of the .exe programs

Now on the path we need to locate the program called *utilman.exe* and *cmd.exe*.

We need to change utilman.exe to utilman.ORI.exe and change cmd.exe to utilman.exe

After that, close all windows and click on continuous.

## Pwning the Shell how Administrator

And now in the login panel whe need to select this icon

<img src="https://i.ibb.co/NnbfX5d/Bottom.png">

After we see a shell, ok now lets type whoami

<img src="https://i.ibb.co/3R00Rh9/LOL-nt-authority-system.png">

Now we put this command:  `net user {USER_WHO_WANTS_DUMP} *`.

**At this point we pwned the Windows 10 🔓**