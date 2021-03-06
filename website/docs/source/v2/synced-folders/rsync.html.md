---
page_title: "RSync - Synced Folders"
sidebar_current: "syncedfolder-rsync"
---

# RSync

**Synced folder type:** `rsync`

Vagrant can use [rsync](http://en.wikipedia.org/wiki/Rsync) as a mechanism
to sync a folder to the guest machine. This synced folder type is useful
primarily in situations where other synced folder mechanisms are not available,
such as when NFS or VirtualBox shared folders aren't available in the guest
machine.

The rsync synced folder does a one-time one-way sync from the machine running
to the machine being started by Vagrant.

The [rsync](/v2/cli/rsync.html) and [rsync-auto](/v2/cli/rsync-auto.html)
commands can be used to force a resync and to automatically resync when
changes occur in the filesystem. Without running these commands, Vagrant
only syncs the folders on `vagrant up` or `vagrant reload`.

## Prerequisites

To use the rsync synced folder type, the machine running Vagrant must have
`rsync` (or `rsync.exe`) on the path. This executable is expected to behave
like the standard rsync tool.

On Windows, rsync installed with Cygwin or MinGW will be detected by
Vagrant and works well.

The destination machine must also have rsync installed, but Vagrant
can automatically install rsync into many operating systems. If Vagrant
is unable to automatically install rsync for your operating system,
it will tell you.

The destination folder will be created as the user initiating the connection,
this is `vagrant` by default. This user requires the appropiate permissions on
the destination folder.

## Options

The rsync synced folder type accepts the following options:

* `rsync_args` (array of strings) - A list of arguments to supply
  to `rsync`. By default this is `["--verbose", "--archive", "--delete", "-z"]`.

* `rsync_auto` (boolean) - If false, then `rsync-auto` will not
  watch and automatically sync this folder. By default, this is true.

* `rsync_chown` (boolean) - If false, then the
  [`owner` and `group`](/v2/synced-folders/basic_usage.html)
  options for the synced folder are ignored and Vagrant won't execute
  a recursive `chown`. This defaults to true. This option exists because
  the `chown` causes issues for some development environments. Note that
  any `rsync_args` options for ownership **will be overridden** by
  `rsync_chown`.

* `rsync_exclude` (string or array of strings) - A list of files or directories
  to exclude from the sync. The values can be any acceptable rsync exclude
  pattern. By default, the ".vagrant/" directory is excluded. We recommend
  excluding revision control directories such as ".git/" as well.

## Example

The following is an example of using RSync to sync a folder:

<pre class="prettyprint">
Vagrant.configure("2") do |config|
  config.vm.synced_folder ".", "/vagrant", type: "rsync",
    rsync_exclude: ".git/"
end
</pre>

## Rsync to a restricted folder

If required to copy to a destination where `vagrant` user doesn't have
permissions, use `"--rsync-path='sudo rsync'"` to run rsync with sudo on the guest

<pre class="prettyprint">
Vagrant.configure("2") do |config|
  config.vm.synced_folder "bin", "/usr/local/bin", type: "rsync",
    rsync_exclude: ".git/"
    rsync_args: ["--verbose", "--rsync-path='sudo rsync'", "--archive", "--delete", "-z"]
end
</pre>
