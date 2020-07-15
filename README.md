# linux-backup
Simple TimeMachine-like Backup Scripts for Linux. This is works for a mounted
volume.

# IMPORTANT NOTE:
This backup system assumes you are backing up to a file system that supports
hard links.

## Some Usage Notes
You must modify the `linuxbackuprc` file to fit your specific circumstances and
move it to your `$HOME` directory. You may rename it with a leading `.` (dot)
to hide it if you like.

Of course, you should review the scripts to see what they do before trying to
run them. There are interesting and useful variables you can set there or in
the rc file.

The `home_backup` script prompts you whether or not to actually do the backup,
since it may not be a convenient time, depending on the performance of your
system while it is backing up. It also checks whether your destination is
actually mounted and gives you a chance to mount it.

The script produces a log file in whatever directory you told it was `_HOME`.

In the `_DEST_ROOT` directory, a new directory will be created for each backup,
and the backup will be named with a timestamp. All files identical between
backups (from rsync's perspective) will be hard-linked, so there will actually
only be one copy on the disk and any can be deleted without affecting the
others. When the backup is completed a symlink called `latest` will point to
the backup directory just created.

**NOTE:** If you cancel a backup, you should confirm that the `latest` symlink
still points to the _previous_ complete backup, or the hardlinking strategy
won't work well.

## Running from cron
Since the starting of a backup is interactive, and launching an interactive
process from cron is discouraged, we have a sort of daemon that can be started
from your "startup" apps that will wait for a signal, and when received, will
launch the backup.  This "daemon" is called `cron_backup_starter`. It starts a
process that runs in the background, but does not use up CPU while waiting. The
PID of the process is stored in `/tmp/cron_backup_starter_PID`. So the cron
task simply sends the expected signal (SIGTRAP/5) to the process, which will
then launch the interactive backup.
```
sudo crontab -u <your-username> -e
```
Then paste the contents of `crontab_contents` into the editor and save it.

## Thanks
Much thanks to [Michael Jakl](https://blog.interlinked.org/about/index.html)'s
"[Time Machine for every Unix out there](https://blog.interlinked.org/tutorials/rsync_time_machine.html)"
blog post, on which the `incremental_backup` script is based.
