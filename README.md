sel4-gpi-manifest
========
sel4-gpi holds the manifest file to build the General Purpose Isolation(GPI) project based on seL4.
The manifest file points to various repo's that are hosted on the seL4 Github site.

However the kernel and some of the libs come from sid-agrawal's forks.


# Setup instructions

```bash
mkdir sel4-gpi-system
cd sel4-gpi-system
repo init -u https://github.com/sid-agrawal/sel4-gpi-manifest -b refs/tags/v3.0 
repo sync

# Jump to the docker sel4 dev environment, omit if you do not care
# https://docs.sel4.systems/projects/dockerfiles/
container 
mkdir build  
cd build
../init-build.sh -DPLATFORM=ia32 -DSIMULATION=TRUE 
ninja  && ./simulate

# To exit Qemu
Ctrl-A X
```
