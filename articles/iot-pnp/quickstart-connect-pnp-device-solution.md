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
ms.openlocfilehash: 246d3eac8f9d8aff6d603ea8686e430ce0f772ea
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881590"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>Guida introduttiva: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione

Plug and Play IoT (anteprima) semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare Node.js per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

Scaricare e installare Node.js da [nodejs.org](https://nodejs.org).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Preparare un hub IoT

Per completare questa guida di avvio rapido, la sottoscrizione di Azure deve includere anche un hub IoT di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Aggiungere l'estensione IoT di Microsoft Azure per l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Eseguire il comando seguente per creare l'identità del dispositivo nell'hub IoT. Sostituire i segnaposto **YourIoTHubName** e **YourDevice** con i nomi effettivi. Se non è disponibile alcun hub IoT, seguire [queste istruzioni](../iot-hub/iot-hub-create-using-cli.md) per crearne uno:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Eseguire i comandi seguenti per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Eseguire i comandi seguenti per ottenere la _stringa di connessione dell'hub IoT_ per l'hub:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>Connettere il dispositivo

In questa guida di avvio rapido come dispositivo Plug and Play IoT si userà un sensore ambientale di esempio scritto in Node.js. Le istruzioni seguenti illustrano come installare ed eseguire il dispositivo:

1. Clonare il repository GitHub:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. In un terminale passare alla cartella radice del repository clonato e quindi alla cartella **/azure-iot-samples-node/digital-twins/Quickstarts/Device**. Installare infine tutte le dipendenze eseguendo il comando seguente:

    ```cmd/sh
    npm install
    ```

1. Configurare la _stringa di connessione del dispositivo_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. Eseguire l'esempio con il comando seguente:

    ```cmd/sh
    node sample_device.js
    ```

1. Viene visualizzato un messaggio che informa che il dispositivo ha inviato i dati di telemetria e le relative proprietà. Il dispositivo è ora pronto per ricevere i comandi e gli aggiornamenti delle proprietà. Non chiudere questo terminale perché sarà necessario in un secondo momento per confermare il funzionamento anche dei servizi di esempio.

## <a name="build-the-solution"></a>Compilare la soluzione

In questa guida di avvio rapido si userà una soluzione IoT di esempio in Node.js per interagire con il dispositivo di esempio.

1. Aprire un altro terminale. Passare alla cartella repository clonato e quindi alla cartella **/azure-iot-samples-node/digital-twins/Quickstarts/Service**. Installare tutte le dipendenze eseguendo il comando seguente:

    ```cmd/sh
    npm install
    ```

1. Configurare la _stringa di connessione dell'hub_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>Leggere una proprietà

1. Quando si connette il dispositivo nel terminale, viene visualizzato il messaggio seguente:

    ```cmd/sh
    reported state property as online
    ```

1. Aprire il file **get_digital_twin.js**. Sostituire `deviceID` con l'ID dispositivo e salvare il file.

1. Passare al terminale aperto per l'esempio di servizio in esecuzione ed eseguire il comando seguente:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Nell'output, sotto il componente _environmentalSensor_, viene segnalato lo stesso stato:

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>Aggiornare una proprietà scrivibile

1. Aprire il file **update_digital_twin_property.js**.

1. All'inizio del file è presente un set di costanti definite con segnaposto in maiuscolo. Sostituire **deviceID** con l'ID effettivo del dispositivo, aggiornare le costanti con i valori seguenti e salvare il file:

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. Passare al terminale aperto per l'esempio di servizio in esecuzione ed eseguire il comando seguente per eseguire l'esempio:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. Nel terminale vengono visualizzate le informazioni sul gemello digitale associate al dispositivo. Individuare il componente _environmentalSensor_. Viene visualizzato il nuovo valore di luminosità, ovvero 60.

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
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
    Received an update for brightness: 60
    updated the property
    ```
2. Tornare al terminale del _servizio_ ed eseguire di nuovo il comando seguente per verificare l'aggiornamento della proprietà.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. Nell'output, sotto il componente environmentalSensor, viene segnalato il valore di luminosità aggiornato. Nota: l'aggiornamento del dispositivo potrebbe richiedere tempo. È possibile ripetere questo passaggio fino a quando il dispositivo non ha effettivamente elaborato l'aggiornamento della proprietà.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>Richiamare un comando

1. Aprire il file **invoke_command.js**.

1. All'inizio del file sostituire `deviceID` con l'ID dispositivo effettivo. Aggiornare le costanti con i valori seguenti e quindi salvare il file:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. Passare al terminale aperto per l'esempio di servizio in esecuzione. Usare il comando seguente per eseguire l'esempio:

    ```cmd/sh
    node invoke_command.js
    ```

1. Nel terminale il risultato dell'operazione è simile al seguente:

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. Passare al terminare del _dispositivo_ e verificare che il comando sia stato riconosciuto:

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare con gli articoli successivi, non eliminare le risorse usate in questa guida di avvio rapido. In caso contrario, è possibile eliminare le risorse create per questa guida di avvio rapido per evitare addebiti aggiuntivi.

Per eliminare l'hub e il dispositivo registrato, completare la procedura seguente usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az group delete --name <Your group name>
```

Per eliminare solo il dispositivo registrato con l'hub IoT, completare la procedura seguente usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi a un dispositivo e interagire con esso](howto-develop-solution.md)
