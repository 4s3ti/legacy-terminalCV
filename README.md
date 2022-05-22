# ARCHIVED

This repository has been archived, will no longer be updated. 

This project is now going to be continued here: https://github.com/4s3t/terminalcv

# terminalCV
[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)

A Fun command line CV for IT Professionals and enthusiasts.
Feel free to use it and modify it to suit your needs.

Forked from https://github.com/danielhauck/terminalCV this is probably the only active and up to date fork of this project.


# watch it live
See it in action right here: https://cv.4s3ti.net

# Usage
terminalCV is a little python script that uses jinja2 to render a html-template with javascript stuff in it.
After it is rendered, you just need to upload it to your webroot or subdirectory

## Install Dependencies
	$  git clone git@github.com:4s3ti/terminalCV.git && cd terminalCV
	$  sudo pip install -r requirements.txt

## Make it your own
Modify the about.yml with your favorite text editor,
All supported variables are in the current file, the ones I am not using will be Commented, if you want to use them just uncomment.

## Render the website
And finally render the stuff:

	$  python render.py

## Publish it
just upload the directory www to your web-server, e.g:

	$  rsync -aH www/ user@webhost:/srv/www/terminalCV


### Publish as a docker image
The Dockerfile I provide its just an example, I have it in a way that works with my environment, in a docker stack behind traefik reverse proxy, follows example on how you can use it.

Follow all the steps above but instead of publishing it to your webserver build the docker image:

	$  docker build -t youregistery/terminalCV:tag .
	$  docker push youregistery/temrinalCV:tag .

#### Run Docker image
nginx is serving the site in port 80 on the docker container.

	$  docker run -tid -p <HostPort>:80 youregistery/terminalCV:tag

test it by accessing to `localhost:<HostPort>`

#### Example on a docker stack with traefik
```
version: '3.3'

services:
  terminalCV:
    image: 4s3ti/websites:cv
    networks:
      - public
      - private
    deploy:
      placement:
        constraints:
          - node.role == worker
      replicas: 2
      update_config:
        parallelism: 1
        delay: 10s
      restart_policy:
        condition: on-failure
      labels:
        - "traefik.enable=true"
        - "traefik.backend=websites"
        - "traefik.cv.port=80"
        - "traefik.docker.network=dockadmin_rp"
        - "traefik.frontend.rule=Host:cv.4s3ti.net"
        - "traefik.frontend.headers.browserxssfilter=true"
        - "traefik.frontend.headers.customframeoptionsvalue=sameorigin"
        - "traefik.frontend.headers.contenttypenosniff=true"
        - "traefik.frontend.headers.ContentTypeNoSniff=true"
        - "traefik.frontend.headers.forceSTSHeader=true"
        - "traefik.frontend.headers.STSSeconds=15552000"
        - "traefik.frontend.headers.STSIncludeSubdomains=true"
        - "traefik.frontend.headers.STSPreload=true"

networks:
  public:
    external: true
  private:

```
Public network should be an overlay network where traefik will be listening.
Please read [traefik](https://docs.traefik.io/) documentation on how to do it, don't ask for any support this matter.

# Jquery-Terminal
terminalCV makes use of J. Cubics JQuery-Terminal http://terminal.jcubic.pl/ .
Thanks for this!
