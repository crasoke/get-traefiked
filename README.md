# Nextcloud and Vaultwarden with Traefik

This is a straightforward docker-compose file for setting up Nextcloud, Vaultwarden and your custom Blog using Traefik as a reverse proxy.

## Requirements

- Docker
- Docker Compose

## Quickstart

1. Update the email address in the docker-compose file.
2. Update the domain name for Nextcloud, Vaultwarden, and Blog in the docker-compose file.
3. Update the database passwords (`MYSQL_PASSWORD` and `MYSQL_ROOT_PASSWORD`) in the docker-compose file.
4. Create the `data/blog` folder and add your custom website or blog static files, for example, using [Hugo](https://gohugo.io/).
5. Run the following command to start the services:
   ```bash
   docker-compose up -d
   ```

## Remarks

- For testing, you can uncomment `--log.level=DEBUG` and `--certificatesresolvers.myresolver.acme.caserver=https://acme-staging-v02.api.letsencrypt.org/directory`. This is useful because Let's Encrypt has a rate limit of 50 certificate requests per week. [Learn more about rate limits here](https://letsencrypt.org/docs/rate-limits/).

- For configuring Vaultwarden, it's suggested to initially add a label to each container, such as `"traefik.http.middlewares.test-ipallowlist.ipallowlist.sourcerange=your_public_ip"`. This allows you to configure the services before restarting the containers without this label.

- Consider disabling signups in Vaultwarden after creating your account by setting `"signups_allowed": false` in the `data/vaultwarden/config.json`.

- Additionally, installing a log analyzer is recommended to enhance security. [Learn more about enhancing security with CrowdSec here](https://www.crowdsec.net/blog/enhance-docker-compose-security).