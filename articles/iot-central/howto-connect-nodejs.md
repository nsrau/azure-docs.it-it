---
title: Connettere un'applicazione client Node.js generica ad Azure IoT Central | Microsoft Docs
description: Come connettere un dispositivo Node.js generico all'applicazione Azure IoT Central in qualità di sviluppatore di dispositivi.
author: dominicbetts
ms.author: dobett
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: cc6857750534dad2ded29178eb3f140fc25cce0d
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410490"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Connettere un'applicazione client generica all'applicazione Azure IoT Central (Node.js)

Questo articolo descrive come connettere, in qualità di sviluppatore di dispositivi, un'applicazione Node.js generica, che rappresenta un dispositivo reale, all'applicazione Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

1. Un'applicazione Azure IoT Central. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
1. Un computer di sviluppo con [Node.js](https://nodejs.org/) versione 4.0.0 o successiva installato. Per controllare la versione, è possibile eseguire `node --version` nella riga di comando. Node.js è disponibile per un'ampia gamma di sistemi operativi.

## <a name="create-a-device-template"></a>Creare un modello di dispositivo

Nell'applicazione Azure IoT Central è necessario un modello di dispositivo con le misure e le proprietà definite seguenti:

### <a name="telemetry-measurements"></a>Misure di telemetria

Aggiungere i dati di telemetria seguenti nella pagina **Measurements** (Misure):

| Nome visualizzato | Nome campo  | Unità | Min | Max | Cifre decimali |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperatura | F     | 60  | 110 | 0              |
| Umidità     | umidità    | %     | 0   | 100 | 0              |
| Pressione     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  Il tipo di dati della misura telemetrica è un numero in virgola mobile.

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi proprietà nel codice dispositivo corrispondente, i dati di telemetria non possono essere visualizzati nell'applicazione.

### <a name="state-measurements"></a>Misure di stato

Aggiungere lo stato seguente nella pagina **Measurements** (Misure):

| Nome visualizzato | Nome campo  | Valore 1 | Nome visualizzato | Valore 2 | Nome visualizzato |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Modalità ventola     | fanmode     | 1       | In esecuzione      | 0       | Arrestato      |

> [!NOTE]
  Il tipo di dati della misura dello stato è di tipo stringa.

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi proprietà nel codice dispositivo corrispondente, lo stato non può essere visualizzato nell'applicazione.

### <a name="event-measurements"></a>Misure di evento

Aggiungere l'evento seguente nella pagina **Measurements** (Misure):

| Nome visualizzato | Nome campo  | Gravità |
| ------------ | ----------- | -------- |
| Surriscaldamento  | overheat    | Tipi di errore    |

> [!NOTE]
  Il tipo di dati della misura dell'evento è di tipo stringa.

### <a name="device-properties"></a>Proprietà dei dispositivi

Aggiungere le proprietà dei dispositivi seguenti nella **pagina delle proprietà**:

| Nome visualizzato        | Nome campo        | Tipo di dati |
| ------------------- | ----------------- | --------- |
| Numero di serie       | serialNumber      | text      |
| Produttore del dispositivo | manufacturer      | text      |

Immettere nel modello di dispositivo i nomi dei campi esattamente come sono visualizzati nella tabella. Se i nomi dei campi non corrispondono ai nomi proprietà nel codice dispositivo corrispondente, l'applicazione non può visualizzare il valore proprietà del dispositivo.

### <a name="settings"></a>Impostazioni

Aggiungere le impostazioni **numeriche** seguenti nella **pagina delle impostazioni**:

| Nome visualizzato    | Nome campo     | Unità | Decimali | Min | Max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Velocità della ventola       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Imposta temperatura | setTemperature | F     | 0        | 20  | 200  | 80      |

Immettere nel modello di dispositivo il nome del campo esattamente come è visualizzato nella tabella. Se i nomi dei campi non corrispondono ai nomi proprietà nel codice dispositivo corrispondente, il dispositivo non può ricevere il valore di impostazione.

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central aggiungere un dispositivo reale dal modello di dispositivo creato e prendere nota della stringa di connessione del dispositivo. Per istruzioni dettagliate su come connettere un'applicazione Node.js a IoT Central, vedere [Generare dall'applicazione la stringa di connessione per un dispositivo reale](tutorial-add-device.md#generate-connection-string-for-real-device-from-application) e [Preparare il codice client](tutorial-add-device.md#prepare-the-client-code) in Esercitazioni > Aggiungere un dispositivo.

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

  > [!NOTE]
  > Azure IoT Central ha eseguito la transizione all'uso del servizio Device Provisioning in hub IoT di Azure per tutte le connessioni dei dispositivi. Seguire queste istruzioni per [ottenere la stringa di connessione del dispositivo](concepts-connectivity.md#getting-device-connection-string) e continuare con il resto dell'esercitazione. Per altre informazioni, è disponibile anche un set completo di istruzioni nella sezione [Preparare il codice client](tutorial-add-device.md#prepare-the-client-code) in Esercitazioni > Aggiungere un dispositivo.


  Aggiornare il segnaposto `{your device connection string}` con la stringa di connessione del dispositivo. In questo esempio il valore `targetTemperature` viene inizializzato impostandolo su zero. Se si preferisce, è possibile usare la lettura corrente dal dispositivo o il valore dal dispositivo gemello. 

1. Per inviare le misure relative a telemetria, stato ed evento all'applicazione Azure IoT Central, aggiungere la funzione seguente al file:

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
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
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

1. Aggiungere il codice seguente per completare la connessione ad Azure IoT Central e associare le funzioni nel codice client:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
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

* Visualizzare i valori delle proprietà del dispositivo inviati dal dispositivo nella pagina **Proprietà**. I riquadri delle proprietà dispositivo verranno aggiornati se la connessione ha esito positivo. 

    ![Visualizzare le proprietà del dispositivo](media/howto-connect-nodejs/viewproperties.png)

* Impostare la velocità della ventola e la temperatura di destinazione dalla pagina **Impostazioni**. Se la connessione ha esito positivo, verranno sincronizzati i valori delle impostazioni. 

    ![Impostare la velocità della ventola](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per connettere un client Node.js generico all'applicazione Azure IoT Central, ecco i passaggi successivi suggeriti:
* [Preparare e connettere un'applicazione Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
