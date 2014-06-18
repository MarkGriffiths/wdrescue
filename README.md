wdrescue for ddrescue
========
ddrescue helper for recovering Western Digital AF drives on Mac OS X.

## Background
While upgrading the storage in my 4 year old DroboPro, a 2TB WD Caviar Green (WDC WD20EARS) went bad during the 'Data Protection' phase. As the bad drive was now critical to the disk pack, I could either lose the whole array and rebuild from scratch, or attempt to clone the bad disk to a new one.

While I had the data on the array mostly backed up or could recreate it from other sources, reorganising the data still represented a non-trivial amount of work so I elected to try and recover it.

After trying a couple of commercial tools that offered block level cloning and finding both the functionality and performance lacking, I looked into using dd and came across ddrescue.

After a some testing, I began using ddrescue to rescue the disk in chunks. I very quickly wanted to protect myself from user error as just one transposition of source and destination disk would have jeopardised any attempt of recovery, so I created this script to manage the process. It grew in functionality as the job progressed and I'm offering to the community as it may help others recover from a bad disk.
