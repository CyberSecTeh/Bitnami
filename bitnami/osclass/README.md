# Bitnami package for Osclass

## Deprecation Notice

The Osclass container will no longer be released in our catalog, but already released container images will still persist in the registries.

## What is Osclass?

> Osclass allows you to easily create a classifieds site without any technical knowledge. It provides support for presenting general ads or specialized ads, is customizable, extensible and multilingual.

[Overview of Osclass](https://osclass-classifieds.com)
Trademarks: This software listing is packaged by Bitnami. The respective trademarks mentioned in the offering are owned by the respective companies, and use of them does not imply any affiliation or endorsement.

## TL;DR

```console
docker run --name osclass bitnami/osclass:latest
```

**Warning**: This quick setup is only intended for development environments. You are encouraged to change the insecure default credentials and check out the available configuration options in the [Environment Variables](#environment-variables) section for a more secure d
eployment.

## Why use Bitnami Images?

* Bitnami closely tracks upstream source changes and promptly publishes new versions of this image using our automated systems.
* With Bitnami images the latest bug fixes and features are available as soon as possible.
* Bitnami containers, virtual machines and cloud images use the same components and configuration approach - making it easy to switch between formats based on your project needs.
* All our images are based on [**minideb**](https://github.com/bitnami/minideb) -a minimalist Debian based container image that gives you a small base container image and the familiarity of a leading Linux distribution- or **scratch** -an explicitly empty image-.
* All Bitnami images available in Docker Hub are signed with [Docker Content Trust (DCT)](https://docs.docker.com/engine/security/trust/content_trust/). You can use `DOCKER_CONTENT_TRUST=1` to verify the integrity of the images.
* Bitnami container images are released on a regular basis with the latest distribution packages available.

Looking to use Osclass in production? Try [VMware Tanzu Application Catalog](https://bitnami.com/enterprise), the enterprise edition of Bitnami Application Catalog.

## Why use a non-root container?

Non-root container images add an extra layer of security and are generally recommended for production environments. However, because they run as a non-root user, privileged tasks are typically off-limits. Learn more about non-root containers [in our docs](https://docs.bitnami.com/tutorials/work-with-non-root-containers/).

## How to deploy Osclass in Kubernetes?

Deploying Bitnami applications as Helm Charts is the easiest way to get started with our applications on Kubernetes. Read more about the installation in the [Bitnami Osclass Chart GitHub repository](https://github.com/bitnami/charts/tree/master/bitnami/osclass).

Bitnami containers can be used with [Kubeapps](https://kubeapps.dev/) for deployment and management of Helm Charts in clusters.

## Supported tags and respective `Dockerfile` links

Learn more about the Bitnami tagging policy and the difference between rolling tags and immutable tags [in our documentation page](https://docs.bitnami.com/tutorials/understand-rolling-tags-containers/).

You can see the equivalence between the different tags by taking a look at the `tags-info.yaml` file present in the branch folder, i.e `bitnami/ASSET/BRANCH/DISTRO/tags-info.yaml`.

Subscribe to project updates by watching the [bitnami/containers GitHub repo](https://github.com/bitnami/containers).

## Get this image

The recommended way to get the Bitnami Osclass Docker Image is to pull the prebuilt image from the [Docker Hub Registry](https://hub.docker.com/r/bitnami/osclass).

```console
docker pull bitnami/osclass:latest
```

To use a specific version, you can pull a versioned tag. You can view the [list of available versions](https://hub.docker.com/r/bitnami/osclass/tags/) in the Docker Hub Registry.

```console
docker pull bitnami/osclass:[TAG]
```

If you wish, you can also build the image yourself by cloning the repository, changing to the directory containing the Dockerfile and executing the `docker build` command. Remember to replace the `APP`, `VERSION` and `OPERATING-SYSTEM` path placeholders in the example command below with the correct values.

```console
git clone https://github.com/bitnami/containers.git
cd bitnami/APP/VERSION/OPERATING-SYSTEM
docker build -t bitnami/APP:latest .
```

## How to use this image

Osclass requires access to a MySQL or MariaDB database to store information. We'll use the [Bitnami Docker Image for MariaDB](https://github.com/bitnami/containers/tree/main/bitnami/mariadb) for the database requirements.

### Using the Docker Command Line

#### Step 1: Create a network

```console
docker network create osclass-network
```

#### Step 2: Create a volume for MariaDB persistence and create a MariaDB container

```console
$ docker volume create --name mariadb_data
docker run -d --name mariadb \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_osclass \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_osclass \
  --network osclass-network \
  --volume mariadb_data:/bitnami/mariadb \
  bitnami/mariadb:latest
```

#### Step 3: Create volumes for Osclass persistence and launch the container

```console
$ docker volume create --name osclass_data
docker run -d --name osclass \
  -p 8080:8080 -p 8443:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env OSCLASS_DATABASE_USER=bn_osclass \
  --env OSCLASS_DATABASE_PASSWORD=bitnami \
  --env OSCLASS_DATABASE_NAME=bitnami_osclass \
  --network osclass-network \
  --volume osclass_data:/bitnami/osclass \
  bitnami/osclass:latest
```

Access your application at `http://your-ip/`

### Run the application using Docker Compose

```console
curl -sSL https://raw.githubusercontent.com/bitnami/containers/main/bitnami/osclass/docker-compose.yml > docker-compose.yml
docker-compose up -d
```

Please be aware this file has not undergone internal testing. Consequently, we advise its use exclusively for development or testing purposes. For production-ready deployments, we highly recommend utilizing its associated [Bitnami Helm chart](https://github.com/bitnami/charts/tree/main/bitnami/osclass).

If you detect any issue in the `docker-compose.yaml` file, feel free to report it or contribute with a fix by following our [Contributing Guidelines](https://github.com/bitnami/containers/blob/main/CONTRIBUTING.md).

## Persisting your application

If you remove the container all your data will be lost, and the next time you run the image the database will be reinitialized. To avoid this loss of data, you should mount a volume that will persist even after the container is removed.

For persistence you should mount a directory at the `/bitnami/osclass` path. If the mounted directory is empty, it will be initialized on the first run. Additionally you should [mount a volume for persistence of the MariaDB data](https://github.com/bitnami/containers/blob/main/bitnami/mariadb#persisting-your-database).

The above examples define the Docker volumes named `mariadb_data` and `osclass_data`. The Osclass application state will persist as long as volumes are not removed.

To avoid inadvertent removal of volumes, you can [mount host directories as data volumes](https://docs.docker.com/engine/tutorials/dockervolumes/). Alternatively you can make use of volume plugins to host the volume data.

### Mount host directories as data volumes with Docker Compose

This requires a minor change to the [`docker-compose.yml`](https://github.com/bitnami/containers/blob/main/bitnami/osclass/docker-compose.yml) file present in this repository:

```diff
   mariadb:
     ...
     volumes:
-      - 'mariadb_data:/bitnami/mariadb'
+      - /path/to/mariadb-persistence:/bitnami/mariadb
   ...
   osclass:
     ...
     volumes:
-      - 'osclass_data:/bitnami/osclass'
+      - /path/to/osclass-persistence:/bitnami/osclass
   ...
-volumes:
-  mariadb_data:
-    driver: local
-  osclass_data:
-    driver: local
```

> NOTE: As this is a non-root container, the mounted files and directories must have the proper permissions for the UID `1001`.

### Mount host directories as data volumes using the Docker command line

#### Step 1: Create a network (if it does not exist)

```console
docker network create osclass-network
```

#### Step 2. Create a MariaDB container with host volume

```console
docker run -d --name mariadb \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_osclass \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_osclass \
  --network osclass-network \
  --volume /path/to/mariadb-persistence:/bitnami/mariadb \
  bitnami/mariadb:latest
```

#### Step 3. Create the Osclass container with host volumes

```console
docker run -d --name osclass \
  -p 8080:8080 -p 8443:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env OSCLASS_DATABASE_USER=bn_osclass \
  --env OSCLASS_DATABASE_PASSWORD=bitnami \
  --env OSCLASS_DATABASE_NAME=bitnami_osclass \
  --network osclass-network \
  --volume /path/to/osclass-persistence:/bitnami/osclass \
  bitnami/osclass:latest
```

## Configuration

### Environment variables

#### Customizable environment variables

| Name                           | Description                                                                                                                   | Default Value                                                                                                  |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `OSCLASS_DATA_TO_PERSIST`      | Files to persist relative to the Osclass installation directory. To provide multiple values, separate them with a whitespace. | `config.php oc-content/uploads oc-content/downloads oc-content/languages oc-content/plugins oc-content/themes` |
| `OSCLASS_USERNAME`             | Osclass user name.                                                                                                            | `user`                                                                                                         |
| `OSCLASS_PASSWORD`             | Osclass user password.                                                                                                        | `bitnami`                                                                                                      |
| `OSCLASS_EMAIL`                | Osclass user e-mail address.                                                                                                  | `user@example.com`                                                                                             |
| `OSCLASS_WEB_TITLE`            | Osclass web title.                                                                                                            | `Sample Web Page`                                                                                              |
| `OSCLASS_DATABASE_HOST`        | Database server host.                                                                                                         | `$OSCLASS_DEFAULT_DATABASE_HOST`                                                                               |
| `OSCLASS_DATABASE_PORT_NUMBER` | Database server port.                                                                                                         | `3306`                                                                                                         |
| `OSCLASS_DATABASE_NAME`        | Database name.                                                                                                                | `bitnami_osclass`                                                                                              |
| `OSCLASS_DATABASE_USER`        | Database user name.                                                                                                           | `bn_osclass`                                                                                                   |

#### Read-only environment variables

| Name                            | Description                                        | Value                            |
|---------------------------------|----------------------------------------------------|----------------------------------|
| `OSCLASS_BASE_DIR`              | Osclass installation directory.                    | `${BITNAMI_ROOT_DIR}/osclass`    |
| `OSCLASS_CONF_FILE`             | Configuration file for Osclass.                    | `${OSCLASS_BASE_DIR}/config.php` |
| `OSCLASS_VOLUME_DIR`            | Osclass directory for mounted configuration files. | `${BITNAMI_VOLUME_DIR}/osclass`  |
| `OSCLASS_DEFAULT_DATABASE_HOST` | Default database server host.                      | `mariadb`                        |
| `OSCLASS_DEFAULT_DATABASE_HOST` | Default database server host.                      | `127.0.0.1`                      |
| `PHP_DEFAULT_MEMORY_LIMIT`      | Default PHP memory limit.                          | `256M`                           |

When you start the Osclass image, you can adjust the configuration of the instance by passing one or more environment variables either on the docker-compose file or on the `docker run` command line. If you want to add a new environment variable:

* For docker-compose add the variable name and value under the application section in the [`docker-compose.yml`](https://github.com/bitnami/containers/blob/main/bitnami/osclass/docker-compose.yml) file present in this repository:

    ```yaml
    osclass:
      ...
      environment:
        - OSCLASS_PASSWORD=my_password
      ...
    ```

* For manual execution add a `--env` option with each variable and value:

    ```console
    $ docker run -d --name osclass -p 80:8080 -p 443:8443 \
      --env OSCLASS_PASSWORD=my_password \
      --network osclass-tier \
      --volume /path/to/osclass-persistence:/bitnami \
      bitnami/osclass:latest
    ```

### Examples

#### SMTP configuration using a Gmail account

This would be an example of SMTP configuration using a Gmail account:

* Modify the [`docker-compose.yml`](https://github.com/bitnami/containers/blob/main/bitnami/osclass/docker-compose.yml) file present in this repository:

    ```yaml
      osclass:
        ...
        environment:
          - OSCLASS_DATABASE_USER=bn_osclass
          - OSCLASS_DATABASE_NAME=bitnami_osclass
          - ALLOW_EMPTY_PASSWORD=yes
          - OSCLASS_SMTP_HOST=smtp.gmail.com
          - OSCLASS_SMTP_PORT=587
          - OSCLASS_SMTP_USER=your_email@gmail.com
          - OSCLASS_SMTP_PASSWORD=your_password
      ...
    ```

* For manual execution:

    ```console
    $ docker run -d --name osclass -p 80:8080 -p 443:8443 \
      --env OSCLASS_DATABASE_USER=bn_osclass \
      --env OSCLASS_DATABASE_NAME=bitnami_osclass \
      --env OSCLASS_SMTP_HOST=smtp.gmail.com \
      --env OSCLASS_SMTP_PORT=587 \
      --env OSCLASS_SMTP_USER=your_email@gmail.com \
      --env OSCLASS_SMTP_PASSWORD=your_password \
      --network osclass-tier \
      --volume /path/to/osclass-persistence:/bitnami \
      bitnami/osclass:latest
    ```

#### Connect Osclass container to an existing database

The Bitnami Osclass container supports connecting the Osclass application to an external database. This would be an example of using an external database for Osclass.

* Modify the [`docker-compose.yml`](https://github.com/bitnami/containers/blob/main/bitnami/osclass/docker-compose.yml) file present in this repository:

    ```diff
       osclass:
         ...
         environment:
    -      - OSCLASS_DATABASE_HOST=mariadb
    +      - OSCLASS_DATABASE_HOST=mariadb_host
           - OSCLASS_DATABASE_PORT_NUMBER=3306
           - OSCLASS_DATABASE_NAME=osclass_db
           - OSCLASS_DATABASE_USER=osclass_user
    -      - ALLOW_EMPTY_PASSWORD=yes
    +      - OSCLASS_DATABASE_PASSWORD=osclass_password
         ...
    ```

* For manual execution:

    ```console
    $ docker run -d --name osclass\
      -p 8080:8080 -p 8443:8443 \
      --network osclass-network \
      --env OSCLASS_DATABASE_HOST=mariadb_host \
      --env OSCLASS_DATABASE_PORT_NUMBER=3306 \
      --env OSCLASS_DATABASE_NAME=osclass_db \
      --env OSCLASS_DATABASE_USER=osclass_user \
      --env OSCLASS_DATABASE_PASSWORD=osclass_password \
      --volume osclass_data:/bitnami/osclass \
      bitnami/osclass:latest
    ```

In case the database already contains data from a previous Osclass installation, you need to set the variable `OSCLASS_SKIP_BOOTSTRAP` to `yes`. Otherwise, the container would execute the installation wizard and could modify the existing data in the database. Note that, when setting `OSCLASS_SKIP_BOOTSTRAP` to `yes`, values for environment variables such as `OSCLASS_USERNAME`, `OSCLASS_PASSWORD` or `OSCLASS_EMAIL` will be ignored.

## Logging

The Bitnami Osclass Docker image sends the container logs to `stdout`. To view the logs:

```console
docker logs osclass
```

Or using Docker Compose:

```console
docker-compose logs osclass
```

You can configure the containers [logging driver](https://docs.docker.com/engine/admin/logging/overview/) using the `--log-driver` option if you wish to consume the container logs differently. In the default configuration docker uses the `json-file` driver.

## Maintenance

### Backing up your container

To backup your data, configuration and logs, follow these simple steps:

#### Step 1: Stop the currently running container

```console
docker stop osclass
```

Or using Docker Compose:

```console
docker-compose stop osclass
```

#### Step 2: Run the backup command

We need to mount two volumes in a container we will use to create the backup: a directory on your host to store the backup in, and the volumes from the container we just stopped so we can access the data.

```console
docker run --rm -v /path/to/osclass-backups:/backups --volumes-from osclass busybox \
  cp -a /bitnami/osclass /backups/latest
```

### Restoring a backup

Restoring a backup is as simple as mounting the backup as volumes in the containers.

For the MariaDB database container:

```diff
 $ docker run -d --name mariadb \
   ...
-  --volume /path/to/mariadb-persistence:/bitnami/mariadb \
+  --volume /path/to/mariadb-backups/latest:/bitnami/mariadb \
   bitnami/mariadb:latest
```

For the Osclass container:

```diff
 $ docker run -d --name osclass \
   ...
-  --volume /path/to/osclass-persistence:/bitnami/osclass \
+  --volume /path/to/osclass-backups/latest:/bitnami/osclass \
   bitnami/osclass:latest
```

### Upgrade this image

Bitnami provides up-to-date versions of MariaDB and Osclass, including security patches, soon after they are made upstream. We recommend that you follow these steps to upgrade your container. We will cover here the upgrade of the Osclass container. For the MariaDB upgrade see: <https://github.com/bitnami/containers/tree/main/bitnami/mariadb#upgrade-this-image>

The `bitnami/osclass:latest` tag always points to the most recent release. To get the most recent release you can simple repull the `latest` tag from the Docker Hub with `docker pull bitnami/osclass:latest`. However it is recommended to use [tagged versions](https://hub.docker.com/r/bitnami/osclass/tags/).

#### Step 1: Get the updated image

```console
docker pull bitnami/osclass:latest
```

#### Step 2: Stop the running container

Stop the currently running container using the command

```console
docker-compose stop osclass
```

#### Step 3: Take a snapshot of the application state

Follow the steps in [Backing up your container](#backing-up-your-container) to take a snapshot of the current application state.

#### Step 4: Remove the currently running container

Remove the currently running container by executing the following command:

```console
docker-compose rm -v osclass
```

#### Step 5: Run the new image

Update the image tag in `docker-compose.yml` and re-create your container with the new image:

```console
docker-compose up -d
```

## Customize this image

The Bitnami Osclass Docker image is designed to be extended so it can be used as the base image for your custom web applications.

### Extend this image

Before extending this image, please note there are certain configuration settings you can modify using the original image:

* Settings that can be adapted using environment variables. For instance, you can change the ports used by Apache for HTTP and HTTPS, by setting the environment variables `APACHE_HTTP_PORT_NUMBER` and `APACHE_HTTPS_PORT_NUMBER` respectively.
* [Adding custom virtual hosts](https://github.com/bitnami/containers/blob/main/bitnami/apache#adding-custom-virtual-hosts).
* [Replacing the 'httpd.conf' file](https://github.com/bitnami/containers/blob/main/bitnami/apache#full-configuration).
* [Using custom SSL certificates](https://github.com/bitnami/containers/blob/main/bitnami/apache#using-custom-ssl-certificates).

If your desired customizations cannot be covered using the methods mentioned above, extend the image. To do so, create your own image using a Dockerfile with the format below:

```Dockerfile
FROM bitnami/osclass
## Put your customizations below
...
```

Here is an example of extending the image with the following modifications:

* Install the `vim` editor
* Modify the Apache configuration file
* Modify the ports used by Apache

```Dockerfile
FROM bitnami/osclass

## Change user to perform privileged actions
USER 0
## Install 'vim'
RUN install_packages vim
## Revert to the original non-root user
USER 1001

## Enable mod_ratelimit module
RUN sed -i -r 's/#LoadModule ratelimit_module/LoadModule ratelimit_module/' /opt/bitnami/apache/conf/httpd.conf

## Modify the ports used by Apache by default
# It is also possible to change these environment variables at runtime
ENV APACHE_HTTP_PORT_NUMBER=8181
ENV APACHE_HTTPS_PORT_NUMBER=8143
EXPOSE 8181 8143
```

Based on the extended image, you can update the [`docker-compose.yml`](https://github.com/bitnami/containers/blob/main/bitnami/osclass/docker-compose.yml) file present in this repository to add other features:

```diff
   osclass:
-    image: bitnami/osclass:latest
+    build: .
     ports:
-      - '80:8080'
-      - '443:8443'
+      - '80:8181'
+      - '443:8143'
     environment:
+      - PHP_MEMORY_LIMIT=512m
     ...
```

## Notable changes

### 4.4.0-debian-10-r12

* The size of the container image has been decreased.
* The configuration logic is now based on Bash scripts in the *rootfs/* folder.
* The Osclass container image has been migrated to a "non-root" user approach. Previously the container ran as the `root` user and the Apache daemon was started as the `daemon` user. From now on, both the container and the Apache daemon run as user `1001`. You can revert this behavior by changing `USER 1001` to `USER root` in the Dockerfile, or `user: root` in `docker-compose.yml`. Consequences:
  * The HTTP/HTTPS ports exposed by the container are now `8080/8443` instead of `80/443`.
  * Backwards compatibility is not guaranteed when data is persisted using docker or docker-compose. We highly recommend migrating the Osclass site by exporting its content, and importing it on a new Osclass container. Follow the steps in [Backing up your container](#backing-up-your-container) and [Restoring a backup](#restoring-a-backup) to migrate the data between the old and new container.

To upgrade a previous Bitnami Osclass container image, which did not support non-root, the easiest way is to start the new image as a root user and updating the port numbers. Modify your docker-compose.yml file as follows:

```diff
       - ALLOW_EMPTY_PASSWORD=yes
+    user: root
     ports:
-      - '80:80'
-      - '443:443'
+      - '80:8080'
+      - '443:8443'
     volumes:
```

### 3.7.4-debian-9-r254 and 3.7.4-ol-7-r322

* This image has been adapted so it's easier to customize. See the [Customize this image](#customize-this-image) section for more information.
* The Apache configuration volume (`/bitnami/apache`) has been deprecated, and support for this feature will be dropped in the near future. Until then, the container will enable the Apache configuration from that volume if it exists. By default, and if the configuration volume does not exist, the configuration files will be regenerated each time the container is created. Users wanting to apply custom Apache configuration files are advised to mount a volume for the configuration at `/opt/bitnami/apache/conf`, or mount specific configuration files individually.
* The PHP configuration volume (`/bitnami/php`) has been deprecated, and support for this feature will be dropped in the near future. Until then, the container will enable the PHP configuration from that volume if it exists. By default, and if the configuration volume does not exist, the configuration files will be regenerated each time the container is created. Users wanting to apply custom PHP configuration files are advised to mount a volume for the configuration at `/opt/bitnami/php/conf`, or mount specific configuration files individually.
* Enabling custom Apache certificates by placing them at `/opt/bitnami/apache/certs` has been deprecated, and support for this functionality will be dropped in the near future. Users wanting to enable custom certificates are advised to mount their certificate files on top of the preconfigured ones at `/certs`.

## Contributing

We'd love for you to contribute to this container. You can request new features by creating an [issue](https://github.com/bitnami/containers/issues) or submitting a [pull request](https://github.com/bitnami/containers/pulls) with your contribution.

## Issues

If you encountered a problem running this container, you can file an [issue](https://github.com/bitnami/containers/issues/new/choose). For us to provide better support, be sure to fill the issue template.

## License

Copyright &copy; 2024 Broadcom. The term "Broadcom" refers to Broadcom Inc. and/or its subsidiaries.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

<http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
