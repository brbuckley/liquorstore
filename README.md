# kong demo

## Description
ToDo: Intro + link to TCC

## Usage
ToDo: Maybe remove if not using Kong

The project is built as a docker compose file, so it should be really easy to run. After you've authenticated your gitlab account, just `docker-compose up` (`sudo` might be needed)

Once everything is up and running you can start to configure your API Gateway, first lets create a service for CustomerMS

```
curl --location --request POST 'http://localhost:8001/services' \
--header 'Content-Type: application/json' \
--data-raw '{
	"url" : "http://customer:8080",
	"name" : "customer-ms",
	"retries" : 5,
	"connect_timeout" : 60000,
	"write_timeout" : 60000,
	"read_timeout" : 60000
}'
```

Then we need to create a route to expose this service to the outside world

```
curl --location --request POST 'http://localhost:8001/services/customer-ms/routes' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "customer-ms-routes",
    "paths": ["/"],
    "strip_path": true
}'
```

Now we can try to reach our exposed endpoint and make and Order. But remember to get an authorization token first.

```
curl --location --request POST 'http://localhost:8000/CST0000001/orders' \
--header 'Authorization: Bearer <your.token>' \
--header 'Content-Type: application/json' \
--data-raw '{
  "items": [
    {
      "quantity": 1,
      "product": {
        "id": "PRD0000001"
      }
    },
    {
      "quantity": 2,
      "product": {
        "id": "PRD0000002"
      }
    },
    {
      "quantity": 3,
      "product": {
        "id": "PRD0000003"
      }
    },
    {
      "quantity": 4,
      "product": {
        "id": "PRD0000004"
      }
    }
  ]
}'
```

We can then start configuring this service and adding plugins

```
curl --location --request POST 'http://localhost:8001/services/customer-ms/plugins' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name":"ip-restriction",
    "config":{
        "allow": ["my.ip.address"]
    }
}'
```