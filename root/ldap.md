```
Change password of arbitrary user:
#!/bin/bash
# changeldappasswd.sh
# wrapper for ldappasswd
# author: Robert Quitt <robertquitt@berkeley.edu>
# 9/4/18

args=("$@")
if [ $# -ne 1 ]; then
        echo usage: $0 \<user\>
        exit 1
fi
TARGETUSER=$1
BASEDN="ou=People,dc=csua,dc=berkeley,dc=edu"

ldappasswd -D "uid=$USER,$BASEDN" -W -x -S "uid=$TARGETUSER,$BASEDN"

Make sure you're on root staff! This is normally located in /csua/changeldappasswd.sh

To see the olcRootDN and olcRootPW:
sudo ldapsearch -H ldapi:// -LLL -Q -Y EXTERNAL -b "cn=config" "(olcRootDN=*)" dn olcRootDN olcRootPW

```
