---
title: Soda setup
---

Start with a fresh debian image
Run: apt update  
Run: apt install -y tmux neovim nslcd nscd python3 python auditd build-essential ufw  
Run: apt full-upgrade -y  
Run: echo “export EDITOR=vim” >> ~/.bashrc  
Run: visudo
- Uncomment the line starting with "#%sudo"  
- Exit

Run: usermod -aG sudo pnunez  
Run: su - pnunez  
Run: sudo update-alternatives --set vim /usr/bin/nvim  

## Set up PAM/LDAP

Find the pam.d files for common-auth and common-password on services.csua.berkeley.edu  
Make the corresponding pam files on soda look exactly like that  
Edit /etc/nsswitch to have these lines

    passwd: compat ldap
    group: compat ldap
    shadow: compat ldap

Edit /etc/nslcd.conf to look exactly like /etc/nslcd.conf on services  
- Exactly alike, just copy paste

Run: sudo systemctl restart nslcd sshd  
Run: getent passwd  
You should get the username and user info for hundreds of users  
FROM ANOTHER TERMINAL WINDOW Run: ssh pnunez@soda.berkeley.edu  
- Does it work? If not, fix your pam files or nslcd, then restart with systemctl

Run: sudo ufw allow ssh  
Run: sudo ufw limit out ssh  
Run: sudo ufw enable  
