# docker-datacenter playbook

# storage driver 

OverlayFS is a modern union filesystem that is similar to AUFS, but faster and with a simpler implementation. Docker provides two storage drivers for OverlayFS: the original overlay, and the newer and more stable overlay2.

This topic refers to the Linux kernel driver as OverlayFS and to the Docker storage driver as overlay or overlay2.
 If you use OverlayFS, use the overlay2 driver rather than the overlay driver, because it is more efficient in terms of inode utilization


for overlay2, Version 4.0 or higher of the Linux kernel, or RHEL or CentOS using version 3.10.0-693 of the kernel or higher. Docker EE users using kernels older than 4.0 need to follow some extra steps, outlined below. If you use an older kernel, you need to use the overlay driver, which is not recommended.

in case of xfs, xfs_info to verify that the ftype option is set to 1
xfs_info / | grep ftype 

output should be "ftype=1"