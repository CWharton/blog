+++
date = '2015-12-16T00:00:00-05:00'
draft = false
title = 'Disable/Enable Windows Lock Screen (Windows + L)'
+++

Although Windows is not my primary operating system, I do need to use it in order to get my day job done. Therefore I run Windows in a VirtualBox VM. A good habit to have, and I often use is to lock my computer whenever I walk away from it. Unfortunately, this can have some unexpected effects on some desktops. When the focus is on the VM and using the hotkeys (Windows + L) the VM will lock leaving the workstation unlocked. A quick web search didn't reveal the correct solution so I figured I would post it..

## Registry Edit

Open up the _**regedit.exe**_ by typing it in the Windows menu search box or the run dialog. Then locate the following key. If it does not exist then create it.

```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\System
```

Create a new DWORD 32-bit and name it '_DisableLockWorkstation'_. Then double click on it to give it the value on '_1'_ for on. This change should work without a restart.

I have not tried this and all Windows versions but at the time of this write-up, it works on Windows 7 & 10.
