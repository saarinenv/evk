
Evaluation kit by Ruuvi
=======================

This guide will walk you through on how to get started with the Wirepas Mesh (WM)
evaluation kit (EVK).


Kit introduction and Content
============================

The Wirepas EVK consists of pre-programmed set of hardware combined with
Wirepas Tools and reference software.


Hardware content
----------------

- Raspberry Pi (RPi)

- Pre-configured SD-Card for the RPi to operate as a Wirepas Gateway

- Pre-configured USB Dongle to operate as a Wirepas Mesh network sink (connected to the Gateway)

- Pre-configured Ruuvi tags (for Low Energy operation)

- WNT backend and client

**Technical requirements**

An Internet connection over Ethernet or Wi-Fi with outbound access to MQTT 8883
is necessary for the Gateway to talk with the Wirepas hosted instance.

For WNT requirements, please review WNT's user guide available in Sharefile.


Software Content
----------------

**Availabe on Wirepas Sharefile repository**
    - Wirepas hosted backend instance hosted on AWS server
    - Wirepas network tool Client for Windows 10 PC
    - Optionally Wirepas mesh SDK
    - Tooling and examples under `Github <https://github.com/wirepas>`_ )

**Availabe on Wirepas Sharefile repository**
    - Wirepas hosted backend instance hosted on AWS server
    - Wirepas network tool Client for Windows 10 PC
    - Optionally Wirepas mesh SDK

**Available on Github**
    - `manifest: manifests for usage with repo tool <https://github.com/wirepas/manifest>`_
    - `backend client: interfacing with MQTT broker and WM gateway <https://github.com/wirepas/backend-client>`_
    - `backend APIs: language wrappers for interfacing with MQTT broker <https://github.com/wirepas/backend-apis>`_
    - `gateway: linux gateway implementation <https://github.com/wirepas/gateway>`_
    - `c-mesh-api: WM reference serial interface protocol <https://github.com/wirepas/c-mesh-api>`_
    - `wm-utils: builder, flasher and logger for SDK users <https://github.com/wirepas/wm-utils>`_
    - `wm-config: host configuration utility <https://github.com/wirepas/wm-config>`_
    - `tutorials: examples and guides on how to setup 3rd party software <https://github.com/wirepas/tutorials>`_



Gettings started
-----------------

**Install and configure Wirepas Network Tool Client (WNT)**

