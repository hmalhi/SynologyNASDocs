# SynologyNASDocs
My experience with Synology NAS

# Improper Shutdown of NAS
I had a NAS with 12TB of data which had improper shutdown due to power ooutage and it resulted in the following:
1. On restart NAS went into read-only mode for file system with warning to backup my data
2. My homes and home folder was no longer visible either via File Explorer and/or ssh using adminstrator account
3. All HDD were showing normal and green
4. Volume showed 12TB used

# Support from Synology
1. I submitted synology support request and they responded with their usual stuff of backing data try to see if home folder was visible etc etc
2. None of those worked and finally they took the ususal route to letting me know that I had installed a 3rd party hardware/software which they did not recommend and hence will not be able to help me recover my data and refered me to local data recovery expert. Good luck!! you are on your own.
3. No doubt I was furstrated with Synology support but that was not going to do me any good so I tried to recover data on my own. I did call few companies for data recovery but seems I had a better chance of recovering my data than them and would end up paying $$$$ and probably no data.

# Steps to recover data
So I went about fixing the issue myself.
1. In storage manager of Synology I did run Data Scrub and let it finish, took about a day to complete and everything looked fine, still read-only mode for file-system.
2. In storage manager using dot-dot-dot menu I changed file system to read-write mode and rebooted
3. The file-system was read-write now and Storage manage showed that file system/volume check was needed
4. I restarted the system few times when clicked on file system check link but it never went away and my home/homes folder were still not accessible
5. This meant my file system was not able to auto recover the issues due to power outage so I had to take some deeper steps to fix.
6. I ran following command by logging into ssh as administrator. ***Make sure you disbale 2FA prior to doing the next steps.***
### Become root
 sudo -i
### Get volume device info
df
e.g
/dev/mapper/cachedev_0 14516766 13691149 825498  95% /volume1
### unmount volume, this is needed to run file system check
sudo synostgvolume --unmount -p /volume1
### run file system check without making any changes. Run next command only if this is successful, it was successful in my case :)
e2fsck -nvf -C 0 /dev/mapper/cachedev_0 (make sure this is same as listed using df command above)
### Run file system check again this time with auto recovery
e2fsck -pvf -C 0 /dev/mapper/cachedev_0 //this failed for me :(
### Run file system check again this time with recovery set to Yes for all, only if previous step did not work
e2fsck -yvf -C 0 /dev/mapper/cachedev_0 //worked for me with recovered files moved to lost+found folder

System reboots, in my case I could not login due to 2FA enabled so had to reset NAS as per <a href="https://kb.synology.com/en-eu/DSM/tutorial/How_to_reset_my_Synology_NAS_7#t1">synology documentation</a>
Log into NAS using ssh and as administrator.
You can now recover folders/files from lost+found folder. Be careful not to delete or overwrite any file/folder when doing this.
cd /volume1/lost+found
There will be lot of numbered folders, you will need to check the contents of each and size to recover the ones you need including contents of home/homes folder

Happy Recovery!!!
