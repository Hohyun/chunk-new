# How to install ffmpeg -- Add to Dockerfile

RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs
RUN apt-get install -y ffmpeg

mkdir -p www/htdocs
echo '<?php echo "hello from php";' > www/htdocs/index.php

docker run -d -it \
 --name php \
 -v $(pwd)/www:/var/www/default \
 devilbox/php-fpm:5.3-prod

docker run -d -it \
 -p 8080:80 \
 -v $(pwd)/www:/var/www/default \
 -e MAIN_VHOST_BACKEND='conf:phpfpm:tcp:php:9000' \
 --link php \
 devilbox/nginx-mainline
