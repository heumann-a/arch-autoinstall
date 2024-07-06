# arch-autoinstall
Config and Scripts to automatically install my Arch Setup

![Logo](https://i.imgur.com/yAyr3S2.png)

## Credits

I copied  Code-Snippets from multiple from multiple Github Authors, which i want to highlight. They helped making this an easy setup for my own config:

- [ChrisTitusTech](https://github.com/ChrisTitusTech/ArchTitus)
- [DoTheEvo](https://github.com/DoTheEvo/ansible-arch)
- [wrzlbrmft](https://github.com/wrzlbrmft/arch-install)


# How to execute

install arch linux ([archinstall](https://github.com/archlinux/archinstall)), log in to a non root account that can sudo

* install ansible and git<br>
  `sudo pacman -S ansible git`
*  clone this repo<br>
  `git clone https://github.com/heumann-a/arch-autoinstall`
* enter the directory<br>
  `cd arch-autoinstall`
* run the playbooks you want
    * `ansible-playbook -u $USER -K ansible/setup_software.yml`
    * `ansible-playbook -u $USER -K ansible/setup_settings.yml`
    * 
Yes, you write `$USER` there, which puts in the user you are logged in <br> The `-K` is short for `--ask-become-pass` which will prompt for password

**Removal**<br>
After running playbooks it be good to remove ansible and archinstall package and bunch of its dependencies. Saves \~600MB and noise during updates.

* `sudo pacman -Rns ansible archinstall`


## Code Snippets after Install


### Add second luks drive to autocrypt
 
- Create keyfile for autoencrypt `dd if=/dev/urandom bs=512 count=8 of=/root/.keyfile`
- Change Permission of keyfile for autoencrypt `chmod 700 /root/.keyfile`
- Encrypt second drive `cryptsetup luksFormat <device>` 
  - Set long password and store it safely
  - Open Drive `cryptsetup open <device> <mapper-name>`
  - Add the keyfile `cryptsetup luksAddKey <device> [/path/to/additionalkeyfile]`

- Get UUID for crypttab with `blkid -f`
  - Set drive auto-encrypt in `/etc/crypttab`
    - `<mapper-name>      UUID=<UUID>   /root/.keyfile   luks,discard`
- Reboot
- Create BTRFS partition: `mkfs.btrfs -L data /dev/mapper/<mapper-name>`
- Add device to `etc/fstab`:
  - `UUID=<UUID>    /data   brfs  rw,noatime,compress=zlib:3,ssd,space_cache=v2 0 2`
- Reboot

### Enroll fingerprint

```bash
lsusb # Check if device is supported
sudo pacman -S fprintd # install fingerprint daemon
fprintd-enroll -f #{left,right}-{thumb,{index,middle,ring,little}-finger} # install specific finger
fprintd-verify 
```
 
#### Activate Fingerprint for authentication

Add user to allow no password login

```bash
groupadd -r nopasswdlogin
gpasswd -a username nopasswdlogin
```


Modify following files and add the two lines

- `/etc/pam.d/kde` for KDE plasma LockScreen
- `/etc/pam.d/sudo` for SUDO (press Enter instead of typing PW in)

```bash
auth     sufficient 		        pam_unix.so try_first_pass likeauth nullok
auth     sufficient 		        pam_fprintd.so
```

- `/etc/pam.d/sddm` for LoginScreen Manager
```bash
auth 	   [success=1 new_authtok_reqd=1 default=ignore]  	pam_unix.so try_first_pass likeauth nullok
auth 	   sufficient  	pam_fprintd.so
```
 
 - `/etc/pam.d/kde` for KDE Lockscreen
```bash
auth      sufficient  	pam_unix.so try_first_pass likeauth nullok
auth      sufficient  	pam_fprintd.so
```


### Add D-Bus Rights to Spotify Flatpak 

-  Open Flatseal and navigate to Spotify
-  Under **System Bus > Own** add
   - `org.mpris.MediaPlayer2.spotify`
 - Restart Spotify

```bash

```

### Install AUR Package Manager aura

```bash
git clone https://aur.archlinux.org/aura-bin.git
cd aura-bin
makepkg
sudo pacman -U <the-package-file-that-makepkg-produces>

```

### Install Snapper

Replace filepath of /.snapshots if its at another location.
Replace XIDX with the actual ID of the newly created subvolume of snapper.

```bash 
sudo umount /.snapshots
sudo rm -rf /.snapshots
snapper -c config create-config /
sudo btrfs subvolume delete -i XIDX /
sudo mkdir /.snapshots
sudo mount /.snapshots 
sudo systemctl snapper-timeline.timer
sudo systemctl snapper-cleanup.time
```
Copy config 




```bash

```


```bash

```
