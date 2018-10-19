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

[[https://pastebin.com/i5edsDfX]{.underline}](https://pastebin.com/i5edsDfX)

import sys

import argparse

import os.path

from getpass import getuser, getpass

from subprocess import Popen, PIPE, STDOUT

from jinja2 import Environment, FileSystemLoader

from ldap3 import Server, Connection, ALL, ALL\_ATTRIBUTES,
MODIFY\_REPLACE

LDAP\_SERVER = \"ldaps://ldap.csua.berkeley.edu\"

LDAP\_USER =
\"uid={0},ou=People,dc=csua,dc=berkeley,dc=edu\".format(getuser())

LDAP\_PASSWORD = \"\"

def sendmail(address, subject, body, verbose=True):

mailx\_cmd = \[\'mailx\', \'-s\', subject, address\]

if verbose:

mailx\_cmd.insert(-1, \'-v\')

mailx\_proc = Popen(mailx\_cmd, stdin=PIPE, stdout=PIPE)

mailx\_proc.communicate(input=body.encode())

print(\"Mail sent to {0}\".format(address))

def ldap\_auth():

s = Server(LDAP\_SERVER, get\_info=ALL)

c = Connection(s, user=LDAP\_USER, password=getpass(\"LDAP Password:
\"))

return c

def ldap\_lock(user, c):

\"\"\"Equivalent to passwd \--lock but in ldap\"\"\"

search\_filter = \'(uid={0})\'.format(user)

base\_dn = \"dc=csua,dc=berkeley,dc=edu\"

dn=\"uid={0},ou=people,dc=csua,dc=berkeley,dc=edu\".format(user)

if c.bind():

c.search(base\_dn, search\_filter, attributes=\[\'userPassword\'\])

userPassword = str(c.entries\[0\].userPassword)

dn = str(c.response\[0\]\[\'dn\'\])

if not userPassword.endswith(\'!\'):

userPassword += \'!\'

changes = {\'userPassword\': \[(MODIFY\_REPLACE, \[userPassword\])\]}

c.modify(dn, changes)

c.unbind()

print(\'Locked user {0}\'.format(user))

return True

else:

print(\'User {0} already locked.\'.format(user))

c.unbind()

return False

print(\'Failed to bind to ldap!\')

return False

def main():

parser = argparse.ArgumentParser(description=\"Send an email from a
template\")

subparsers = parser.add\_subparsers(dest=\'cmd\')

mail\_parser = subparsers.add\_parser(\'mail\')

mail\_parser.add\_argument(\'subject\')

mail\_parser.add\_argument(\'template\_name\')

mail\_parser.add\_argument(\'userfile\')

lock\_parser = subparsers.add\_parser(\'lock\')

lock\_parser.add\_argument(\'userfile\')

args = parser.parse\_args()

if args.cmd == \'mail\':

env =
Environment(loader=FileSystemLoader(os.path.dirname(args.template\_name)))

template = env.get\_template(args.template\_name)

for username in open(args.userfile):

username = username.strip()

body = template.render(username=username)

sendmail(\'{0}@csua.berkeley.edu\'.format(username), args.subject,
body)

elif args.cmd == \'lock\':

c = ldap\_auth()

for user in open(args.userfile):

user = user.strip()

ldap\_lock(user, c)

if \_\_name\_\_ == \'\_\_main\_\_\':

main()
