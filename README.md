duplicity-nfs-backup
====================

Version 1.0 (2013-07-26)


**duplicity-nfs-backup** is a wrapper script designed to ensure that an NFS/CIFS-mounted target directory is indeed accessible before commencing with backup, utilizing zertrin's [duplicity-backup.sh](http://zertrin.org/projects/duplicity-backup/), and ultimately [duplicity](http://duplicity.nongnu.org/).  While duplicity-backup.sh can be used to back up to a variety of mediums (ftp, rsync, sftp, local file...), this script is specifically intended to be used with NFS/CIFS shares as backup targets.

The inspiration for this script came from another script, [nfs_automount](http://my.galagzee.com/2013/07/13/nfs-automount-the-fourth-iteration/), that I released about a week before this script.  After completing nfs_automount I first experimented with [rdiff-backup](http://rdiff-backup.nongnu.org/), which is great for local disks, but which, I soon found, has known issues when backing up to NFS/CIFS shares. The most glaring and obvious problems arose due to permissions that did not always match across the systems (NFS client & server), but depending on the environment, other more cryptic problems surfaced also. The gzip-based duplicity works much better under such conditions, and has no problems utilizing NFS/CIFS shares as backup targets.  But what if the NFS share is not available?  My other script, nfs_automount, attempts to keep the shares online at all times, but the client system can't always help with such situations. What if the target system becomes unreachable due to a network problem? Or what if a disk, or a filesystem mount fails on the target while the share is still available? In any of these cases duplicity-backup/duplicity would back up into an empty mountpoint. duplicity-nfs-backup adds the necessary checks to ensure that this won't happen, and issues log/syslog warnings when a backup fails due to a share that has gone M.I.A.

Paraphrasing zertrin, I mention here:

Please note: **duplicity-nfs-backup IS NOT duplicity, nor is it duplicity-backup!** It is only a wrapper script for duplicity-backup [also] written in bash!

This means that you will need to install and configure duplicity and duplicity-backup.sh before you can utilize duplicity-nfs-backup.

* I recommend that you would make use of [nfs_automount](http://my.galagzee.com/2013/07/13/nfs-automount-the-fourth-iteration/) as it significantly improves the chances that the NFS target share will be online when duplicity-nfs-backup attempts to access it.

* This script is intended to be run from crontab.  duplicity-nfs-backup takes no arguments, simply set the configuration parameters in duplicity-nfs-backup.conf and you're done!

* Provides clear logging which provides critical alerts by default, and more informative detail if you turn 'DEBUGLOG' setting to 'true'.

* Written in bash script with modular and clear syntax.  

* Distributed under MIT license.


Please let me know if you come across any problems! Pull requests are welcome.


Installation
------------

**PRE-REQUISITES: duplicity and duplicity-backup are required, nfs_automount is recommended!**

Clone duplicity-nfs-backup to the location of your choice (here '/opt/duplicity-nfs-backup'), assign the ownership of the file **nfs_automount** to the root, and set its permissions to 700.

    sudo su
    cd /opt
    git clone https://github.com/vwal/duplicity-nfs-backup.git
    cd duplicity-nfs-backup
    chmod 700 duplicity-nfs-backup
    chown root:root duplicity-nfs-backup

You can keep the configuration file **duplicity-nfs-backup.conf** in the same directory as where the script itself resides (the script will look for it there by default). However, you can also move it to the location of your choice, such as /etc/duplicity-nfs-backup.conf.  If you do so, reflect the change by editing the 'CONFIG_FILE' parameter on the top of the duplicity-nfs-backup script. 

Modify the configuration in duplicity-nfs-backup.conf to meet your needs.  Specifically, you want to replace the example "BACKUPS" entries with those of your own.  Perhaps you have just one backup process, but you can define as many as you need.

If duplicity-backup is not on path (try 'which duplicity-backup'), set its location with DUPLICITY_BACKUP_COMMAND parameter in the configuration file.

At this point you can test the script by running it at the console.  If you set TEST parameter to "true" in duplicity-nfs-backup.conf, the script will run without actually executing the backup.  By also setting LOGTYPE to "log" and DEBUGLOG to "true", you can run it at the prompt to confirm that everything is working as expected before deploying it into production.

When you're ready to put it into use, make sure to set TEST variable back to "false" (or comment it out which has the same effect).  You probably also want to set LOGTYPE to "log", and DEBUGLOG to "false". Then simply add a new job into root's crontab to execute "/opt/duplicity-nfs-backup/duplicity-nfs-backup" (or path that you chose) at an interval of your choice.


License
-------

Copyright (c) 2013 Ville Walveranta

[http://my.galagzee.com](http://my.galagzee.com)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions: 

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
