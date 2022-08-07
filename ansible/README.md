# ansible container image
Build the ansible image with redhat's ubi9/ubi-minimal image

# To build the container:
```
podman build ./ -f Dockerfile.ansible -t ansible
```
# To run ansible:
```
podman run -it --rm  ghcr.io/deamen/ubi/ansible /bin/bash

ansible-playbook test.yml
```
