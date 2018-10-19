Goal: to find weak user passwords, secure the accounts, and enforce
strict password requirements for users. This is to secure soda so that
we can bring it back up.

Actions Taken
=============

Breaking User Passwords
-----------------------

-   Used ldapvi to write out entire ldap database to a text file, made a
    quick script in Python to parse into a john-compatible format

    -   sudo ldapvi -D "(uid=robertq)"

    -   :w /tmp/ldap.txt

    -   sudo chown robertq:pnunez /tmp/ldap.txt

    -   cat ldap.txt \| python3 getpwfromldap.py \> passwords.txt

-   Build/install john that supports breaking SSHA encryption
    ---------------------------------------------------------

    -   [[http://openwall.info/wiki/john/tutorials/Ubuntu-build-howto]{.underline}](http://openwall.info/wiki/john/tutorials/Ubuntu-build-howto)

-   Get cracking!
    -------------

    -   Moved password.txt to compute server

    -   Ran john \--fork=8 passwords.txt

    -   Lots of baaaaaad passwords

    -   Some blank passwords even

    -   Wow, we really need to enforce security on our passwords\....

    -   Stats so far:

        -   5024 total users

        -   1981 with "Salted SHA1" encryption

            -   After about 30 minutes of running, john cracked 325 of
                them.

            -   Many were in the first 10 seconds!!! Bad!

    -   Had to reformat to work with the crypt passwords

    -   cat passwords.txt \| grep -i {crypt} \| awk
        \'{split(\$0,a,\":\"); print a\[1\]\":\"substr(a\[2\],8)}\' \>
        crypt\_passwords.txt

    -   format=descrypt

        -   421/2957 broken

    -   format=md5crypt

        -   6/424 broken

    -   format=salted-sha1

        -   327/1981 broken

    -   Use \--format flag to crack md5 passwords too

-   Lockdown the accounts
    ---------------------

    -   passwd \--lock \<user\>\\

    -   Used this for going from user:password -\> user

        -   awk \'{split(\$1,a,\":\"); print(a\[1\])}\'

    -   If only it were this easy! Passwords are in SLAPd so we have to
        do some LDAP operations!

-   Secure accounts from setting bad passwords
    ------------------------------------------

    -   Installed pam\_cracklib.so

    -   Configuration: /etc/pam.d/common-password

    -   Added line:

        -   password required pam\_cracklib.so retry=3 minlen=9 difok=3

    -   TODO: change newuser script to force good passwords

    -   TODO: change website to have password strength indicator

-   Notify the users of the changes
    -------------------------------

Crackdown.py
------------

[https://pastebin.com/i5edsDfX](https://pastebin.com/i5edsDfX)
```python
# crackdown.py
# robert quitt 2/18/18
# requires: jinja2, ldap3
# working on python 2.7.14
import sys
import argparse
import os.path
from getpass import getuser, getpass
from subprocess import Popen, PIPE, STDOUT

from jinja2 import Environment, FileSystemLoader
from ldap3 import Server, Connection, ALL, ALL_ATTRIBUTES, MODIFY_REPLACE

LDAP_SERVER = "ldaps://ldap.csua.berkeley.edu"
LDAP_USER = "uid={0},ou=People,dc=csua,dc=berkeley,dc=edu".format(getuser())
LDAP_PASSWORD = ""

def sendmail(address, subject, body, verbose=True):
    mailx_cmd = ['mailx', '-s', subject, address]
    if verbose:
        mailx_cmd.insert(-1, '-v')

    mailx_proc = Popen(mailx_cmd, stdin=PIPE, stdout=PIPE)
    mailx_proc.communicate(input=body.encode())
    print("Mail sent to {0}".format(address))

def ldap_auth():
    s = Server(LDAP_SERVER, get_info=ALL)
    c = Connection(s, user=LDAP_USER, password=getpass("LDAP Password: "))
    return c

def ldap_lock(user, c):
    """Equivalent to passwd --lock but in ldap"""
    search_filter = '(uid={0})'.format(user)
    base_dn = "dc=csua,dc=berkeley,dc=edu"
    dn="uid={0},ou=people,dc=csua,dc=berkeley,dc=edu".format(user)
    if c.bind():
        c.search(base_dn, search_filter, attributes=['userPassword'])
        userPassword = str(c.entries[0].userPassword)
        dn = str(c.response[0]['dn'])
        if not userPassword.endswith('!'):
            userPassword += '!'
            changes = {'userPassword': [(MODIFY_REPLACE, [userPassword])]}
            c.modify(dn, changes)
            c.unbind()
            print('Locked user {0}'.format(user))
            return True
        else:
            print('User {0} already locked.'.format(user))
            c.unbind()
            return False
    print('Failed to bind to ldap!')
    return False

def main():
    parser = argparse.ArgumentParser(description="Send an email from a template")
    subparsers = parser.add_subparsers(dest='cmd')

    mail_parser = subparsers.add_parser('mail')
    mail_parser.add_argument('subject')
    mail_parser.add_argument('template_name')
    mail_parser.add_argument('userfile')
    lock_parser = subparsers.add_parser('lock')
    lock_parser.add_argument('userfile')

    args = parser.parse_args()

    if args.cmd == 'mail':
        env = Environment(loader=FileSystemLoader(os.path.dirname(args.template_name)))
        template = env.get_template(args.template_name)
        for username in open(args.userfile):
            username = username.strip()

            body = template.render(username=username)
            sendmail('{0}@csua.berkeley.edu'.format(username), args.subject, body)

    elif args.cmd == 'lock':
        c = ldap_auth()
        for user in open(args.userfile):
            user = user.strip()
            ldap_lock(user, c)

if __name__ == '__main__':
    main()
```
