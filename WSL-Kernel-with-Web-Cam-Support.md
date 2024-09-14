# WSL2 Kernel Build with USB Camera Support

This guide will walk you through the process of building a custom WSL2 kernel with USB camera support.

Reference: https://youtu.be/t_YnACEPmrM?feature=shared

## Prerequisites

- Windows Subsystem for Linux (WSL2) installed
- Administrator access on your Windows machine

## Steps

### 1. Determine Kernel Version

In WSL, run:

```bash
uname -a
```

Note the kernel version and set it as a variable:

```bash
VERSION=5.15.153.1  # Replace with your actual kernel version
```

### 2. Install Dependencies

Update your system and install necessary packages:

```bash
sudo apt update 
sudo apt upgrade -y
sudo apt install -y build-essential flex bison libgtk2.0-dev libelf-dev \
    libncurses-dev autoconf libudev-dev libtool zip unzip v4l-utils \
    libssl-dev python3-pip cmake git iputils-ping net-tools dwarves bc
```

### 3. Clone WSL2 Kernel Source

```bash
sudo mkdir /usr/src
cd /usr/src
sudo git clone -b linux-msft-wsl-${VERSION} \
    https://github.com/microsoft/WSL2-Linux-Kernel.git \
    ${VERSION}-microsoft-standard
cd ${VERSION}-microsoft-standard
```

### 4. Prepare Kernel Configuration

```bash
sudo cp /proc/config.gz config.gz
sudo gunzip config.gz
sudo mv config .config
```

### 5. Configure Kernel

Run the menuconfig tool:

```bash
sudo make menuconfig
```

In the manual configuration, enable the following options (hit the spacebar twice to mark option with `*`, i.e. kernel built-in):

- Device Drivers -> Multimedia support -> Filter media drivers
- Device Drivers -> Multimedia support -> Media device types -> Cameras and video grabbers
- Device Drivers -> Multimedia support -> Video4Linux options -> V4L2 sub-device userspace API
- Device Drivers -> Multimedia support -> Media drivers -> Media USB Adapters -> USB Video Class (UVC)
- Device Drivers -> Multimedia support -> Media drivers -> Media USB Adapters -> UVC input events device support
- Device Drivers -> Multimedia support -> Media drivers -> Media USB Adapters -> GSPCA based webcams

### 6. Build and Install Kernel

```bash
sudo make -j$(nproc)
sudo make modules_install -j$(nproc)
sudo make install -j$(nproc)
```

### 7. Copy Kernel to Windows

Create a `Sources` folder under `C:` in Windows, then:

```bash
sudo cp -rf vmlinux /mnt/c/Sources/
```

### 8. Configure WSL to Use New Kernel

In your Windows user folder, create a file named `.wslconfig` with the following content:

```
[wsl2]
kernel=C:\\Sources\\vmlinux
```

### 9. Testing

1. In PowerShell (Admin), use `usbipd` to bind and attach the webcam and the Coral TPU to WSL.
2. In WSL, install and run `guvcview`:

```bash
sudo apt install v4l-utils guvcview
sudo guvcview
```

### 10. Try Google Coral Example

```bash
cd ~
mkdir coral && cd coral
git clone https://github.com/google-coral/examples-camera.git --depth 1
cd examples-camera
sh download_models.sh
cd gstreamer
bash install_requirements.sh
python3.9 detect.py
```

## Troubleshooting

If you encounter any issues, please check the WSL documentation or open an issue in this repository.

## Contributing

Contributions to improve this guide are welcome. Please submit a pull request or open an issue to discuss proposed changes.

## License

[Specify your license here]
