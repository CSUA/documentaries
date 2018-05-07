# Notes on CSUA Root History/Tech Documentation

## Root Staff History

According to the holy scriptures, Root Staff has existed since the beginning of history.

The VP of CSUA has historically been in charge of leading this group in their various technological exploits.

## 2014 RAID Crash Incident

I've been reading about what happened to the CSUA server in 2014. 30 years of
historical data have been lost. I feel a deep sense of regret over this. I want
to right the mistakes of my ancestors and reconnect with our past. I will talk
to the data recovery specialists. For now, I am trying to figure out what
happened.

Here are some links to posts that I've found so far.

- [Post-mortem announcement from CSUA root: Oct 18, 2014][1]
- [Post about outage on Facebook group: Oct 4, 2014][2]
- [Another breach, Feb 12, 2017][3]
- [Jim notes the loss of homedirs on Soda][4]

[1]: https://www.facebook.com/groups/csuahosers/permalink/10152411168169856/
[2]: https://www.facebook.com/groups/csuahosers/permalink/10152377861569856/
[3]: https://www.facebook.com/groups/csuahosers/permalink/10154395758704856/
[4]: https://www.facebook.com/groups/csuahosers/permalink/10152430612724856/

—Robert

## Physical Servers

CSUA owns a server rack in 340 Soda. We have a bunch of computers there.

`minibar` is deprecated in favor of minifridge.

`minibar` is stil connected to the old NFS server. The drives that are still
spinning are functional but the old NFS disks are destroyed.

All our active services run on two VMs on `minifridge`, `soda` and `tap`

The NFS mount is `/dev/sdb`, which is really a QEMU mapped hard drive.

The replacement NFS disks were used as a temporary solution in 2014, but now
NFS is just some drives on `minifridge`.

`nixclub` belongs to CSUA, but it was not used during Caleb's time here. No alumni from the April 2018 alumni lunch knew what the server was nor how to access it.
