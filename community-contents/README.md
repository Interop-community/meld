### Requirements

Docker Compose >= 2.17

DockerDesktop >= 4.29  (only applicable to DockerDesktop users)


### Setup

Two setup options: with local dns mapping and without. The setup without dns mapping requires host networking feature for dockerdesktop users. For linux users, host networking is enabled out-of-the-box in docker engine daemon.


| With local dns mapping  |
|-------------|
- Add the following dns mapping to `hosts`. please refer this [link](https://www.liquidweb.com/blog/edit-hosts-file-macos-windows-linux/) on how to add dns mapping to your system `hosts` file. 

```text
127.0.0.1  keycloak
127.0.0.1  auth
``` 
- #### Usage
```console
curl -o docker-compose.yml https://raw.githubusercontent.com/Interop-community/meld/main/community-contents/dockerdesktop-compose.yml
COMPOSE_PARALLEL_LIMIT=1 docker compose -f - -p meld up -d
```
---
<br/>

| Without local dns mapping  |
|-------------|
- Enable host networking as shown in capture below (only for dockerdektop users)

![DockerDesktop Setting](https://raw.githubusercontent.com/Interop-community/meld/main/community-contents/DockerDesktopSetting.png "https://docs.docker.com/network/drivers/host/#docker-desktop")

- #### Usage
```console 
curl -o docker-compose.yml https://raw.githubusercontent.com/Interop-community/meld/main/community-contents/docker-compose.yml
COMPOSE_PARALLEL_LIMIT=1 docker compose -f - -p meld up -d
```
---
<br/>

#### Once all the services are up, sandbox-manager ui will be available at &#8594; `localhost:3001` .


**COMPOSE_PARALLEL_LIMIT=1** is important to avoid `toomanyrequests` response from the container registry as ecr public gallery limits concurrent pull. [More on ECR Public service quota](https://docs.aws.amazon.com/AmazonECR/latest/public/public-service-quotas.html)

`docker-compose` has been deprecated in favor of `docker compose` (without hyphen, newer one). If you are still using docker-compose, please update your compose tool or adjust above command with docker-compose to make it work.




### Contact
please reach out Kendrah Baker [kendrah.baker@interoperabilityinstitute.org](mailto:kendrah.baker@interoperabilityinstitute.org) for questions regarding setup and services in the compose file.


-----


### Service Configuration (with host networking as default)

| Service              | Container Name       | Image                                                      | Ports          | Environment Variables                                                                                                                                                                                                 | Command | Extra Hosts                            | Hostname | Depends On                     | Volumes                       | Healthcheck                                                                                      |
|----------------------|-----------------------|------------------------------------------------------------|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|--------------------------------------|----------|------------------------------|-------------------------------|------------------------------------------------------------------------------------------------|
| `keycloak`           | `keycloak`            | `quay.io/keycloak/keycloak:legacy`                  | `8080:8080`    | `DB_VENDOR=mysql`<br>`DB_ADDR=127.0.0.1`<br>`DB_USER=root`<br>`DB_PASSWORD=password`<br>`KEYCLOAK_ADMIN=admin`<br>`KEYCLOAK_ADMIN_PASSWORD=admin`<br>`KEYCLOAK_IMPORT=/tmp/realm_properties.json`<br>`JAVA_OPTS_APPEND="-Dkeycloak.profile.feature.upload_scripts=enabled"` |         |                                      |          |       |       `keycloak:/opt/jboss/keycloak/standalone/data/`                        |                                                                                                |
| `dstu2`              | `dstu2`               | `public.ecr.aws/interoperabilityinstitute/meld/api:latest`  | `8078:8078`    | `SPRING_PROFILES_ACTIVE=dstu2,multitenant`<br>`API_PORT=8078`<br>`SANDBOX_NAME=hspc9`<br>`SBMA_HOST=localhost`<br>`SBMA_PORT=12000`<br>`DB_HOST=127.0.0.1`<br>`DB_PORT=3306`<br>`DB_USER=root`<br>`DB_PASSWORD=password`<br>`AUTH_HOST=localhost`<br>`AUTH_PORT=8060`<br>`MANIFEST_OVERRIDE=false`<br>`MESSAGING_OVERRIDE=true` |         |                                      |          | `mysql: service_healthy`      |                               |                                                                                                |
| `stu3`              | `stu3`                 | `public.ecr.aws/interoperabilityinstitute/meld/api:latest`  | `8079:8079`    | `SPRING_PROFILES_ACTIVE=stu3,multitenant`<br>`API_PORT=8079`<br>`SANDBOX_NAME=hspc9`<br>`SBMA_HOST=localhost`<br>`SBMA_PORT=12000`<br>`DB_HOST=127.0.0.1`<br>`DB_PORT=3306`<br>`DB_USER=root`<br>`DB_PASSWORD=password`<br>`AUTH_HOST=localhost`<br>`AUTH_PORT=8060`<br>`MANIFEST_OVERRIDE=false`<br>`MESSAGING_OVERRIDE=true` |         |                                      |          | `mysql: service_healthy`      |                               |                                                                                                |
| `r4`                | `r4`                  | `public.ecr.aws/interoperabilityinstitute/meld/api:latest` | `8070:8070`    | `SPRING_PROFILES_ACTIVE=r4,multitenant`<br>`API_PORT=8070`<br>`SANDBOX_NAME=hspc10`<br>`SBMA_HOST=localhost`<br>`SBMA_PORT=12000`<br>`DB_HOST=127.0.0.1`<br>`DB_PORT=3306`<br>`DB_USER=root`<br>`DB_PASSWORD=password`<br>`AUTH_HOST=localhost`<br>`AUTH_PORT=8060`<br>`MANIFEST_OVERRIDE=false` |         |                                      | `r4`     | `mysql: service_healthy`      |                               |                                                                                                |
| `r5`                | `r5`                  | `logicahealth/api`                                         | `8071:8071`    | `SPRING_PROFILES_ACTIVE=r5,multitenant`<br>`API_PORT=8071`<br>`SANDBOX_NAME=hspc10`<br>`SBMA_HOST=localhost`<br>`SBMA_PORT=12000`<br>`DB_HOST=127.0.0.1`<br>`DB_PORT=3306`<br>`DB_USER=root`<br>`DB_PASSWORD=password`<br>`AUTH_HOST=localhost`<br>`AUTH_PORT=8060`<br>`MANIFEST_OVERRIDE=false` |         |                                      |          | `mysql: service_healthy`      |                               |                                                                                                |
| `sandbox`           | `sandbox`            | `public.ecr.aws/interoperabilityinstitute/meld/sandbox-manager:local` | `3001:3000`    |                                                                                                                                                                                                                         |         |                                      |          | `stu3`                        |                               |                                                                                                |
| `ehr-app`           | `ehr-app`            | `public.ecr.aws/interoperabilityinstitute/meld/ehr-app`     | `3000:3000`    |                                                                                                                                                                                                                         |         |                                      |          |                              |                               |                                                                                                |
| `sandbox-manager-api` | `sandbox-manager-api` | `public.ecr.aws/interoperabilityinstitute/meld/sandbox-manager-api` | `12000:12000`  | `DB_HOST=mysql`<br>`DB_PORT=3306`<br>`DB_NAME=sandman`<br>`DB_USER=root`<br>`DB_PASSWORD=password`<br>`AUTH_HOST=host.docker.internal`<br>`AUTH_PORT=8060`<br>`API_DSTU2_HOST=host.docker.internal`<br>`API_STU3_HOST=host.docker.internal`<br>`API_R4_HOST=host.docker.internal`<br>`API_R5_HOST=host.docker.internal` |         | `host.docker.internal:host-gateway` |          | `mysql: service_healthy`      |                               |                                                                                                |
| `mysql`             | `mysql`               | `public.ecr.aws/interoperabilityinstitute/meld/meld-mysql`  | `3306:3306`    | `MYSQL_PASSWORD=password`<br>`MYSQL_ROOT_PASSWORD=password`                                                                                                                                                        |         |                                      |          |                              | `meld:/var/lib/mysql` | `test: ["CMD-SHELL", "/wait-for-db.sh"]`<br>`interval: 2s`<br>`timeout: 30s`<br>`retries: 20` |
| `auth`              | `reference-auth`      | `public.ecr.aws/interoperabilityinstitute/meld/auth:local`  | `8060:8060`    | `AUTH_HOST=localhost`<br>`AUTH_PORT=8060`<br>`DB_NAME=oic`<br>`DB_HOST=localhost`<br>`DB_USER=root`<br>`DB_PASSWORD=password`<br>`SHOW_SQL=true`<br>`API_DSTU2_HOST=localhost`<br>`API_DSTU2_PORT=8078`<br>`API_STU3_HOST=localhost`<br>`API_STU3_PORT=8079`<br>`API_R4_HOST=localhost`<br>`API_R4_PORT=8070`<br>`PATIENT_PICKER_HOST=localhost`<br>`PATIENT_PICKER_PORT=8094`<br>`SANDMAN_API_HOST=localhost`<br>`SANDMAN_API_PORT=12000`<br>`SPRING_PROFILES_ACTIVE=users-keycloak,local`<br>`KEYCLOAK_REALM=Development`<br>`KEYCLOAK_AUTH_SERVER_URL=http://localhost:8080/auth`<br>`KEYCLOAK_SSL_REQUIRED=none`<br>`KEYCLOAK_CREDENTIALS=593ada9c-b1dc-444f-a2e6-30cc4add8146`<br>`KEYCLOAK_LOGOUT_SUCCESS_URL=http://localhost:8080/auth/realms/Development/protocol/openid-connect/logout?redirect_uri=http://localhost:3000` |         | `host.docker.internal:host-gateway` |          | `mysql: service_healthy`      |                               |                                                                                                |
| `patient-data-manager` | `patient-data-manager` | `public.ecr.aws/interoperabilityinstitute/meld/patient-data-manager` | `8096:8096`    |                                                                                                                                                                                                                         |         |                                      |          |                              |                               |                                                                                                |
| `bilirubin-risk-chart`  | `bilirubin-risk-chart` | `public.ecr.aws/interoperabilityinstitute/meld/bilirubin-risk-chart` | `8086:8086`    |                                                                                                                                                                                                                         |         |                                      |          |                              |                               |                                                                                                |

### Detailed Breakdown

#### Environment Variables

**keycloak**

| Variable                    | Value                                         |
|----------------------------- |----------------------------------------------- |
| `DB_VENDOR`                 | `mysql`                                       |
| `DB_ADDR`                   | `127.0.0.1`                                   |
| `DB_USER`                   | `root`                                        |
| `DB_PASSWORD`               | `password`                                    |
| `KEYCLOAK_USER`            | `admin`                                       |
| `KEYCLOAK_PASSWORD`   | `admin`                                       |
| `KC_HTTP_ENABLED`           |  `true`
| `KEYCLOAK_IMPORT`           | `/tmp/realm_properties.json`                  |
| `JAVA_OPTS_APPEND`          | `"-Dkeycloak.profile.feature.upload_scripts=enabled"` |

**dstu2**

| Variable                  | Value                              |
|--------------------------- |------------------------------------ |
| `SPRING_PROFILES_ACTIVE`   | `dstu2,multitenant`                |
| `API_PORT`                 | `8078`                              |
| `SANDBOX_NAME`             | `hspc9`                             |
| `SBMA_HOST`                | `localhost`    or `sandbox-manager-api` for dockerdesktop                      |
| `SBMA_PORT`                | `12000`                             |
| `DB_HOST`                  | `127.0.0.1`    or `mysql` for dockerdesktop                     |
| `DB_PORT`                  | `3306`                              |
| `DB_USER`                  | `root`                              |
| `DB_PASSWORD`              | `password`                          |
| `AUTH_HOST`                | `localhost`    or `auth` for dockerdesktop                      |
| `AUTH_PORT`                | `8060`                              |
| `MANIFEST_OVERRIDE`        | `false`                             |
| `MESSAGING_OVERRIDE`       | `true`                              |

**stu3**

| Variable                  | Value                              |
|--------------------------- |------------------------------------ |
| `SPRING_PROFILES_ACTIVE`   | `stu3,multitenant`                 |
| `API_PORT`                 | `8079`                              |
| `SANDBOX_NAME`             | `hspc9`                             |
| `SBMA_HOST`                | `localhost`   or `sandbox-manager-api` for dockerdesktop                       |
| `SBMA_PORT`                | `12000`                             |
| `DB_HOST`                  | `127.0.0.1`    or `mysql` for dockerdesktop                        |
| `DB_PORT`                  | `3306`                              |
| `DB_USER`                  | `root`                              |
| `DB_PASSWORD`              | `password`                          |
| `AUTH_HOST`                | `localhost`   or `auth` for dockerdesktop                       |
| `AUTH_PORT`                | `8060`                              |
| `MANIFEST_OVERRIDE`        | `false`                             |
| `MESSAGING_OVERRIDE`       | `true`                              |

**r4**

| Variable                  | Value                              |
|--------------------------- |------------------------------------ |
| `SPRING_PROFILES_ACTIVE`   | `r4,multitenant`                   |
| `API_PORT`                 | `8070`                              |
| `SANDBOX_NAME`             | `hspc10`                            |
| `SBMA_HOST`                | `localhost`   or `sandbox-manager-api` for dockerdesktop                        |
| `SBMA_PORT`                | `12000`                             |
| `DB_HOST`                  | `127.0.0.1`    or `mysql` for dockerdesktop                       |
| `DB_PORT`                  | `3306`                              |
| `DB_USER`                  | `root`                              |
| `DB_PASSWORD`              | `password`                          |
| `AUTH_HOST`                | `localhost`   or `auth` for dockerdesktop                        |
| `AUTH_PORT`                | `8060`                              |
| `MANIFEST_OVERRIDE`        | `false`                             |

**r5**

| Variable                  | Value                              |
|--------------------------- |------------------------------------ |
| `SPRING_PROFILES_ACTIVE`   | `r5,multitenant`                   |
| `API_PORT`                 | `8071`                              |
| `SANDBOX_NAME`             | `hspc10`                            |
| `SBMA_HOST`                | `localhost`  or `sandbox-manager-api` for dockerdesktop                 |
| `SBMA_PORT`                | `12000`                             |
| `DB_HOST`                  | `127.0.0.1`   or `mysql` for dockerdesktop                        |
| `DB_PORT`                  | `3306`                              |
| `DB_USER`                  | `root`                              |
| `DB_PASSWORD`              | `password`                          |
| `AUTH_HOST`                | `localhost`   or `auth` for dockerdesktop                     |
| `AUTH_PORT`                | `8060`                              |
| `MANIFEST_OVERRIDE`        | `false`                             |

**sandbox-manager-api**

| Variable                  | Value                              |
|--------------------------- |------------------------------------ |
| `DB_HOST`                 | `mysql`                            |
| `DB_PORT`                 | `3306`                             |
| `DB_NAME`                 | `sandman`                          |
| `DB_USER`                 | `root`                             |
| `DB_PASSWORD`             | `password`                         |
| `AUTH_HOST`               | `host.docker.internal`   or `auth` for dockerdesktop          |
| `AUTH_PORT`               | `8060`                             |
| `API_DSTU2_HOST`          | `host.docker.internal`   or `dstu2` for dockerdesktop          |
| `API_STU3_HOST`           | `host.docker.internal`   or `stu3` for dockerdesktop          |
| `API_R4_HOST`             | `host.docker.internal`   or `r4` for dockerdesktop           |
| `API_R5_HOST`             | `host.docker.internal`   or `r5` for dockerdesktop           |

**mysql**

| Variable                  | Value                              |
|--------------------------- |------------------------------------ |
| `MYSQL_PASSWORD`          | `password`                         |
| `MYSQL_ROOT_PASSWORD`     | `password`                         |

**auth**

| Variable                  | Value                                                                                                                   |
|--------------------------- |-------------------------------------------------------------------------------------------------------------------------|
| `AUTH_HOST`                | `localhost`                                                                                                             |
| `AUTH_PORT`                | `8060`                                                                                                                  |
| `DB_NAME`                  | `oic`                                                                                                                   |
| `DB_HOST`                  | `localhost`        or `mysql` for dockerdesktop                                                                                                    |
| `DB_USER`                  | `root`                                                                                                                 |
| `DB_PASSWORD`              | `password`                                                                                                             |
| `SHOW_SQL`                 | `true`                                                                                                                  |
| `API_DSTU2_HOST`           | `localhost`                                                                                                            |
| `API_DSTU2_PORT`           | `8078`                                                                                                                 |
| `API_STU3_HOST`            | `localhost`                                                                                                            |
| `API_STU3_PORT`            | `8079`                                                                                                                 |
| `API_R4_HOST`              | `localhost`                                                                                                            |
| `API_R4_PORT`              | `8070`                                                                                                                 |
| `PATIENT_PICKER_HOST`     | `localhost`                                                                                                            |
| `PATIENT_PICKER_PORT`     | `8094`                                                                                                                 |
| `SANDMAN_API_HOST`        | `localhost`   or `sandbox-manager-api` for  dockerdesktop                                                              |
| `SANDMAN_API_PORT`        | `12000`                                                                                                                |
| `SPRING_PROFILES_ACTIVE`  | `users-keycloak,local`                                                                                                |
| `KEYCLOAK_REALM`          | `Development`                                                                                                          |
| `KEYCLOAK_AUTH_SERVER_URL`| `http://localhost:8080/auth`  or `http://keycloak:8080/auth` for dockerdesktop                                                                                          |
| `KEYCLOAK_SSL_REQUIRED`   | `none`                                                                                                                  |
| `KEYCLOAK_CREDENTIALS`    | `593ada9c-b1dc-444f-a2e6-30cc4add8146`                                                                                |
| `KEYCLOAK_LOGOUT_SUCCESS_URL` | `http://localhost:8080/auth/realms/Development/protocol/openid-connect/logout?redirect_uri=http://localhost:3001` or `http://keycloak:8080/auth/realms/Development/protocol/openid-connect/logout?redirect_uri=http://localhost:3001` for dockerdesktop |

**patient-data-manager**

| Variable  | Value |
|-----------|-------|
|           |       |

**bilirubin-risk-chart**

| Variable  | Value |
|-----------|-------|
|           |       |

### Command

**keycloak**

| Command |
|---------|
| `-Dkeycloak.profile.feature.upload_scripts=enabled` |

### Extra Hosts

**sandbox**

| Host                   | IP Address         |
|------------------------ |------------------- |
| `host.docker.internal` | `host-gateway`     |

**auth**

| Host                   | IP Address         |
|------------------------ |------------------- |
| `host.docker.internal` | `host-gateway`     |

### Hostname

**r4**

| Hostname |
|--------- |
| `r4`     |

### Depends On

**dstu2, stu3, r4**

| Service | Condition        |
|---------|------------------|
| `mysql` | `service_healthy`|

**sandbox-manager-api**

| Service | Condition        |
|---------|------------------|
| `mysql` | `service_healthy`|

### Volumes

**mysql**

| Volume Name           | Path                    |
|---------------------- |------------------------  |
| `meld-sandbox-volume` | `/var/lib/mysql57`       |

### Healthcheck

**mysql**

| Test Command               | Interval | Timeout | Retries |
|---------------------------  |---------- |--------- |---------|
| `["CMD-SHELL", "/wait-for-db.sh"]` | `2s`     | `30s`    | `20`     |

### Ports

| Service                | Container Port | Host Port |
|------------------------ |----------------|-----------|
| `keycloak`              | `8080`         | `8080`    |
| `dstu2`                 | `8078`         | `8078`    |
| `stu3`                  | `8079`         | `8079`    |
| `r4`                    | `8070`         | `8070`    |
| `r5`                    | `8071`         | `8071`    |
| `sandbox`               | `3000`         | `3001`    |
| `ehr-app`               | `3000`         | `3000`    |
| `sandbox-manager-api`   | `12000`        | `12000`   |
| `mysql`                 | `3306`         | `3306`    |
| `auth`                  | `8060`         | `8060`    |
| `patient-data-manager`  | `8096`         | `8096`    |
| `bilirubin-risk-chart`  | `8086`         | `8086`    |




**Disclaimer**:
Part of this document is generated by chatgpt 4.0 based on the compose file.