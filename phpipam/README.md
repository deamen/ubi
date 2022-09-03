# phpipam container images

Build the following images for phpipam:
1. `ubi/phpipam-www` Frontend Apache/PHP container.
2. `ubi/phpipam-cron` cron container for scheduled network discovery jobs.

# To build the container:
```
podman build --squash ./ -f Dockerfile.phpipam-www -t phpipam-www
podman build --squash ./ -f Dockerfile.phpipam-cron -t phpipam-cron
```
# To run phpipam manually:
```bash
# Create pod
podman pod create --name phpipam-pod -p 8080:8080

# Create volume for mariadb
podman volume create phpipam-pod-mariadb-vol

# Prepare database credentials
source creds.sh 

# Start PHP/Apache frontend
podman run -d --pod phpipam-pod \
  --name phpipam-pod-www \
  --env IPAM_DATABASE_PASS \
  --env IPAM_DATABASE_USER="phpipam" \
  --env IPAM_DATABASE_HOST="127.0.0.1" \
  localhost/phpipam-www

# Startup database 
podman run -d --pod phpipam-pod \
  --name phpipam-pod-mariadb \
  --env MARIADB_USER="phpipam" \
  --env MARIADB_ROOT_PASSWORD \
  --env IPAM_DATABASE_PASS \
  -v phpipam-pod-mariadb-vol:/var/lib/mysql:Z \
  docker.io/library/mariadb:latest

# Optional, startup CRON jobs for auto discovery 
podman run -d --pod phpipam-pod \
  --name phpipam-pod-cron \
  --env IPAM_DATABASE_PASS \
  --env IPAM_DATABASE_USER="phpipam" \
  --env IPAM_DATABASE_HOST="127.0.0.1" \
  localhost/phpipam-cron 

```

# Prepare database before installation
```sql
mysql -u root -p
mysql> create database phpipam;
Query OK, 1 row affected (0.02 sec)

mysql>  grant ALL on phpipam.* to 'phpipam'@'localhost' identified by 'phpipam-pass';
Query OK, 0 rows affected (0.02 sec)

mysql> grant ALL on phpipam.* to 'phpipam'@'127.0.0.1' identified by 'phpipam-pass';
Query OK, 0 rows affected (0.00 sec)

mysql> exit
Bye
```

# To install phpipam:
Open http://127.0.0.1:8080, select automatic as usual, but click on advanced and uncheck all three options:

- [] Drop exisitng database - Drop existing database if it exists 
- [] Create database - Create new database 
- [] Create permissions - Set permissions to tables


[^1]: The database preparation is required due to
https://github.com/phpipam/phpipam/issues/1549#issuecomment-343477993


# To run phpipam with podman play kube:
1. Copy phpipam-kube.yml.j2 as phpipam-kube.yml
2. Replace {{ ipam_secret.db_password }} and {{ ipam_secret.db_root_password }} with secure passwords
3. Start the pod

```bash
podman play kube phpipam-kube.yml
```