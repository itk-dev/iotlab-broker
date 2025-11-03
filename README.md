# IOTLab broker

Simple [Scorpio Broker](https://scorpio.readthedocs.io/en/latest/) setup based on
<https://scorpio.readthedocs.io/en/latest/buildScorpio.html>.

Start the show:

``` shell
docker compose pull
docker compose up --detach --wait
```

Call the API:

``` shell name=get-entities
curl "http://$(docker compose port scorpio 9090)/ngsi-ld/v1/entities?type=toilet"
```

``` shell name=create-entity
curl "http://$(docker compose port scorpio 9090)/ngsi-ld/v1/entities" --header 'content-type: application/json' --data @- <<'JSON'
{
   "type": "toilet",
   "id": "urn:ngsi-ld:toilet:001",
   "name": {"type": "Property", "value": "Floater …"}
}
JSON
```

## Production

Create/edit `.env.docker.local`:

``` dotenv
COMPOSE_PROJECT_NAME=iotlab-broker
COMPOSE_DOMAIN=iotlab-broker.srvitkiotlab.itkdev.dk
COMPOSE_FILES=docker-compose.yml,docker-compose.prod.yml
```

``` shell
itkdev-docker-compose-server pull
itkdev-docker-compose-server up --detach --wait
```

Use the API on <https://iotlab-broker.srvitkiotlab.itkdev.dk/>, e.g.
<https://iotlab-broker.srvitkiotlab.itkdev.dk/ngsi-ld/v1/entities?type=toilet>.
