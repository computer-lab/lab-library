# Setting Up Calibre Web

### Cloud Setup

I wanted to use a stateless platform as a service (ex: Fargate on AWS) but I've
found that working with file storage / databases on a stateless service and
dealing with the load balancers for an n=1 service increases complexity and
costs. EKS + ECS are also expensive and would causes lock-in to proprietary
tech, but I still want to use docker.

Solution: just make a ec2 instance and wing it installing docker.
- associate it with a elastic ip
- open ports 80, 433, and 8083

Try it out locally first, commit this git repo and pull it to the ECS server.

Follow instructions [here](https://docs.mattermost.com/install/prod-docker.html)
for docker and docker-compose setup on host, then run `docker-compose up` in
this directory.

The books db will show as invalid unless there is a calibre db there

Okay looks like we will use [this letsencrypt setup](https://github.com/linuxserver/docker-letsencrypt)

- added it to the docker-compose
- ensured access via library.computerlab.io:8083
- ran docker-compose up, this created the cert
- set up reverse proxy
  - see sample files in this dir
  - copied + renamed /letsencrypt-config/nginx/proxy-confs/calibre-web.subfolder.conf.sample, use / instead of /calibre-web
  - included this config in sites/default location directive (not the one at bottom of file) 

scp -i calibre-web.pem ~/Dropbox/resources/calibre/metadata.db ubuntu@library.computerlab.io:/home/ubuntu/lab-library/books

I tried out the [Google Drive sync feature](https://github.com/janeczku/calibre-web/wiki/Configuration) built into calibre-web, but it was
quite buggy and also slow. Then I tried out syncing the books via Dropbox, but
it seemed difficult to set up robust group access to the files without also allowing
access to the rest of my Dropbox files.

I finally tried out using keybase, which worked quite nicely. With keybase,
there's also the added bonus of the files being encrypted, and streamed to the
device on request. This would allow hundreds of gigabytes to be "stored" on a
device with limited disk space.


One of the features of calibre-web is being able to email epubs to your kindle.
I set up email using email setup: using gmail SMTP relay.

### Setup on Raspberry Pi

In making my docker setup, I stupidly failed to consider the fact that the
Raspberry Pi is an ARM device, and therefore I can't simply copy my
docker-compose file to the Pi and then run `docker-compose up` and have things
work.

The first problem I ran into was the lack of a install script / binary for
`docker-compose` on arm7l. I had to create a virtualenv and install
docker-compose via pip to get it to work.
