# Batocera Backup Service

A script for [Batocera](https://batocera.org/) to back up your data to cloud storage using [rclone](https://rclone.org/).

It is for uploading user data to cloud storage, so in case of a hardware failure/loss, the data can be easily restored. The script itself cannot restore data, that must be done manually.

It has two modes:

* [Automatic](#automatic): using Batocera's [services](https://wiki.batocera.org/launch_a_script#services) feature, it periodically uploads your saves in the background.
* [Manual](#manual): by running the script manually, you can do a full backup of your data.

It's tested and works with Batocera [v40](https://batocera.org/changelog) and rclone's [OneDrive provider](https://rclone.org/onedrive/). Other providers very likely work just as well, they just haven't been tested by me. Open an issue if you run into one that doesn't work.

> [!IMPORTANT]  
> Steam data is currently not backed up by this script. Only the [saves handled by Steam](https://store.steampowered.com/account/remotestorage) are saved outside of Batocera.
>
> **Background**: since [Steam on Batocera](https://wiki.batocera.org/systems:steam) is installed using [Flatpak](https://wiki.batocera.org/systems:flatpak), it's data is structured differently than the other systems. All game data, not just saves, is stored in the `/userdata/saves/flatpak` directory, which is currently ignored by the script.

## FAQ

### Can I use this script to keep Batocera installs on multiple devices in sync?

No. rclone can upload **or** download files to/from a cloud storage. This script only does the former. rclone's [bidirectional sync](https://rclone.org/bisync/) feature is currently in beta. As soon as that changes, this script will be most likely updated to take advantage of it. Until then, [Syncthing](https://wiki.batocera.org/syncthing) is the best solution for this use case.

### Can I use this script to back up to the same cloud storage from multiple devices?

Yes. If you'd like to do that, make sure you configure the [destDir](/backup#L6) variable to point to a different directory for each device to avoid collisions. Otherwise they'd overwrite each other's files that are named the same.

## Setup

1. Copy the [backup](/backup) script to your Batocera installation's `/userdata/system/services` directory.
2. Modify the [configuration](/backup#L3) at the top of the file according to your needs.
3. Follow the steps in one of the methods [here](https://rclone.org/remote_setup/) and set up a remote with the name `backup`.
4. Enable the service with `batocera-services enable backup`.
5. Restart Batocera or use `batocera-services start backup`.

## Usage

### Automatic

Refer to the [Batocera wiki](https://wiki.batocera.org/launch_a_script#services) to learn how to manage services.

### Manual

To do a manual backup: `bash services/backup manual`

This does a full backup including not just saves, but your configuration (`batocera.conf`), ROMs and BIOS files. Use this after modifying any of those.

## Development

Open this repository in VS Code and install the [workspace recommended extensions](https://code.visualstudio.com/docs/editor/extension-marketplace#_workspace-recommended-extensions), so the script can be [automatically copied](/.vscode/settings.json#L6) to the Batocera machine on save.

Local dependencies that need to be installed for the copying to work: `sshpass`, `scp`.
