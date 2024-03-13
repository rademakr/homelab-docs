# Nextcloud

While services like Google Drive, Dropbox, and OneDrive are practical options for cloud storage, they can be limiting with their available disk space—making it challenging to host large files such as a music library. Nextcloud eliminates this limitation by enabling you to host your own cloud service that’s accessible from anywhere.

I utilize a Raspberry Pi 5 equipped with a [NVMe hat](https://www.amazon.fr/dp/B0CPLF6JYX?psc=1&ref=ppx_yo2ov_dt_b_product_details) and a [NVMe SSD 2TB drive](https://www.amazon.fr/dp/B0CP42QVT8?psc=1&ref=ppx_yo2ov_dt_b_product_details), thus ensuring ample disk space for my needs.

Prerequisites
-------------

<div class="annotate" markdown>

* A Raspberry PI 5 (or equivalent hardware) (1)
* Docker

</div>

1. Using a Raspberry Pi for this project is cost-effective, not only in terms of initial investment but also for ongoing power consumption, with only about 5W used when idle. However, if you already have a server, you can follow the same installation steps on that hardware and operating system.

Docker
------

Although Docker is a prerequisite, let’s briefly cover its installation, as we won’t be using the version that comes with the operating system’s packages. Instead, we’ll follow the installation guide provided on the [Docker website](https://docs.docker.com/engine/install/debian/).


Reverse proxy
-------------

To avoid exposing the Nextcloud instance directly and to keep ports 80 & 443 available for other services, a reverse proxy is essential. Despite being more familiar with Apache, in the spirit of learning, I chose Nginx, specifically the [Nginx Proxy Manager](https://github.com/NginxProxyManager/nginx-proxy-manager) project, which has a Docker image and offers an intuitive UI. This image is also available on [Docker Hub](https://hub.docker.com/r/jc21/nginx-proxy-manager).


Nextcloud
---------

Nextcloud offers several installation options and provides a [Docker image](https://hub.docker.com/_/nextcloud) alongside instructions for setting it up.


docker-compose.yml
------------------

Both Nginx and Nextcloud require seperate database containers.
We'll create one `docker-compose.yml` file to manage all four Docker images:

```yaml
version: '3'

volumes:
  nextcloud-data:
  nextcloud-db:
  npm-data:
  npm-ssl:
  npm-db:

networks:
  frontend:
    # add this if the network is arleardy existing
    # external: true
  backend:
    # external: true

services:
  nextcloud-app:
    image: nextcloud
    restart: unless-stopped
    volumes:
      - nextcloud-data:/var/www/html
    environment:
      - MYSQL_PASSWORD=secure_password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=nextcloud-db
    networks:
      - frontend
      - backend

  nextcloud-db:
    image: mariadb
    restart: unless-stopped
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - nextcloud-db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=very_secure_password
      - MYSQL_PASSWORD=secure_password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
    networks:
      - backend

  npm-app:
    image: jc21/nginx-proxy-manager:latest
    restart: unless-stopped
    ports:
      - "80:80"
      - "81:81"
      - "443:443"
    environment:
      - DB_MYSQL_HOST=npm-db
      - DB_MYSQL_PORT=3306
      - DB_MYSQL_USER=npm
      - DB_MYSQL_PASSWORD=another_secure_password
      - DB_MYSQL_NAME=npm
    volumes:
      - npm-data:/data
      - npm-ssl:/etc/letsencrypt
    networks:
      - frontend
      - backend

  npm-db:
    image: jc21/mariadb-aria:latest
    restart: unless-stopped
    environment:
      - MYSQL_ROOT_PASSWORD=yet_another_very_secure_password
      - MYSQL_DATABASE=npm
      - MYSQL_USER=npm
      - MYSQL_PASSWORD=yet_another_secure_password
    volumes:
      - npm-db:/var/lib/mysql
    networks:
      - backend
```

This `docker-compose.yml` file should be located in the `/opt/nextcloud` directory.


Starting up
-----------

To launch the services defined in the `docker-compose.yml` file, use the following command:

```bash
sudo docker compose up -d
```


Helpful docker commands:
------------------------

Retrieve logs from all containers:

<div class="annotate" markdown>
```
docker compose logs (1)
```
</div>

1. This will dump all the different containers' logs in your terminal. You can grep for a specific container is you wish or pipe it to `more`.

Connect to the Nextcloud container using bash:

<div class="annotate" markdown>
```
docker exec -it nextcloud-nextcloud-app-1 /bin/bash (1)
```
</div>

1. This will connect you to the nextcloud-app instance with a bash shell.

!!! note
    Not all docker images come with a bash shell packaged in. One of the DB instances running mariadb is running in alpine and you'll have to specify `sh`

To reset the Nextcloud admin password from the command line:

<div class="annotate" markdown>
```
docker exec -it -u www-data nextcloud-nextcloud-app-1 php /var/www/html/occ user:resetpassword admin (1)
```
</div>

1. If you forgot your admin password... the "admin" is the account you create once you connect to the Nextcloud web UI

Stopping all running containers:

<div class="annotate" markdown>
```
docker compose stop (1)
```
</div>

1. Very explicit command, but you can also append a specific container name.


Stop and remove all containers, networks and volumes:

```
docker compose down
```


Adding your user to the docker group:

<div class="annotate" markdown>
```
sudo usermod -aG docker rademakr (1)
```
</div>

1. Add you user account to the `docker` group. Avoids having to run `sudo` for (most) docker commands.

Essential details
-----------------

Configuration adjustments are necessary within the Nextcloud instance. Modify the `config/config.php` file to include your Fully Quqlidied Domain Name (FQDN) in the trusted domains array:


```py
'trusted_domains' =>
array (
  0 => '10.5.5.210:8080',
  1 => 'my.zen-cloud.net',
),
```

While editing `config/config.php`, also ensure these lines are present or uncommented to force the use of HTTPS:

```py
'overwrite.cli.url' => 'https://my.zen-cloud.net',
'overwriteprotocol' => 'https',
```

To automate updates for Docker containers, consider using Watchtower. The following command will run Watchtower once in debug mode:

```bash
docker run --name watchtower -v /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --run-once --debug
```


TODO
----

Verify that all necessary crontabs are in place, especially for managing Nginx and Let's Encrypt certificate renewals.
