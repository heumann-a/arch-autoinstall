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
    * `ansible-playbook -u $USER -K ansible/playbook_core.yml`
    * `ansible-playbook -u $USER -K ansible/playbook_settings.yml`
    * `ansible-playbook -u $USER -K ansible/playbook_software.yml`

Yes, you write `$USER` there, which puts in the user you are logged in <br> The `-K` is short for `--ask-become-pass` which will prompt for password

**Removal**<br>
After running playbooks it be good to remove ansible and archinstall package and bunch of its dependencies. Saves \~600MB and noise during updates.

* `sudo pacman -Rns ansible archinstall`
