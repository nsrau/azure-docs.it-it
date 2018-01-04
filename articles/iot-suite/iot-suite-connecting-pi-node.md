---
title: Effettuare il provisioning di dispositivi Raspberry Pi nella soluzione di monitoraggio remoto in Node.js - Azure | Microsoft Docs
description: Descrive come connettere un dispositivo Raspberry Pi alla soluzione preconfigurata di monitoraggio remoto Azure IoT Suite usando un'applicazione scritta in Node.js.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: d2cc72f26568a362049f24323ffa598b6012d77f
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Connettere il dispositivo Raspberry Pi alla soluzione preconfigurata di monitoraggio remoto (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Questa esercitazione mostra come connettere un dispositivo fisico alla soluzione preconfigurata di monitoraggio remoto. In questa esercitazione viene usato Node.js, una buona scelta per gli ambienti con vincoli minimi in termine di risorse.

### <a name="required-hardware"></a>Requisiti hardware

Un computer desktop per potersi connettere in remoto alla riga di comando in Raspberry Pi.

[Starter kit di Microsoft Azure IoT per Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) o componenti equivalenti. Questa esercitazione usa gli elementi seguenti del kit:

- Raspberry Pi 3
- Scheda microSD (con NOOBS)
- Un cavo USB mini
- Un cavo Ethernet

### <a name="required-desktop-software"></a>Software desktop necessario

È necessario un client SSH nel computer desktop per poter accedere in remoto alla riga di comando in Raspberry Pi.

- Windows non include un client SSH. È consigliabile usare [PuTTY](http://www.putty.org/).
- La maggior parte delle distribuzioni Linux e Mac OS includono l'utilità SSH della riga di comando. Per altre informazioni, vedere [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH con Linux o Mac OS).

### <a name="required-raspberry-pi-software"></a>Software Raspberry Pi necessario

Se non è già stato fatto, installare Node.js 4.0.0 o versioni successive nel dispositivo Raspberry Pi. I passaggi seguenti mostrano come installare Node.js v6.11.4 nel dispositivo Raspberry Pi:

1. Connettersi al dispositivo Raspberry Pi con `ssh`. Per altre informazioni, vedere [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) nel [sito Web Raspberry Pi](https://www.raspberrypi.org/).

1. Usare il comando seguente per aggiornare Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Usare il comando seguente per scaricare i file binari di Node.js su Raspberry Pi:

    ```sh
    wget https://nodejs.org/dist/v6.11.4/node-v6.11.4-linux-armv7l.tar.gz
    ```

1. Usare il comando seguente per installare i file binari:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.11.4-linux-armv7l.tar.gz
    ```

1. Usare il comando seguente per verificare di aver installato correttamente Node.js v6.11.4:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Creare una soluzione Node.js

Completare i passaggi seguenti usando la connessione `ssh` al dispositivo Raspberry Pi:

1. Creare una cartella denominata `RemoteMonitoring` nella cartella principale nel dispositivo Raspberry Pi. Passare a questa cartella nella riga di comando:

    ```sh
    cd ~
    mkdir RemoteMonitoring
    cd RemoteMonitoring
    ```

1. Per scaricare e installare i pacchetti necessari per completare l'app di esempio, eseguire i comandi seguenti:

    ```sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Nella cartella `RemoteMonitoring` creare un file denominato **remote_monitoring.js**. Aprire il file in un editor di testo. Nel dispositivo Raspberry Pi è possibile usare l'editor di testo `nano` o `vi`.

1. Nel file **remote_monitoring.js**, aggiungere le istruzioni `require` seguenti:

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Aggiungere le seguenti dichiarazioni di variabili dopo le istruzioni `require` . Sostituire i valori segnaposto `{Device Id}` e `{Device Key}` con i valori annotati per il dispositivo di cui è stato effettuato il provisioning nella soluzione di monitoraggio remoto. Usare il nome host dell'hub IoT nella soluzione per sostituire `{IoTHub Name}`. Ad esempio, se il nome host dell'hub IoT è `contoso.azure-devices.net`, sostituire `{IoTHub Name}` con `contoso`:

    ```nodejs
    var connectionString = 'HostName={IoTHub Name}.azure-devices.net;DeviceId={Device Id};SharedAccessKey={Device Key}';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Per definire alcuni dati di telemetria di base, aggiungere le variabili seguenti:

    ```nodejs
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Per definire alcuni valori di proprietà, aggiungere le variabili seguenti:

    ```nodejs
    var temperatureSchema = 'chiller-temperature;v1';
    var humiditySchema = 'chiller-humidity;v1';
    var pressureSchema = 'chiller-pressure;v1';
    var interval = "00:00:05";
    var deviceType = "Chiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    ```

1. Aggiungere la variabile seguente per definire le proprietà indicate da inviare alla soluzione. Queste proprietà includono i metadati per descrivere i metodi e i dati di telemetria usati dal dispositivo:

    ```nodejs
    var reportedProperties = {
      "Protocol": "MQTT",
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        "TemperatureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
          "MessageSchema": {
            "Name": temperatureSchema,
            "Format": "JSON",
            "Fields": {
              "temperature": "Double",
              "temperature_unit": "Text"
            }
          }
        },
        "HumiditySchema": {
          "Interval": interval,
          "MessageTemplate": "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}",
          "MessageSchema": {
            "Name": humiditySchema,
            "Format": "JSON",
            "Fields": {
              "humidity": "Double",
              "humidity_unit": "Text"
            }
          }
        },
        "PressureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}",
          "MessageSchema": {
            "Name": pressureSchema,
            "Format": "JSON",
            "Fields": {
              "pressure": "Double",
              "pressure_unit": "Text"
            }
          }
        }
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude
    }
    ```

1. Per stampare i risultati dell'operazione, aggiungere la funzione helper seguente:

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Aggiungere la funzione helper seguente per impostare in modo casuale i valori di telemetria:

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Aggiungere la funzione seguente per gestire le chiamate ai metodi diretti dalla soluzione. La soluzione usa metodi diretti per intervenire sui dispositivi:

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (!!err) {
          console.error('An error ocurred when sending a method response:\n' +
            err.toString());
        } else {
          console.log('Response to method \'' + request.methodName +
            '\' sent successfully.');
        }
      });
    }
    ```

1. Aggiungere il codice seguente per inviare dati di telemetria alla soluzione. L'app client aggiunge proprietà al messaggio per identificare lo schema del messaggio:

    ```node.js
    function sendTelemetry(data, schema) {
      var d = new Date();
      var payload = JSON.stringify(data);
      var message = new Message(payload);
      message.properties.add('$$CreationTimeUtc', d.toISOString());
      message.properties.add('$$MessageSchema', schema);
      message.properties.add('$$ContentType', 'JSON');

      console.log('Sending device message data:\n' + payload);
      client.sendEvent(message, printErrorFor('send event'));
    }
    ```

1. Aggiungere il codice seguente per creare un'istanza del client:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Aggiungere il codice seguente a:

    - Aprire la connessione.
    - Impostare un gestore per le proprietà desiderate.
    - Inviare le proprietà segnalate.
    - Registrare i gestori dei metodi diretti.
    - Avviare l'invio dei dati di telemetria.

    ```nodejs
    client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onDirectMethod);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendTemperatureInterval = setInterval(function () {
          temperature += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit
          };
          sendTelemetry(data, temperatureSchema)
        }, 5000);

        var sendHumidityInterval = setInterval(function () {
          humidity += generateRandomIncrement();
          var data = {
            'humidity': humidity,
            'humidity_unit': humidityUnit
          };
          sendTelemetry(data, humiditySchema)
        }, 5000);

        var sendPressureInterval = setInterval(function () {
          pressure += generateRandomIncrement();
          var data = {
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, pressureSchema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
    });
    ```

1. Salvare le modifiche apportate al file **remote_monitoring.js**.

1. Per avviare l'applicazione di esempio, eseguire il comando seguente al prompt dei comandi in Raspberry Pi:

    ```sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
