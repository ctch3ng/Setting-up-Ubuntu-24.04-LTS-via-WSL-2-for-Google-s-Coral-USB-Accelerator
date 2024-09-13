# Setting-up-Ubuntu-24.04-LTS-via-WSL-2-for-Google-s-Coral-USB-Accelerator
This guide provides step-by-step instructions to set up the Google Coral USB Accelerator on Windows 11 with WSL2 using Ubuntu 24.04 LTS. Due to some outdated documentation on the official website, this guide includes the necessary workarounds to ensure proper installation.

Table of Contents
- [Introduction](#item-1)
- [Prerequisites](#item-2)
- [Step-by-Step Guide](#item-3)
  - [Install Python3.9, set up and activate a virtual environment](#item-3-1)
  - [Install Required Python Packages](#item-3-2)
  - [Install usbipd-win on Windows](#item-3-3)
  - [Install usbipd on WSL](#item-3-4)
  - [List USB Devices in Windows](#item-3-5)
  - [Bind and Attach the USB Device](#item-3-6)
  - [Verify Device Attachment in WSL](#item-3-6)
- [Run a model on the Edge TPU](#item-4)
- [Troubleshooting](#item-5)

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

<a id="item-3"></a>
## Step-by-Step Guide

<a id="item-3-1"></a>
### Install Python3.9 and set up a virtual environment

See https://github.com/ctch3ng/Installing-Different-Versions-of-Python-and-Managing-Virtual-Environments for details.

<a id="item-3-2"></a>
### Install Required Python Packages

[WSL] Update 'apt-get' with Coral's REPO

```
echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | sudo tee /etc/apt/sources.list.d/coral-edgetpu.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt-get update
```

[WSL] Install the Edge TPU runtime.

```
sudo apt-get install libedgetpu1-std
```

Plug-in your Coral USB Accelerator. Reboot your WSL by hitting `Windows Key + R` to open Windows' `Run` command and run the following

```
wsl.exe --shutdown
```

[WSL] Install the `pycoral` library using the wheels, along with Pillow, and downgrade numpy to a compatible version:

```
python3.9 -m pip install https://github.com/google-coral/pycoral/releases/download/v2.0.0/tflite_runtime-2.5.0.post1-cp39-cp39-linux_x86_64.whl
python3.9 -m pip install https://github.com/google-coral/pycoral/releases/download/v2.0.0/pycoral-2.0.0-cp39-cp39-linux_x86_64.whl
python3.9 -m pip install Pillow
python3.9 -m pip install "numpy<2"
```

Note: The above code is for Python3.9. You can find other versions here https://coral.ai/software/#debian-packages .

<a id="item-3-3"></a>
### Install usbipd-win on Windows

Go to the usbipd-win release page https://github.com/dorssel/usbipd-win/releases/tag/v4.3.0 .
Download the .msi installer file and run it to install.

Note: For details, visit https://learn.microsoft.com/en-us/windows/wsl/connect-usb .

<a id="item-3-4"></a>
### Install usbipd on WSL

[WSL] Open a WSL terminal and run:
```
sudo apt-get update
sudo apt-get install usbutils
```

<a id="item-3-5"></a>
### List USB Devices in Windows

[Windows] In Windows, open PowerShell as `Administrator` and run:

```
usbipd list
```

Note the BUSID of the Coral USB Accelerator. The following codes assume the BUSID is 1-4

<a id="item-3-6"></a>
### Bind and Attach the USB Device

[Windows] In the Windows PowerShell, run the following command to bind and attach the device to the default WSL VM:

```
usbipd bind --force -b 1-4
usbipd attach --wsl --busid 1-4
```

Note: If your default WSL is something else, you will need to run `wsl --setdefault Ubuntu-24.04` to upadte it first.

<a id="item-3-7"></a>
### Verify Device Attachment in WSL

[WSL] In WSL, run `lsusb` to confirm the device is listed. Look for “Google Inc.” in the list. If showing `Global Unichip Corp.`, see [Troubleshooting](#item-5).

<a id="item-4"></a>
## Run a model on the Edge TPU

[WSL] Now, you can return to https://coral.ai/docs/accelerator/get-started/#3-run-a-model-on-the-edge-tpu to follow the procedures, which are replicated as follow.

```
mkdir coral && cd coral

git clone https://github.com/google-coral/pycoral.git

cd pycoral

bash examples/install_requirements.sh classify_image.py

python3.9 examples/classify_image.py \
--model test_data/mobilenet_v2_1.0_224_inat_bird_quant_edgetpu.tflite \
--labels test_data/inat_bird_labels.txt \
--input test_data/parrot.jpg
```
<a id="item-5"></a>
## Troubleshooting

[Windows] In PowerShell, run the following all at once while WSL is open and `venv` is activated.

```
usbipd unbind -a
usbipd bind --force -b 1-4
usbipd attach --wsl -b 1-4
usbipd list
```
[WSL] Run an example (it will fail, don't worry) [It appears that running this will trigger the device to change its ID]

```
cd coral
cd pycoral
python3.9 examples/classify_image.py \
--model test_data/mobilenet_v2_1.0_224_inat_bird_quant_edgetpu.tflite \
--labels test_data/inat_bird_labels.txt \
--input test_data/parrot.jpg
```
[Windows] Back to PowerShell, run the following all at once (again) while WSL is open and `venv` is activated.

```
usbipd unbind -a
usbipd bind --force -b 1-4
usbipd attach --wsl -b 1-4
usbipd list
```
[Windows] Look for “Google Inc.” in the list. If showing `Global Unichip Corp.`, repeat the steps above.
