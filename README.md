# dockerfile

docker pull ubuntu:20.04
docker run -p 8080:8080 --rm -v $(pwd):$(pwd) php:7.4-cli php -S 0.0.0.0:8080 $(pwd)/index.php
