# certbot-docker

Adds docker to certbot's image, so that the deploy-hook can restart other docker
containers.

To use:
* specify the domain in the `-d` arg
* save cloudflare credentials to file ~/.secrets/certbot/credentials.ini in form:
  ```
  dns_cloudflare_api_token = GR0F-xxxxxxxxxxxxx
  ```
* choose your 'deploy hook'

Example run command:

```bash

docker-compose run certbot certonly \
        --dns-cloudflare \
        --dns-cloudflare-credentials /secrets/certbot/cloudflare.ini \
        -d domain.example.com \
        -n --agree-tos -m me@example.com \
        --deploy-hook 'docker restart example-container-name'
```

Remember to schedule a cron job to renew your certs:

```
export CERTBOT_DOCKER_DIR=/root/certbot-docker

SLEEPTIME=$(awk 'BEGIN{srand(); print int(rand()*(3600+1))}'); echo "0 0,12 * * * root sleep $SLEEPTIME && cd ${CERTBOT_DOCKER_DIR} && docker-compose run certbot renew -q" | sudo tee -a /etc/crontab > /dev/null
```
