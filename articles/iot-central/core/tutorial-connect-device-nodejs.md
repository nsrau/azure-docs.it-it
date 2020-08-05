---
title: "Esercitazione: Connettere un'app client Node.js generica ad Azure IoT Central | Microsoft Docs"
description: Questa esercitazione illustra agli sviluppatori di dispositivi come connettere un dispositivo che esegue un'app client Node.js all'applicazione Azure IoT Central. Per creare un modello di dispositivo, importare un modello di funzionalità di dispositivo e aggiungere visualizzazioni che consentono di interagire con un dispositivo connesso
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt, devx-track-javascript
ms.openlocfilehash: 92b28b89d181de368ef1e39eb695be2e1fad6c37
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423523"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Esercitazione: Creare e connettere un'applicazione client all'applicazione Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Le informazioni di questo articolo sono destinate a generatori di soluzioni e sviluppatori di dispositivi.*

Questa esercitazione illustra agli sviluppatori di dispositivi come connettere un'applicazione client Node.js all'applicazione Azure IoT Central. L'applicazione Node.js simula il comportamento di un dispositivo sensore ambientale reale. Verrà usato un _modello di funzionalità di dispositivo_ di esempio per creare un _modello di dispositivo_ in IoT Central. È possibile aggiungere visualizzazioni al modello di dispositivo per consentire a un operatore di interagire con un dispositivo.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Importare un modello di funzionalità di dispositivo per creare un modello di dispositivo.
> * Aggiungere visualizzazioni predefinite e personalizzate a un modello di dispositivo.
> * Pubblicare un modello di dispositivo e aggiungere un dispositivo reale all'applicazione IoT Central.
> * Creare ed eseguire il codice del dispositivo Node.js per connetterlo all'applicazione IoT Central.
> * Visualizzare i dati di telemetria simulati inviati dal dispositivo.
> * Usare una visualizzazione per gestire le proprietà del dispositivo.
> * Chiamare i comandi sincroni e asincroni per controllare il dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Un'applicazione Azure IoT Central creata usando il modello **Applicazione personalizzata**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md). L'applicazione deve essere stata creata a partire dal 14/07/2020 incluso.
* Un computer di sviluppo con [Node.js](https://nodejs.org/) versione 10.0.0 o successiva installato. Per controllare la versione, è possibile eseguire `node --version` nella riga di comando. Le istruzioni riportate in questa esercitazione presuppongono che il comando **node** venga eseguito al prompt dei comandi di Windows. È tuttavia possibile usare Node.js in numerosi altri sistemi operativi.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

I passaggi seguenti illustrano come creare un'applicazione client Node.js che si connette al dispositivo reale aggiunto all'applicazione. L'applicazione Node.js simula il comportamento di un dispositivo reale.

1. Nell'ambiente della riga di comando passare alla cartella `environmental-sensor` creata in precedenza.

1. Per inizializzare il progetto Node.js e installare le dipendenze necessarie, eseguire i comandi seguenti e accettare tutte le opzioni predefinite quando si esegue `npm init`:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Creare un file denominato **environmentalSensor.js** nella cartella `environmental-sensor`.

1. Aggiungere le istruzioni `require` seguenti all'inizio del file **environmentalSensor.js**:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Aggiungere le dichiarazioni di variabili seguenti al file:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    Aggiornare i segnaposto `{your Scope ID}`, `{your Device ID}` e `{your Primary Key}` con i valori annotati in precedenza. In questo esempio il valore `targetTemperature` viene inizializzato impostandolo su zero. È anche possibile usare la lettura corrente dal dispositivo o il valore dal dispositivo gemello.

1. Per inviare i dati di telemetria simulati all'applicazione Azure IoT Central, aggiungere la funzione seguente al file:

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    I nomi degli elementi di telemetria (`temp` e `humid`) devono corrispondere ai nomi usati nel modello di dispositivo.

1. Per inviare le proprietà del dispositivo gemello all'applicazione Azure IoT Central, aggiungere la funzione seguente al file:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central usa dispositivi gemelli per sincronizzare i valori delle proprietà tra il dispositivo e l'applicazione IoT Central. I valori delle proprietà del dispositivo usano le proprietà segnalate del dispositivo gemello. Le proprietà scrivibili usano sia le proprietà segnalate che quelle desiderate del dispositivo gemello.

1. Per definire e gestire le proprietà scrivibili a cui risponde il dispositivo, aggiungere il codice seguente. Il messaggio inviato dal dispositivo in risposta all'[aggiornamento delle proprietà scrivibili](concepts-telemetry-properties-commands.md#writeable-property-types) deve includere i campi `av` e `ac`. Il campo `ad` è facoltativo:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed', 200);
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed', 200);
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting]}`);
            writeableProperties[setting](desiredChange[setting], (newValue, status, code) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  ad: status,
                  ac: code,
                  av: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Quando l'operatore imposta una proprietà scrivibile nell'applicazione IoT Central, l'applicazione usa una proprietà desiderata del dispositivo gemello per inviare il valore al dispositivo. Il dispositivo risponde quindi usando una proprietà segnalata dal dispositivo gemello. Quando IoT Central riceve il valore della proprietà segnalata, aggiorna la visualizzazione delle proprietà con lo stato **sincronizzato**.

    I nomi delle proprietà (`name` e `brightness`) devono corrispondere ai nomi usati nel modello di dispositivo.

1. Aggiungere il codice seguente per gestire i comandi inviati dall'applicazione IoT Central:

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    I nomi dei comandi (`blink`, `turnon`, `turnoff` e `rundiagnostics`) devono corrispondere ai nomi usati nel modello di dispositivo.

    Attualmente, IoT Central non usa lo schema di risposta definito nel modello di funzionalità di dispositivo. Per un comando sincrono, il payload della risposta può essere qualsiasi JSON valido. Per un comando asincrono, il dispositivo deve restituire immediatamente una risposta 202, seguita dall'aggiornamento della proprietà segnalata al termine dell'attività. Il formato dell'aggiornamento della proprietà segnalata è:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Un operatore può visualizzare il payload della risposta nella cronologia dei comandi.

1. Aggiungere il codice seguente per completare la connessione ad Azure IoT Central e associare le funzioni nel codice client:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true',
              processorArchitecture: 'ARM',
              swVersion: '1.0.0'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Eseguire l'applicazione Node.js

Per avviare l'applicazione client del dispositivo, immettere il comando seguente nell'ambiente della riga di comando:

```cmd/sh
node environmentalSensor.js
```

È possibile osservare che il dispositivo si connette all'applicazione Azure IoT Central e inizia a inviare i dati di telemetria:

![Eseguire l'applicazione client](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

È possibile visualizzare il modo in cui il dispositivo risponde ai comandi e agli aggiornamenti delle proprietà:

![Osservare l'applicazione client](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="view-raw-data"></a>Visualizzare dati non elaborati

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base relative alla creazione di un dispositivo con Node.js, i passaggi successivi consigliati per gli sviluppatori di dispositivi sono i seguenti:

* Vedere [Che cosa sono i modelli di dispositivo?](./concepts-device-templates.md) per altre informazioni sul ruolo dei modelli di dispositivo durante l'implementazione del codice del dispositivo.
* Articolo [Connettersi ad Azure IoT Central](./concepts-get-connected.md) per altre informazioni su come registrare dispositivi con IoT Central e sulla protezione delle connessioni ai dispositivi in IoT Central.

Se si preferisce continuare il set di esercitazioni di IoT Central e saperne di più sulla creazione di una soluzione di IoT Central, vedere:

> [!div class="nextstepaction"]
> [Creare un modello di dispositivo gateway](./tutorial-define-gateway-device-type.md)
