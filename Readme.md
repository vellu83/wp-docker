# Extremely fast and easy wordpress docker setup
## Features:
- Wordpress 5.9 PHP-FPM
- Php 8.1
- MariaDB 10.1
- SSL with certbot/ Lets Encrypt
- Nginx with FastCGI cache (tmpfs)
- Memcached
- sSMTP
- W3 total cache
- Webp converter

The main focus of this stack is to easily install performance optimized wordpress to any VPC

## Install:
- [Install docker](https://docs.docker.com/engine/install/) 
- and [docker-compose](https://docs.docker.com/compose/install/)
- git clone this repository
- vim or nano `.env.template` with your domain and other details
- rename env file: `mv .env.template .env`
- run: `docker-compose -f init.docker-compose.yml` this will fetch the ssl certificate and save it for nginx to use
(note that if you run docker-compose.yml before this step the nginx will fail because of missing ssl certification)
- if no errors on previous step, run: `docker-compose up`
- open your url in browser and complete wordpress setup
- activate W3 Total cache and WebP Converter plugins
- activate database cahing and object caching with memcached in W3 settings
- activate minificaton for js and css
- NOTE: do NOT activate page caching or browser caching, these are already taken care by Nginx
- Recommendation: if you are using elementor, disable google fonts to improve loading speed. This can be done by adding: `add_filter( 'elementor/frontend/print_google_fonts', '__return_false' );`
to child theme funtions.php

### Know issues:
- fastCGI cache not automatically purgin, I am working on this...
if you need to purge cache, it's possible to do manually: \
exec into nginx container:`sudo docker exec -it wp-nginx bash` \
delete files in cache directory: `rm -rf /var/run/nginx-cache/*`