Install and connect the Wirepas WNT client (requires windows 10) and follow
the instructions (see WNT's user guide in Sharefile) to connect it to your
trial backend.


.. # Setup and configure the Ruuvi Tags:

**Setup and configure Gateway**

Once you receive your kit, start by plugging the sink (usb dongle) to
one of the usb ports of the raspberry pi. Connect your ethernet or configure
the pi to access a WiFi network by setting the appropriate configuration
under */boot/wirepas/custom.env* (see `wm-config`_).

Ensure the sdcard is plugged in the RPi and power up the device.

Allow a few minutes (10 to 20 minutes) for the device to install any necessary
updates to the host system and to pull Wirepas service updates. Once the
gateway is ready, data will start flowing towards the MQTT broker hosted by
Wirepas for a period of 3 months (see `Setting up your own MQTT broker`_ on how
to setup your own broker).

Once you login, you will be able to monitor your Mesh network and interact
with your Ruuvi devices. The application flashed on your Ruuvi tags
allows you to customize the data rate and which sensors to read from. Such
customization is done through the AppConfig.

Please review the application note (found in Sharefile) to learn how to set the AppConfig according
to your needs.

This completes your unboxing!

Keep reading the next section to learn how to take your evaluation further, such as
setting up your own broker, building your own visualizations and test cases.


Taking the evaluation further
==============================

In addition to the kit components you will need a x86_64 Linux where you
will be installing:

- Docker engine

- MQTT broker

- Python utilities

- Visualization software


Throughout this guide we assume that you will be cloning and creating directories
under the path */home/${USER}/wirepas/*.


Installing Docker
-----------------

Using the examples in this repository requires that you have docker and docker-compose installed on your system.
Please review `Docker's installation instructions <https://docs.docker.com/install/>`_
or use the convenience script from `get.docker.com <https://get.docker.com>`_.

Using the convenience script you can install docker with the following one liner:


::

    curl https://get.docker.com/ | sh


To install docker-compose you must have python and pip (recommended) on your host.
If you match these requirements you can install docker-compose using the following command

::

    pip install docker-compose


.. _Setting up your own MQTT broker:

Setting up the MQTT broker
--------------------------

The MQTT broker is an essential component in the Wirepas EVK as it allows
you to retrieve data from the WM network and to interact with it.


Start by cloning the repository from https://github.com/wirepas/tutorials/

::

    git clone https://github.com/wirepas/tutorials.git



Change into the mosquitto folder

::

    cd mosquitto


.. _credentials:

Customize the broker settings present in the
`Dockerfile <https://github.com/wirepas/tutorials/blob/master/mosquitto/Dockerfile>`_
and the `mosquitto.conf <https://github.com/wirepas/tutorials/blob/master/mosquitto/mosquitto.conf>`_


Start the broker with

::

    docker-compose up -d



Validate that the broker is running by inspecting the container status

::

    docker-compose ps

    or

    docker ps -a

After these steps your message broker is ready to serve publishers and
subscribers according to your `credentials`_. For your information, the
mosquitto broker has several
`tools to help you inspect its status <https://github.com/eclipse/mosquitto>`_.


Over the next section you will need to know:

- broker ip or hostname

- broker secure port number

- mosquitto username

- mosquitto password



Setting up the gateway software
-------------------------------

These steps require that you have access to the RPi included with the EVK.


**Logging into the pi**


Assuming your host and RPi share the same network environment, you have
two options to connect remotely to the pi:

- using private and public key pairs (more secure)

- using plain text logins over ssh (not recommended)

The software shipped with the Wirepas EVK allows you to connect with both
methods. The private and public method is always available but you can
enable or disable the plain text login.


*To enable the plain text logins*, insert the RPi sdcard on a host with a
sdcard reader and open the file in /boot/wirepas/custom.env. Locate and
ensure that the following key has value set to true

::

    WM_HOST_SSH_ENABLE_NETWORK_LOGIN="true"

and that you change the password in

::

    WM_HOST_USER_PASSWORD


It is also important to known what is the hostname of your device. You can
read or change hostname from the key

::

    WM_HOST_SET_HOSTNAME=wm-evk



After you insert the sdcard back on the RPi and power it on you can
connect remotely using

::

    ssh pi@wm-evk.local

    password: the value of WM_HOST_USER_PASSWORD



*To enable the logins with private and public keys* you will need to have
a private and public key pair. You can easily generate a pair by using the
`ssh-keygen <https://linux.die.net/man/1/ssh-keygen>`_.

Locate and copy the value of your *public key* to the following key in
`/boot/wirepas/custom.env
<https://github.com/wirepas/wm-config>`_

::

    WM_HOST_USER_PPKI


After you insert the sdcard back on the RPi and power it on you can
connect remotely using


::

    ssh -i <path to private key> pi@wm-evk.local


If you opt for private key login, it is recommended that you drop the
plain text login by setting

::

    WM_HOST_SSH_ENABLE_NETWORK_LOGIN="false"



Defining where to publish data
------------------------------

The WM EVK will source from /boot/wirepas/custom.env where the data needs
to be publish to.

Ensure that the values in the following keys are correct

::

    WM_SERVICES_HOST:  broker ip or hostname
    WM_SERVICES_MQTT_PORT: broker secure port (8883 default)
    WM_SERVICES_MQTT_USER: user defined in the MQTT broker `credentials`_
    WM_SERVICES_MQTT_PASSWORD: password defined in the MQTT broker `credentials`_


If you need to change a value, remember to run wm-config after each change
to the configuration file.

Assuming the keys have the correct values, ensure that the services are
running by inspecting their status with


::

    cd ~/wirepas/lxgw

    docker-compose ps

    docker-compose logs


If everything is working as expected, you will see data being published from

::

    2019-02-27 07:55:38,255 | [INFO] transport_service: (...)
    2019-02-27 07:55:38,305 | [DEBUG] transport_service: (...)
    2019-02-27 07:55:38,315 | [DEBUG] transport_service: (...)



If there is no data being sent by the transport service ensure that

- Your MQTT credentials are correct

- Your MQTT broker is running

- Your sink is properly connected (inspect the value of docker logs wm-sink)

- Your devices are powered on (make sure the battery protector has been removed)



Consuming data from the MQTT broker
------------------------------------


These steps require that you have the Wirepas Backend Client tool installed
on your host.


Start by cloning the repository with

::

    git clone https://github.com/wirepas/backend-client.git

Review the installion steps and install the package. Once you have installed
it, move into the examples folder.

Start my creating a settings file with the following details matching your
environment

::

    # tabs are forbidden in yaml

    mqtt_hostname: the value of WM_SERVICES_HOST
    mqtt_username: the value of WM_SERVICES_MQTT_USER
    mqtt_password: the value of WM_SERVICES_MQTT_PASSWORD
    mqtt_port: the value of WM_SERVICES_MQTT_PORT

Save the file under the examples folder with the name settings.yml.


Run the mqtt viewer example with


::

    python mqtt_viewer.py --settings settings.yml


Your will start seeing on your screen data from your network.

If you don't see traffic information being printed on your screen make sure
that your gateway settings are correct and that they match the ones
in settings.yml.



Visualizing data in Kibana
--------------------------

As one last step, we will use the backend client tool to push data into
fluentd and kibana. To start kibana and fluentd shell into

::


    cd ${HOME}/wirepas/examples-backend-services/.


Change directory to

::

    cd ${HOME}/wirepas/examples-backend-services/fluentd

read the installation steps and start the service with

::

    docker-compose up -d


Change directory to

::

    cd ${HOME}/wirepas/examples-backend-services/elastic_search


read the installation steps, fulfill the requirements and start the service
with

::

    docker-compose up -d


Inspect the status of both fluentd, kibana and elastic search using

::

    docker ps -a

If the containers are restarting, read the installation steps again. If
everything seems to be in order inspect the stdout of the containers with


::

    docker ps <container name>



Assuming all the services are up and running the last step to get data on
kibana is to configure the backend client to push data to fluentd.


Change directory back to


::

    cd ${HOME}/wirepas/backend-client/examples

and edit the settings.yml you created before. Add your fluentd host information
to the file under the keys


::

    # tags stream with app.ruuvi
    fluentd_hostname: localhost
    fluentd_record: ruuvi
    fluentd_tag: app


Restart the mqtt viewer example and open your browser (chrome recommended).
Navigate to http://localhost:5601 and kibana will start loading.

Once kibana finishes loading, navigate to Management on the top left menu
and click on *Saved Objects > Import*. Click import on the right side menu
and import the json file located at

::

    cd ${HOME}/wirepas/examples-backend-services/elastic_search/dashboards/evk_ruuvi.json


Once the import is done, navigate to Dashboard on Kibana's left menu and
click on *[WM] Ruuvi Evk*. The dashboard will display metrics regarding your network.


This completes the setting up of the evaluation framework for the WM EVK. You
can customize Kibana according to your needs and use the backend client to
develop custom evaluation scripts.



.. _`wm-config`: https://github.com/wirepas/wm-config


My new section
==============

This is an example section during the academy


