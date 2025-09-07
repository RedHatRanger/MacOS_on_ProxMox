# The Definitive Guide to Running MacOS in Proxmox [26.0 Beta]
💡 [soure](https://klabsdev.com/definitive-guide-to-running-macos-in-proxmox/)

UPDATED: September 2024 | Added New Mac Images August 2025

💾

Scroll down for Pre-Built Mac Images

### Introduction

There are a few different ways to install macOS on Proxmox, but most of them require you to own a Mac already. In this guide, we will go over both how to install MacOS inside Proxmox with and without owning a Mac already. Most guides would require you to download and create an for your installation. I will provide steps for both installing with and without a Mac.

Huge thanks for i12bretro tutorials as most of this come from their tutorials. I wanted to re-create them with screenshots for others to follow and offer the ISO images for anyone without a Mac to build them! The tutorials this one is based off of are listed below in the sources list.

### Requirements

If you would like to create your own MacOS images you will need to do this on a pre-existing Mac that you may own or have created. If you would like to simply download an image, then you can proceed to the Installation steps.

Download & Prepare a MacOS Image
--------------------------------

**Step 1)** On a Mac, we will begin by downloading [gibMacOS](https://github.com/corpnewt/gibMacOS?ref=klabsdev.com) from Github. Double-click on the downloaded .zip file to extract it. Then enter the extracted folder.

![Download gibMacOS from Github](https://klabsdev.com/content/images/2024/01/MacOS-01.png)

![](https://klabsdev.com/content/images/2024/01/MacOS-02.png)

Extract the file to a new folder

**Step 2)** Now we need to enter the extracted folder and double-click on the `gibMacOS.command` file.

⚠️

There is a good chance the file will not open. You will need to do this with the security settings window open and allow the file after you open it for the first time.

![Open the gibMacOS.command file and allow the security exception](https://klabsdev.com/content/images/2024/01/MacOS-03.png)

💡

When the file opens, if prompted, make sure to choose yes (Y) to install Python.

**Step 3)** Once the script has finished installing Python, you will now be at a screen in the terminal asking what version of MacOS you would like to download. In this example, we will be using MacOS Sonoma 14.1.1. So I will be choosing option 9.

💡

Make sure to take note of the size of the file you are downloading. Later we will create an image from this file and it will need to be larger than the downloaded file.

![Choose the version of MacOS you would like to download from the above list in terminal](https://klabsdev.com/content/images/2024/01/MacOS-04.png)

![The image you chose will begin downloading](https://klabsdev.com/content/images/2024/01/MacOS-05.png)

**Step 4)** Once the download has completed, enter the newly created download folder. If you ran from your downloads folder it will look like `~/Downloads/gibMacOS-master/macOS Downloads/publicrelease/042-89627 - 14.1.1 macOS Sonoma (23B81)/` if following this directly. Your version numbers may differ if you are downloading a different version of MacOS. Inside this folder, you will need to double-click on the `InstallAssistant.pkg` application. Follow the prompts to install. This installs the application we will be using to build our Proxmox friendly ISO image.

![Run the InstallAssistant.pkg to install the required builder](https://klabsdev.com/content/images/2024/01/MacOS-06.png)

**Step 5)** Next, we will begin building out Proxmox ISO image. In a new terminal window, you will need to create a temporary disk image. You will need to ensure it is larger than the downloaded images from before. For the MacOS Sonoma 14.1.1 image I had luck with a 16GB image.

```
hdiutil create -o /tmp/macOS -size 16000m -volname macOS -layout SPUD -fs HFS+J
```


**Step 6)** Next we are going to mount the temporary disk image we just created.

```
hdiutil attach /tmp/macOS.dmg -noverify -mountpoint /Volumes/macOSISO
```


**Step 7)** Now we are going to use the program that we previously installed to create an ISO image that can be used with Proxmox. This will take a few minutes to complete.

```
sudo /Applications/Install\ macOS\ Sonoma.app/Contents/Resources/createinstallmedia --volume /Volumes/macOSISO --nointeraction
```


⚠️

Ensure the path to the program/version MacOS you are installing is correct. In this case my application that was installed for the version of MacOS I am downloading was called "Install macOS Sonoma.app" so my path is `/Applications/Install\ macOS\ Sonoma.app/` If you were installing a beta for example it might need to be changed to `/Applications/Install\ macOS\ Ventura\ beta.app/` It just depends on the version you are using.

**Step 8)** Unmount the image using the following command:

