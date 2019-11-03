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


