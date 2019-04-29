---
title: Connettere un'applicazione client Node.js generica ad Azure IoT Central | Microsoft Docs
description: Gli sviluppatori di dispositivo come connettere un dispositivo di Node. js generico per l'applicazione Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 5497e4956fbdc74eced302867c33a66d07d6a184
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888942"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Connettere un'applicazione client generica all'applicazione Azure IoT Central (Node.js)

Questo articolo descrive come connettere, in qualità di sviluppatore di dispositivi, un'applicazione Node.js generica, che rappresenta un dispositivo reale, all'applicazione Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

1. Un'applicazione Azure IoT Central. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
1. Un computer di sviluppo con [Node.js](https://nodejs.org/) versione 4.0.0 o successiva installato. Per controllare la versione, è possibile eseguire `node --version` nella riga di comando. Node.js è disponibile per un'ampia gamma di sistemi operativi.

## <a name="create-a-device-template"></a>Creare un modello di dispositivo

Nell'applicazione Azure IoT Central, è necessario un modello di dispositivo con le misurazioni, le proprietà dei dispositivi, impostazioni e i comandi seguenti:

### <a name="telemetry-measurements"></a>Misure di telemetria

Aggiungere i seguenti dati di telemetria sul **misurazioni** pagina:

| Nome visualizzato | Nome campo  | Unità | Min | Max | Cifre decimali |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperatura | F     | 60  | 110 | 0              |
| Umidità     | umidità    | %     | 0   | 100 | 0              |
| Pressione     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> Il tipo di dati della misura telemetrica è un numero in virgola mobile.

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi di proprietà nel codice del dispositivo corrispondente, i dati di telemetria non può essere visualizzata nell'applicazione.

### <a name="state-measurements"></a>Misure di stato

Aggiungere il seguente stato sul **misurazioni** pagina:

| Nome visualizzato | Nome campo  | Valore 1 | Nome visualizzato | Valore 2 | Nome visualizzato |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Modalità ventola     | fanmode     | 1       | In esecuzione      | 0       | Arrestato      |

> [!NOTE]
> Il tipo di dati della misura dello stato è di tipo stringa.

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi di proprietà nel codice del dispositivo corrispondente, lo stato non è visualizzato nell'applicazione.

### <a name="event-measurements"></a>Misure di evento

Aggiungere il seguente evento nel **misurazioni** pagina:

| Nome visualizzato | Nome campo  | Gravità |
| ------------ | ----------- | -------- |
| Surriscaldamento  | overheat    | Tipi di errore    |

> [!NOTE]
> Il tipo di dati della misura dell'evento è di tipo stringa.

### <a name="device-properties"></a>Proprietà dei dispositivi

Aggiungere le seguenti proprietà del dispositivo sul **proprietà** pagina:

| Nome visualizzato        | Nome campo        | Tipo di dati |
| ------------------- | ----------------- | --------- |
| Numero di serie       | serialNumber      | text      |
| Produttore del dispositivo | manufacturer      | text      |

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi di proprietà nel codice del dispositivo corrispondente, le proprietà non possono essere visualizzate nell'applicazione.

### <a name="settings"></a>Impostazioni

Aggiungere il codice seguente **numero** impostazioni di **impostazioni** pagina:

| Nome visualizzato    | Nome campo     | Unità | Decimali | Min | Max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Velocità della ventola       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Imposta temperatura | setTemperature | F     | 0        | 20  | 200  | 80      |

Immettere nel modello di dispositivo il nome del campo esattamente come è visualizzato nella tabella. Se i nomi dei campi non corrispondono ai nomi di proprietà nel codice del dispositivo corrispondente, il dispositivo non può ricevere il valore dell'impostazione.

### <a name="commands"></a>Comandi:

Aggiungere il seguente comando sul **comandi** pagina:

| Nome visualizzato    | Nome campo     | Timeout predefinito | Tipo di dati |
| --------------- | -------------- | --------------- | --------- |
| Conto alla rovescia       | Conto alla rovescia      | 30              | number    |

Aggiungere il campo di input seguente al comando conto alla rovescia:

| Nome visualizzato    | Nome campo     | Tipo di dati | Value |
| --------------- | -------------- | --------- | ----- |
| Il conteggio da      | countFrom      | number    | 10    |

Immettere i nomi di campo esattamente come indicato nelle tabelle nel modello di dispositivo. Se i nomi dei campi non corrispondono ai nomi di proprietà nel codice del dispositivo corrispondente, il dispositivo non è possibile elaborare il comando.

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central, aggiungere un dispositivo reale al modello del dispositivo creato nella sezione precedente.

Seguire le istruzioni dell'esercitazione "Aggiungere un dispositivo" per [genera una stringa di connessione per il dispositivo reale](tutorial-add-device.md#generate-connection-string). Usare questa stringa di connessione nella sezione seguente:

### <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

I passaggi seguenti illustrano come creare un'applicazione client che implementa il dispositivo reale aggiunto all'applicazione. In questo contesto, l'applicazione Node.js rappresenta il dispositivo reale. 

1. Nel computer creare una cartella denominata `connected-air-conditioner-adv`. Aprire tale cartella nell'ambiente della riga di comando.

1. Per inizializzare il progetto di Node.js, eseguire il comando seguente:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Creare un file denominato **connectedAirConditionerAdv.js** nella cartella `connected-air-conditioner-adv`.

1. Aggiungere le istruzioni `require` seguenti all'inizio del file **connectedAirConditionerAdv.js**:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Aggiungere le dichiarazioni di variabili seguenti al file:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    Aggiornare il segnaposto `{your device connection string}` con il [stringa di connessione del dispositivo](tutorial-add-device.md#generate-connection-string). In questo esempio, si inizializza `targetTemperature` a zero, è possibile usare un valore da un dispositivo gemello o lettura corrente dal dispositivo.

1. Per inviare dati di telemetria, stato e le misurazioni di eventi per l'applicazione Azure IoT Central, aggiungere la funzione seguente al file:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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

1. Aggiungere il codice seguente per gestire un comando del conto alla rovescia inviato dall'applicazione IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
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
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
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

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Eseguire l'applicazione Node.js

Eseguire il comando seguente nell'ambiente della riga di comando:

```cmd/sh
node connectedAirConditionerAdv.js
```

Per il dispositivo reale, un operatore nell'applicazione Azure IoT Central può:

* Visualizzare i dati di telemetria nella pagina **Measurements** (Misure):

    ![Visualizzare i dati di telemetria](media/howto-connect-nodejs/viewtelemetry.png)

* Visualizzare i valori delle proprietà del dispositivo inviati dal dispositivo nella pagina **Proprietà**. La proprietà riquadri aggiornamento del dispositivo quando si connette il dispositivo:

    ![Visualizzare le proprietà del dispositivo](media/howto-connect-nodejs/viewproperties.png)

* Impostare la temperatura della ventola velocità e la destinazione dal **impostazioni** pagina:

    ![Impostare la velocità della ventola](media/howto-connect-nodejs/setfanspeed.png)

* Chiamare il comando del conto alla rovescia dal **comandi** pagina:

    ![Chiamare il comando del conto alla rovescia](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come connettere un client Node. js generico per l'applicazione Azure IoT Central, il passaggio successivo consigliato consiste informazioni su come [configurare un modello di dispositivo personalizzata](howto-set-up-template.md) per dispositivo IoT.
