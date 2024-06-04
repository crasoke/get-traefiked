# Set Up Nextcloud, Vaultwarden, Wireguard, Blog, Crowdsec with Traefik reverse proxy

This project provides straightforward `docker-compose` files for setting up:

1. [Nextcloud](https://nextcloud.com/), [Vaultwarden](https://github.com/dani-garcia/vaultwarden), [Wireguard](https://github.com/wg-easy/wg-easy), [Crowdsec](https://www.crowdsec.net/), and your custom Blog.
2. [Nextcloud](https://nextcloud.com/), [Vaultwarden](https://github.com/dani-garcia/vaultwarden), [Wireguard](https://github.com/wg-easy/wg-easy), and your custom Blog.

These setups use [Traefik](https://traefik.io/) as a reverse proxy.

## Requirements

- Docker
- Docker Compose

## Quickstart Default (With Crowdsec)

1. Create a [Crowdsec](https://www.crowdsec.net/) account and go to the [console](https://app.crowdsec.net). Copy the enroll key and insert it into the [.env](.env) file.
2. Fill in the rest of the information in the [.env](.env) file. An example is provided in the [.env.example](.env.example) file:
   ```bash
   EMAIL=max.mustermann@example.com
   DOMAIN=example.com
   VAULTWARDEN_ADMIN_PASSWORD=KDEcaaGUd9kCjmN623U2PMWjUwUqNrLJ
   MYSQL_ROOT_PASSWORD=RJnLKLWVT6uCGrVxqzc4bfew2CVUSDP7
   MYSQL_PASSWORD=coahwpXuLTRYhbrJY2UVqgrPri9hLJnE
   WG_PASSWORD=b2rVHQmvgaNHQDY9jNfRrbzVWKCLDQHy
   IPALLOWLIST=172.16.0.0/12
   CROWDSEC_LAPI_KEY=2abpGRXqQnq8KSaHgfFtdV/CnVVvWmU8cCZ2CDhgJZH
   CROWDSEC_ENROLL_KEY=gxyc3igakixgge23ei3bo4f6i
   ```
3. Insert your custom static blog/website in the [blog](data/blog/) folder, for example, using [Hugo](https://gohugo.io/).
4. Run the following command to start the services:
   ```bash
   docker-compose up -d
   ```

## Quickstart (Without Crowdsec)

1. cd into the [without-crowdsec](without-crowdsec/) folder.
2. Fill in the information in the [.env](without-crowdsec/.env) file. An example is provided in the [.env.example](without-crowdsec/.env.example) file.
3. Insert your custom static blog/website in the [blog](without-crowdsec/data/blog/) folder, for example, using [Hugo](https://gohugo.io/).
4. Run the following command in the [without-crowdsec](without-crowdsec/) folder to start the services:
   ```bash
   docker-compose up -d
   ```

## Remarks

- For testing, you can uncomment `--log.level=DEBUG` and `--certificatesresolvers.myresolver.acme.caserver=https://acme-staging-v02.api.letsencrypt.org/directory` in the docker-compose file. This is useful for debugging and because Let's Encrypt has a rate limit of 50 certificate requests per week. [Learn more about rate limits here](https://letsencrypt.org/docs/rate-limits/).
- For the initial configuration of Vaultwarden and Wireguard, it is recommended to change the `IPALLOWLIST` environment variable to your public IP address (from where you SSH into) and then change it back after the configuration is complete.
- For the initial configuration of Nextcloud, it is recommended to add the following labels so that the system can only be accessed from your IP address:
   ```yaml
   - "traefik.http.middlewares.test-ipallowlist.ipallowlist.sourcerange=your_public_ip"
   - "traefik.http.routers.nextcloud.middlewares=test-ipallowlist@docker"
   ```
- Consider disabling signups in Vaultwarden after creating your account by setting `"signups_allowed": false` in the `data/vaultwarden/config.json` or visiting the admin page (`bitwarden.example.com/admin`).
- It is recommended to use the version with [Crowdsec](https://www.crowdsec.net/) to enhance security.