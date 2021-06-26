# hydro-notes

This repository is my notes as I work on setting up some kind of automated
hydroponics. So far, it describes setting up Mycodo and a mqtt broker, and getting ssl certs
for the same.


## prereq installs

* git
* [docker-compose](https://docs.docker.com/compose/install/#install-compose-on-linux-systems)


## get the certs

Set up DNS records to point to the public ip of the server you will use. Then,
use certbot to generate certs for both mycodo and mqtt.

See the [/certbot-docker](/certbot-docker) folder in this repository.


## MyCodo

For visualizing sensor data. Eventually for controlling relays based on sensor input.
Data posted via MQTT.

mycodo server will be on mycodo.daveeargle.com

Using the ./docker/ images within https://github.com/kizniche/Mycodo.

Setup like this:

```bash
git clone https://github.com/kizniche/Mycodo
cd Mycodo/docker
sudo bash setup.sh dependencies
```

add the following two volumes to the nginx service in ./docker-compose.yml:

```
- /etc/letsencrypt/live/mycodo.daveeargle.com:/etc/letsencrypt/live/mycodo.daveeargle.com
- /etc/letsencrypt/archive/mycodo.daveeargle.com:/etc/letsencrypt/archive/mycodo.daveeargle.com
```

edit ./nginx/project.conf to point to your letsencrypt certs dir, like this:

```
ssl_certificate     /etc/letsencrypt/live/mycodo.daveeargle.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/mycodo.daveeargle.com/privkey.pem;
```

Spin up images:

```bash
docker-compose up -d
```


## MQTT Broker

I want a TLS-enabled MQTT Broker. But it's a pain in the butt
to get MCU do to secure MQTT posting. So I'm going to have MCUs communicate over
unencrypted traffic on a local network. Then the "local" MQTT broker will bridge
to the "secure" MQTT broker.

Secure broker will be on mqtt.daveeargle.com. On the same server as the mycodo one.

Using the eclipse-mosquitto docker image. See the [/mosquitto](/mosquitto) directory
in this repository.
