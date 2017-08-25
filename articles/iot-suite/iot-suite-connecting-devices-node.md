---
title: Connettere un dispositivo con Node.js | Documentazione Microsoft
description: "Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in Node.js."
services: 
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
ms.date: 08/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 8f45d0e86a95779d5ceeddb72638b14e0e7a80eb
ms.contentlocale: it-it
ms.lasthandoff: 04/10/2017

---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-nodejs-sample-solution"></a>Creare una soluzione di esempio di Node.js

Verificare che nel computer di sviluppo sia installato Node.js 0.11.5 o versione successiva. Per controllare la versione è possibile eseguire `node --version` nella riga di comando.

1. Nel computer di sviluppo creare una cartella denominata **RemoteMonitoring**. Aprire la cartella nell'ambiente della riga di comando.

1. Eseguire i comandi seguenti per scaricare e installare i pacchetti necessari per completare l'applicazione di esempio:

    ```
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Nella cartella **RemoteMonitoring**, creare un file denominato **remote_monitoring.js**. Aprire il file in un editor di testo.

1. Nel file **remote_monitoring.js**, aggiungere le istruzioni `require` seguenti:

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Aggiungere le seguenti dichiarazioni di variabili dopo le istruzioni `require` . Sostituire i valori segnaposto [ID dispositivo] e [Chiave dispositivo] con i valori annotati per il dispositivo nel dashboard della soluzione di monitoraggio remoto. Usare il nome host hub IoT del dashboard della soluzione per sostituire [Nome IoTHub]. Ad esempio, se il nome host dell'hub IoT è **contoso.azure-devices.net**, sostituire [Nome IoTHub] con **contoso**:

    ```nodejs
    var connectionString = 'HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Aggiungere le seguenti variabili per definire alcuni dati di telemetria di base:

    ```nodejs
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    ```

1. Aggiungere la funzione helper seguente per stampare i risultati dell'operazione:

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

1. Aggiungere la definizione seguente per l'oggetto **DeviceInfo** inviato dal dispositivo all'avvio:

    ```nodejs
    var deviceMetaData = {
        'ObjectType': 'DeviceInfo',
        'IsSimulatedDevice': 0,
        'Version': '1.0',
        'DeviceProperties': {
            'DeviceID': deviceId,
            'HubEnabledState': 1
        }
    };
    ```

1. Aggiungere la definizione seguente per i valori segnalati del dispositivo gemello. La definizione include le descrizioni dei metodi diretti supportati dal dispositivo:

    ```nodejs
    var reportedProperties = {
        "Device": {
            "DeviceState": "normal",
            "Location": {
                "Latitude": 47.642877,
                "Longitude": -122.125497
            }
        },
        "Config": {
            "TemperatureMeanValue": 56.7,
            "TelemetryInterval": 45
        },
        "System": {
            "Manufacturer": "Contoso Inc.",
            "FirmwareVersion": "2.22",
            "InstalledRAM": "8 MB",
            "ModelNumber": "DB-14",
            "Platform": "Plat 9.75",
            "Processor": "i3-9",
            "SerialNumber": "SER99"
        },
        "Location": {
            "Latitude": 47.642877,
            "Longitude": -122.125497
        },
        "SupportedMethods": {
            "Reboot": "Reboot the device",
            "InitiateFirmwareUpdate--FwPackageURI-string": "Updates device Firmware. Use parameter FwPackageURI to specifiy the URI of the firmware file"
        },
    }
    ```

1. Aggiungere la funzione seguente per gestire la chiamata al metodo diretto **Reboot**:

    ```nodejs
    function onReboot(request, response) {
        // Implement actual logic here.
        console.log('Simulated reboot...');

        // Complete the response
        response.send(200, "Rebooting device", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

1. Aggiungere la funzione seguente per gestire la chiamata al metodo diretto **InitiateFirmwareUpdate**. Questo metodo diretto usa un parametro per specificare il percorso dell'immagine del firmware da scaricare, e avvia l'aggiornamento del firmware sul dispositivo in modo asincrono:

    ```nodejs
    function onInitiateFirmwareUpdate(request, response) {
        console.log('Simulated firmware update initiated, using: ' + request.payload.FwPackageURI);

        // Complete the response
        response.send(200, "Firmware update initiated", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });

        // Add logic here to perform the firmware update asynchronously
    }
    ```

1. Aggiungere il codice seguente per creare un'istanza del client:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Aggiungere il codice seguente a:

    * Aprire la connessione.
    * Inviare l'oggetto **DeviceInfo**.
    * Impostare un gestore per le proprietà desiderate.
    * Inviare le proprietà segnalate.
    * Registrare i gestori dei metodi diretti.
    * Avviare l'invio dei dati di telemetria.

    ```nodejs
    client.open(function (err) {
        if (err) {
            printErrorFor('open')(err);
        } else {
            console.log('Sending device metadata:\n' + JSON.stringify(deviceMetaData));
            client.sendEvent(new Message(JSON.stringify(deviceMetaData)), printErrorFor('send metadata'));

            // Create device twin
            client.getTwin(function(err, twin) {
                if (err) {
                    console.error('Could not get device twin');
                } else {
                    console.log('Device twin created');

                    twin.on('properties.desired', function(delta) {
                        console.log('Received new desired properties:');
                        console.log(JSON.stringify(delta));
                    });

                    // Send reported properties
                    twin.properties.reported.update(reportedProperties, function(err) {
                        if (err) throw err;
                        console.log('twin state reported');
                    });

                    // Register handlers for direct methods
                    client.onDeviceMethod('Reboot', onReboot);
                    client.onDeviceMethod('InitiateFirmwareUpdate', onInitiateFirmwareUpdate);
                }
            });

            // Start sending telemetry
            var sendInterval = setInterval(function () {
                temperature += generateRandomIncrement();
                externalTemperature += generateRandomIncrement();
                humidity += generateRandomIncrement();

                var data = JSON.stringify({
                    'DeviceID': deviceId,
                    'Temperature': temperature,
                    'Humidity': humidity,
                    'ExternalTemperature': externalTemperature
                });

                console.log('Sending device event data:\n' + data);
                client.sendEvent(new Message(data), printErrorFor('send event'));
            }, 5000);

            client.on('error', function (err) {
                printErrorFor('client')(err);
                if (sendInterval) clearInterval(sendInterval);
                client.close(printErrorFor('client.close'));
            });
        }
    });
    ```

1. Salvare le modifiche apportate al file **remote_monitoring.js**.

1. Eseguire il comando seguente al prompt dei comandi per avviare l'applicazione di esempio:
   
    ```
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

