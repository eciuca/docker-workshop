## Workshops

1. [Docker workshop](https://workshops.emanuelciuca.com/docker)
docker run --name mysqldb -e MYSQL_DATABASE=greetings -e MYSQL_ROOT_PASSWORD=greetings --network=greetings-network -d mysql:8

docker run -d -p 8080:8080 -e MYSQL_HOST=mysqldb --network=greetings-network --name crazy-cow greetings-ws:mysqldb