```
hdiutil detach -force /Volumes/Install\ macOS\ Sonoma
```


⚠️

If you are following along directly, you can copy and paste this command. If you are downloading a different version of MacOS, you will need to check finder to ensure you are going to unmount the correct volume. You can check the exact volume name by looking on the right hand side of a Finder window.

**Step 9)** Now we are going to convert our unmounted image to an ISO file that can be uploaded directly to Proxmox using this command:

```
hdiutil convert /tmp/macOS.dmg -format UDTO -o ~/Desktop/macOS-Sonoma.cdr
```


**Step 10)** Finally, change the file extension from `.cdr` to `.iso`.

```
mv ~/Desktop/macOS-Sonoma.cdr ~/Desktop/macOS-Sonoma.iso
```


**Step 11)** The last thing we should do is a little housecleaning! We just need to remove the temp image.

```
rm /tmp/macOS.dmg
```


### Pre-Existing Images

This table is a list of MacOS images that I have already created. You can you can use these directly with Proxmox if you do not have a Mac to make your own.

💡

In the table below, are pre-built macOS image downloads that I have created for use with this article.


|MacOS   |Version|Download                                                    |
|--------|-------|------------------------------------------------------------|
|Tahoe   |26.0   |https://cdn.klabsdev.com/MacImages/macOS-Tahoe-Beta-26.0.iso|
|Sequoia |15.4   |https://cdn.klabsdev.com/MacImages/macOS-Sequoia-15.4.iso   |
|Sonoma  |14.1.1 |https://cdn.klabsdev.com/MacImages/macOS-Sonoma-14.1.1.iso  |
|Ventura |13.6.3 |https://cdn.klabsdev.com/MacImages/macOS-Ventura-13.6.3.iso |
|Monterey|12.7.1 |https://cdn.klabsdev.com/MacImages/macOS-Monterey-12.7.1.iso|


Proxmox Installation
--------------------

