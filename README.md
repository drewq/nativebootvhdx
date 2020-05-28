# nativebootvhdx
Instructions for creating native boot vhdx for windows

# About 

There are several ways to create a native boot vhdx for windows. This document covers the process that lessens the chance of accidental data loss.

Microsoft Reference Docs: https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/deploy-windows-on-a-vhd--native-boot 

**This doc assumes that you have access to Windows or Linux ISO**

# Step 1 - Create Virtual Machine

Launch **Hyper-v**

**Action** > **New** > **Virtual Machine**

Click **Next**, Enter a **name** for your new virtual machine

Select **Generation 2**

Specify the desired **memory** for your hardware and needs

_(optional)_ Select your network adapter settings

Create a **virtual hard disk**, specify desired:
* Virtual hard disk name _[.vhdx format is required]_
* Location
* Size _[must have this much storage space available to boot native boot vhdx]_

Select **install an operating system from a bootable image file**

# Step 2 - Install Virtual Machine Operating System

**Start** the virtual machine, press any key to boot from CD or DVD

Perform a OS **install** as you would a regular virtual machine

## Windows OS

_(optional)_ Create an answerfile called **unattend.xml** and place it in \Windows\Panther\Unattend\

Launch either **command prompt as admin** or **powershell as admin**
> \windows\system32\sysprep\sysprep.exe /generalize /oobe /shutdown

The VM will **shutdown automatically** if there is no error during sysprep

# Step 3 - Prepare Virtual Machine for Native boot

Delete all **checkpoints** for the virtual machine

After checkpoints merge, copy the **virtual hard disk path** in **Action** > **Settings** > **SCSI Controller** > **Hard Drive**

_[ensure its .vhdx and not a checkpoint .avhdx]_

_(optional)_ backup the vhdx if you have the time and storage space available


# Step 4 - Create boot entries in BCD
Launch either **command prompt as admin** or **powershell as admin**

### CMD: 
Copy the current boot entry:

`bcdedit /copy {current} /d "My Dualboot Description"`

Copy the UUID including the brackets {...} from the **output** of the previous command

Change the **device** path of the new boot entry to the vhdx:

`bcedit /set {UUID from clipboard} device vhd="[DriveLetter:]\PathToVHDXFromStep3.vhdx"`

> _bcdedit /set {...} device vhd="[C:]\Users\Public\Documents\Hyper-V\Virtual hard disks\Win 10.vhdx"_

Change the **osdevice** path of the new boot entry to the vhdx:

`bcedit /set {UUID from clipboard} osdevice vhd="[DriveLetter:]\PathToVHDXFromStep3.vhdx"`

> _bcdedit /set {...} osdevice vhd="[C:]\Users\Public\Documents\Hyper-V\Virtual hard disks\Win 10.vhdx"_

### PS:
Copy the **current** boot entry:

`invoke-expression 'bcdedit.exe /copy "{current}" /d "My Dualboot Description"'`

Copy the UUID including the brackets {...} from the **output** of the previous command

Change the **device** path of the new boot entry to the vhdx:

`Invoke-Expression 'bcdedit.exe /set "{UUID from clipboard}" device vhd="[DriveLetter:]\PathToVHDXFromStep3.vhdx"'`

> _Invoke-Expression 'bcdedit /set "{...}" device vhd="[C:]\Users\Public\Documents\Hyper-V\Virtual hard disks\Win 10.vhdx"'_

Change the **osdevice** path of the new boot entry to the vhdx:

`Invoke-Expression 'bcdedit.exe /set "{UUID from clipboard}" osdevice vhd="[DriveLetter:]\PathToVHDXFromStep3.vhdx"'`

> _Invoke-Expression 'bcdedit /set "{...}" osdevice vhd="[C:]\Users\Public\Documents\Hyper-V\Virtual hard disks\Win 10.vhdx"'_
# Step 5 - Reboot and Setup Drivers

Since the machine has been **sysprepped** in **Step 2** it will need to **reboot** a couple of times in order to prepare Windows

Windows will go through the **user** and **region** setup question again unless an answerfile was provided

Once logged in, verify that you have internet connection and **install** any necessary **drivers**




