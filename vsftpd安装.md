<center>CentOS7安装vsftpd</center>

1. 关闭防火墙<br/>
systemctl stop fireware

2. 零时关闭selLinux，或者放行ftp<br/>
setenforce 0

3. 创建ftp本地用户<br/>
useradd -d /data/ftp -s /sbin/nologin ftper

4. 创建密码<br/>
passwd ftper

5. 配置属组和权限<br/>
chown -R ftper:ftper /data/ftp<br/>
chmod -R 755 /data/ftp

6. 使用yum安装vsftpd<br/>
yum -y install vsftpd

7. 修改/etc/vsftpd/vsftpd.conf内容如下：<br/>
listen=YES<br/>
listen_port=自定义端口<br/>
listen_ipv6=NO<br/>
anonymous_enable=NO<br/>
local_enable=YES<br/>
write_enable=YES<br/>
local_umask=022<br/>
dirmessage_enable=YES<br/>
xferlog_enable=YES<br/>
chown_uploads=NO<br/>
xferlog_file=/var/log/xferlog<br/>
xferlog_std_format=YES<br/>
ascii_upload_enable=YES<br/>
ascii_download_enable=YES<br/>
chroot_local_user=YES<br/>
chroot_list_enable=NO<br/>
allow_writeable_chroot=YES<br/>
local_root=/data/ftp<br/>
pam_service_name=vsftpd<br/>
userlist_enable=YES<br/>
userlist_deny=NO<br/>
userlist_file=/etc/vsftpd/user_list<br/>
tcp_wrappers=YES<br/>
xferlog_file=/var/log/xferlog<br/>
dual_log_enable=YES<br/>
vsftpd_log_file=/var/log/vsftpd.log<br/>
connect_from_port_20=NO<br/>
pasv_enable=YES<br/>
pasv_min_port=最小端口<br/>
pasv_max_port=最大端口<br/>
pasv_address=真实IP<br/>
pasv_addr_resolve=YES<br/>
pasv_promiscuous=YES<br/>
reverse_lookup_enable=NO<br/>

8. 修改/etc/vsftpd/user_list文件，加入创建的ftp用户<br/>
echo "ftper" >> /etc/vsftpd/user_list

9. 修改/etc/vsftpd/user_list中的pam_shells.so为pam_nologin.so，示例如下：<br/>
session    optional     pam_keyinit.so    force revoke<br/>
auth       required	pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed<br/>
auth       required	pam_nologin.so<br/>
auth       include	password-auth<br/>
account    include	password-auth<br/>
session    required     pam_loginuid.so<br/>
session    include	password-auth<br/>