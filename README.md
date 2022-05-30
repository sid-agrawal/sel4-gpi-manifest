sel4-gpi-manifest
========
sel4-gpi holds the manifest file to build the General Purpose Isolation(GPI) project based on seL4.
The manifest file points to various repo's that are hosted on the seL4 Github site.

However the kernel and some of the libs come from sid-agrawal's forks.


# Setup instructions

```bash
mkdir sel4-gpi-system
cd sel4-gpi-system
repo init -u https://github.com/sid-agrawal/sel4-gpi-manifest 
repo sync

# Jump to the docker sel4 dev environment, omit if you do not care
# https://docs.sel4.systems/projects/dockerfiles/
container 
mkdir build  
cd build
../init-build.sh -DPLATFORM=qemu-arm-virt -DSIMULATION=TRUE 
ninja  && ./simulate

# To exit Qemu
Ctrl-A X
```

# Setup(as a dev) instructions

```bash
mkdir sel4-gpi-system
cd sel4-gpi-system
repo init -u https://github.com/sid-agrawal/sel4-gpi-manifest  -m dev.xml
repo sync
./checkout.sh

# Jump to the docker sel4 dev environment, omit if you do not care
# https://docs.sel4.systems/projects/dockerfiles/
container 
mkdir build  
cd build
../init-build.sh -DPLATFORM=qemu-arm-virt -DSIMULATION=TRUE 
ninja  && ./simulate

# To exit Qemu
Ctrl-A X
```
