# OP2-in-Termux
This guide aim to walk you through the method that will get [OpenBullet2](https://github.com/openbullet/OpenBullet2) in Termux or your phone. ("OpenBullet2 will be refered to as 'OP2'")

## Install proot-distro for Debian

```bash
pkg update && pkg upgrade
termux-setup-storage #just in case
pkg install proot-distro
#We will be using debian
proot-distro install debian
```

## Get Debian ready for OP2

```
proot-distro login debian
apt update && apt upgrade
apt install git wget ca-certificates psmisc
wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
# If wget fails, get new link from
# https://learn.microsoft.com/en-us/dotnet/core/install/linux-scripted-manual#scripted-install
chmod +x ./dotnet-install.sh
# Arm64 is common for most android devices, change that part if your phone CPU isn't arm64
./dotnet-install.sh --channel 8.0 --install-dir $HOME/.dotnet --architecture arm64 --runtime aspnetcore
echo 'export DOTNET_ROOT=$HOME/.dotnet' >> ~/.bashrc
echo 'export PATH=$DOTNET_ROOT:$PATH' >> ~/.bashrc
source ~/.bashrc
```
## Verify .NET

```
dotnet --info
```
You should see version and make sure AspNetCore runtime is listed. OP2 need this, and if you don't see it, you'll have to install it menually

## Get OP2 Updater to Install OP2
Visit https://github.com/openbullet/OpenBullet2/releases/ and get the **ob2-web-updater-linux-arm64**, the name might be diferrant but will have linux and arm64 in it.
```
mkdir OP2 && cd OP2
wget https://github.com/openbullet/OpenBullet2/releases/download/0.3.2/ob2-web-updater-linux-arm64
# If download fails with SSL error, menually download and
# youll find menually downloaded file in /sdcard/Download/ob2-web-updater-linux-arm64
chmod +x ob2-web-updater-linux-arm64
./ob2-web-updater-linux-arm64
```
Now select Latest Release, or you could select for Nighty version, follow on-screen instruction for the final install.

## Running OP2
```
nano run.sh
```
add these in the nano window
```
#!/bin/bash
export DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=1
export DOTNET_GCHeapHardLimit=0x40000000
fuser -k 8070/tcp
MY_IP=$(hostname -I | awk '{print $1}')
if [[ -z "$MY_IP" ]]; then
    MY_IP="localhost"
fi
dotnet ~/OP2/OpenBullet2.Web.dll --urls "http://$MY_IP:8070"
```
To save it, Ctrl + X => (just press) Y => Press Enter from keyboard
```
chmod +x run.sh
```

Now ran with 
```
./run.sh
```

If youre connected to WiFi, it will be *"YourDeviceIP":8070*. You'll be able to access web interface from any device connected to that same WiFi with that IP and port.
If connected with Mobile Data, It will fail, just do it menually and run
```
export DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=1
export DOTNET_GCHeapHardLimit=0x40000000
dotnet ~/OP2/OpenBullet2.Web.dll --urls "http://localhost:8070"
```
If not connected in any network, It will be available in localhost:8070
