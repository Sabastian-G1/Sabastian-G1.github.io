#Installation guide

1- Downloaded my iso from rit.edu, the archlinux-2026.10.01-x86_64.iso version to be specific

2-used the typical VMWare set-up, then pressed install the OS later, used the otherLinux6.k version, named the VM Arch Linux (straight forward so far)

3-Set the max disk size to 30GB, then finished the set-up, went into it's settings and changed the hardware, 4096 MB memory, 2 processors, 30 Gb hard disk, CD: clicked browse then my iso, connect at power on. Now doing firmware

4-Same window, went to options, advanced, under firmware type I selected UEFI, then OK

5-Powered on the VM, selected the medium without speech, then typed in ls /sys/firmware/efi/efivars to confirm UEFI mode, checked the internet with ping -c 3 archlinux.org, worked all well. (little nervous ngl)

6-ran the lsblk command to find the disk name, then did fdisk /dev/sda to start the fdisk
	typed g to make a new partition table
	n, enter, enter +1G to make partition 1
		t, then 1 to make it into a EFI system
	n, enter, enter, +4G to make partition 2
		t, then 2 then 19 to make partition 2 to a Linux swap
	n, then enter, enter, enter to make partition 3, root
		t then 3 then 23 to make partition 3 Linux root(x86-64)
	p to check the table, all good, then w to write the changes and exit fdisk
	
7- I used these commands to format the partitions, mkfs.fat -F 32 /dev/sda1 | mkswap /dev/sda2 | mkfs.ext4 /dev/sda3  it made 1 a Fat32, 2 a swap space and 3 a ext4 root

8- I then mounted the disks with the commands mount /dev/sda3 /mnt | mount --mkdir /dev/sda1 /mnt/boot |swapon /dev/sda2 | I then check that it worked with the command lsblk and it all worked perfectly after a second attempt (thank god)

9- I started to install the base system and I started that by installing packages using this long command - pacstrap -K /mnt base linux linux-firmware base-devel sudo nano networkmanager grub efibootmgr git, failed bc I spelt network wrong so I took a snapshot bc I got scared, trying again, worked the second time

10- Generated an fstab to tell the system what partitions to mount on boot - genfstab -U /mnt >> /mnt/etc/fstab

11- I entered the new system which was chroot so future commands change ethe system not the installer - arch-chroot /mnt
	set the time zone with the cmds-ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime | hwclock --systohc
	opened the language list in nano, set to English (surprisingly complicated)
	set the hostname - echo archvm > /etc/hostname
	turned on networking - systemctl enable NetworkManager
	set the root password -passwd
	Installed the bootloader - grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB --removable and the grub-mkconfig -o /boot/grub/grub.cfg command
	
12- I then exited unmounted the disk and shut down the vm, took another snapshot in case I mess something up

13 - I then detached the iso from the VM by going into the settings, CD/DVD and unselecting the connect at power on

14- Logged into the VM with the root login and checked the internet connection, then checked for updates using the  pacman -Syu command, there were none.

15- I turned on SUDO for the wheel group through the EDITOR=nano visudo command and searched for wheel

16- I added the users Sabastian & Codi and set up their very complicated passwords - useradd -m -G wheel -s /bin/bash Sabastian (second time's the charm ig)

17- To double check I did everything right I logged into the Sabastian user and ran the command sudo whoami and received the response "root"

18-Logged out of the root account and logged in under Sabastian to do the following commands
	To install the display server, mouse and keyboard support, LXQt desktop, login screen, icon set and font I ran the sudo pacman -S --needed xorg-server xf86-input-libinput lxqt sddm breeze-icons ttf-dejavu command 	(took a 	minute to complete)
	I then installed zsh with the command sudo pacman -S zsh zsh-completions
	I then stopped zsh from running its setup menu by using the touch ~/.zshrc command
	The next 3 commands made zsh the login shell for codi and Sabastian sudo -u codi touch /home/codi/.zshrc ||
	chsh -s /usr/bin/zsh || sudo chsh -s /usr/bin/zsh codi
	
19- Still under the Sabastian user I installed and started SSH with these commands sudo pacman -S openssh || sudo systemctl enable --now sshd || systemctl status sshd
	I then ssh'ed into the Sabastian user from my windows powershell just to double check everything works.
	
20- I opened my zsh config to add some terminal colors, I made it so the first line was a green user and host name and a blue folder name, I also added color to pacman just for funsies.

21- I then set the system to boot into the GUI, ran these two commands to start SDDM login on every boot and make the graphical target the default - sudo systemctl enable sddm || sudo systemctl set-default graphical.target

22-I added aliases for commands to save some time bc im lazy (bit me in the butt later)
	alias ll='ls -lah'
	alias ..='cd ..'
	alias update='sudo pacman -Syu'
	alias myip='ip -brief address'
	alias ports='ss -tulpn'
	alias c='clear'
	
23- I then rebooted into the desktop on my second try

24- I added the Qterminal to my application bar at the bottom to save some time, I then ran the command sudo pacman -S firefox, then proceeded to waste 30 minutes watching Minecraft on youtube to celebrate

25-I used the following commands to install an AUR helper just to help
	cd ~
	git clone https://aur.archlinux.org/yay-bin.git
	cd yay-bin
	makepkg -si
	
26-Using that helper I installed VS code using the following command yay -S visual-studio-code-bin


Problems I ran into
	Adding a space after the = in all of the alias prompts, took me 10 minutes and a google search to figure that out
	I ran the mount commands in reverse order, had to google why the mount point didn't exist(mount /dev/sda1 /mnt/boot before making the directory)
	Ran into an issue with the Sabastian user not being in the sudoers file, forgot the -G wheel in the useradd -m -G wheel -s /bin/bash Sabastian command
	I had a black screen after my reboot, put a 1 in the sudo systemctl enable sddm command (sudo systemct1 enable sddm), just being stupid and not reading thoroughly

Overall, it was a lot of reading and many tabs being opened at once but it was really educational and a fun experience, took about as long as I thought it would and I had shockingly less errors than I thought I would have, probably due to the amount of reading I did and double checking what I had read was actually correct and would work.	
	
	
