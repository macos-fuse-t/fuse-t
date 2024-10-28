Abstract
========
FUSE-T is a kext-less implementation of FUSE for macOS that uses NFS v4 local server instead of a kernel extension.\
The main motivation is to replace 'macfuse' project (https://osxfuse.github.io/) that implements its own kext to make fuse work. 
With each version of macOS it's getting harder and harder to load kernel extensions. Apple strongly discourages it thus making software distributions that use 'macfuse' difficult to install. Additionally, the 'macfuse' kext is unstable, may cause frequent system crashes and kernel lock-ups. Given those limitations many software publishers are unable to use 'macfuse' project anymore.

FUSE-T doesn't make use of kernel extension, it implements its own userspace server that converts between FUSE protocol and NFS and let macOS mount NFS volume instead of a kernel filesystem. 

The main features of FUSE-T
----
- Drop-in replacement for 'macfuse' (https://osxfuse.github.io/). API headers in libfuse are unchanged so there's no need to change anything in filesystem implementation. The APIs are compatible with 'macfuse' but differ from the Linux version of libfuse and this is because the 'macfuse' project was not updated in sync with the Linux version
- Kext-less, no kernel extensions needed
- NFSv4 protocol to better support macOS features such as extended attributes, file locks and others
- Stable, easy to mount and unmount volumes with macOS built-in tools, no need for proprietary fusermount
- Performance, FUSE-T offers much better performance and this is due to excellent macOS client side NFSv4 implementation

How FUSE-T works
-----------------
When a filesystem issues a mount API call, libfuse launches a FUSE-T NFS server that exposes a local TCP port to the macOS mount proccess and another communication channel to libfuse. After that a macOS mount_nfs command is executed and NFS rpcs are getting called on the server.
The server converts NFS rpc calls into FUSE requests that emulate original kernel-libfuse protocol. Those requests are executed in libfuse and corresponding FUSE filesystem, then the result is converted back to the macOS NFS client.
After the filesystem process dies the NFS server terminates as well and it also unmounts the NFS folder mounted previously.
Experimental SMB backend was added as NFS alternative

Other details
------
See wiki for more information.

Download the latest version of fuse-t:\
https://github.com/macos-fuse-t/fuse-t/releases/download/1.0.43/fuse-t-macos-installer-1.0.43.pkg

if you have any questions or comments, contact me at alex@fuse-t.org

Installing from brew
-----
```
brew install macos-fuse-t/homebrew-cask/fuse-t
```
For sshfs:
```
brew install macos-fuse-t/homebrew-cask/fuse-t-sshfs
```

Possible Issues
-----------------

-- "Operation not permitted"

From a terminal, if you find that you can not read the contents of a folder under
the FUSE file system, it may be that access to "Network Volumes" is not enabled.
See "System Settings" / "Privacy & Security" / "Files and Folders" / 
<name of your terminal application> / "Network Volumes" and ensure it is 
enabled.  This is a consequence of fuse-t leveraging the nfs system under the 
covers.
