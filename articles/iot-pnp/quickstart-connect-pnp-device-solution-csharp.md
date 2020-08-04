---
title: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione - C# | Microsoft Docs
description: Usare C# (.NET) per connettersi e interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione IoT di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 226acdda94eb88825d60e35d48bfdd476ad1339c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044097"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Guida introduttiva: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Plug and Play IoT (anteprima) semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare C# (con .NET) per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessario installare .NET Core (2.x.x o 3.x.x) nel computer di sviluppo. È possibile scaricare la versione preferita di .NET Core SDK per più piattaforme dalla pagina [Download di .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

Per verificare la versione di .NET presente nel computer di sviluppo, eseguire il comando seguente in una finestra del terminale locale: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire questo comando per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prenderne nota per usarla in un secondo momento:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

In questa guida di avvio rapido come dispositivo Plug and Play IoT si userà un sensore ambientale di esempio scritto in C#. Le istruzioni seguenti illustrano come installare ed eseguire il dispositivo:

1. Aprire una finestra del terminale nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub degli [esempi di Azure IoT per C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) in questo percorso:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Questa finestra del terminale verrà ora usata come terminale del _dispositivo_. Passare alla cartella del repository clonato e quindi alla cartella **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample**.

1. Configurare la _stringa di connessione del dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Per compilare i pacchetti necessari ed eseguire l'esempio, usare il comando seguente:

    ```cmd\sh
        dotnet run
    ```

1. Vengono visualizzati messaggi per informare che il dispositivo è stato registrato correttamente ed è in attesa di aggiornamenti dal cloud. Questi messaggi indicano che il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà ed ha iniziato a inviare dati di telemetria all'hub. Non chiudere questo terminale perché sarà necessario in un secondo momento per verificare il funzionamento anche dei servizi di esempio.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In questa guida di avvio rapido si userà una soluzione IoT di esempio in C# per interagire con il dispositivo di esempio.

1. Aprire un'altra finestra del terminale, che fungerà da terminale del _servizio_. Passare alla cartella repository clonato e quindi alla cartella **/azure-iot-samples-csharp/digitaltwin/Samples/service**.

1. Configurare la _stringa di connessione dell'hub IoT_ e l'_ID dispositivo_ per consentire al servizio di connettersi ad entrambi:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Leggere una proprietà

1. Quando è stata effettuata la connessione del _dispositivo_ nel relativo terminale, è stato visualizzato il messaggio seguente che ne ha indicato lo stato online:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Passare al terminale del _servizio_ e usare i comandi seguenti per eseguire l'esempio per la lettura delle informazioni sul dispositivo:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Nell'output del terminale del _servizio_ scorrere fino al componente `environmentalSensor`. Notare che la proprietà `state`, che viene usata per indicare se il dispositivo è o meno online, è impostata su _true_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Aggiornare una proprietà scrivibile

1. Passare al terminale del _servizio_ e impostare le variabili seguenti per definire la proprietà da aggiornare:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Usare i comandi seguenti per eseguire l'esempio per l'aggiornamento della proprietà:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
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
            "value": true
          }
        }
      }
    }
    ```

1. Passare al terminare del _dispositivo_ e verificare che il dispositivo abbia ricevuto l'aggiornamento:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Tornare al terminale del _servizio_ ed eseguire i comandi seguenti per ottenere di nuovo le informazioni sul dispositivo e verificare l'aggiornamento della proprietà.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. Nell'output del terminale del _servizio_ si può osservare nel componente `environmentalSensor` che il valore di luminosità aggiornato è stato segnalato. Nota: l'aggiornamento del dispositivo potrebbe richiedere tempo. È possibile ripetere questo passaggio fino a quando il dispositivo non ha effettivamente elaborato l'aggiornamento della proprietà.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Richiamare un comando

1. Passare al terminale del _servizio_ e impostare le variabili seguenti per definire il comando da richiamare:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Usare i comandi seguenti per eseguire l'esempio per la chiamata del comando:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. L'output nel terminale del _servizio_ dovrebbe mostrare la conferma seguente:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Passare al terminare del _dispositivo_ e verificare che il comando sia stato riconosciuto:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi a un dispositivo e interagire con esso](howto-develop-solution.md)
