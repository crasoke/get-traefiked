# Nextcloud, Vaultwarden and Wireguard with Traefik

This is a straightforward docker-compose file for setting up [Nextcloud](https://nextcloud.com/), [Vaultwarden](https://github.com/dani-garcia/vaultwarden), [Wireguard](https://github.com/wg-easy/wg-easy) and your custom Blog using Traefik as a reverse proxy.

## Requirements

- Docker
- Docker Compose

## Quickstart

1. Fill in information in the `.env` file
2. Create the `data/blog` folder and add your custom website or blog static files, for example, using [Hugo](https://gohugo.io/).
3. Run the following command to start the services:
   ```bash
   docker-compose up -d
   ```

## Remarks

- For testing, you can uncomment `--log.level=DEBUG` and `--certificatesresolvers.myresolver.acme.caserver=https://acme-staging-v02.api.letsencrypt.org/directory`. This is useful because Let's Encrypt has a rate limit of 50 certificate requests per week. [Learn more about rate limits here](https://letsencrypt.org/docs/rate-limits/).

- For the initial configuration of Vaultwarden and Wiregurard, it is recommended to change the `IPALLOWLIST` environment variable to your public IP address and then change it back after the configuration is complete.

- For the initial configuration of Nextcloud, it is recommended to add the following labels so that the system can only be accessed from your IP address:
   ```yml
   - "traefik.http.middlewares.test-ipallowlist.ipallowlist.sourcerange=your_public_ip"
   - "traefik.http.routers.nextcloud.middlewares=test-ipallowlist@docker"
   ```

- Consider disabling signups in Vaultwarden after creating your account by setting `"signups_allowed": false` in the `data/vaultwarden/config.json` or visiting the admin page (`/admin`).

- Additionally, installing a log analyzer is recommended to enhance security. [Learn more about enhancing security with CrowdSec here](https://www.crowdsec.net/blog/enhance-docker-compose-security).