# NCAE2025
This code is pretty much required based on the NCAE specifications,
```
anonymous_enable=NO
local_enable=YES
write_enable=YES
chroot_local_user=YES
allow_writeable_chroot=YES
```
SPECIFIC USERS ALLOWED

userlist_enable=YES
userlist_file=/etc/vsftpd.allowed_users
userlist_deny=NO
If we do this for all the scored users, they wouldn't be able to execute programs on their home machines via FTP.
```
sudo chmod a-w /home/ftpuser
```
The idea is to only allow limited shells to users when using FTP and to prohibit the use of SSH to log into FTP (WHICH COULD BE BAD< WE NEED TO REVIEW THIS).
```
echo "/usr/sbin/nologin" | sudo tee -a /etc/shells
sudo usermod -s /usr/sbin/nologin ftpuser
```
##LOGGING
This sets up logging on FTP and sends all the logs to /var/log/vsftpd.log
```
xferlog_enable=YES
log_ftp_protocol=YES
xferlog_file=/var/log/vsftpd.log
```
JAIL
If we want to limit DoS attacks (which might not even be an issue but we have to ask), we can use fail2ban to limit attempts to access the ftp on failed attempts.
```
sudo apt install fail2ban -y
sudo nano /etc/fail2ban/jail.local
```
WIthin the file, make sure this is present:
```
[vsftpd]
enabled = true
port = ftp
filter = vsftpd
logpath = /var/log/vsftpd.log
maxretry = 3
bantime = 600
```
Once you do everything, restart the fail2ban service:
```
sudo systemctl restart fail2ban
```
