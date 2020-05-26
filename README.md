# Setting Up Calibre Web

## Current Admin Setup Notes

0. Get ssh key and .env file from Computer Lab Monitor

1. ssh into library server

2. install docker, clone repo, scp .env file into repo directory

3. `docker-compose up -d`

4. set up letsencrypt to work with calibre-web:
    - `cp ./letsencrypt-config/nginx/proxy-confs/calibre-web.subdomain.conf.sample ./letsencrypt-config/nginx/proxy-confs/calibre-web.subdomain.conf`
    - modify `./letsencrypt-config/nginx/site-confs/default` to use the subdomain conf, comment out main server block

5. to update: `docker-compose pull`

6. If the keybase service doesn't gracefully stop, you may get an error when starting the service again.
To fix, manually umount the host mount point. See "Caveats" [here](https://github.com/waldner/keybase-kbfs-docker).

```
fusermount -u /home/ubuntu/lab-library/kbfs
```

## Old Notes

### Cloud Setup

I wanted to use a stateless platform as a service (ex: Fargate on AWS) but I've
found that working with file storage / databases on a stateless service and
dealing with the load balancers for an n=1 service increases complexity and
costs. EKS + ECS are also expensive and would causes lock-in to proprietary
tech, but I still want to use docker.

Solution: just make a ec2 instance and wing it installing docker.
- associate it with a elastic ip
- open ports 80, 433, and 8083

Try it out locally first, commit this git repo and pull it to the EC2 server.

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

#### Note on KBFS mount (from keybase-kbfs-docker repo README)

If you're exporting the KBFS mount back to the host and the container crashes
without cleaning up, you might have to manually umount (ie `fusermount -u
/home/you/kbfs`) the host mount point.

One of the features of calibre-web is being able to email epubs to your kindle.
I set up email using email setup: using gmail SMTP relay.
This ended up breaking on reboot.

https://support.google.com/a/answer/2956491?hl=en

## Old Notes

### Setup on Raspberry Pi

In making my docker setup, I stupidly failed to consider the fact that the
Raspberry Pi is an ARM device, and therefore I can't simply copy my
docker-compose file to the Pi and then run `docker-compose up` and have things
work.

The first problem I ran into was the lack of a install script / binary for
`docker-compose` on arm7l. I had to create a virtualenv and install
docker-compose via pip to get it to work.

Then, I looked at the calibre-web docker image and, while they supported arm,
the convert function only works on x86. This, and the effort that I anticipated
modifying the Keybase image to work on arm, inspired me to look for an x86
platform for the library.

After considering ordering a [$35 Intel Compute Stick](https://www.pcgamer.com/the-original-intel-compute-stick-is-just-dollar35-right-now/),
I looked around my office and noticed a Chromebook that Google Cloud Platform
had sent as a promotion. Was it an x86book? It is!


### Setup on Chromebook


