---
title: Soda User Policy (1995)
---
# General User Policy -- Soda.CSUA.Berkeley.CSUA

This document describes the general user policy of
Soda.CSUA.Berkeley.EDU. This file is subject to change at any time.

Last edited 21 Jan 95 by Ali "Hair Trigger Squish Finger" Rahimi.

HTMLized by Erik R. Agee.

Markdowned by Robert "Never" Quitt 13 Apr 18, after finding a copy of this page
on archive.org

## [1] GENERAL COMMUNICATION

Soda is designed to promote a user community. Because of this, all users
should have accurate Real Name information. We've hacked `chfn` to
prevent users from making changes to this field; if you need yours
changed, mail root. Custom `.plan` files can be used to let others get to
know a bit about you. We also provide users with the ability to create WWW
pages for themselves. Also, don't be afraid to talk to other users.

In the interests of a user community, several group communication features are
available aside from the usual person-to-person ones:

**The newsgroup**: The Usenet newsgroup ucb.org.csua is a forum for discussion
of issues related to the Computer Science Undergraduate Association, soda among
them.

> Usenet newsgroups have fallen out of usage, but there is a mirror hosted on
> [Google groups here][usenet]. It contains ~700 posts dating from 1992-2007.
>
> --Robert

[usenet]: https://groups.google.com/forum/#!forum/ucb.org.csua

(DEPRECATED (for now)) **The Message of the Day**: The message of the day
(`/etc/motd`) is created from the files `/etc/motd.public` and
`/etc/motd.official`. All users can modify the public file to post questions
and such, while the official file is reserved for important root type
announcements. The actual `motd` is created from these files every few minutes
by `cron`, so if you make changes, it may take some time to show up.

**`Wall`**: The program `wall` broadcasts a message to all users on the machine and
everyone is encouraged to use it. By default, all new accounts are created so
that they will not receive "walls", but will receive writes and talks. To
receive walls, type `wallall y`. To turn them back off, type `wallall n`.

There are a number of files in `/usr/local/csua` which are also related to
promoting a user community. Many of these file are world-writable to encourage
you to add your own input.

## [2] HOW-TO QUESTIONS

The mail alias staff@soda has been provided for reporting problems with the
system's operation. This alias is not intended for new user support and asking
"how do I do this?" questions. Soda is not only for Computer Science
undergraduates, but users are expected to know what they are doing. If you do
not know what you are doing, UClink (241 Evans) or the OCF (Barrows basement)
are probably more appropriate machines for you to use.

Users are strongly encouraged to solicit help from each other, as well as the
man pages available online. As a last resort, the CSUA provides the help@csua
mailing list for soda specific or general UNIX questions. You are invited to
attend the UNIX help sessions presented as a joint effort of the CSUA, OCF, and
XCF if you're interested in learning more about UNIX. Finger help@csua or look
here for more info.

## [3] NEW SOFTWARE

Requests to install MUD clients or other similar software are likely to be
ignored. In general, if you want some piece of software, install it yourself.

If you're going to run a background process while you're not logged on, you
MUST leave a description of it in your home directory, with the name <prog;
name>.doc;. Not doing so will result in the death of your process. If your
background process takes up too many resources, it will be killed. If the load
on soda is too high, your unattended background jobs will be killed.

It is possible for you to make the software you install on soda available to
everyone by putting it in /usr/local/bin. You need special privileges to do
this, however. Root may decide to give you these privileges if it deems it
appropriate.

## [4] SECURITY / PRIVACY ISSUES

Soda should be a reasonably comfortable environment, so protection of user
privacy is taken seriously. Any user who intentionally violates other soda
users' privacy will be squished. Any user who uses soda to violate the privacy
of users on other machines will be squished. Any user who makes repeated
violations of net.etiquette will be squished. Your soda account is not a
birthright, and it's much easier for us to squish you than to worry about Yet
Another Problem User.

## [5] REINFORCEMENT OF THE ABOVE WARNINGS

We just want to reiterate our position on hosers (you know who you are).

**READ THIS if you are even REMOTELY thinking about EVEN POSSIBLY doing
ANYTHING nasty on this machine EVER:**

The staff on soda runs this machine, and has run many other types of machines
in the past. We have experience in dealing with clever but misguided attempts
to foil detection, and are resourceful at detecting abuses. And while we may
not all have had childhoods as bad as blojo's, many of us find that squishing
bozos is a great way to relieve the built-up tension of a stressful week. There
have been times in the past when bozos were tolerated on soda; this is not one
of those times.

Specifically, the following will get your account turned off after one warning:

- Sharing your account.
- Running a program like Crack.
- Running a MUD or other server without permission. No, we won't give you
  permission.
- Running a recursive shell script or other program which causes a load spike.
- Running a shell script or other program which abuses soda's net or the net of
  other machines (i.e. finger @every\_machine\_in\_the\_web).
- Abusing write permission on a soda user's tty.
- Pissing off admins of other machines.

The following will get your account turned off *WITHOUT* a warning:

- Having a weak password (see /usr/local/adm/passwd.help)
- Cracking root.
- Failing to respond to mail from staff regarding a violation of soda policy
  (if the mail requested a response).
- Having pirated commercial software in your directory or anywhere else on
  soda.

We do not give more than one warning. These are not exhaustive lists, but they
should give you an idea of what 'not being a hoser' entails.
