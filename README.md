# wordpress-docker with mysql
Used for wordpress development in docker. 

You can spin up a clean wordpress instance using this repo
as a template.
 
This example defines one of the basic setups for WordPress. More details on how this works can be found on the official [WordPress image page](https://hub.docker.com/_/wordpress).


Project structure:
```
.
├── config
├── docker-compose.yml
├── export.sh
├── LICENSE
├── README.md
├── wp-app
└── wp-data
```

[docker-compose.yaml_](docker-compose.yml)
```
services:
  wp:
    image: wordpress:latest
    ports:
      - 80:80
    restart: always 
    volumes:
      - ./config/php.conf.ini:/usr/local/etc/php/conf.d/conf.ini
      - ./wp-app:/var/www/html # Full wordpress project  
  db:
    # We use a stable mysql image to prevent breaking database changes or updates
    image: mysql:5.7 
    # we create volumes to keep persistent data 
    volumes:
      - ./wp-data:/docker-entrypoint-initdb.d
      - db_data:/var/lib/mysql
    ...  
    ... 

```

When deploying this setup, docker compose maps the WordPress container port 80 to
port 80 of the host as specified in the compose file.

> **_INFO_**    
> We use a MySQL image as the database for wordpress.
> The /wp-app dir is where the full wordpress projects lives
> to use a docker volume you can comment out  ./wp-app:/var/www/html
> and uncomment the wp_app line under the wp image 
> and the docker volumes wp_app under the volumes    
> `line 11: #- wp-app:/var/www/html `
> `line 59: #wp_app:`
> We use phpMyAdmin for controlling the mysql database through a GUI 
> phpMyAdmin is a free software tool written in PHP, intended to handle the administration of MySQL over the Web. 
## Deploy with docker compose

```
$ docker compose up -d
Creating network "wordpress-docker_dfault" with the default driver
Creating volume "wordpress-docker_db_data" with default driver
... 


## Expected result

Check containers are running and the port mapping:
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES 
1e85262a2715   phpmyadmin:latest   "/docker-entrypoint.…"   16 seconds ago   Up 14 seconds   0.0.0.0:8090->80/tcp, :::8090->80/tcp                                                      pma
fac13c98df19   wordpress:latest    "docker-entrypoint.s…"   16 seconds ago   Up 14 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp                                                          wp
b79974b2c634   mysql:5.7           "docker-entrypoint.s…"   19 seconds ago   Up 15 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 0.0.0.0:33060->33060/tcp, :::33060->33060/tcp   db
```

Navigate to `http://localhost:80` in your web browser to access WordPress.

Navigate to `http://localhost:8090` in your web browser to access PhpMyAdmin.
 

Stop and remove the containers

```
$ docker compose down
```

To remove all WordPress data, delete the named volumes by passing the `-v` parameter:
```
$ docker compose down -v
```
