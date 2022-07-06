# ubi-ansible container image
Build the ubi-ansible image with redhat's ubi9/ubi-minimal image

# To build the container:
```
podman build -f ubi-ansible -t ubi-ansible
```
# To run ansible:
```
podman run -it --rm --name ansible localhost/ubi-ansible /bin/bash
cd /ansible
ansible-playbook test.yml
```
