# buildbot-master container image
Build the ubi-buildbot-master image with redhat's ubi9/ubi-minimal image

# To build the container:
```
podman build ./ -f Dockerfile.buildbot-master -t buildbot-master
```
# To run buildbot-master:
```
podman run -d --name buildbot \ 
  -v /opt/podman/buildbot:/buildbot_pyvenv/buildmaster:Z \
  -p 9989:9989 \
  -p 8010:8010 \
  ghcr.io/deamen/ubi/buildbot-master

```