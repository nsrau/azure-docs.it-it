---
title: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione IoT di Azure | Microsoft Docs
description: Usare Node.js pere connettersi e interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione IoT di Azure.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3275c01059a61e4eb8591948695656f4e4b722ed
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152117"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Guida introduttiva: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione (Node.js)

Plug and Play IoT (anteprima) semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare Node.js per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessario Node.js nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="connect-your-device"></a>Connettere il dispositivo

In questa guida di avvio rapido come dispositivo Plug and Play IoT si userà un sensore ambientale di esempio scritto in Node.js. Le istruzioni seguenti illustrano come installare ed eseguire il dispositivo:

1. Aprire una finestra del terminale nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub di [esempi di Azure IoT per Node.js](https://github.com/azure-samples/azure-iot-samples-node) in tale percorso:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Questa finestra del terminale verrà ora usata come terminale del _dispositivo_. Passare al repository clonato e quindi alla cartella **/azure-iot-samples-node/digital-twins/Quickstarts/Device**. Installare tutte le dipendenze eseguendo il comando seguente:

    ```cmd/sh
    npm install
    ```

1. Configurare la _stringa di connessione del dispositivo_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Eseguire l'esempio con il comando seguente:

    ```cmd/sh
    node sample_device.js
    ```

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Ciò significa che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questo terminale perché sarà necessario in un secondo momento per verificare il funzionamento anche dei servizi di esempio.

## <a name="build-the-solution"></a>Compilare la soluzione

In questa guida di avvio rapido si userà una soluzione IoT di esempio in Node.js per interagire con il dispositivo di esempio.

1. Aprire un'altra finestra del terminale, che fungerà da terminale del _servizio_. Passare alla cartella repository clonato e quindi alla cartella **/azure-iot-samples-node/digital-twins/Quickstarts/Service**. Installare tutte le dipendenze eseguendo il comando seguente:

    ```cmd/sh
    npm install
    ```

1. Configurare la _stringa di connessione dell'hub IoT_ per consentire al servizio di connettervisi:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Leggere una proprietà

1. Quando è stata effettuata la connessione del _dispositivo_ nel relativo terminale, è stato visualizzato il messaggio seguente che ne ha indicato lo stato online:

    ```cmd/sh
    reported state property as online
    ```

1. Nella cartella **/azure-iot-samples-node/digital-twins/Quickstarts/Service** aprire il file **get_digital_twin.js**. Sostituire il segnaposto `<DEVICE_ID_GOES_HERE>` con l'ID dispositivo e salvare il file.

1. Passare al terminale del _servizio_ e usare questo comando per eseguire l'esempio per la lettura delle informazioni sul dispositivo:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Nell'output del terminale del _servizio_ scorrere fino al componente `environmentalSensor`. Si può osservare che per la proprietà `state` è stato segnalato il valore _online_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Aggiornare una proprietà scrivibile

1. Aprire il file **update_digital_twin_property.js**.

1. All'inizio del file è presente un set di costanti definite con segnaposto in maiuscolo. Sostituire il segnaposto `<DEVICE_ID_GOES_HERE>` con l'ID dispositivo effettivo, aggiornare le costanti rimanenti con i valori seguenti e salvare il file:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Passare al terminale del _servizio_ e usare questo comando per eseguire l'esempio per l'aggiornamento della proprietà:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. L'output del terminale del _servizio_ mostrerà le informazioni sul dispositivo aggiornate. Scorrere fino al componente `environmentalSensor` per visualizzare il nuovo valore di luminosità di 42.

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. Passare al terminare del _dispositivo_ e verificare che il dispositivo abbia ricevuto l'aggiornamento:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Tornare al terminale del _servizio_ ed eseguire questo comando per ottenere di nuovo le informazioni sul dispositivo e verificare l'aggiornamento della proprietà.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. Nell'output del terminale del _servizio_ si può osservare nel componente `environmentalSensor` che il valore di luminosità aggiornato è stato segnalato. Nota: l'aggiornamento del dispositivo potrebbe richiedere tempo. È possibile ripetere questo passaggio fino a quando il dispositivo non ha effettivamente elaborato l'aggiornamento della proprietà.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Richiamare un comando

1. Aprire il file **invoke_command.js**.

1. All'inizio del file sostituire il segnaposto `<DEVICE_ID_GOES_HERE>` con l'ID dispositivo effettivo. Aggiornare le costanti rimanenti con i valori seguenti e quindi salvare il file:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Passare al terminale del _servizio_. Usare questo comando per eseguire l'esempio per la chiamata del comando:

    ```cmd/sh
    node invoke_command.js
    ```

1. L'output nel terminale del _servizio_ dovrebbe mostrare la conferma seguente:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Passare al terminare del _dispositivo_ e verificare che il comando sia stato riconosciuto:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi a un dispositivo e interagire con esso](howto-develop-solution.md)
