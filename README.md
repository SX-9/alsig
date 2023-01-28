# Arch Linux Simple Install Guide (alsig)

This Is A Simple Guide For Installing Arch Linux
If You Dont Care Of The Details In The Wiki
This Is For You!



## Step1: Verify If In EFI Mode

Type: `ls /sys/firmware/efi/efivars`

If This Returns A Big List Continue 
But If It Doesnt Head Into Your BIOS 
And Enable It There.



## Step2: Connecting To Internet

Now If You Use Ethernet Continue. 
If You Want Wifi Follow The Steps.

First We Need To Unblock Our Wifi Card 
So Type The Following: 
```
rfkill unblock wlan; 
ip link set wlan0 up; 
```
Now We Connect To Wifi. 
Type The Following:
```
iwctl station wlan0 scan; 
iwctl station wlan0 get-networks; 
```
Now Find Your Wifi Name In There. 
If Your Wifi Is Not Shown Rerun It. 

Now Type: `iwctl station wlan0 connect YOUR_WIFI`

Replace `YOUR_WIFI` With Your *Wifi Name*

Confirm Your Connected By Typing: `ping google.com -c 3`
If it Fails Try Unblocking Your Wifi Card. 



## Step3: Setup The Disk

Type: `lsblk`

This List All Disks In Your PC *(And Flash Disks)*.
Find The Disk Where Your Gonna Install Arch Linux.

*WARNING: WE WILL BE DELETING ALL DATA ON THE DISK MAKE 
SURE THERES NOTHING ON THE DISK THATS IMPORTANT TO YOU*

For The Rest Of The Guide Replace `sda` With Your Disk You Selected.
Keep The Number At The End Cause Its The Partition Number.

Now Type: `fdisk /dev/sda`

This Enters You In An Interactive Prompt.
Now Do The Following Keystrokes *(New Lines Or (,) = Enter)*:
```
g, 
n, Enter, Enter, +500M, 
1, 1, 
n, Enter, Enter, Enter, 
t, Enter, 23, 
w, 
```
Now That Were Done Partitioning Type The Following:
```
mkfs.fat -F 32 /dev/sda1; 
mkfs.ext4 /dev/sda2; 
```
This Formats The Partitions.
Now Type:
```
mount /dev/sda2 /mnt; 
mount /dev/sda1 /mnt/boot --mkdir; 
```
Now That We Mounted Our Partitions Continue.



## Step4: Installing Arch Linux

This Is Where We Actually Install Our System.
Type The Following: 
```
sudo pacman -Syy archlinux-keyring; 
sudo pacstrap /mnt base base-devel linux linux-firmware nano; 
```
Now This May Take A While.
If Its Done Continue.



## Step5: Configure Things

After Its Done Installing Type:
```
genfstab -U /mnt >> /mnt/etc/fstab
```
This Will Generate The File System Table.
Now We Chroot By Typing:
```
arch-chroot /mnt
```
This Chroots Us Into Our Arch Linux We Installed.
Now We Sync Our System Clock By Typing:
```
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```
Note: Replace `Region/City` With Your Region And City.
Now Type:
```
hwclock --systohc
```
Now That We Have Our Clock Synced Were Gonna Configure Our Locale.
So Type:
```
nano /etc/locale.gen
```
This Will Edit That File
Find Your Locale By Pressing `Ctrl + W` And Searching Your Locale, 
In Most Cases Its Just `en_US.UTF-8` So Uncomment That 
And Your Locale By Removing The `#` At The Start Of The Line.
Now Save And Exit By Doing:
```
Ctrl + X 
Y 
Enter 
```
Now We Configure Our System With Our Locale.
So Type:
```
locale-gen; 
echo "LANG=en_US.UTF-8" >> /etc/locale.conf; 
```
Now We Give Our System A Name *(Aka Hostname)*.
So Type:
```
echo "localhost" >> /etc/hostname
```
Replace `hostname` With Whatever You Want To Name Your System.
After That Continue.



## Step6: Adding Users

Now Type: `passwd`

This Sets A Password For Our Root User *(Aka Admin User)*.
Now We Add Your User So Type:
```
useradd -mG wheel username; 
passwd username; 
```
Replace username With The Name You Want *(NO SPACES)*.
Now We Need Root Permissions So Type:
```
EDITOR=nano visudo
```
Now Find The First Line That Starts With `# %wheel` .
Uncomment It By Removing The `#`.
Save And Exit By Doing:
```
Ctrl + X 
Y 
Enter 
```
Now Continue.



## Step7: Ucode And Drivers

To Install Ucode And Drivers Type:
```
pacman -Syy CPU-ucode GPU
```
Replace `CPU` With `intel` Or `amd` Or *Whatever Your System Uses*.
Replace `GPU` With `mesa` Or `nvidia` Or *Whatever Your System Uses*.



## Step8: Install Bootloader 

For This Step Just Type The Following:
```
pacman -S grub efibootmgr; 
grub-install --efi-directory=/boot --bootloader-id=ArchLinux; 
grub-mkconfig -o /boot/grub/grub.cfg; 
```


## Step9 *(Optional)*: Recommended Software

I Recommend You Install the Following Packages Cause You May Need Them:
```
pacman -S pulseaudio reflector leafpad thunar falkon xfce4-terminal neofetch openbox xorg-drivers xorg xorg-xinit dhcpcd iwd viewnior vlc tint2
```


## Step10: Finishing And Cleaning Up

You May Still Be In The Chroot Envoirment So Type The Following:
```
exit; 
umount -a; 
reboot; 
```



Now You Have Arch Linux Installed, *Have Fun :)*
