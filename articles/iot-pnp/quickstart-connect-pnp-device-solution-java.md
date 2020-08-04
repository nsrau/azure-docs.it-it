---
title: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione - Java | Microsoft Docs
description: Usare Java per connettersi e interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione IoT di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 752d1d925c55ed79c7eb1673c6602adb9c2371fb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320987"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Guida introduttiva: Interagire con un dispositivo Plug and Play IoT (anteprima) connesso alla soluzione (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Plug and Play IoT (anteprima) semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare Java per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessario Java SE 8 nel computer di sviluppo. È anche necessario installare Maven 3.

Per informazioni dettagliate su come completare la configurazione, vedere le istruzioni per [preparare l'ambiente di sviluppo](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) in Microsoft Azure IoT SDK per dispositivi per Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire questo comando per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prenderne nota per usarla in un secondo momento:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

In questa guida di avvio rapido come dispositivo Plug and Play IoT si userà un sensore ambientale di esempio scritto in Java. Le istruzioni seguenti illustrano come installare ed eseguire il dispositivo:

1. Aprire una finestra del terminale nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub degli [esempi di Azure IoT per Java](https://github.com/Azure-Samples/azure-iot-samples-java) in questo percorso:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Questa finestra del terminale verrà ora usata come terminale del _dispositivo_. Passare alla cartella repository clonato e quindi alla cartella **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample**. Eseguire il comando seguente per installare le librerie necessarie e compilare l'applicazione del dispositivo simulato:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configurare la _stringa di connessione del dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Eseguire il comando seguente per eseguire l'esempio dalla cartella del dispositivo.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Vengono visualizzati messaggi per informare che il dispositivo è connesso, esegue diversi passaggi di configurazione ed è in attesa degli aggiornamenti del servizio, seguiti dai log di telemetria. Questi messaggi indicano che il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà ed ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi. Non chiudere questo terminale perché sarà necessario in un secondo momento per verificare il funzionamento anche dei servizi di esempio.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In questa guida di avvio rapido si userà una soluzione IoT di esempio in Java per interagire con il dispositivo di esempio.

1. Aprire un'altra finestra del terminale, che fungerà da terminale del _servizio_. Passare alla cartella repository clonato e quindi alla cartella **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample**.

1. Eseguire il comando seguente per installare le librerie necessarie e compilare l'esempio del servizio:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configurare la _stringa di connessione dell'hub IoT_ e l'_ID dispositivo_ per consentire al servizio di connettersi ad entrambi:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Leggere una proprietà

1. Quando è stata effettuata la connessione del _dispositivo_ nel relativo terminale, è stato visualizzato il messaggio seguente per indicarne lo stato online. La proprietà `state`, che viene usata per indicare se il dispositivo è o meno online, è impostata su _true_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Passare al terminale del _servizio_ e usare questo comando per eseguire l'esempio del servizio per la lettura delle informazioni sul dispositivo:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Nell'output del terminale del _servizio_ scorrere fino al componente `environmentalSensor`. Notare che la proprietà `state` è stata impostata su _true_:
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
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

1. Usare il comando seguente per eseguire l'esempio del servizio per l'aggiornamento della proprietà:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. L'output del terminale del _servizio_ mostrerà le informazioni sul dispositivo aggiornate. Scorrere fino al componente `environmentalSensor` per visualizzare il nuovo valore di luminosità di 42.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Passare al terminare del _dispositivo_ e verificare che il dispositivo abbia ricevuto l'aggiornamento:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Tornare al terminale del _servizio_ ed eseguire questo comando per ottenere di nuovo le informazioni sul dispositivo e verificare l'aggiornamento della proprietà.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. Nell'output del terminale del _servizio_ si può osservare nel componente `environmentalSensor` che il valore di luminosità aggiornato è stato segnalato. Nota: l'aggiornamento del dispositivo potrebbe richiedere tempo. È possibile ripetere questo passaggio fino a quando il dispositivo non ha effettivamente elaborato l'aggiornamento della proprietà.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Richiamare un comando

1. Passare al terminale del _servizio_ e impostare le variabili seguenti per definire il comando da richiamare:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Usare questo comando per eseguire l'esempio del servizio per la chiamata del comando:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. L'output nel terminale del _servizio_ dovrebbe mostrare la conferma seguente:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Passare al terminare del _dispositivo_ e verificare che il comando sia stato riconosciuto:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi a un dispositivo e interagire con esso](howto-develop-solution.md)
