# How to Upgrade a Postgres DB

Whenever there is a major release of PostgreSQL, you may run into the following issue
after an update:

!!! failure
    PostgreSQL Database directory appears to contain a database; skipping initialization  
    1969-01-01 00:00:00.505 UTC [1] FATAL: database files are incompatible with server  
    1969-01-01 00:00:00.505 UTC [1] DETAIL: The data directory was initialized by PostgreSQL version 13, which is not compatible with this version 14.0

In these cases you will need to initialize a new db using postgreSQL:latest and then import
the old records into the new database.  This guide assumes you are using docker-compose to manage
your database.

## Downgrading PostgreSQL Database

If you've run into the error above, then it's probably safe to assume your db is in bad shape.
When this happened to me, my container was in a permanently "restarting" state.  You can work around
this by downgrading PostgreSQL to the last good configuration:

- Edit your docker-compose file and specify the last good tag for your db image

```
image: postgres:13.4
```

- Then use `docker ps` to find the container ID of your running PostgreSQL instance
- Stop the container and remove the image
- Pull the db image for the newly added tag and recreate the container

```
$ docker container [container_id] stop
$ docker container prune
$ docker image prune -a
$ docker-compose pull [service_name]
$ docker-compose up --force-recreate -d [service_name]
```

!!! note
    _Once the container is up and running you can double check to see if your service(s) are working again_
    _I find this helpful to do before proceeding, just to make sure there isn't another issue to worry about_

## Make a Snapshot of the Database

After rolling back to a good state we're ready to start the upgrade.
The first thing to do is make a backup of the current database.  Before doing this, though,
you may want to stop any services that depend on your database.  Doing this makes sure
none of the data is changed while you're peforming updates!

- First, start by jumping into the docker container's shell:

```
$ docker exec -it [container_id] bash
root@[container_id]#/
```

- Create a sql dump from the root shell:

```
$ pg_dumpall -U [db_username] > /var/lib/postgresql/data/dump.sql
```

- Enter your (root) db password when prompted
- If all goes well you should see clean output.  Congrats, your data is now backed up to the dump.sql file we specified in our command

_`/var/lib/postgresql/data/` is the path to the docker volume.  If your path is different, make sure to change it._

- Stop the PostgreSQL container and move the `postgresql` folder to a backup location:

```
$ docker container [container_id] stop
$ sudo mv /var/lib/postgresql /home/$USER/
```

## Update PostgreSQL & Create a Fresh Database

- Update the PostgreSQL tag in your docker-compose file:

```
$ image: postgres:14
```

- Pull & recreate the db container:

```
$ docker-compose pull postgres
$ docker-compose up postgres --force-recreate -d
```

_This will recreate the container's volume and initialize a fresh database_

## Import the Data Snapshot into the New Database

- Copy the database backup into the refreshed docker volume
- Import the data into the newly set up database

```
$ sudo mv /home/$USER/postgresql/data/dump.sql /var/lib/postgresql/data/
$ docker exec -it [container_id] bash
$ psql -U [db_user] -d [db_name] < /var/lib/postgresql/data/dump.sql
```

If all goes well, you should be able to restart your services without any issues.
