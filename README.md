# Enviro MQTT Logger

`enviro-mqtt` is a Python service that publishes environmental data from an [Enviro](https://shop.pimoroni.com/products/enviro-plus) via MQTT.

This is a fork of the [Enviro+ MQTT library](https://github.com/hotplot/enviroplus-mqtt) that removes the extra sensors the Enviro hat does not support.

## Setting Up Your Device

1) Set up your RPi as you normally would.
2) Connect the Enviro board, and the PMS5003 sensor if you are using one.
3) Install the Enviro library by following the instructions at https://github.com/pimoroni/enviroplus-python/ (make sure the library is installed for Python 3)
4) Clone this repository to `/usr/src/enviro-mqtt`:

       sudo git clone https://github.com/Sixfifty/enviro-mqtt /usr/src/enviro-mqtt

5) Add a new file at `/etc/systemd/system/envlogger.service` with the following content:

       [Unit]
       Description=Enviro MQTT Logger
       After=network.target
   
       [Service]
       ExecStart=/usr/bin/python3 /usr/src/enviro-mqtt/src/main.py <arguments>
       WorkingDirectory=/usr/src/enviro-mqtt
       StandardOutput=inherit
       StandardError=inherit
       Restart=always
       User=pi
   
       [Install]
       WantedBy=multi-user.target
       
   **Note that you must replace `<arguments>` with flags appropriate to your MQTT server.**

6) Enable and start the service:

       sudo systemctl enable envlogger.service
       sudo systemctl start envlogger.service

## Supported Arguments

- The MQTT host, port, username, password and client ID can be specified.
- The update interval can be specified, and defaults to 5 seconds.
- The initial delay before publishing readings can be specified, and defaults to 15 seconds.
- If you are using a PMS5003 sensor, enable it by passing the `--use-pms5003` flag.

        usage: main.py -h HOST [-p PORT] [-U USERNAME] [-P PASSWORD] [--prefix PREFIX]
                    [--client-id CLIENT_ID] [--interval INTERVAL] [--delay DELAY]
                    [--use-pms5003] [--help]

        optional arguments:
        -h HOST, --host HOST  the MQTT host to connect to
        -p PORT, --port PORT  the port on the MQTT host to connect to
        -U USERNAME, --username USERNAME
                            the MQTT username to connect with
        -P PASSWORD, --password PASSWORD
                            the password to connect with
        --prefix PREFIX       the topic prefix to use when publishing readings, i.e.
                            'lounge/enviroplus'
        --client-id CLIENT_ID
                            the MQTT client identifier to use when connecting
        --interval INTERVAL   the duration in seconds between updates
        --delay DELAY         the duration in seconds to allow the sensors to
                            stabilise before starting to publish readings
        --help                print this help message and exit

## Published Topics

Readings will be published to the following topics:

- `<prefix>/proximity`
- `<prefix>/lux`
- `<prefix>/temperature`
- `<prefix>/pressure`
- `<prefix>/humidity`
