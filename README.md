sel4-gpi-manifest
========
sel4-gpi holds the manifest file to build the General Purpose Isolation(GPI) project based on seL4.
The manifest file points to various repo's that are hosted on the seL4 Github site.

However the kernel and some of the libs come from sid-agrawal's forks.


# Setup instructions

```bash
mkdir sel4-gpi-system
cd sel4-gpi-system
repo init -u https://github.com/sid-agrawal/sel4-gpi-manifest && repo sync
```
