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

Other details
------
See wiki for more information.

Download the latest version of fuse-t:\
https://github.com/macos-fuse-t/fuse-t/releases/download/1.0.12/fuse-t-macos-installer-1.0.12.pkg \
sshfs:\
https://github.com/macos-fuse-t/fuse-t/releases/download/1.0.1/sshfs-macos-installer-1.0.1.pkg

if you have any questions or comments, contact me at alex@fuse-t.org

Installing from brew
-----
```
brew tap macos-fuse-t/homebrew-cask
brew install fuse-t
brew install fuse-t-sshfs
```


