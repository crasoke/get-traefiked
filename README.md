# Get TRAEFIKed

This project provides a `docker-compose` file for setting up a server with:

- [Nextcloud](https://nextcloud.com/)
- [Vaultwarden](https://github.com/dani-garcia/vaultwarden)
- [Wireguard](https://github.com/wg-easy/wg-easy)
- your custom Blog, for example, using [Hugo](https://gohugo.io/)

All those services are behind a [Traefik](https://traefik.io/) reverse proxy, as well as all public accessible sites are using [Crowdsec](https://www.crowdsec.net/) as IPS system.

## Requirements

- Docker
- Docker Compose

## Quickstart

1. Insert the necessary A Records in your Domain DNS configuration ([more information](https://www.cloudflare.com/learning/dns/dns-records/dns-a-record/)), for example:

| Subdomain               | Record Type | Value       | TTL   |
|-------------------------|-------------|-------------|-------|
| @ (example.com)         | A           | 8.8.8.8     | 14400 |
| cloud.example.com       | A           | 8.8.8.8     | 14400 |
| bitwarden.example.com   | A           | 8.8.8.8     | 14400 |
| wg.example.com          | A           | 8.8.8.8     | 14400 |
2. Create a [Crowdsec](https://www.crowdsec.net/) account 
3. Log into the [Crowdsec console](https://app.crowdsec.net), copy the [enrollment key](https://docs.crowdsec.net/u/getting_started/post_installation/console/#engines-page) and insert it into the [.env](.env) file (`CROWDSEC_ENROLL_KEY=...`).
4. Now generate the crowdsec API key for the LAPI. ([more info](https://plugins.traefik.io/plugins/6335346ca4caa9ddeffda116/crowdsec-bouncer-traefik-plugin))
   ```bash
   docker compose up -d crowdsec
   docker exec crowdsec cscli bouncers add crowdsecBouncer
   docker compose down crowdsec
   ```
   Insert the generated key into the [.env](.env) file (`CROWDSEC_LAPI_KEY=...`).


4. Fill in the rest of the information in the [.env](.env) file. An example is provided in the [.env.example](.env.example) file (consider adding your public IP to the IPALLOWLIST for setting up):
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
5. Insert your custom static blog/website in the [blog](data/blog/) folder, for example, using [Hugo](https://gohugo.io/).
6. Run the following command to start the services:
   ```bash
   docker-compose up -d
   ```

## Subdomain Map

- running the compose file, will spin up the following subdomains

| Subdomain                        | Service                   | Externally Reachable                                                   |
|----------------------------------|---------------------------|------------------------------------------------------------------------|
| `cloud.<your domain>`            | Nextcloud                 | No (except shared links via `/s/`)                                     |
| `bitwarden.<your domain>`        | Vaultwarden (Bitwarden)   | Yes (except admin interface at `/admin`)                               |
| `wg.<your domain>`               | WireGuard VPN             | No (web interface not reachable, but VPN port is exposed externally)   |
| `<your domain>`                  | Blog                      | Yes                                                                    |

## Services

- besides the services in the `docker-compose` file, the project has the file [examples.yml](examples.yml) which has some configuration for other services:
   - [OpenProject](https://www.openproject.org/)
   - TODO
- for them to run, just copy the service you want to run, and insert it in the `docker-compose` file (and if necessary add additional env vars)

## Remarks

- For testing, uncomment `--log.level=DEBUG` and `--certificatesresolvers.myresolver.acme.caserver=https://acme-staging-v02.api.letsencrypt.org/directory` lines in the docker-compose file. This is useful for debugging and because Let's Encrypt has a rate limit of 50 certificate requests per week. [more about rate limits here](https://letsencrypt.org/docs/rate-limits/).
- For the initial configuration of Vaultwarden, Wireguard and Nextcloud, it is recommended to change the `IPALLOWLIST` environment var to your public IP address (from where you SSH into) and then change it back after the configuration is complete.
- Consider disabling signups in Vaultwarden after creating your account by setting `"signups_allowed": false` in the `data/vaultwarden/config.json` or visiting the admin page (`bitwarden.<your_domain>/admin`).
- right now traefik uses the HTTP challange for certificate validation ([more info](https://letsencrypt.org/docs/challenge-types/)), in case you want to change it to another challange (for example if you want to have wildcard certificates), look [here](https://doc.traefik.io/traefik/https/acme/).