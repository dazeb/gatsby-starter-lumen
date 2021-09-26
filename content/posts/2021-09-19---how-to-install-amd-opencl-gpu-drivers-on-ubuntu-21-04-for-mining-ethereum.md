---
draft: false
coverImage: media/mining-new.png
template: post
title: How to Install AMD OpenCL GPU Drivers on Ubuntu 21.04 for Miners
slug: How-to-Install-AMD-OpenCL-GPU-Drivers-on-Ubuntu-21.04-for-Miners
socialImage: /media/mining-new.png
date: 2021-09-19
description: One of lifes greatest mysteries, how to install AMD graphics drivers on Linux.
category: Mining
tags:
  - amd-drivers
  - crypto
  - ethereum
  - farming
  - mining
  - overclocking-gpu-on-linux
  - ubuntu-21-04
  - wattman-on-linux
categories:
  - crypto
  - drivers
  - ethereum
  - linux
  - ubuntu
---
Installing drivers on Linux can be an absolute disaster waiting to happen. You install the drivers and you get either a black screen or the system will simply refuse to boot. I have tried absolutely every way to install them and i almost always have problems. (bare with me and i promise in the end you will have a solution and an understanding why so you will never have problems with AMD drivers on Linux again)

If you google "how to install AMD drivers on Linux" you will inevitably get led down the wrong path and there is no clear and easy way out of that rabbit hole of disasters.

The problem is all the guides are meant for users wanting to game on their system not mine Ethereum so that's where the first problem arises because you will see everywhere that the AMD drivers are already installed to the kernel, which they are BUT the part you need to mine is not there because ***AMD did not include the OpenCL drivers in the kernel driver.***

Type the command below and see for yourself, you will see the card is recognised and drivers have been loaded.

```bash
sudo lshw -c video
```

So now you can see how we can easily be led down the wrong path, are they installed or not?.

# Driver Installation

First and foremost. You want to be on the latest version of Linux with the latest kernel. At the time of writing the details are:-

* Ubuntu 21.04 (Kubuntu)
* Kernel: 5.11
* AMD RX 580 8GB STRIX

You may read online that older drivers are the best option but this is outdated information. All we need to do is install the OpenCL package on its own, without any of the other addons.

First lets install a couple of packages:

```bash
sudo apt install build-essential dkms
```

From here we need to download the drivers from [AMD Official Website](https://www.amd.com/en/support/graphics/radeon-500-series/radeon-rx-500-series/radeon-rx-580) and unzip the package.

```bash
tar -xJpf amdgpu-pro-*.tar.xz
```

Open a terminal in the folder you extracted and issue the following command:

```bash
./amdgpu-install --opencl=legacy --headless --no-dkms
```

What this command does is install just the OpenCL driver so you can get your cards mining again on the latest OS!

We can install `clinfo` to check if OpenCL is installed use `sudo apt install clinfo` and you should be greeted with something like the below

```bash
Number of platforms                               1
  Platform Name                                   AMD Accelerated Parallel Processing
  Platform Vendor                                 Advanced Micro Devices, Inc.
  Platform Version                                OpenCL 2.1 AMD-APP (3302.5)
  Platform Profile                                FULL_PROFILE
  Platform Extensions                             cl_khr_icd cl_amd_event_callback cl_amd_offline_devices 
  Platform Extensions function suffix             AMD
  Platform Host timer resolution                  1ns

  Platform Name                                   AMD Accelerated Parallel Processing
Number of devices                                 1
  Device Name                                     Ellesmere
  Device Vendor                                   Advanced Micro Devices, Inc.
  Device Vendor ID                                0x1002
  Device Version                                  OpenCL 2.0 AMD-APP (3302.5)
```

Congratulations, you can get back to mining again.

UPDATE: As an added extra you can also install the Wattman alternative for overclocking. The software is called Corectl and you can get it on [GitLab](https://gitlab.com/corectrl/corectrl)

First install the [ernstp/ppa-mesarc](https://github.com/ernstp/ppa-mesarc) repo for your system from [HERE](https://launchpad.net/~ernstp/+archive/ubuntu/mesarc) for other distros check the [installation notes](https://gitlab.com/corectrl/corectrl#installation).

Then unlock power controls by adding "amdgpu.ppfeaturemask=0xffffffff" to your grub file

first do

```bash
sudo nano /etc/default/grub
```

We need to add `"amdgpu.ppfeaturemask=0xffffffff"` to the line that starts with `GRUB_CMDLINE_LINUX_DEFAULT` and add it in like below

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quick splash amdgpu.ppfeaturemask=0xffffffff"
```

Update the grub with

```bash
sudo update-grub
```

Reboot

```cwl
sudo reboot
```

We can now install correctl

```bash
sudo apt install corectl
```

Now we can control the overclocking voltages and speeds

Below is a picture of corectl stats, if your GPU stats look like this you should change your thermal paste. As i quickly found out soon after posting this.

![Corectl graphical user interface](/media/corectl.png)

CoreCtl GUI

Here is an image of my GPU stats AFTER applying new thermal paste. 😂

![corectl proper image](/media/corectl-proper.png "corectl main gui")

Corectl main GUI after applying new thermal paste.

Corectl will ask for root password every time on boot, you can disable this by following the instructions on the corectl setup page [HERE](https://gitlab.com/corectrl/corectrl/-/wikis/Setup#dont-ask-for-user-password)