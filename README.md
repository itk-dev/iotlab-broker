# IOTLab broker

Simple [Scorpio Broker](https://scorpio.readthedocs.io/en/latest/) setup based on
<https://scorpio.readthedocs.io/en/latest/buildScorpio.html>.

We use [basic access authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) for (some) security, so
the first step is to create the [.htpasswd](https://en.wikipedia.org/wiki/.htpasswd) file
`.docker/scorpio/nginx/templates/.htpasswd`:

``` shell name=create-htpasswd
htpasswd -b -c .docker/scorpio/nginx/templates/.htpasswd iotlab this-is-really-not-a-good-password
```

We can now start the show:

``` shell name=start
docker compose pull
docker compose up --detach --wait
```

Call the API:

``` shell name=create-entity
curl --user iotlab:this-is-really-not-a-good-password --silent "http://$(docker compose port scorpio-nginx 8080)/ngsi-ld/v1/entities" --header 'content-type: application/json' --data @- <<'JSON' | docker run --rm -i ghcr.io/jqlang/jq:latest
{
   "type": "toilet",
   "id": "urn:ngsi-ld:toilet:001",
   "name": {"type": "Property", "value": "Floater …"}
}
JSON
```

``` shell name=get-entities
curl --user iotlab:this-is-really-not-a-good-password --silent "http://$(docker compose port scorpio-nginx 8080)/ngsi-ld/v1/entities?type=toilet" | docker run --rm -i ghcr.io/jqlang/jq:latest
```

## Production

Create/edit `.env.docker.local`:

``` dotenv
COMPOSE_PROJECT_NAME=iotlab-broker
COMPOSE_DOMAIN=iotlab-broker.srvitkiotlab.itkdev.dk
COMPOSE_FILES=docker-compose.yml,docker-compose.prod.yml
```

Create `.docker/scorpio/nginx/templates/.htpasswd`:

``` shell
htpasswd -c .docker/scorpio/nginx/templates/.htpasswd iotlab
```

``` shell
itkdev-docker-compose-server pull
itkdev-docker-compose-server up --detach --wait
```

Use the API on <https://iotlab-broker.srvitkiotlab.itkdev.dk/>, e.g.
<https://iotlab-broker.srvitkiotlab.itkdev.dk/ngsi-ld/v1/entities?type=toilet>.
