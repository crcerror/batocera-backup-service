# Batocera Backup Service

A script for [Batocera](https://batocera.org/) to back up your data to cloud storage using [rclone](https://rclone.org/).

It is for uploading user data to cloud storage, so in case of a hardware failure/loss, the data can be easily restored. The script itself cannot restore data, that must be done manually.

It has two modes:

* [Automatic](#automatic): using Batocera's [services](https://wiki.batocera.org/launch_a_script#services) feature, it periodically uploads your saves in the background, if you set `sync` or `copy`-mode. If you set `bisync` as working mode, then the sync is performed early in ES start and after you shutdown Batocera. Background is here that the process of bisyncing is much slower then a rare copy or sync of files only.
  * **copy**: All files from the local will copied to the host and never will be deleted
  * **sync**: All files from the local will be mirrored to the host, in doubts you lose your cloud backup if you setup a new mashine
  * **bisync**: All files will be copied from the local to the host and vice versa. You can also sync with a second mashine, but therefore use `--resync` flag **BEFORE** you fire up the script automatic!
    
* [Manual](#manual): by running the script manually, you can do a full backup of your data.

It's tested and works with Batocera [v40](https://batocera.org/changelog) and rclone's [OneDrive provider](https://rclone.org/onedrive/). Other providers very likely work just as well, they just haven't been tested by me. Open an issue if you run into one that doesn't work.

> [!IMPORTANT]  
> Steam data is currently not backed up by this script. Only the [saves handled by Steam](https://store.steampowered.com/account/remotestorage) are saved outside of Batocera.
>
> **Background**: since [Steam on Batocera](https://wiki.batocera.org/systems:steam) is installed using [Flatpak](https://wiki.batocera.org/systems:flatpak), it's data is structured differently than the other systems. All game data, not just saves, is stored in the `/userdata/saves/flatpak` directory, which is currently ignored by the script.

## FAQ

### Can I use this script to keep Batocera installs on multiple devices in sync?

Yes. rclone's [bidirectional sync](https://rclone.org/bisync/) feature is currently in beta state but it works stable here. But you have to assure to use `--dry-run` before you perform any productive tests. [Syncthing](https://wiki.batocera.org/syncthing) is the best solution for this use case.

### Can I use this script to back up to the same cloud storage from multiple devices?

Yes. If you'd like to do that, make sure you configure the [destDir](/rclone_backup#L9) variable to point to a different directory for each device to avoid collisions. Otherwise they'd overwrite each other's files that are named the same.

## Setup

1. Copy the [backup](/rclone_backup) script to your Batocera installation's `/userdata/system/services` directory.
2. Modify the [configuration](/backup#L3) at the top of the file according to your needs.
3. Follow the steps in one of the methods [here](https://rclone.org/remote_setup/) and set up a remote. The remote name is automatically detected as first entry into rclones config file.
4. Enable the service with `batocera-services enable backup`.
5. Restart Batocera or use `batocera-services start backup`.

## Usage

### Automatic

Refer to the [Batocera wiki](https://wiki.batocera.org/launch_a_script#services) to learn how to manage services.

### Manual

To do a manual backup or restore: `bash services/rclone_backup manual <backup/restore>`

This does a full backup or restore including not just saves, but your configuration (`batocera.conf`), ROMs and BIOS files. Use this after modifying any of those.

### Original Author
Péter Bozsó - https://gitlab.com/peterbozso/batocera-backup-service/

