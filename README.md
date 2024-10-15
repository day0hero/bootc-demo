# boot-c demo

Bootable Container demo where we build a container image and then use it as a disk for a virtual machine in OpenShift Virtualization (kubevirt) and KVM.


## thoughts for the demo

1. create the image using podman/buildah
1. convert the image to a disk format 
1. deploy a virtual machine using the disk image
1. create disk image using kickstart and lvm configured
1. test/profit


### bootc images

| OS | Image registry/tag |
|----|--------------------|
|Red Hat| registry.redhat.io/rhel9/rhel-bootc:9.4 |
|Fedora | quay.io/fedora/fedora-bootc:40 |


### Required Packages on build host
- virt-install
- bootc
- container-tools 

### Building the container
`podman build -t localhost/cloudinit-bootc:latest .`

### Creating a qcow2 image
```shell
sudo podman run \
--rm \
-it \
--privileged \
--pull=newer \
--security-opt label=type:unconfined_t \
-v $(pwd)/config.toml:/config.toml:ro \
-v $(pwd)/output:/output \
-v /var/lib/containers/storage:/var/lib/containers/storage \
registry.redhat.io/rhel9/bootc-image-builder:latest  \
--local --type qcow2 \
localhost/cloudinit-bootc:latest
```

### Creating an AMI 
```shell
sudo podman run \
--rm \
-it \
--privileged \
--pull=newer \
-v $HOME/.aws:/root/.aws:ro \
--env AWS_PROFILE=default \
registry.redhat.io/rhel9/bootc-image-builder:latest \
--type ami \
--aws-ami-name rhel-bootc-x86 \
--aws-bucket rhel-bootc-bucket \
--aws-region us-east-1 \
quay.io/<namespace>/<image>:<tag>
```

Omitting `--local` and `-v /var/lib/containers/storage` options will require the image to be public. 


Fedora/CentOS bootc-image-builder [bootc-image-builder](https://github.com/osbuild/bootc-image-builder)

podman-bootc CLI for Fedora40: 

`sudo dnf -y install 'dnf-command(copr)'`

`sudo dnf -y copr enable gmaglione/podman-bootc`

`sudo dnf install -y podman-bootc`

### Resources
- Fedora podman-bootc Docs: https://github.com/containers/podman-bootc
- Fedora bootc Docs: https://docs.fedoraproject.org/en-US/bootc/
