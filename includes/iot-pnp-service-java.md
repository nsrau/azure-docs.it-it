---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 788aa0ebe9df91caba2ee279df96cbea175975e4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487821"
---
Plug and Play IoT semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare Java per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Per completare questa guida di avvio rapido in Windows, installare il software seguente nell'ambiente Windows locale:

* Java SE Development Kit 8. In [Supporto a lungo termine di Java per Azure e Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) selezionare **Java 8** nella sezione **Supporto a lungo termine**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonare il repository dell'SDK con il codice di esempio

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione per dispositivi Plug and Play IoT di esempio in esecuzione in Windows all'hub IoT (Java)](../articles/iot-pnp/quickstart-connect-device.md), il repository è già stato clonato.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub [Microsoft Azure IoT SDK per Java](https://github.com/Azure/azure-iot-sdk-java) in questo percorso:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Compilare ed eseguire il dispositivo di esempio

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

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

In [Configurare l'ambiente per le esercitazioni e le guide di avvio rapido di Plug and Play IoT](../articles/iot-pnp/set-up-environment.md) sono state create due variabili di ambiente per configurare l'esempio da connettere all'hub IoT e al dispositivo:

* **IOTHUB_CONNECTION_STRING**: stringa di connessione dell'hub IoT annotata in precedenza.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

In questa guida di avvio rapido si userà una soluzione IoT di esempio scritta in Java per interagire con il dispositivo di esempio appena configurato.

> [!NOTE]
> In questo esempio viene usato lo spazio dei nomi **com.microsoft.azure.sdk.iot.service** dal **client del servizio hub IoT**. Per altre informazioni sulle API, tra cui l'API gemelli digitali, vedere la [guida per sviluppatori di servizi](../articles/iot-pnp/concepts-developer-guide-service.md).

1. Aprire un'altra finestra del terminale da usare come terminale del **servizio**.

1. Nel repository dell'SDK per Java clonato passare alla cartella *service\iot-service-samples\pnp-service-sample\thermostat-service-sample*.

1. Per eseguire l'applicazione del servizio di esempio, usare il comando seguente:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>Recuperare il dispositivo gemello

Il frammento di codice seguente mostra come recuperare il dispositivo gemello nel servizio:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>Aggiornare un dispositivo gemello

Questo frammento di codice mostra come usare una *patch* per aggiornare le proprietà tramite il dispositivo gemello:

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
