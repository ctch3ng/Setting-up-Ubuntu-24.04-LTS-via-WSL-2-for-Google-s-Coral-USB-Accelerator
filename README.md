# Setting-up-Ubuntu-24.04-LTS-via-WSL-2-for-Google-s-Coral-USB-Accelerator
This guide provides step-by-step instructions to set up the Google Coral USB Accelerator on Windows 11 with WSL2 using Ubuntu 24.04 LTS. Due to some outdated documentation on the official website, this guide includes the necessary workarounds to ensure proper installation.

Table of Contents
- [Introduction](#item-1)
- [Prerequisites](#item-2)
- [Step-by-Step Guide](item-3)
  - Install Python3.9 and setup a virtual environment
  - Install Required Python Packages
  - Install usbipd-win on Windows
  - Install usbipd on WSL
  - List USB Devices in Windows
  - Bind and Share the USB Device
  - Attach USB Device to WSL
  - Verify Device Attachment in WSL
  - Make the Coral TPU Unavailable from Host PC (If Required)
- Troubleshooting Tips
- References
- Contributors

<a id="item-1"></a>
## Introduction

The Coral USB Accelerator by Google is a USB device that provides an Edge TPU, a small ASIC designed to accelerate machine learning inference at the edge. However, setting up the Coral USB Accelerator on WSL2 can present challenges due to outdated documentation and Python version issues.

This guide aims to help you successfully set up the Coral USB Accelerator on a system running Windows 11 with WSL2 and Ubuntu 24.04 LTS.

<a id="item-2"></a>
## Prerequisites
- Windows 11 with WSL2 enabled
- Ubuntu 24.04 LTS installed on WSL2
- Coral USB Accelerator
- Admin access to the Windows machine

- <a id="item-3"></a>
## Step-by-Step Guide

- <a id="item-3-1"></a>
### Install Python3.9 and setup a virtual environment

See https://github.com/ctch3ng/Installing-Different-Versions-of-Python-and-Managing-Virtual-Environments for details
