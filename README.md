# Kafka Connect file and directory config provider
This implementation is a copy of the original file provider and adds the capability to not only read files as properties. It can read directories as well.

```
- my-secret-mount-point
 |- user.password
 |- user.name
 |- database.url
```

This config provider will read the directory and provide the files as property keys like this
``ssl.keystore.password=${directory:/var/run/my-secret-mount-point:user.passwrod}``

## Installation

In order to install this config provider on a `Kafka connect` instance:

- build the `kafka-connect-file-directory-config-provider` jar using gradle:
  ```bash
  mvn build
  ```
- include the jar under `build/libs` in the `Kafka connect` instance you are
  using in a separate specific directory
  (e.g., `/usr/local/share/java/kafka-connect/plugins/kafka-connect-file-directory-config-provider`)
  (if are using Docker, create an image that extends the `Kafka connect` standard
  one from Confluent, adding this jar)
- in the `Kafka connect` worker configuration, add the directory where you
  placed the jar to the plugins path
  - if you are using the `confluent-oss` platform, edit the file
    `etc/schema-registry/connect-avro-distributed.properties` and add the jar
    directory to the `plugin.path` property as follows:
    ```
    plugin.path=share/java,/usr/local/share/java/kafka-connect/plugins/kafka-connect-file-directory-config-provider
    ```
  - if you are extending the `confluentinc/cp-kafka-connect` Docker image, set
    the environment variable `CONNECT_PLUGIN_PATH` value with the jar directory
    when running the container

## Usage

In order to use this config provider in `Kafka connect`, you need to declare it
in the worker configuration as follows:

- in the `confluent-oss` platform, edit the file
  `etc/schema-registry/connect-avro-distributed.properties` adding the
  following properties:
  ```
  config.providers=directory
  config.providers.directory.class=ch.w3tec.kafka.connect.config.FileDirectoryConfigProvider
  ```
- if you are extending the `confluentinc/cp-kafka-connect` Docker image, add the
  following environment variables when running the container:
  ```bash
  CONNECT_CONFIG_PROVIDERS=directory
  CONNECT_CONFIG_PROVIDERS_DIRECTORY_CLASS=ch.w3tec.kafka.connect.config.FileDirectoryConfigProvider
  ```

You can then reference file variables from `Kafka connect` connector
configurations as follows:

```
${directory:/path-to-directory:file-name}
```

## Thanks
https://github.com/apache/kafka/pull/5596