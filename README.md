# nfrastack/container-postgres

## About

This will build a container image for [PostgreSQL](https://postgres.org),A relational database.

Features:

- Customizable Super user account and password
- Multiple database and user creation
- Extension Support
- Replication (Main, Secondary, Snapshot) support
- Monitoring support via Zabbix Agent 2
- Customizable paths for logs, config, data

## Maintainer

- [Nfrastack](https://www.nfrastack.com)

## Table of Contents

- [About](#about)
- [Maintainer](#maintainer)
- [Table of Contents](#table-of-contents)
- [Installation](#installation)
  - [Prebuilt Images](#prebuilt-images)
  - [Quick Start](#quick-start)
  - [Persistent Storage](#persistent-storage)
- [Environment Variables](#environment-variables)
  - [Base Images used](#base-images-used)
  - [Core Configuration](#core-configuration)
  - [Container Options](#container-options)
  - [Server Options](#server-options)
  - [Database Options](#database-options)
  - [Replication Options](#replication-options)
  - [Monitoring Options](#monitoring-options)
- [Users and Groups](#users-and-groups)
  - [Networking](#networking)
- [Maintenance](#maintenance)
  - [Shell Access](#shell-access)
- [Support & Maintenance](#support--maintenance)
- [License](#license)

## Installation

### Prebuilt Images
Feature limited builds of the image are available on the [Github Container Registry](https://github.com/nfrastack/container-postgres/pkgs/container/container-postgres) and [Docker Hub](https://hub.docker.com/r/nfrastack/postgres).

To unlock advanced features, one must provide a code to be able to change specific environment variables from defaults. Support the development to gain access to a code.

To get access to the image use your container orchestrator to pull from the following locations:

```
ghcr.io/nfrastack/container-postgres:(image_tag)
docker.io/nfrastack/postgres:(image_tag)
```

Image tag syntax is:

`<image>:<branch>-<optional tag>-<optional_distribution>_<optional_distribution_variant>`

Example:

`ghcr.io/nfrastack/container-postgres:16` or

`ghcr.io/nfrastack/container-postgres:latest` or

`ghcr.io/nfrastack/container-postgres:16-1.0` or


* `latest` will be the most recent postgresql version and commit
* `branch` will be the repositories branch, typically matching with the version of Postgres eg `16`
* An optional `tag` may exist that matches the [CHANGELOG](CHANGELOG.md) - These are the safest
* If there are multiple distribution variations it may include a version - see the registry for availability

Have a look at the container registries and see what tags are available.

#### Multi-Architecture Support

Images are built for `amd64` by default, with optional support for `arm64` and other architectures.

### Quick Start

* The quickest way to get started is using [docker-compose](https://docs.docker.com/compose/). See the examples folder for a working [compose.yml](examples/compose.yml) that can be modified for your use.

* Map [persistent storage](#persistent-storage) for access to configuration and data files for backup.
* Set various [environment variables](#environment-variables) to understand the capabilities of this image.

### Persistent Storage

The following directories are used for configuration and can be mapped for persistent storage.

| Directory | Description                           |
| --------- | ------------------------------------- |
| `/certs/` | (optional) Drop TLS Certificates here |
| `/data/`  | Databases                             |
| `/logs/`  | Logfiles                              |

### Environment Variables

#### Base Images used

This image relies on a customized base image in order to work.
Be sure to view the following repositories to understand all the customizable options:

| Image                                                   | Description |
| ------------------------------------------------------- | ----------- |
| [OS Base](https://github.com/nfrastack/container-base/) | Base Image  |

Below is the complete list of available options that can be used to customize your installation.

* Variables showing an 'x' under the `Advanced` column can only be set if the containers advanced functionality is enabled.

#### Core Configuration

#### Container Options

| Parameter                    | Description                                                                                                | Default                       |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------- | ----------------------------- |
| `CERT_PATH`                  | Certificates location                                                                                      | `/certs/`                     |
| `CONFIG_PATH`                | Custom location for configuration e.g `/config/` - Drop custom .conf files here                            |                               |
| `CONFIG_MODE`                | Configuration mode `CORE` `DEFAULT` - To be used at a later release                                        | `CORE`                        |
| `DATA_PATH`                  | Database storage                                                                                           | `/data/`                      |
| `HBA_FILE`                   | Host based access file name                                                                                | `/etc/postgres/pg_hba.conf`   |
| `IDENT_FILE`                 | Identity file name                                                                                         | `/etc/postgres/pg_ident.conf` |
| `LOG_FILE`                   | Logfile name                                                                                               | `postgresql.log`              |
| `LOG_FORMAT`                 | Log format `NORMAL` `JSON` or `CSV` Filename extension will change from `.log` to either `.json` or `.csv` | `NORMAL`                      |
| `LOG_LEVEL`                  | Log level messages                                                                                         | `WARNING`                     |
|                              | Values can be in descending detail `DEBUG5`,`DEBUG4`,`DEBUG3`,`DEBUG2`,`DEBUG1`,                           |                               |
|                              | `INFO`,`NOTICE`,`WARNING`,`ERROR`,`LOG`,`FATAL`,`PANIC`                                                    |                               |
| `LOG_LEVEL_ERROR_STATEMENTS` | Log level for errors                                                                                       | `ERROR`                       |
| `LOG_PATH`                   | Store log files here                                                                                       | `/logs/`                      |
| `LOG_TYPE`                   | Log Type `CONSOLE` or `FILE`                                                                               | `FILE`                        |
| `SETUP_MODE`                 | `AUTO` generate configuration files based on env vars                                                      | `AUTO`                        |
| `WAL_PATH`                   | Write ahead log path if needing to be seperate from `DATA_PATH`                                            |                               |

#### Server Options

These options are related to overall server operations. Those bracketed with `(init)` cannot be changed after first run.

| Parameter               | Description                                      | Default    | `_FILE` |
| ----------------------- | ------------------------------------------------ | ---------- | ------- |
| `ENABLE_DATA_CHECKSUMS` | (init) Enable Data Checksumming                  | `FALSE`    |         |
| `INITDB_ARGS`           | Send arguments to initdb function                |            |         |
| `INITDB_ENCODING`       | (init) DB Encoding                               | `UTF-8`    |         |
| `INITDB_LC_COLLATE`     | (init) Locale Collation                          | `C`        |         |
| `INITDB_LC_CTYPE`       | (init) Locale CType                              | `C`        |         |
| `INITDB_LOCALE`         | (init) Locale                                    | `en`       |         |
| `LISTEN_IP`             | Listen Interface                                 | `*`        |         |
| `LISTEN_PORT`           | Listening Port                                   | `5432`     |         |
| `MAX_CONNECTIONS`       | Maximum concurrent connections to accept         | `100`      |         |
| `SERVER_ARGS`           | Send arguments to main Postgresql server process |            |         |
| `SUPERUSER_PASS`        | Password for `postgres` super user account       | ``         | x       |
| `SUPERUSER_USER`        | Name of super user account                       | `postgres` | x       |
| `WAL_SEGMENT_SIZE_MB`   | (init) Write ahead log segment size in MB        | `16`       |         |


#### Database Options

Automatically create user databases on startup. This can be done on each container start, and then removed on subsequent starts if desired.

| Parameter      | Description                                   | Default | `_FILE` |
| -------------- | --------------------------------------------- | ------- | ------- |
| `CREATE_DB`    | Automatically create databases on startup     | `TRUE`  | x       |
| `DB_NAME`      | Database Name e.g. `database`                 |         | x       |
| `DB_USER`      | Database User e.g. `user`                     |         | x       |
| `DB_PASS`      | Database Pass e.g. `password`                 |         | x       |
| `DB_EXTENSION` | (optional) Database Extension e.g. `unaccent` |         | x       |

**OR**

Create multiple databases and different usernames and passwords to access. You can share usernames and passwords for multiple databases by using the same user and password in each entry.

| Parameter        | Description                                        | Default | `_FILE` | Advanced |
| ---------------- | -------------------------------------------------- | ------- | ------- | -------- |
| `DB01_NAME`      | First Database Name e.g. `database1`               |         | x       |          |
| `DB01_USER`      | First Database User e.g. `user1`                   |         | x       |          |
| `DB01_PASS`      | First Database Pass e.g. `password1`               |         | x       |          |
| `DB01_EXTENSION` | (optional) Database Extension e.g. `unaccent`      |         | x       |          |
| `DB02_NAME`      | Second Database Name e.g. `database1`              |         | x       |          |
| `DB02_USER`      | Second Database User e.g. `user2`                  |         | x       |          |
| `DB02_PASS`      | Second Database Pass e.g. `password2`              |         | x       |          |
| `DB02_EXTENSION` | (optional) Database Extension e.g. `unaccent`      |         |         |          |
| `DBXX_...`       | As above, should be able to go all the way to `99` |         |         | x        |

Advanced mode allows you to maange more than 3 databases.

#### Replication Options

Enable replication from a `main` provider to a `secondary` read only node or a one time `snapshot` that can be used for read write later on.

| Parameter              | Description                                                 | Default     | `_FILE` |
| ---------------------- | ----------------------------------------------------------- | ----------- | ------- |
| `ENABLE_REPLICATION`   | Enable Replication Functionality                            | `FALSE`     |         |
| `REPLICATION_IP_ALLOW` | (main) Allow connections from this IP                       | `0.0.0.0/0` |         |
| `REPLICATION_MODE`     | Replication Mode `main`,`secondary`,`snapshot`              | `main`      |         |
| `REPLICATION_USER`     | (main/secondary/snapshot) Replication User                  | `replicate` | x       |
| `REPLICATION_HOST`     | (secondary/snapshot) Hostname of Replication Main server    |             | x       |
| `REPLICATION_PASS`     | (main/secondary/snapshot) Password of Replication User      |             | x       |
| `REPLICATION_PORT`     | (secondary/snapshot) Port number of Replication Main server | `5432`      | x       |
| `REPLICATION_TLS_MODE` | Replication TLS Mode                                        | `prefer`    |         |

#### Monitoring Options

- Zabbix Monitoring only at this time

| Parameter                     | Description                      | Default       | `FILE` |
| ----------------------------- | -------------------------------- | ------------- | ------ |
| `CONTAINER_ENABLE_MONITORING` | Enable Zabbix Agent 2 Monitoring | `TRUE`        |        |
| `MONITOR_USER`                | Monitoring User                  | `zbx_monitor` | x      |
| `MONITOR_PASS`                | Monitoring Password              | `zabbix`      | x      |

## Users and Groups

| Type  | Name       | ID  |
| ----- | ---------- | --- |
| User  | `postgres` | 70  |
| Group | `postgres` | 70  |

### Networking

| Port   | Protocol | Description     |
| ------ | -------- | --------------- |
| `5432` | tcp      | Postgres Server |

* * *

## Maintenance

### Shell Access

For debugging and maintenance, `bash` and `sh` are available in the container.

## Support & Maintenance

- For community help, tips, and community discussions, visit the [Discussions board](/discussions).
- For personalized support or a support agreement, see [Nfrastack Support](https://nfrastack.com/).
- To report bugs, submit a [Bug Report](issues/new). Usage questions will be closed as not-a-bug.
- Feature requests are welcome, but not guaranteed. For prioritized development, consider a support agreement.
- Updates are best-effort, with priority given to active production use and support agreements.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

