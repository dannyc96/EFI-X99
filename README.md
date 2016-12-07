# My Hackintosh

This repository is the EFI directory (basically, a boot drive) that can boot a macOS (10.12.1, at time of writing) installation. This EFI will only work with my specific setup, but may work with similar ones via tweaking.

![macpro6_1_-_geekbench_browser](https://cloud.githubusercontent.com/assets/73924/20958327/f1370bc8-bc09-11e6-823b-833d1da15cbd.png)

## Why Github?

This is the easiest way to make and track changes, and allow others to benefit from the updates I make to my Hackintosh. Not having your EFI in revision control is bad. Eventually you will end up breakign something and not remember what you did.

## Installation

### My Hackintosh Hardware

You need to have mostly the same hardware for this to work. Parts that can not be swapped easily will be noted.

* [Cooler Master HAF XB II EVO, HTPC Computer Case](http://amzn.to/2h4oiqI)
* [Corsair RMx Series, RM750x, 750W, Fully Modular Power Supply](http://amzn.to/2g8zbe4)
* [Gigabyte X99P-SLI Motherboard](http://amzn.to/2g8vxAR) [1]
* [2 x Corsair Vengeance LPX 32GB DDR4 3200](http://amzn.to/2h4l729)
* [Intel Core i7-6950X Processor](http://amzn.to/2gBbozC) [2]
* [MasterAir Pro 3 CPU Air Cooler](http://amzn.to/2h4pL0k)
* [Gigabyte GeForce GTX 980Ti](http://amzn.to/2h1rDXd) [3]
* [BCM94360CD Wireless and Bluetooth Card](http://amzn.to/2g8AQ3m) [4]
* [Samsung 840 EVO 1TB SSD](http://amzn.to/2g9lczB) [5]

#### Notes
1. This motherboard is a little wonky, so while other Gigabyte or Asus X99 motherboards may work, my EFI repository may not work with it. Change at your own risk.
2. If you get a different CPU, you MUST modify [this line](https://github.com/koush/EFI-X99/blob/master/CLOVER/kexts/Other/VoodooTSCSync.kext/Contents/Info.plist#L54) in your checkout with the correct IOCPUNumber value. Each CPU core has two hyper threading cores. Subtract 1 to get the max IOCPUNumber (zero indexed). _IOCPUNumber = (Number of CPU Cores * 2) - 1_. For example, the 6950X has 10 cores. So (10*2)-1=19, as shown in the link.
3. Do not get any Pascal (1070, 1080, or Titan X) hardware. It is not supported on Mac yet. I recommend sticking with recent NVidia hardware. You must install [NVidia Web Drivers](http://www.insanelymac.com/forum/topic/306535-nvidia-web-driver-updates-for-el-capitan-update-10242016/) after booting. That will get your graphics card drivers installed, and you won't be stuck in VESA graphics mode.
4. Any USB wifi will work. I chose this card because this is the the best/easiest way to get AirDrop working. Totally optional though. You don't have to get any wifi at all.
5. Any SSD or HDD drive works. Not sure about the new M.2 drives yet though.

### macOS Preparation
0. Attach your Hackintosh OS drive to a real Mac. Use a [USB enclosure](http://amzn.to/2h4wuY0) to do this.
1. Use Disk Utility to erase and create a GPT drive with a single Fat32 partition. The Fat32 partition is not where the EFI is stored, as the ESP is an automatically created hidden partition of around 100MB in size. The Fat32 partition will potentially be used to do a BIOS update later in these steps.
2. On the Mac, download the latest macOS (Sierra, 10.12.1 at time of writing).
3. Using that Mac, change install target, and install macOS to the attached Hackintosh hard drive.

### Boot USB Setup

This EFI directory lives on an ESP (EFI System Partition). This partition is typically hidden from operating systems. On the real Mac, again:

0. Download Clover bootloader (I am using r3949 at time of writing): https://sourceforge.net/projects/cloverefiboot/files/Installer/
1. Use Disk Utility to erase and create a GPT drive with an HFS+ partition. The ESP parititon will be automatically created and hidden.
2. Install Clover to the USB stick using these options:
  * Change Install Location to the USB Stick (DO NOT FORGET THIS!!!!)
  * Customize, with only the following checked:
   * Install for UEFI booting only
   * Install Clover in the ESP.
3. After installation of Clover is complete, the installer will leave the ESP mounted.
4. In that ESP, there will be an EFI directory. So typically, the directory structure will be as follows _/Volumes/ESP/EFI_.
5. In a command prompt:
```sh
cd /Volumes/ESP/
# wipe this out to overwrite with this EFI
rm -rf EFI
# check out this EFI
git clone https://github.com/koush/EFI-X99.git EFI
# If you chose a different processor, modify the aforementioned file in VoodooTSCSync.kext.
```

### Hackintosh Setup

1. Assemble your computer.
2. The OS drive should already be set up with macOS from a real Mac. Connect it to your Hackintosh if you haven't already.
3. Boot into the BIOS. [Update the BIOS](http://www.gigabyte.com/products/product-page.aspx?pid=5658#bios) to the latest (F23, at time of writing) using the Fat32 partitioned USB boot stick that was previously set up.
  * You may want to shut down and reset your CMOS by connecting the CMOS reset pins.
4. Restart and get into the BIOS again. Load Optimized Defaults.
5. Boot from the USB drive (spam F12 at POST for a boot menu and select the UEFI USB drive).
6. You'll see 2 or 3 options show up in the Clover bootloader.
 * Boot Clover
 * Boot macOS
 * Boot Recovery
7. Boot macOS
8. You should be fully booted in with ancient VESA graphics (low resolution and framerate). You just need the Nvidia drivers. [Install the correct version for your version of macOS](http://www.insanelymac.com/forum/topic/306535-nvidia-web-driver-updates-for-el-capitan-update-10242016/) and restart.
9. Done!
