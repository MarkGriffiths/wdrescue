wdrescue for ddrescue
========
#### ddrescue helper for recovering Western Digital AF drives on Mac OS X.

While upgrading the storage in my 4 year old DroboPro, a 2TB WD Caviar Green (WDC WD20EARS) went bad during the 'Data Protection' phase. As the bad drive was now critical to the disk pack, I could either lose the whole array and rebuild from scratch, or attempt to clone the bad disk to a new one.

While I had the data on the array mostly backed up or could recreate it from other sources, reorganising the data still represented a non-trivial amount of work so I elected to try and recover it.

After trying a couple of commercial tools that offered block level cloning and finding both the functionality and performance lacking, I looked into using dd and came across ddrescue.

After a some testing, I began using ddrescue to rescue the disk in chunks. I very quickly wanted to protect myself from user error as just one transposition of source and destination disk would have jeopardised any attempt of recovery, so I created this script to manage the process. It grew in functionality as the job progressed and I'm offering it to the community as it may help others recover from a bad disk.

## Installation
First, install `ddrescue`, best done on the Mac with [HomeBrew](http://brew.sh).

    brew install ddrescue

Clone this repository to your system, move `wdrescue` from inside the repo into a directory in your `$PATH` (I use `/usr/local/bin` or another location would be `~/bin`) and make sure it's executable.

```bash
git clone https://github.com/MarkGriffiths/wdrescue.git
cd wdrescue
mv wdrescue /usr/local/bin/
chmod +x /usr/local/bin/wdrescue
```

## Usage
`wdrescue` creates a directory on your local disk to act as a workbench for a single recovery task. This directory will contain a set of task commands as well as storing options and ddrescue's recovery log. 

General help: ``wdrescue --help``

To create a workbench directory with 'safe' source (/dev/zero) and destination (/dev/null) to try out the rescue commands without touching the bad disk.
```bash
wdrescue --setup test_project_name
cd test_project_name 
```

Try out some of the recovery commands shown below to be sure you're comfortable before moving to live recovery.

Once ready, connect both the bad disk and the new replacement disk (if used), or make sure you have enough space on another volume to hold an image of the bad disk.

Lookup the device mount points and verify you know the source and destination.
```bash
diskutil list
diskutil info /dev/diskX
```

Now create the recovery project. You'll need to use the raw disk devices for best performance which are found at /dev/rdiskX. It's possible to use the standard block devices at /dev/diskX, but these will be much slower and the kernel is likely to try and interfere has you hit bad blocks on the bad drive.
```bash
wdrescue --setup project_name /dev/rdisk(Source) /dev/rdisk(Destination)
cd project_name
```

```bash
wdrescue --help
wdrescue - ddrescue helper for recovering Western Digital AF drives.
Tools to help rescue a bad or failing WD drive by cloning or imaging
while minimising user error and enhancing performance.

Setup: wdrescue --setup name [source device] [destination device or file]

Creates a directory 'name' on the current path and configures it as
a 'rescue workbench' containing rescue subcommands and cache:

  ./scan    [options] start [size]: Scanning tools.
  ./fix     [options] start [size]: Fixing tools.
  ./check   [options] start [size]: Check status of recovery.

Use -h for more help on each command.

Source and destination devices should be raw disks: /dev/rdiskX

After reboot: wdrescue --check

Checks config for changes in disk mounting points.
```

### Scanning
```text
./scan -h
wdrescue - scanning tools.

Usage: ./scan -hmlcnsRIM start [size]

  start: scan start point in GB
  size: optional scan length in GB (default: 1GB)

  Options:
    -h: Show help
    -m: Max read cluster (64MB)
    -l: Large read cluster (32MB)
    -c size: Custom read cluster size
    -n: Standard read cluster (64k)
    -s: Auto skip
    -R: Reverse scan
    -I: Infinite timeout
    -M: Work in MBs instead of GBs
```

### Fixing
```text
./fix -h
wdrescue - fixing tools.

Usage: ./fix -htrRIM start [size]

  start: scan start point in GB
  size: optional scan length in GB (default: 1GB)

  Options:
    -h: Show help
    -t: Retrim errors
    -r: Retry sectors
    -R: Reverse scan
    -I: Infinite timeout
    -M: Work in MBs instead of GBs
```

### Checking Status
```text
./check -h
wdrescue - Log checking tools.

Usage: ./check -hM start [size]

  start: scan start point in GB
  size: optional scan length in GB (default: 1GB)

  Options:
    -h: Show help
    -M: Work in MBs instead of GBs

Note: check will return an error if scan or fix hasn't been run
```

## TODO
- [ ] Proper notications on completion of a rescue phase.
- [ ] Improve `wdrescue --check` to report if mount points have changed.