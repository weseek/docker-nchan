# docker-nchan
[NGINX](nginx.org/) + [Nchan](https://nchan.io/) Dockerfiles 

# Supported tags and respective `Dockerfile` links

-	[`1.0.1`, `latest`](https://github.com/weseek/docker-nchan/blob/9d21fd952fdb4b7153bf79aa25ac4bf431777204/mainline/Dockerfile)

# How to use this image

## Launch simply

```console
$ docker run -d weseek/nginx-nchan
```

- The container provides nginx on port 80 and the endpoint `/pubsub` to publish/subscribe
- See also: [default.conf](https://github.com/weseek/docker-nchan/blob/9d21fd952fdb4b7153bf79aa25ac4bf431777204/mainline/conf.d/default.conf)

Alternatively, a simple `Dockerfile` can be used to generate a new image that includes the necessary content (which is a much cleaner solution than the bind mount above):

```dockerfile
FROM nginx
COPY /your-conf-directory/default.conf /etc/nginx/conf.d/
```

Place this file in the same directory as your directory of content ("your-conf-directory"), run `docker build -t my-nginx-nchan .`, then start your container:

```console
$ docker run -d my-nginx-nchan
```

## Exposing external port

```console
$ docker run -d -p 8080:80 my-nginx-nchan
```

Then you can hit `http://localhost:8080` or `http://host-ip:8080` in your browser.

## Other configuration

Check the reference of [Official build of Nginx](https://hub.docker.com/_/nginx).
