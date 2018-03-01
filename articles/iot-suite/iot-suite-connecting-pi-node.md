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
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 7f489a6b26edb9a58b21d318785d3804197b33cb
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
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

Se non è già stato fatto, installare Node.js 4.0.0 o versioni successive nel dispositivo Raspberry Pi. I passaggi seguenti mostrano come installare Node.js v6 nel dispositivo Raspberry Pi:

1. Connettersi al dispositivo Raspberry Pi con `ssh`. Per altre informazioni, vedere [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) nel [sito Web Raspberry Pi](https://www.raspberrypi.org/).

1. Usare il comando seguente per aggiornare Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Usare i comandi seguenti per rimuovere qualsiasi installazione esistente di Node.js dal dispositivo Raspberry Pi:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Usare il comando seguente per scaricare e installare Node.js v6 nel dispositivo Raspberry Pi:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs -y
    ```

1. Usare il comando seguente per verificare di aver installato correttamente Node.js v6.11.4:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Creare una soluzione Node.js

Completare i passaggi seguenti usando la connessione `ssh` al dispositivo Raspberry Pi:

1. Creare una cartella denominata `remotemonitoring` nella cartella principale nel dispositivo Raspberry Pi. Passare a questa cartella nella riga di comando:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Per scaricare e installare i pacchetti necessari per completare l'app di esempio, eseguire i comandi seguenti:

    ```sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. Nella cartella `remotemonitoring` creare un file denominato **remote_monitoring.js**. Aprire il file in un editor di testo. Nel dispositivo Raspberry Pi è possibile usare l'editor di testo `nano` o `vi`.

1. Nel file **remote_monitoring.js**, aggiungere le istruzioni `require` seguenti:

    ```nodejs
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Aggiungere le seguenti dichiarazioni di variabili dopo le istruzioni `require` . Sostituire il valore segnaposto `{device connection string}` con il valore annotato per il dispositivo di cui è stato eseguito il provisioning nella soluzione di monitoraggio remoto:

    ```nodejs
    var connectionString = '{device connection string}';
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
    var deviceOnline = true;
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
      "Longitude": deviceLongitude,
      "Online": deviceOnline
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

1. Aggiungere la funzione generica seguente per gestire le chiamate ai metodi diretti dalla soluzione. La funzione visualizza informazioni sul metodo diretto richiamato, ma in questo esempio non modifica il dispositivo in alcun modo. La soluzione usa metodi diretti per intervenire sui dispositivi:

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (err) console.error('Error sending method response :\n' + err.toString());
        else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
      });
    }
    ```

1. Aggiungere la funzione seguente per gestire le chiamate al metodo diretto **FirmwareUpdate** dalla soluzione. La funzione verifica i parametri passati nel payload del metodo diretto e quindi esegue in modo asincrono una simulazione dell'aggiornamento del firmware:

    ```node.js
    function onFirmwareUpdate(request, response) {
      // Get the requested firmware version from the JSON request body
      var firmwareVersion = request.payload.Firmware;
      var firmwareUri = request.payload.FirmwareUri;
      
      // Ensure we got a firmware values
      if (!firmwareVersion || !firmwareUri) {
        response.send(400, 'Missing firmware value', function(err) {
          if (err) console.error('Error sending method response :\n' + err.toString());
          else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
        });
      } else {
        // Respond the cloud app for the device method
        response.send(200, 'Firmware update started.', function(err) {
          if (err) console.error('Error sending method response :\n' + err.toString());
          else {
            console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

            // Run the simulated firmware update flow
            runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
          }
        });
      }
    }
    ```

1. Aggiungere la funzione seguente per simulare un flusso di aggiornamento del firmware con esecuzione prolungata che segnala lo stato di avanzamento alla soluzione:

    ```node.js
    // Simulated firmwareUpdate flow
    function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
      console.log('Simulating firmware update flow...');
      console.log('> Firmware version passed: ' + firmwareVersion);
      console.log('> Firmware URI passed: ' + firmwareUri);
      async.waterfall([
        function (callback) {
          console.log("Image downloading from " + firmwareUri);
          var patch = {
            FirmwareUpdateStatus: 'Downloading image..'
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(10000, callback);
        },
        function (callback) {
          console.log("Downloaded, applying firmware " + firmwareVersion);
          deviceOnline = false;
          var patch = {
            FirmwareUpdateStatus: 'Applying firmware..',
            Online: false
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(8000, callback);
        },
        function (callback) {
          console.log("Rebooting");
          var patch = {
            FirmwareUpdateStatus: 'Rebooting..'
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(10000, callback);
        },
        function (callback) {
          console.log("Firmware updated to " + firmwareVersion);
          deviceOnline = true;
          var patch = {
            FirmwareUpdateStatus: 'Firmware updated',
            Online: true,
            Firmware: firmwareVersion
          };
          reportUpdateThroughTwin(patch, callback);
          callback(null);
        }
      ], function(err) {
        if (err) {
          console.error('Error in simulated firmware update flow: ' + err.message);
        } else {
          console.log("Completed simulated firmware update flow");
        }
      });

      // Helper function to update the twin reported properties.
      function reportUpdateThroughTwin(patch, callback) {
        console.log("Sending...");
        console.log(JSON.stringify(patch, null, 2));
        client.getTwin(function(err, twin) {
          if (!err) {
            twin.properties.reported.update(patch, function(err) {
              if (err) callback(err);
            });      
          } else {
            if (err) callback(err);
          }
        });
      }

      function sleep(milliseconds, callback) {
        console.log("Simulate a delay (milleseconds): " + milliseconds);
        setTimeout(function () {
          callback(null);
        }, milliseconds);
      }
    }
    ```

1. Aggiungere il codice seguente per inviare dati di telemetria alla soluzione. L'app client aggiunge proprietà al messaggio per identificare lo schema del messaggio:

    ```node.js
    function sendTelemetry(data, schema) {
      if (deviceOnline) {
        var d = new Date();
        var payload = JSON.stringify(data);
        var message = new Message(payload);
        message.properties.add('$$CreationTimeUtc', d.toISOString());
        message.properties.add('$$MessageSchema', schema);
        message.properties.add('$$ContentType', 'JSON');

        console.log('Sending device message data:\n' + payload);
        client.sendEvent(message, printErrorFor('send event'));
      } else {
        console.log('Offline, not sending telemetry');
      }
    }
    ```

1. Aggiungere il codice seguente per creare un'istanza del client:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Aggiungere il codice seguente a:

    * Aprire la connessione.
    * Impostare un gestore per le proprietà desiderate.
    * Inviare le proprietà segnalate.
    * Registrare i gestori dei metodi diretti. L'esempio usa un gestore separato per il metodo diretto di aggiornamento del firmware.
    * Avviare l'invio dei dati di telemetria.

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
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
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
