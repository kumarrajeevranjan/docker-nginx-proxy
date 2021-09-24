# WordPress VIP Go development for Docker

This repo provides a Docker-based network proxy for WordPress VIP Go
development. It is required to run WordPress VIP Go projects with [Docker for Wordpress VIP Go](https://github.com/kumarrajeevranjan/docker-wpcomvip).

## Set up

1. Clone or fork this repo.

2. Add `project.local` to your `/etc/hosts` file, where `project` is your desired domain name:

   ```
   127.0.0.1 localhost project.local
   ```
   Repeat this for every concurrent project you want to run under this nginx based proxy, e.g:
   ```
   127.0.0.1 localhost project.local
   127.0.0.1 localhost anotherproject.local
   ```

3. Copy `sample.env` to `.env` to provide domain names (same as in step 1), one domain per line.

4. Run `./bin/setup.sh`.

5. Run `docker network create nginx-proxy`.

6. Run `docker-compose up -d`.

6. Use ([Docker for Wordpress VIP Go](https://github.com/kumarrajeevranjan/docker-wpcomvip) to setup VIP Go projects.

## HTTPS support

This repo provide HTTPS support out of the box. The setup script generates
self-signed certificates for the domain specified in `.env`. To enforce the use
of HTTPS, comment out (or remove) `HTTPS_METHOD: "nohttps"` from the
`services/proxy/environment` section of `docker-compose.yml` in your VIP Go project folder.

You may wish to add the generated root certificate to your system’s trusted root
certificates. This will allow you to browse your dev environment over HTTPS
without accepting a browser security warning. On OS X:

```sh
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain certs/ca-root/ca.crt
```

## Troubleshooting

If your stack is not responding, the most likely cause is that a container has
stopped or failed to start. Check to see if all of the containers are "Up":

```
docker-compose ps
```

If not, inspect the logs for that container:

```
docker-compose logs nginx-proxy
```

If your self-signed certs have expired (`ERR_CERT_DATE_INVALID`), simply delete
the `certs/self-signed` directory, stop docker-compose, run `./bin/setup.sh`, and restart
the stack.

If you wish to remove this external network, run:

```
docker-compose down && docker network prune
```
