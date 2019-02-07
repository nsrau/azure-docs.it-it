---
title: Effettuare il provisioning di dispositivi nella soluzione di monitoraggio remoto in Node.js - Azure | Microsoft Docs
description: Descrive come connettere un dispositivo all'acceleratore di soluzioni di monitoraggio remoto con un'applicazione scritta in Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: e7a9659a7796777cabb6dc73a41e3a361fd1733c
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567963"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Connettere il dispositivo all'acceleratore di soluzioni di monitoraggio remoto (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Questa esercitazione mostra come connettere un dispositivo reale all'acceleratore di soluzioni di monitoraggio remoto. In questa esercitazione viene usato Node.js, una buona scelta per gli ambienti con vincoli minimi in termine di risorse.

Se si preferisce simulare un dispositivo, vedere [Creare e testare un nuovo dispositivo simulato](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="create-a-nodejs-solution"></a>Creare una soluzione Node.js

Assicurarsi che nel computer di sviluppo sia installato [Node.js](https://nodejs.org/) 4.0.0 o versioni successive. Per controllare la versione è possibile eseguire `node --version` nella riga di comando.

1. Nel computer di sviluppo creare una cartella denominata `remotemonitoring`. Aprire la cartella nell'ambiente della riga di comando.

1. Per scaricare e installare i pacchetti necessari per completare l'app di esempio, eseguire i comandi seguenti:

    ```cmd/sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. Nella cartella `remotemonitoring` creare un file denominato **remote_monitoring.js**. Aprire il file in un editor di testo.

1. Nel file **remote_monitoring.js**, aggiungere le istruzioni `require` seguenti:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Aggiungere le seguenti dichiarazioni di variabili dopo le istruzioni `require` . Sostituire il valore segnaposto `{device connection string}` con il valore annotato per il dispositivo di cui è stato eseguito il provisioning nella soluzione di monitoraggio remoto:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Per definire alcuni dati di telemetria di base, aggiungere le variabili seguenti:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Per definire alcuni valori di proprietà, aggiungere le variabili seguenti:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Aggiungere la variabile seguente per definire le proprietà indicate da inviare alla soluzione. Queste proprietà includono i metadati da visualizzare nell'interfaccia utente Web:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
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

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Aggiungere la funzione helper seguente per impostare in modo casuale i valori di telemetria:

    ```javascript
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Aggiungere la funzione generica seguente per gestire le chiamate ai metodi diretti dalla soluzione. La funzione visualizza informazioni sul metodo diretto richiamato, ma in questo esempio non modifica il dispositivo in alcun modo. La soluzione usa metodi diretti per intervenire sui dispositivi:

    ```javascript
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

    ```javascript
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

    ```javascript
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

    ```javascript
    function sendTelemetry(data, schema) {
      if (deviceOnline) {
        var d = new Date();
        var payload = JSON.stringify(data);
        var message = new Message(payload);
        message.properties.add('iothub-creation-time-utc', d.toISOString());
        message.properties.add('iothub-message-schema', schema);

        console.log('Sending device message data:\n' + payload);
        client.sendEvent(message, printErrorFor('send event'));
      } else {
        console.log('Offline, not sending telemetry');
      }
    }
    ```

1. Aggiungere il codice seguente per creare un'istanza del client:

    ```javascript
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Aggiungere il codice seguente a:

    * Aprire la connessione.
    * Impostare un gestore per le proprietà desiderate.
    * Inviare le proprietà segnalate.
    * Registrare i gestori dei metodi diretti. L'esempio usa un gestore separato per il metodo diretto di aggiornamento del firmware.
    * Avviare l'invio dei dati di telemetria.

    ```javascript
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
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
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

1. Per avviare l'applicazione di esempio, eseguire il comando seguente al prompt dei comandi:

    ```cmd/sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
