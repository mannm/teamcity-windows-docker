# teamcity-docker-compose
Compose to create working [TeamCity](https://www.jetbrains.com/teamcity/) server with SQLServer and Agents


# How to use

Clone this repository or download the zip.

## Configuration

First of all, you should set your Postgres username and password variables in `env.example` and rename `env.example` to `.env`

Don't push `.env` file in public repositories!

It's all. PostgreSQL already configured according to the
JetBrains [recommendations](https://confluence.jetbrains.com/pages/viewpage.action?pageId=74847395#HowTo...-ConfigureNewlyInstalledPostgreSQLServer)

## Build and setup

cd into folder

docker-compose build
```

Now you can start up the service and continue configuring settings in Web Interface:

```
docker-compose up
```



### Setup Teamcity

Open `https://yourdockerhost/` or `http://yourdockerhost:8111/`

Set SQL server as teamcity database
Configure DB connection
Authorize your Agent:

Done, TeamCity basically ready to work.

## Scaling

Scaling you workers (agents) supported as well. Just use `docker-compose scale` command:

```
docker-compose scale teamcity-agent=3
```
**Keep in mind: currently, agents are stateless**

# build new version
docker-compose build --pull --no-cache

# stop and remove old containers
docker-compose stop
docker-compose rm

# create and up new containers
docker-compose up -d
```

After an update, you need to reauthorize your agents.

### Updating maintenance

Sometimes, during update you may get «maintenance is required» message instead of login page. 
It's ok! To login in a maintenance mode you need to enter an authentication token. You may find it in the logs:
`docker-compose logs -f`

Try to find something like this:

```
teamcity-server_1                    | [TeamCity] Administrator can login from web UI using authentication token: 8755994969038184734
```