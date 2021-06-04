# dockerfile

1. Dockerfile

FROM ubuntu:20.04

LABEL description = "Imagen para desarrollar con php"
LABEL mainteiner = "Diana Meneses"
LABEL version = "0.1"

RUN apt update 

RUN apt install -y php-fpm
RUN apt install -y php-sqlite3
RUN apt install -y sqlite3

2. Build Docker image

docker build -t php_sqlite:v1 .

3. Create docker container

docker run -it -v /workspace/dockerfile/volume:/volume -p 8080 --name docker_php -h php php_sqlite:v1

4. RUN PHP SERVER

$cd /volume
$php -$ 0.0.0.0:8080