# Docker compose files

**These files are used in our test harness and are not intended for production.**

If you want to use these as a starting point then each consecutive file used will map over the previous one.  So if in
we are starting the dockwer compose with:

    docker-compose -f docker-compose.base.yml -f docker-compose.apache.dev.yml -f docker-compose.mysql.yml up

Then docker compose will open each compose file in order and the resulting effective docker-compose.yml will be:

```yaml
version: '3.5'
services:

    # Apache with sqlite, the dev/evaluate option
    kimai:
        image: kimai/kimai2:apache-debian-$KIMAI-dev # Added from docker-compose.apache.dev.yml
        environment:
            - APP_ENV=dev
            - TRUSTED_HOSTS=localhost
            - ADMINMAIL=admin@kimai.local
            - ADMINPASS=changemeplease
            - DATABASE_URL=mysql://kimaiuser:kimaipassword@sqldb/kimai #  Added from docker-compose.mysql.yml
        volumes:
            - /opt/kimai/var
        ports:
            - 8001:8001
        restart: unless-stopped
        healthcheck:
            test: curl -s -o /dev/null http://localhost:8001 || exit 1
            interval: 20s
            start_period: 10s
            timeout: 10s
            retries: 3

    # All the DB settings come from docker-compose.mysql.yml
    sqldb:
        image: mysql:5.7
        environment:
            - MYSQL_DATABASE=kimai
            - MYSQL_USER=kimaiuser
            - MYSQL_PASSWORD=kimaipassword
            - MYSQL_ROOT_PASSWORD=changemeplease
        volumes:
            - /var/lib/mysql
        command: --default-storage-engine innodb
        restart: unless-stopped
        healthcheck:
            test: mysqladmin -pchangemeplease ping -h localhost
            interval: 20s
            start_period: 10s
            timeout: 10s
            retries: 3
```

## Reset and clean

```sh
docker-compose stop && docker-compose rm && docker volume prune
```

## Apache/Kimai dev/sqlite

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.apache.dev.yml up
```

## Apache/Kimai prod/sqlite

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.apache.prod.yml up
```

## Apache/Kimai dev/mysql

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.apache.dev.yml -f docker-compose.mysql.yml up
```

## Apache/Kimai prod/mysql

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.apache.prod.yml -f docker-compose.mysql.yml up
```

## FPM/Kimai dev/sqlite

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.fpm.dev.yml -f docker-compose.nginx.yml up
```

## FPM/Kimai prod/sqlite

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.fpm.prod.yml -f docker-compose.nginx.yml up
```

## FPM/Kimai dev/mysql

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.fpm.dev.yml -f docker-compose.nginx.yml -f docker-compose.mysql.yml up
```

## FPM/Kimai prod/mysql

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.fpm.prod.yml -f docker-compose.nginx.yml -f docker-compose.mysql.yml up
```

## Apache/Kimai dev/sqlite/LDAP

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.apache.dev.yml -f docker-compose.ldap.yml up
```

## FPM/Kimai dev/sqlite/LDAP

```sh
docker-compose -f docker-compose.base.yml -f docker-compose.fpm.dev.yml -f docker-compose.nginx.yml -f docker-compose.ldap.yml up
```
