# Setting Up Calibre Web

use stateless service like fargate? kinda good to do on AWS because I already
have the domain there

manually run docker on ecs? but I really dislike ECS
EKS is too expensive
whatever just make a ec2 instance
- associate it with a elastic ip
- open ports 80, 433, and 8083

try it out locally first
books db will show as invalid unless there is a calibre db there

follow instructions here for docker setup on host: https://docs.mattermost.com/install/prod-docker.html

okay looks like we will use this letsencrypt setup
https://github.com/linuxserver/docker-letsencrypt

- added it to the docker-compose
- ensured access via library.computerlab.io:8083
- ran docker-compose up, this created the cert
- set up reverse proxy
  - see sample files in this dir
  - copied + renamed /letsencrypt-config/nginx/proxy-confs/calibre-web.subfolder.conf.sample, use / instead of /calibre-web
  - included this config in sites/default location directive (not the one at
    bottom of file) 



scp -i calibre-web.pem ~/Dropbox/resources/calibre/metadata.db ubuntu@library.computerlab.io:/home/ubuntu/lab-library/books


set up gdrive sync: follow instructions here: https://github.com/janeczku/calibre-web/wiki/Configuration
- use urdomain.com/calibre-web/gdrive/callback





