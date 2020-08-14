# docker-compose Cheatsheet
## Basic config example

```yml
# docker-compose.yml
version: '3'

services:
 
    frontend: 
        container_name: containername_frontend
        build: ./frontend
        restart: unless-stopped
        ports:
            - "3000:3000"
        volumes:
            - ./frontend/src:/usr/src/app/src

    db:
        container_name: containername_db
        image: mongo:latest
        restart: unless-stopped
        volumes:
            - mongodb_api:/data/db
    
    volumes:
        mongodb_api:
```

## Common commands

```bash
# Builds, (re)creates, starts, and attaches to containers for a service
docker-compose up
docker-compose up -d                  # Background detached mode
docker-compose up --build             # Forcefully Build images before starting containers
docker-compose up --no-build          # Skips the image build process
docker-compose up --force-recreate    # Recreate containers even if their configuration
                                      # and image haven’t changed
docker-compose -f filename.yml -f filenamelocal.yml up    # Use custom yml files

# Stops containers and removes containers, networks, volumes, and images created by up
docker-compose down

# Starts existing containers
docker-compose start

# Stops running containers without removing them
docker-compose stop
docker-compose stop -t    # Specify a shutdown timeout in seconds (default 10)

# Restart containers
docker-compose restart

# Pauses running containers of a service
docker-compose pause

# Unpauses paused containers of a service
docker-compose unpause

# Shows list of containers for a service
docker-compose ps       # Use docker-compose.yml
docker-compose ps -q    # Only display IDs

# Prints the version of docker-compose
docker-compose version

# Validate and view the Compose file
docker-compose config

# View the processes running within each service container
docker-compose top

# Pulls an image associated with a service defined in a docker-compose.yml file,
# but does not start containers based on those images 
docker-compose pull

# Pushes images for services to their respective registry/repository
docker-compose push

# Removes stopped service containers. By default, anonymous volumes attached to containers
#  are not removed. You can override this with -v. To list all volumes, use docker volume ls
docker-compose rm
docker-compose rm -f    # Don’t ask to confirm the removal
docker-compose rm -s    # Stop the containers, if required, before removing
docker-compose rm -v    # Remove any anonymous volumes attached to containers
```

## Config file reference

### Building

```yml
web:
  # build from Dockerfile
  build: .
  # build from custom Dockerfile
  build:
    context: ./dir
    dockerfile: Dockerfile.dev
  # build from image
  image: ubuntu
  image: ubuntu:14.04
  image: tutum/influxdb
  image: example-registry:4000/postgresql
  image: a4bc65fd
```

### Ports

```yml
ports:
  - "3000"
  - "8000:80"  # guest:host
# expose ports to linked services (not to host)
expose: ["3000"]
```

### Commands

```yml
# command to execute
command: bundle exec thin -p 3000
command: [bundle, exec, thin, -p, 3000]

# override the entrypoint
entrypoint: /app/start.sh
entrypoint: [php, -d, vendor/bin/phpunit]
```

### Environment variables

```yml
# environment vars
environment:
  RACK_ENV: development
environment:
  - RACK_ENV=development

# environment vars from file
env_file: .env
env_file: [.env, .development.env]
```

### Dependencies

```yml
# makes the `db` service available as the hostname `database`
# (implies depends_on)
links:
  - db:database
  - redis

# make sure `db` is alive before starting
depends_on:
  - db
```

### Other options

```yml
# make this service extend another
extends:
  file: common.yml  # optional
  service: webapp
volumes:
  - /var/lib/mysql
  - ./_data:/var/lib/mysql
```

## Advanced features

### Labels

```yml
services:
  web:
    labels:
      com.example.description: "Accounting web app"
```

### DNS servers

```yml
services:
  web:
    dns: 8.8.8.8
    dns:
      - 8.8.8.8
      - 8.8.4.4
```

### Devices

```yml
services:
  web:
    devices:
      - "/dev/ttyUSB0:/dev/ttyUSB0"
```

### External links

```yml
services:
  web:
    external_links:
      - redis_1
      - project_db_1:mysql
```

### Hosts

```yml
services:
  web:
    extra_hosts:
      - "somehost:192.168.1.100"
```

### Network

```yml
# creates a custom network called `frontend`
networks:
  frontend:
```

### External network

```yml
# join a preexisting network
networks:
  default:
    external:
      name: frontend
```

## References

<https://devhints.io/docker-compose>

<https://cheatography.com/gauravpandey44/cheat-sheets/docker-compose>