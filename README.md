# boot-c demo

Bootable Container demo where we build a container image and then use it as a disk for a virtual machine in OpenShift Virtualization (kubevirt) and KVM.


## thoughts for the demo

1. create the image using podman/buildah
1. convert the image to a disk format 
1. deploy a virtual machine using the disk image
1. test/profit


### bootc images

| OS | Image registry/tag |
|----|--------------------|
|Red Hat| registry.redhat.io/rhel9/rhel-bootc:9.4 |
|Fedora | fedora-bootc:40 |
