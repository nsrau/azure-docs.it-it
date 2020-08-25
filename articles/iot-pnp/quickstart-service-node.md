---
title: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione IoT di Azure (Node.js) | Microsoft Docs
description: Usare Node.js pere connettersi e interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione IoT di Azure.
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: fd65dcc9ce0be07daa5848a0ac583cf795150e47
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184755"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Avvio rapido: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

Plug and Play IoT (anteprima) semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare Node.js per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessario Node.js nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
node --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire il comando seguente per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questo argomento di avvio rapido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Eseguire questo comando per ottenere la _stringa di connessione del dispositivo_ aggiunto all'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questo argomento di avvio rapido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonare il repository dell'SDK con il codice di esempio

L'SDK del servizio è disponibile in anteprima, quindi è necessario clonare gli esempi da un [ramo di anteprima di Node SDK](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh). Aprire una finestra terminale in una cartella di propria scelta. Eseguire il comando seguente per clonare il ramo **pnp-preview-refresh** del repository GitHub [Microsoft Azure IoT SDK per Node.js](https://github.com/Azure/azure-iot-sdk-node):

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

In questa guida di avvio rapido è possibile usare come dispositivo Plug and Play IoT un dispositivo termostato di esempio scritto in Node.js. Per eseguire il dispositivo di esempio:

1. Aprire una finestra del terminale e passare alla cartella locale che contiene il repository Microsoft Azure IoT SDK per Node.js clonato da GitHub.

1. Questa finestra del terminale viene ora usata come terminale del **dispositivo**. Passare alla cartella del repository clonato e quindi alla cartella */azure-iot-sdk-node/device/samples/pnp*. Installare tutte le dipendenze eseguendo il comando seguente:

    ```cmd/sh
    npm install
    ```

1. Configurare la _stringa di connessione del dispositivo_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Eseguire il dispositivo termostato di esempio con il comando seguente:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Questi messaggi indicano che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questo terminale perché sarà necessario per verificare il funzionamento del servizio di esempio.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In questa guida di avvio rapido si userà una soluzione IoT di esempio in Node.js per interagire con il dispositivo di esempio appena configurato.

1. Aprire un'altra finestra del terminale da usare come terminale del **servizio**. L'SDK del servizio è disponibile in anteprima, quindi è necessario clonare gli esempi da un [ramo di anteprima di Node SDK](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
    ```

1. Passare alla cartella del ramo del repository clonato e poi alla cartella */azure-iot-sdk-node/digitaltwins/samples/service/javascript*. Installare tutte le dipendenze eseguendo il comando seguente:

    ```cmd/sh
    npm install
    ```

1. Configurare le variabili di ambiente per l'ID dispositivo e la _stringa di connessione dell'hub IoT_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>Leggere una proprietà

1. Quando è stato eseguito il dispositivo termostato di esempio nel terminale del **dispositivo**, sono stati visualizzati i messaggi seguenti che ne indicano lo stato online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Passare al terminale del **servizio** e usare questo comando per eseguire l'esempio per la lettura delle informazioni sul dispositivo:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Nell'output del terminale del **servizio** verificare la risposta del gemello digitale. Verificare l'ID modello del dispositivo e le proprietà associate segnalate:

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. Il frammento di codice seguente mostra il codice in *get_digital_twin.js* che recupera l'ID modello del dispositivo gemello:

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

In questo scenario restituisce come output `Model Id: dtmi:com:example:Thermostat;1`.

### <a name="update-a-writable-property"></a>Aggiornare una proprietà scrivibile

1. Aprire il file *update_digital_twin.js* in un editor di codice.

1. Esaminare il codice di esempio. È possibile ottenere informazioni sulla procedura di creazione di una patch JSON per aggiornare il gemello digitale del dispositivo. In questo esempio il codice sostituisce la temperatura del termostato con il valore 42:

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. Nel terminale del **servizio** usare questo comando per eseguire l'esempio per l'aggiornamento della proprietà:

    ```cmd/sh
    node update_digital_twin.js
    ```

1. Nel terminale del **dispositivo** verificare che il dispositivo abbia ricevuto l'aggiornamento:

    ```cmd/sh
    The following properties will be updated for root interface:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    Properties have been reported for component
    ```

1. Nel terminale del **servizio** eseguire questo comando per confermare che la proprietà è stata aggiornata:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Nell'output del terminale del **servizio** nella risposta del gemello digitale del componente `thermostat1` verificare la temperatura di destinazione aggiornata segnalata. L'aggiornamento del dispositivo potrebbe richiedere tempo. Ripetere questo passaggio fino a quando il dispositivo non ha elaborato l'aggiornamento della proprietà:

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>Richiamare un comando

1. Aprire il file *invoke_command.js* ed esaminare il codice.

1. Passare al terminale del **servizio**. Usare questo comando per eseguire l'esempio per la chiamata del comando:

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
    node invoke_command.js
    ```

1. L'output nel terminale del **servizio** mostra la conferma seguente:

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. Nel terminale del **dispositivo** verificare che il comando sia stato riconosciuto:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per gli sviluppatori alla modellazione di Plug and Play IoT (anteprima)](concepts-developer-guide.md)
