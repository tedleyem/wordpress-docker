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
    # The /wp-app dir is where the full wordpress projects lives
    # to use a docker volume you can comment out  ./wp-app:/var/www/html
    # and uncomment the wp_app line under the wp image 
    # and the docker volumes wp_app under the volumes 
    volumes:
      - ./config/php.conf.ini:/usr/local/etc/php/conf.d/conf.ini
      - ./wp-app:/var/www/html # Full wordpress project 
      #- wp_app:/var/www/html  
  db:
    # We use a stable mysql image to prevent breaking database changes or updates
    image: mysql:5.7 
    # we create volumes to keep persistent data 
    volumes:
      - ./wp-data:/docker-entrypoint-initdb.d
      - db_data:/var/lib/mysql
    ... 
    # 
  volumes:
  db_data:
  #wp_app:
    ... 

```

When deploying this setup, docker compose maps the WordPress container port 80 to
port 80 of the host as specified in the compose file.

> ℹ️ **_INFO_**  
> For compatibility purpose between `AMD64` and `ARM64` architecture, we use a MariaDB as database instead of MySQL.  
> You still can use the MySQL image by uncommenting the following line in the Compose file   
> `#image: mysql:8.0.27`

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
5fbb4181a069        wordpress:latest    "docker-entrypoint.s…"   35 seconds ago      Up 34 seconds       0.0.0.0:80->80/tcp    wp
e0884a8d444d        mysql:8.0.19        "docker-entrypoint.s…"   35 seconds ago      Up 34 seconds       3306/tcp, 33060/tcp   db
```

Navigate to `http://localhost:80` in your web browser to access WordPress.
 

Stop and remove the containers

```
$ docker compose down
```

To remove all WordPress data, delete the named volumes by passing the `-v` parameter:
```
$ docker compose down -v
```
