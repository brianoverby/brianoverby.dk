---
title: "Notes"
date: 2019-11-11T12:43:28+01:00
draft: false
---

## Misc
### journalctl -- log files
```
$ journalctl -b
$ journalctl --boot=-1
$ journalctl -u sshd
$ journalctl --list-boots
```

### systemctl -- services
```
$ systemctl list-unit-files --all
$ systemctl list-unit-files --state=enabled
$ sudo systemctl restart <unit>
```

### ssh
kill remote login:
```
$ who
$ pkill -9 -t pts/<tty-num-to-kick-out>
```

### Bash
Tab completion in sudo: `$ echo 'complete -cf sudo' >> ~/.bashrc`

## Nice \*nix tools
* [tmux](https://github.com/tmux/tmux/wiki) -- terminal multiplexer
* [rsnapshot](http://rsnapshot.org/) -- nice simple backup tool
* [NZBGet](https://nzbget.net/) -- Usenet downloader
* [aria2](https://aria2.github.io/) -- lightweight multi-protocol command-line download utility


## Git
[Git survival guide](http://rogerdudler.github.io/git-guide/)  

- new repo: `$ git init` 
- checkout repo: `$ git clone <repo address>` 
- pull updates: `$ git pull` 
- update changes: `$ git add -A && git commit -m "comment"` 
- push changes: `$ git push origin master` 
- add remote server: `$ git  remote add origin <server>` 

## 7-Zip
Create password protected 7z archive from command-line
```
$ 7z a -mx0 -mhe=on -p <archiveName>.7z <folderName>
```
a: Add files to archive  
m0=copy: Storage only, no compression  
mhe=on: Encrypt filenames - no listing of files without password  
p: password :-)  
  
Test archive
```
$ 7z l -slt <archiveName>.7z
```


## tmux
scroll buffer (PuTTY): `CTRL+Space, PgUP/PgDown`

## Jekyll
[Jekyll quick start on Arch Linux](https://jekyllrb.com/docs/)

```
$ sudo pacman -S ruby
$ echo 'export PATH=$PATH:~/.gem/ruby/2.4.0/bin' >> ~/.bashrc
$ gem update
$ gem install jekyll bundler jekyll-paginate
```

## UBNT EdgeRouter 
### Setup L2TP VPN on EdgeRouter

Firewall rules:
```
set firewall name WAN_LOCAL rule 40 action accept
set firewall name WAN_LOCAL rule 40 description IKE
set firewall name WAN_LOCAL rule 40 destination port 500
set firewall name WAN_LOCAL rule 40 log disable
set firewall name WAN_LOCAL rule 40 protocol udp

set firewall name WAN_LOCAL rule 50 action accept
set firewall name WAN_LOCAL rule 50 description ESP
set firewall name WAN_LOCAL rule 50 log disable
set firewall name WAN_LOCAL rule 50 protocol esp

set firewall name WAN_LOCAL rule 60 action accept
set firewall name WAN_LOCAL rule 60 description NAT-T
set firewall name WAN_LOCAL rule 60 destination port 4500
set firewall name WAN_LOCAL rule 60 log disable
set firewall name WAN_LOCAL rule 60 protocol udp

set firewall name WAN_LOCAL rule 70 action accept
set firewall name WAN_LOCAL rule 70 description L2TP
set firewall name WAN_LOCAL rule 70 destination port 1701
set firewall name WAN_LOCAL rule 70 ipsec match-ipsec
set firewall name WAN_LOCAL rule 70 log disable
set firewall name WAN_LOCAL rule 70 protocol udp
```
VPN config:
```
set vpn l2tp remote-access ipsec-settings authentication mode pre-shared-secret
set vpn l2tp remote-access ipsec-settings authentication pre-shared-secret .....

set vpn l2tp remote-access authentication mode local
set vpn l2tp remote-access authentication local-users username usr1 password .....

set vpn l2tp remote-access client-ip-pool start 10.10.0.100
set vpn l2tp remote-access client-ip-pool stop 10.10.0.120

set vpn l2tp remote-access dns-servers server-1 8.8.8.8
set vpn l2tp remote-access dns-servers server-2 8.8.4.4

set vpn l2tp remote-access dhcp-interface eth0

set vpn ipsec ipsec-interfaces interface eth0
```
Remember to commit and save:
```
commit;save;exit
```

### Edit config file
You can edit the config file from the console (ssh) and load it to the running config.
```
~$ vi /config/config.boot
```
Save changes and load the config.
```
~$ configure
~# load
~# compare
~# commit
~# exit
```

### Upgrade firmware from CLI
Check system storage
```
~$ sudo df -h
~$ show system image storage
```
Delete old system image (not the running image)
```
~$ delete system image
```
Download image from ubnt.com
```
~$ add system image https://dl.ubnt.com/firmw...7989.tar
```
Check the new image is ready
```
~$ show system image
```
If all ok, reboot
```
~$ reboot
```
If you need to revert to old image use `set system image default-boot`
