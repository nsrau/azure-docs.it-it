---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487811"
---
Plug and Play IoT semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare Node.js per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Per completare questa guida di avvio rapido, è necessario Node.js nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonare il repository dell'SDK con il codice di esempio

Clonare gli esempi dal [repository dell'SDK per Node](https://github.com/Azure/azure-iot-sdk-node). Aprire una finestra terminale in una cartella di propria scelta. Eseguire questo comando per clonare il repository GitHub di [Microsoft Azure IoT SDK per Node.js](https://github.com/Azure/azure-iot-sdk-node):

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

In questa guida di avvio rapido come dispositivo Plug and Play IoT verrà usato un dispositivo termostato di esempio scritto in Node.js. Per eseguire il dispositivo di esempio:

1. Aprire una finestra del terminale e passare alla cartella locale che contiene il repository Microsoft Azure IoT SDK per Node.js clonato da GitHub.

1. Questa finestra del terminale viene ora usata come terminale del **dispositivo**. Passare alla cartella del repository clonato e quindi alla cartella */azure-iot-sdk-node/device/samples/pnp*. Installare tutte le dipendenze eseguendo il comando seguente:

    ```cmd/sh
    npm install
    ```

1. Eseguire il dispositivo termostato di esempio con il comando seguente:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Questi messaggi indicano che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questo terminale perché sarà necessario per verificare il funzionamento del servizio di esempio.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In [Configurare l'ambiente per le esercitazioni e le guide di avvio rapido di Plug and Play IoT](../articles/iot-pnp/set-up-environment.md) sono state create due variabili di ambiente per configurare l'esempio da connettere all'hub IoT e al dispositivo:

* **IOTHUB_CONNECTION_STRING**: stringa di connessione dell'hub IoT annotata in precedenza.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

In questa guida di avvio rapido si userà una soluzione IoT di esempio in Node.js per interagire con il dispositivo di esempio appena configurato.

1. Aprire un'altra finestra del terminale da usare come terminale del **servizio**.

1. Nel repository dell'SDK per Node.js clonato passare alla cartella */azure-iot-sdk-node/service/samples/javascript*. Installare tutte le dipendenze eseguendo il comando seguente:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Leggere una proprietà

1. Quando è stato eseguito il dispositivo termostato di esempio nel terminale del **dispositivo**, sono stati visualizzati i messaggi seguenti che ne indicano lo stato online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Passare al terminale del **servizio** e usare questo comando per eseguire l'esempio per la lettura delle informazioni sul dispositivo:

    ```cmd/sh
    node twin.js
    ```

1. Nell'output del terminale del **servizio** verificare la risposta del dispositivo gemello. Verificare l'ID modello del dispositivo e le proprietà associate segnalate:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. Il frammento di codice seguente mostra il codice in *twin.js* che recupera l'ID modello del dispositivo gemello:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

In questo scenario restituisce come output `Model Id: dtmi:com:example:Thermostat;1`.

> [!NOTE]
> Questi esempi di servizio usano la classe **Registry** del **client del servizio hub IoT**. Per altre informazioni sulle API, tra cui l'API gemelli digitali, vedere la [guida per sviluppatori di servizi](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="update-a-writable-property"></a>Aggiornare una proprietà scrivibile

1. Aprire il file *twin.js* in un editor di codice.

1. Esaminare il codice di esempio, in cui vengono illustrati due modi per aggiornare il dispositivo gemello. Per usare il primo modo, modificare la variabile `twinPatch` come indicato di seguito:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    La proprietà `targetTemperature` è definita come proprietà scrivibile nel modello di dispositivo termostato.

1. Nel terminale del **servizio** usare questo comando per eseguire l'esempio per l'aggiornamento della proprietà:

    ```cmd/sh
    node twin.js
    ```

1. Nel terminale del **dispositivo** verificare che il dispositivo abbia ricevuto l'aggiornamento:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. Nel terminale del **servizio** eseguire questo comando per confermare che la proprietà è stata aggiornata:

    ```cmd/sh
    node twin.js
    ```

1. Nell'output del terminale del **servizio**, nella sezione delle proprietà `reported`, verrà visualizzata la temperatura di destinazione aggiornata segnalata. L'aggiornamento del dispositivo potrebbe richiedere tempo. Ripetere questo passaggio fino a quando il dispositivo non ha elaborato l'aggiornamento della proprietà:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Richiamare un comando

1. Aprire il file *device_method.js* ed esaminare il codice.

1. Passare al terminale del **servizio**. Usare questo comando per eseguire l'esempio per la chiamata del comando:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. L'output nel terminale del **servizio** mostra la conferma seguente:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. Nel terminale del **dispositivo** verificare che il comando sia stato riconosciuto:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
