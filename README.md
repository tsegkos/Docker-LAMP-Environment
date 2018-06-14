# Installation
Requirements
- You need to have [Docker](https://docs.docker.com/engine/installation/) installed

Run in root folder,
~~~~
docker-compose build && docker-compose up -d
~~~~

Login to the container,
~~~~
docker exec -it server /bin/bash -c "TERM=$TERM exec bash"
~~~~

# Information

- Ubuntu Server 16.04
- PHP 7 & ionCube & Extensions
- MariaDB 10.3.7
- phpMyAdmin 4.8
- Redis 3.2

# More about project

- This project made only for Development use. I would recommend to make changes before use it as Production environment.
