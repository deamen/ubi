# buildbot-master container image
Build the salt-master image with redhat's ubi9/ubi-init image

# To build the container:
```
podman build ./ -f Dockerfile.salt-master -t salt-master
```
# To run salt-master:
```
podman run -d --name salt-master \ 
  -v /opt/podman/salt/salt:/srv/salt:Z \
  -v /opt/podman/salt/winrepo-ng:/srv/salt/win/repo-ng:Z \
  -v /opt/podman/salt/etc:/etc/salt:Z \
  -p 4505:4505 \
  -p 4506:4506 \
  quay.io/deamen/salt-master
```