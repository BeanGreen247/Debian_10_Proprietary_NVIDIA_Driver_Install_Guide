# Install proprietary NVIDIA driver on Debian 10
## Shoutout
Go follow these legends, they helped making this guide what it is today

[ichirodev](https://github.com/ichirodev)

[HangedScratcher](https://github.com/HangedScratcher)

## The prep
edit your **/etc/apt/sources.list** file by appending "contrib non-free" at the end of the lines beginning with deb.
```
deb  http://deb.debian.org/debian stretch main contrib non-free
deb-src  http://deb.debian.org/debian stretch main

deb  http://deb.debian.org/debian stretch-updates main contrib non-free
deb-src  http://deb.debian.org/debian stretch-updates main

deb http://security.debian.org/ stretch/updates main contrib non-free
deb-src http://security.debian.org/ stretch/updates main
```
Then run 
```
sudo apt update
```
## The steps (part 1)
Set root password
```
sudo -i
passwd
```
It will ask you to type a new sudo password.

Use root account
```
su
```
OR
```
sudo -i
```
First install nvidia-detect
```
sudo apt -y install nvidia-detect
```
Next use nvidia-detect to find your gpu
```
nvidia-detect
```
First install these packages and build linux depemdecies.
```
sudo apt-get autoclean
sudo apt-get clean
sudo apt-get update 
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo apt-get -f install
sudo dpkg --configure -a
sudo apt install firmware-linux build-essential gcc-multilib linux-headers-amd64 dracut
sudo apt build-dep linux
```

**NOTE: If it says that it will remove initramfs-tools its fine let it remove it**

Download driver from https://www.nvidia.com/object/unix.html
Those who need the legacy driver look for Latest Legacy GPU version.

Disable Nouveau and other drivers that might have conflicts with the installation.
```
echo 'blacklist amd76x_edac' | sudo tee -a /etc/modprobe.d/blacklist.conf
echo 'blacklist vga16fb' | sudo tee -a /etc/modprobe.d/blacklist.conf
echo 'blacklist rivafb' | sudo tee -a /etc/modprobe.d/blacklist.conf
echo 'blacklist rivatv' | sudo tee -a /etc/modprobe.d/blacklist.conf
echo 'blacklist nvidiafb' | sudo tee -a /etc/modprobe.d/blacklist.conf
echo 'blacklist nouveau' | sudo tee -a /etc/modprobe.d/blacklist.conf

sudo dracut -v /boot/initramfs-$(uname -r).img $(uname -r)
```
Next disable display manager. To find out witch you are using type
```
sudo cat /etc/X11/default-display-manager
```
if using lightdm
```
sudo systemctl stop lightdm
```
if using gdm3
```
sudo systemctl stop gdm3
```
If you see a black screen just press **CTRL+ALT+F1** and type
```
sudo apt install initramfs-tools
sudo systemctl set-default multi-user.target
```

**NOTE: If it says that it will remove dracut its fine let it remove it**

Use this command next
```
sudo update-initramfs -u
```
if it fails run these commands
```
sudo apt-get autoclean
sudo apt-get clean
sudo apt-get update 
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo apt-get -f install
sudo dpkg --configure -a
sudo update-initramfs -u
```
Next reboot
```
sudo systemctl reboot
```
## The steps (part 2)
Log in as root with the password we have set up earlier.

Next go to Downloads forlder
```
cd Downloads
```
Run this commands
```
sudo apt install linux-headers`uname -r` 
```
Now, add some libraries to your system 
```
sudo apt install build-essential
sudo apt install libglvnd-dev pkg-config
```
* This fixes Error: Unable to determine the path to install libglvnd

Lastly run the installer
```
sudo bash NVIDIA-....run
```
I would recommend to enable 32-bit support if prompted.

Next select Install and overwrite existing filesort installation.

It should ask you to generate X-org configuration. Accept that too.

If not, run nvidia-xconfig after the installer finishes.

Reenable GUI
```
sudo systemctl set-default graphical.target
``` 
Then reboot.
```
sudo reboot
```
And that's it. You should have succesfully installed the proprietary NVIDIA driver.
