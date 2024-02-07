# Easy to use IoT Stack

This repository contains the documentation for an easy to use IoT stack.
[I](https://drumm.sh) created this repository for one of my lectures at the [FH Aachen](http://fh-aachen.de)

## Quickstart ⚡

To use this IoT stack you need to follow the steps below. It is very important
to change all the passwords before deploying the stack. To generate secure passwords
I recommend using a password manager like [Bitwarden](https://bitwarden.com).

### Preparation

1. Clone this repository using the command below.

   ```zsh
   git clone https://github.com/ceedee666/simple-iot.git
   ```

   The result should be a file structure similar to this:

   ```zsh
   simple-iot
   ├── README.md
   ├── docker-compose.yaml
   ├── entrypoint.sh
   ├── example_environment.txt
   ├── mosquitto
   │   └── mosquitto.conf
   └── telegraf
       └── telegraf.conf
   ```

1. Copy the file `example_evironment.txt` to `.env`. For example, by
   using the following command.

   ```zsh
   cp example_evironment.txt environment.env
   ```

1. Edit the file `environment.env` and replace all `-----> changeme <-----` values.
   Different steps are necessary to generate the values. These steps are shown below.

   1. The first step is to set the username (parameter `DOCKER_INFLUXDB_INIT_PASSWORD`)
      and password (parameter `DOCKER_INFLUXDB_INIT_USERNAME`) for the Influx database.
      A possible username is `admin`. A secure password can be generated using the
      a password manager. Using the command line interface of [Bitwarden](https://bitwarden.com)
      a secure password can be generated using the following command.

      ```zsh
        bw generate -p -c --words 5 --includeNumber
      ```

      After adding the username and password to the `environment.env` should look
      similar to the excerpt below.

      ```env
        # Primary InfluxDB admin/superuser credentials
        DOCKER_INFLUXDB_INIT_USERNAME=admin
        DOCKER_INFLUXDB_INIT_PASSWORD=dummy-123-...
      ```

   1. Next, the API token for the Influx database needs to be generated
      (parameter `DOCKER_INFLUXDB_INIT_ADMIN_TOKEN`). This can be done using
      [OpenSSH](https://www.openssh.com/) command line interface.

      ```zsh
        openssh rand -hex 32
      ```

   1. The final step is to create the username and password for the
      Mosquitto MQTT broker.
      This username and password need to be added to the `environment.env` file.
      These values are used by [Telegraf](https://github.com/influxdata/telegraf)
      to authenticate with the broker.
      Additionally, the values need to be added to the `passwd` file in the
      `mosquitto` container.

      A possible username is `mosquitto`. A secure password can be generated using
      a password manager. The resulting lines in the `environment.env` should look
      similar to the excerpt below.

      ```env
        # Mosquitto username and password
        MOSQUITTO_USERNAME=mosquitto
        MOSQUITTO_PASSWORD=dummy-123-...
      ```

      To add the password for the user mosquitto to the `passwd` file use the
      following command

      ```zsh
        docker-compose run --rm simple-iot-mosquitto mosquitto_passwd -c /mosquitto/conf/passwd mosquitto
      ```

### Starting the stack

After finishing the configuration is is now time to start the IoT stack.

```zsh
  docker-compose up -d
```

## Acknowledgement

This repository is based on the following repositories, videos and documentation:

- [Telegraf, InfluxDB, Grafana (TIG) Stack](https://github.com/huntabyte/tig-stack)
- [Easy IoT data infrastructure setup via docker](https://github.com/Miceuz/docker-compose-mosquitto-influxdb-telegraf-grafana)
- [Easily Install InfluxDB, Telegraf, & Grafana with Docker](https://youtu.be/QGG_76OmRnA)
- [Running InfluxDB 2.0 and Telegraf Using Docker](https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/)
- [How to setup standalone mosquitto MQTT broker using docker-compose](https://techoverflow.net/2021/11/25/how-to-setup-standalone-mosquitto-mqtt-broker-using-docker-compose/)
