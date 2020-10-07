---
title: Interagire con un dispositivo Plug and Play IoT connesso alla soluzione Azure IoT (Java) | Microsoft Docs
description: Usare Java per connettersi e interagire con un dispositivo Plug and Play IoT connesso alla soluzione Azure IoT.
author: ericmitt
ms.author: ericmitt
ms.date: 9/17/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: cd618cf5f2f82b9c87981e961ed401f3409ec9d4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580802"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-java"></a>Avvio rapido: Interagire con un dispositivo Plug and Play IoT connesso alla soluzione (Java)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

Plug and Play IoT semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare Java per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per completare questa guida di avvio rapido in Windows, installare il software seguente nell'ambiente Windows locale:

* Java SE Development Kit 8. In [Supporto a lungo termine di Java per Azure e Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) selezionare **Java 8** nella sezione **Supporto a lungo termine**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonare il repository dell'SDK con il codice di esempio

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione per dispositivi Plug and Play IoT di esempio in esecuzione in Windows all'hub IoT (Java)](quickstart-connect-device-java.md), il repository è già stato clonato.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub [Microsoft Azure IoT SDK per Java](https://github.com/Azure/azure-iot-sdk-java) in questo percorso:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Compilare ed eseguire il dispositivo di esempio

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md).

In questa guida di avvio rapido verrà usato come dispositivo Plug and Play IoT un dispositivo termostato di esempio scritto in Java. Per eseguire il dispositivo di esempio:

1. Aprire una finestra del terminale e passare alla cartella locale che contiene il repository Microsoft Azure IoT SDK per Java clonato da GitHub.

1. Questa finestra del terminale viene ora usata come terminale del **dispositivo**. Passare alla cartella radice del repository clonato. Installare tutte le dipendenze eseguendo il comando seguente:

1. Eseguire il comando seguente per compilare l'applicazione del dispositivo di esempio:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Per eseguire l'applicazione del dispositivo di esempio, passare alla cartella *device\iot-device-samples\pnp-device-sample\thermostat-device-sample* ed eseguire il comando seguente:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare il dispositivo di esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In [Configurare l'ambiente per le esercitazioni e le guide di avvio rapido di Plug and Play IoT](set-up-environment.md) sono state create due variabili di ambiente per configurare l'esempio da connettere all'hub IoT e al dispositivo:

* **IOTHUB_CONNECTION_STRING**: stringa di connessione dell'hub IoT annotata in precedenza.
* **DEVICE_ID**: `"my-pnp-device"`.

In questa guida di avvio rapido si userà una soluzione IoT di esempio scritta in Java per interagire con il dispositivo di esempio appena configurato.

> [!NOTE]
> In questo esempio viene usato lo spazio dei nomi **com.microsoft.azure.sdk.iot.service.*;** dal **client del servizio hub IoT**. Per altre informazioni su come recuperare l'ID modello, vedere la [Guida per sviluppatori](concepts-developer-guide-device-csharp.md).

1. Aprire un'altra finestra del terminale da usare come terminale del **servizio**.

1. Nel repository dell'SDK per Java clonato passare alla cartella *service\iot-service-samples\pnp-service-sample\thermostat-service-sample*.

1. Per eseguire l'applicazione del servizio di esempio, usare il comando seguente:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-digital-twin"></a>Ottenere il gemello digitale

Il frammento di codice seguente mostra come recuperare il dispositivo gemello nel servizio:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-digital-twin"></a>Aggiornare un gemello digitale

Questo frammento di codice mostra come usare una *patch* per aggiornare le proprietà tramite il gemello digitale:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

L'output del dispositivo mostra il modo in cui il dispositivo risponde all'aggiornamento della proprietà.

### <a name="invoke-a-command"></a>Richiamare un comando

Il frammento di codice seguente mostra come chiamare un comando sul dispositivo:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

L'output del dispositivo mostra il modo in cui il dispositivo risponde a questo comando.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](concepts-developer-guide-device-csharp.md)
