---
title: Connettere un'app client Node. js generica ad Azure IoT Central | Microsoft Docs
description: Come sviluppatore di dispositivi, come connettere un dispositivo node. js generico all'applicazione IoT Central di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 09/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 87dbd7ab4d75150d09a8c26db50ce2e3b1a085db
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930248"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Connettere un'applicazione client generica all'applicazione Azure IoT Central (Node.js)

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Questo articolo descrive come connettere, in qualità di sviluppatore di dispositivi, un'applicazione Node.js generica, che rappresenta un dispositivo reale, all'applicazione Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Un'applicazione Azure IoT Central. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
- Un computer di sviluppo con [Node.js](https://nodejs.org/) versione 4.0.0 o successiva installato. Per controllare la versione, è possibile eseguire `node --version` nella riga di comando. Node.js è disponibile per un'ampia gamma di sistemi operativi.

## <a name="create-a-device-template"></a>Creare un modello di dispositivo

Nell'applicazione IoT Central di Azure è necessario un modello di dispositivo con le misurazioni, le proprietà del dispositivo, le impostazioni e i comandi seguenti:

### <a name="telemetry-measurements"></a>Misure di telemetria

Aggiungere i dati di telemetria seguenti nella pagina **misure** :

| Nome visualizzato | Nome campo  | Unità | Min | max | Cifre decimali |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperatura | F     | 60  | 110 | 0              |
| Umidità     | umidità    | %     | 0   | 100 | 0              |
| Pressione     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> Il tipo di dati della misura telemetrica è un numero in virgola mobile.

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi delle proprietà nel codice del dispositivo corrispondente, i dati di telemetria non possono essere visualizzati nell'applicazione.

### <a name="state-measurements"></a>Misure di stato

Aggiungere lo stato seguente nella pagina **misure** :

| Nome visualizzato | Nome campo  | Valore 1 | Nome visualizzato | Valore 2 | Nome visualizzato |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Modalità ventola     | fanmode     | 1       | In esecuzione      | 0       | Stopped      |

> [!NOTE]
> Il tipo di dati della misura dello stato è di tipo stringa.

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi delle proprietà nel codice del dispositivo corrispondente, non è possibile visualizzare lo stato nell'applicazione.

### <a name="event-measurements"></a>Misure di evento

Aggiungere l'evento seguente nella pagina **misure** :

| Nome visualizzato | Nome campo  | Gravità |
| ------------ | ----------- | -------- |
| Surriscaldamento  | overheat    | Tipi di errore    |

> [!NOTE]
> Il tipo di dati della misura dell'evento è di tipo stringa.

### <a name="location-measurements"></a>Misurazioni località

Aggiungere la misurazione percorso seguente nella pagina **misure** :

| Nome visualizzato | Nome campo  |
| ------------ | ----------- |
| Location     | location    |

Il tipo di dati di misurazione della posizione è costituito da due numeri a virgola mobile per longitudine e latitudine e da un numero a virgola mobile facoltativo per l'altitudine.

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi delle proprietà nel codice del dispositivo corrispondente, il percorso non può essere visualizzato nell'applicazione.

### <a name="device-properties"></a>Proprietà dei dispositivi

Aggiungere le seguenti proprietà del dispositivo nella pagina delle **Proprietà** :

| Nome visualizzato        | Nome campo        | Tipo di dati |
| ------------------- | ----------------- | --------- |
| Numero di serie       | serialNumber      | text      |
| Produttore del dispositivo | manufacturer      | text      |

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi delle proprietà nel codice del dispositivo corrispondente, le proprietà non possono essere visualizzate nell'applicazione.

### <a name="settings"></a>Settings

Aggiungere le seguenti impostazioni di **numero** nella pagina **Impostazioni** :

| Nome visualizzato    | Nome campo     | Unità | Decimali | Min | max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Velocità della ventola       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Imposta temperatura | setTemperature | F     | 0        | 20  | 200  | 80      |

Immettere nel modello di dispositivo il nome del campo esattamente come è visualizzato nella tabella. Se i nomi dei campi non corrispondono ai nomi delle proprietà nel codice del dispositivo corrispondente, il dispositivo non può ricevere il valore dell'impostazione.

### <a name="commands"></a>Comandi:

Aggiungere il comando seguente nella pagina **comandi** :

| Nome visualizzato    | Nome campo     | Timeout predefinito | Tipo di dati |
| --------------- | -------------- | --------------- | --------- |
| Conto alla rovescia       | Conto alla rovescia      | 30              | number    |

Aggiungere il campo di input seguente al comando Countdown:

| Nome visualizzato    | Nome campo     | Tipo di dati | Valore |
| --------------- | -------------- | --------- | ----- |
| Conteggio da      | countFrom      | number    | 10    |

Immettere i nomi dei campi esattamente come illustrato nelle tabelle nel modello di dispositivo. Se i nomi dei campi non corrispondono ai nomi delle proprietà nel codice del dispositivo corrispondente, il dispositivo non è in grado di elaborare il comando.

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione IoT Central di Azure aggiungere un dispositivo reale al modello di dispositivo creato nella sezione precedente.

Prendere nota delle informazioni di connessione del dispositivo nella pagina **connessione del dispositivo** : **ID ambito**, **ID dispositivo**e **chiave primaria**. Questi valori vengono aggiunti nel codice del dispositivo più avanti in questa guida dettagliata:

![Informazioni di connessione del dispositivo](./media/howto-connect-nodejs/device-connection.png)

### <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

I passaggi seguenti illustrano come creare un'applicazione client che implementa il dispositivo reale aggiunto all'applicazione. In questo contesto, l'applicazione Node.js rappresenta il dispositivo reale.

1. Nel computer creare una cartella denominata `connected-air-conditioner-adv`. Aprire tale cartella nell'ambiente della riga di comando.

1. Per inizializzare il progetto di Node.js, eseguire il comando seguente:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Creare un file denominato **connectedAirConditionerAdv.js** nella cartella `connected-air-conditioner-adv`.

1. Aggiungere le istruzioni `require` seguenti all'inizio del file **connectedAirConditionerAdv.js**:

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
    var locLong = -122.1215;
    var locLat = 47.6740;
    ```

    Aggiornare i segnaposto `{your Scope ID}`, `{your Device ID}`e `{your Primary Key}` con i valori annotati in precedenza. In questo esempio si inizializza `targetTemperature` su zero, è possibile usare la lettura corrente dal dispositivo o un valore del dispositivo gemello.

1. Per inviare le misurazioni di telemetria, stato, evento e località all'applicazione IoT Central di Azure, aggiungere la funzione seguente al file:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Per inviare le proprietà del dispositivo all'applicazione Azure IoT Central, aggiungere la funzione seguente al file:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Per definire le impostazioni a cui il dispositivo risponde, aggiungere la definizione seguente:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Per gestire le impostazioni aggiornate dall'applicazione Azure IoT Central, aggiungere il codice seguente al file:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Aggiungere il codice seguente per gestire un comando di conto alla rovescia inviato dall'applicazione IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');
    
      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;
    
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          hubClient.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }
    
            doCountdown();
          });
        }
      });
    }
    ```

1. Aggiungere il codice seguente per completare la connessione ad Azure IoT Central e associare le funzioni nel codice client:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        hubClient.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
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

Eseguire il comando seguente nell'ambiente della riga di comando:

```cmd/sh
node connectedAirConditionerAdv.js
```

Per il dispositivo reale, un operatore nell'applicazione Azure IoT Central può:

* Visualizzare i dati di telemetria nella pagina **Measurements** (Misure):

    ![Visualizzare i dati di telemetria](media/howto-connect-nodejs/viewtelemetry.png)

* Visualizzare il percorso nella pagina **misure** :

    ![Visualizza misure posizione](media/howto-connect-nodejs/viewlocation.png)

* Visualizzare i valori delle proprietà del dispositivo inviati dal dispositivo nella pagina **Proprietà**. I riquadri delle proprietà del dispositivo vengono aggiornati quando si connette il dispositivo:

    ![Visualizzare le proprietà del dispositivo](media/howto-connect-nodejs/viewproperties.png)

* Impostare la velocità della ventola e la temperatura di destinazione dalla pagina **Impostazioni** :

    ![Impostare la velocità della ventola](media/howto-connect-nodejs/setfanspeed.png)

* Chiamare il comando Countdown dalla pagina **comandi** :

    ![Comando call Countdown](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un client Node. js generico all'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [configurare un modello di dispositivo personalizzato](howto-set-up-template.md) per il proprio dispositivo.
