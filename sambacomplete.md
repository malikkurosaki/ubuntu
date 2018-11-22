//from https://www.linuxbabe.com/ubuntu/install-samba-server-ubuntu-16-04
```pyton
How to Install and Configure Samba Server on Ubuntu 16.04 for File Sharing

sudo apt install samba samba-common-bin 
//or
sudo apt install samba

//samba status
sudo smbstatus

//check version
smbd --version

//cara cek status yang lainnya smnd dan nmbd
systemctl status smbd
systemctl status nmbd

//untuk start samba server
sudo systemctl start smbd
sudo systemctl start nmbd

//keterangan
Once started, smbd will be listening on TCP port 139 and 445. nmbd will be listening on UDP port 137 and 138.
TCP 139: used for file and printer sharing and other operations.
TCP 445: the NetBIOS-less CIFS port.
UDP 137: used for NetBIOS network browsing.
UDP 138: used for NetBIOS name service.

//untuk memangil config
/etc/samba/smb.conf
//contoh 
sudo nano /etc/samba/smb.conf

//untuk nama workgroup
workgroup = WORKGROUP

//membuat config

[Private]

comment = needs username and password to access
path = /srv/private/
browseable = yes
guest ok = no
writable = yes
valid users = @samba

//untuk menambah username
sudo adduser username
sudo smbpasswd -a username

//membuat group samba
sudo groupadd samba

//menambahkan user ke samba group
sudo gpasswd -a username samba

//membuat private folder
sudo mkdir /srv/private/

//memberikan ijin permisi pada folder
sudo setfacl -R -m "g:samba:rwx" /srv/private/
//alternatif
sudo chmod -R 777 /svr/private/

//untuk mengetes
testparm

//untuk merestart server samba
sudo systemctl restart smbd nmbd

//membuat public
[public]

comment = public share, no need to enter username and password
path = /srv/public/
browseable = yes
writable = yes
guest ok = yes

//mengakses samba melalui isp
smb://192.168.1.13/publick
// keteranga 192.168.1.113 adalah ip dari komputer caranya ifconfig pada cli

//untuk memastikan akses publik no body
sudo setfacl -R -m "u:nobody:rwx" /srv/public/

//restart server
sudo systemctl restart smbd nmbd

//jika muncul peringatan seperti ini 
//You do not have permission to access \\hostname\share-name. Contact your network administrator to request access.

net use \\hostname\share-name /delete
net use \\hostname\share-name /user:samba-username password