**Step 1)** Now that our MacOS image is ready, we will can begin the Proxmox setup. First we will need to head over to Github again and download the [KVM OpenCore Bootloader](https://github.com/thenickdude/KVM-Opencore?ref=klabsdev.com). You'll want to navigate to the releases tab and download the `.iso.gz` file. Once downloaded, extract the ISO image from the GZ file.

![Extract OpenCore ISO file from .gz archive file](https://klabsdev.com/content/images/2024/01/OpenCore01.png)

**Step 2)** Open and log into your Proxmox server's web UI. You will want to upload the `OpenCore-v20.iso` file to your server's ISO library. You will also want to upload the `MacOS.iso` image you have either downloaded or created!

![Upload OpenCore ISO file to your Proxmox server](https://klabsdev.com/content/images/2024/01/Step2-Fixed.png)

**Step 3)** We are now going to create the VM that will run MacOS! In the top right corner of the Proxmox Web UI choose "Create VM". Match your settings to the following screenshots.

Choose your VM's ID in this example we will choose `999`. Also, create your VM name. In this example I will choose `MacOS`. Then click Next.

![Choose Proxmox ID and VM Name](https://klabsdev.com/content/images/2024/01/pm02.png)

On the next screen, you will choose the `OpenCorev20.iso` image that you uploaded. Then choose `Other` for OS Type. Then click Next.

![Choose your ISO image and Guest OS Type.](https://klabsdev.com/content/images/2024/01/pm03.png)

On the next screen you will choose `VMWare compatible` for Graphics Card. Machine type will be `q35`. The BIOS should be `OVMF (UEFI)`. Next, check the box for Add EFI Disk. Choose the storage where you would like that saved. Then for SCSI Controller, choose `VirtIO SCSI`. Then click Next.

🛑

Make sure that you have the "Pre-Enroll keys" option un-checked. If you don't you will run into booting issues!

![](https://klabsdev.com/content/images/2024/01/pm04.png)

On the Disks screen, set the Bus/Device to `VirtIO Block` and set the storage to 64GB or more depending on how much space you would like. Next set the Cache to `Write back (unsafe)`. Finally, if you are running this VM on ZFS make sure that the `Discard` box has been checked. If you are NOT using ZFS, then leaved the box unchecked. Then click Next.

💡

If you would like to read more about Discard and ZFS, check out [this thread](https://forum.proxmox.com/threads/turn-on-discard-option.89208/?ref=klabsdev.com) on the Proxmox forums.

![](https://klabsdev.com/content/images/2024/01/pm05.png)

On the CPU screen, choose `4` on the Cores section. Then click Next.

![](https://klabsdev.com/content/images/2024/01/pm06.png)

On the Memory screen, set the amount in Megabytes to `4096`. Then click Next.

![](https://klabsdev.com/content/images/2024/01/pm07.png)

On the Network tab, choose `VMware vmxnet3` for the Model. Then click next.

![](https://klabsdev.com/content/images/2024/01/pm08.png)

At this point you can choose to finish the VM setup. Make sure the option to start the VM is UNCHECKED as we do not want the VM to start yet. There are a few hardware changes we need to make first.

**Step 4)** Next, we need to attach our MacOS image/ISO file we made to the VM. To do this simply navigate to the hardware tab and click the "Add" button at the top and choose "CD/DVD Drive".

![Add a DVD drive to VM](https://klabsdev.com/content/images/2024/01/DVD.png)

Then choose the macOS image you have uploaded! Click "Add".

![mount the ISO image](https://klabsdev.com/content/images/2024/01/macos-image.png)

**Step 5)** Next, click on you MacOS VM to select it. Then navigate to Options -> Boot Order. We need to make sure that `OpenCore.iso` is set to the first option in the boot order.

![Change the boot order to ensure OpenCore is booting first](https://klabsdev.com/content/images/2024/01/chrome_B2bId3tGpX.png)

**Step 6)** Now we will need to manually add some arguments to the end of the VM .conf file. To do this you will need to enter the Proxmox Host console. Do this by clicking on your node and clicking Shell at the top right corner.

![Open the Proxmox node console](https://klabsdev.com/content/images/2024/01/OpenConsole.png)

After the console has been opened, you can edit the VM config file with the following command: Change `VMIDHERE` with the ID number from you MacOS VM. In this example I am using 999.

```
nano /etc/pve/qemu-server/VMIDHERE.conf
```


Once, the file has been opened, go to the bottom of the file and add the following:

FOR INTEL HOSTS:

```
args: -device isa-applesmc,osk="ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc" -smbios type=2 -device usb-kbd,bus=ehci.0,port=2 -global nec-usb-xhci.msi=off -global ICH9-LPC.acpi-pci-hotplug-with-bridge-support=off -cpu host,vendor=GenuineIntel,+invtsc,+hypervisor,kvm=on,vmware-cpuid-freq=on
```


FOR AMD HOSTS:

```
args: -device isa-applesmc,osk="ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc" -smbios type=2 -device usb-kbd,bus=ehci.0,port=2 -global nec-usb-xhci.msi=off -global ICH9-LPC.acpi-pci-hotplug-with-bridge-support=off -cpu Haswell-noTSX,vendor=GenuineIntel,+invtsc,+hypervisor,kvm=on,vmware-cpuid-freq=on
```


You will also need to edit the 2 lines with the config regarding the CDROM drives that are mounting the OpenCore and MacOS ISO images. You will need to remove `,media=cdrom` and add `cache=unsafe`. The lines should look like this:

```
ide0: local:iso/macOS-Sonoma.iso,size=14450M,cache=unsafe
ide2: local:iso/OpenCore-v20.iso,size=150M,cache=unsafe
```


⚠️

DO NOT COPY the lines above as your IDE device numbers may not be the same.

Press `CTRL + X` then `Y` then `ENTER` to save the changes.

**Step 7)** Finally, we can start up the VM! Right click on the VM and click Start. After this click on the console button on the top right of the screen.

![Start up the VM](https://klabsdev.com/content/images/2024/01/Step7-Fixed.png)

Once the console window has been opened, press `ENTER` to open the `UEFI Shell`.

![Start the UEFI Shell pre pressing ENTER](https://klabsdev.com/content/images/2024/01/UEFIShellOpen.png)

Mount the UEFI location by typing:

```
fs0:
```


Then we can run the UEFI boot file by typing:

```
System\Library\CoreServices\boot.efi
```


![Boot the UEFI file](https://klabsdev.com/content/images/2024/01/UEFI-Boot.png)

After a while, you will boot into MacOS Recovery.

**Step 8)** Once booted into MacOS Recovery, choose "Disk Utility". From the Disk Utility window you will need to select VirtIO Block Media and Erase the disk.

![](https://klabsdev.com/content/images/2024/01/EraseDisk.png)

A new window will open. Choose `MacOS` for Name, `APFS` for Format, and `GUID Partition Map` for Scheme. One set, click "Erase". Once this has finished, click "Done". Finally, you can close the Disk Utility window.

![Set the options for the installation drive ](https://klabsdev.com/content/images/2024/01/Scheme.png)

**Step 9)** Once the Disk Utility window has been closed, choose the "Install macOS Sonoma" option. Follow the next few window prompts. When you get to a screen asking what disk to install MacOS to, choose the "MacOS Drive" we formatted before. Then click continue.

![Choose the "macOS" drive we erased previously](https://klabsdev.com/content/images/2024/01/Choose-Drive.png)

After some time, the data will be written to the drive MacOS will be booting from. The system will reboot a few times. Choose "macOS Installer" if brought back to the UEFI screen.

![Continue to macOS Installer](https://klabsdev.com/content/images/2024/01/MacOS-Installer.png)

**Step 10)** After doing this a few times, you will eventually end up at a Region selection screen. Choose your Region. Click Continue.

![Choose your region](https://klabsdev.com/content/images/2024/01/RegionSelection.png)

Next, click continue if your Preferred languages, Input Sources, and Dictation is correct. Click Continue.

![Confirm Preferred Languages, Input Sources, and Dictation is correct.](https://klabsdev.com/content/images/2024/01/spokenLanguages.png)

Choose "Not Now" on the Accessibility screen.

![Choose "Not Now" on the Accessibility screen](https://klabsdev.com/content/images/2024/01/Accessibility.png)

Click Continue on the "Data & Privacy" screen.

![Choose "Continue" on the Data & Privacy screen](https://klabsdev.com/content/images/2024/01/Data-Privacy.png)

Make sure to choose "Not Now" on the Migration Assistant screen.

![Choose "Not Now" on the Migration Assistant screen](https://klabsdev.com/content/images/2024/01/MigrationAssistant.png)

Finally, choose **"Set Up Later"** when asked to Sign in with your Apple ID.

🛑

DO NOT SIGN IN WITH AN APPLE ID! MAKE SURE YOU ARE SKIPPING TO THE NEXT STEP!

![Choose "Set Up Later" when asked to sign in with your Apple ID](https://klabsdev.com/content/images/2024/01/SignIn.png)

Lastly, you'll need to create your local account. Your VM will take a few moments to load into the desktop.

![Fill out your local account information](https://klabsdev.com/content/images/2024/01/CreateAccount.png)

Welcome to your MacOS VM running in Proxmox

![You are now at your MacOS Desktop](https://klabsdev.com/content/images/2024/01/desktop.png)

Boot MacOS W/out OpenCore
-------------------------

With the current configuration you will still need manually select MacOS when booting the VM. We can fix this by fixing the EFI partition.

**Step 1)** Log into your MacOS VM and open a web browser and download the [KVM OpenCore EFI folder](https://github.com/thenickdude/KVM-Opencore/releases?ref=klabsdev.com).

![Download the OpenCore EFI Folder from Github](https://klabsdev.com/content/images/2024/01/Download-OpenCore-Folder.png)

**Step 2)** Next, we also need to download [MountEFI from Github](https://github.com/corpnewt/MountEFI?ref=klabsdev.com) as well.

![Download MountEFI from Github](https://klabsdev.com/content/images/2024/01/MountEFI.png)

**Step 3)** Once both things are downloaded we'll start setting up the proper EFI. Open a terminal window and enter:

```
cd ~/Downloads/MountEFI-update
```


```
chmod +x MountEFI.command
```


```
./MountEFI.command
```


**Step 4)** In the terminal screen that opens, choose the number that matches the drive you created when installing MacOS. In my case, it's #1.

![Terminal screen showing the list of drives to mount an EFI partition from](https://klabsdev.com/content/images/2024/01/DriveNumber.png)

After hitting enter on the drive, you will be asked to enter the password for your account.

**Step 5)** Next open a Finder window. Open the EFI drive that has just been mounted from the left-hand panel. If there is a folder in there called `EFI`, rename it to `EFI.orig`.

![Copy the downloaded EFI folder to the EFI mounted drive](https://klabsdev.com/content/images/2024/01/EFICopy.png)

**Step 6)** Once the EFI folder has been copied, **SHUTDOWN** the vm.

**Step 7)** Back inside of the Proxmox web UI, Click back onto the MacOS VM and navigate to Hardware. We are going to remove the OpenCore CD/DVD Drive.

![Detach the EFI OpenCore disk in the Proxmox Web UI](https://klabsdev.com/content/images/2024/01/Detatch-EFI.png)

Once the disk has been detached, you can remove it. You can now boot the VM and verify you can launch into MacOS without OpenCore mounted.

### Conclusion

I have been playing around with the hardware allocation and I have noticed that the performance is not fantastic. It seems that macOS 14 Sonoma does not run very well in a Proxmox virtual environment. Increasing the amount of CPU cores and RAM can help improve performance, however there are dimensioning returns. This could very very useful for testing macOS IT deployments or building software for Mac regardless of the performance.

Huge thanks for i12bretro tutorials as most of this come from their tutorials. I wanted to re-create them with screenshots for others to follow and offer the ISO images for anyone without a Mac to build them! The tutorials this one is based off of are listed below in the sources list.

Thanks for reading!

Written By: Max Kulik

* * *

### Sources

*   [KVM OpenCore](https://github.com/thenickdude/KVM-Opencore/releases?ref=klabsdev.com) by [thenickdude](https://www.nicksherlock.com/?ref=klabsdev.com).
*   [gibMacOS](https://github.com/corpnewt/gibMacOS?ref=klabsdev.com) from [Github](https://github.com/corpnewt?ref=klabsdev.com) by [CorpNewt](https://www.reddit.com/user/corpnewt?ref=klabsdev.com)
*   [KVM OpenCore Bootloader](https://github.com/thenickdude/KVM-Opencore?ref=klabsdev.com) from [Github](https://github.com/thenickdude?ref=klabsdev.com) by [thenickdude](https://twitter.com/thenickdude1?ref=klabsdev.com)
*   [i12bretro Tutorials](https://i12bretro.wordpress.com/?ref=klabsdev.com)
    *   [Create Almost Any MacOS Installation Media](https://i12bretro.github.io/tutorials/0763.html?ref=klabsdev.com)
    *   [Running a MacOS 14 Sonoma VM in Proxmox VE](https://i12bretro.github.io/tutorials/0566.html?ref=klabsdev.com)
