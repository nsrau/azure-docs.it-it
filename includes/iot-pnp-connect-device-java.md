---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7961dae0ef227a7e321992e0b239f7a3e6e66b0a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511655"
---
Questa guida di avvio rapido illustra come creare un'applicazione di dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria inviati. L'applicazione di esempio è scritta in Java ed è inclusa in Azure IoT SDK per dispositivi per Java. Un generatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Per completare questa guida di avvio rapido in Windows, installare il software seguente nell'ambiente Windows locale:

* Java SE Development Kit 8. In [Supporto a lungo termine di Java per Azure e Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) selezionare **Java 8** nella sezione **Supporto a lungo termine**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Scaricare il codice

In questo argomento di avvio rapido viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare l'SDK dell'hub IoT di Azure per dispositivi per Java.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub di [SDK e librerie di Azure IoT per Java](https://github.com/Azure/azure-iot-sdk-java) in tale percorso:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>Compilare il codice

In Windows passare alla cartella radice del repository dell'SDK Java clonato.

Eseguire il comando seguente per compilare l'applicazione di esempio:

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Eseguire l'esempio del dispositivo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

Passare alla cartella *\device\iot-device-samples\pnp-device-sample\thermostat-device-sample*.

Per eseguire l'applicazione di esempio, usare il comando seguente:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un semplice dispositivo termostato Plug and Play IoT. Il modello implementato da questo esempio non usa [componenti](../articles/iot-pnp/concepts-components.md) di Plug and Play IoT. Il [file di modello DTDL per il dispositivo termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definisce i dati di telemetria, le proprietà e i comandi implementati dal dispositivo.

Il codice del dispositivo usa la classe `DeviceClient` standard per la connessione all'hub IoT. Il dispositivo invia l'ID modello del modello DTDL implementato nella richiesta di connessione. Un dispositivo che invia un ID modello è un dispositivo Plug and Play IoT:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

L'ID modello viene archiviato nel codice come mostrato nel frammento di codice seguente:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Il codice che aggiorna le proprietà, gestisce i comandi e invia i dati di telemetria è identico al codice per un dispositivo che non usa le convenzioni di Plug and Play IoT.

L'esempio usa una libreria JSON per analizzare oggetti JSON nei payload inviati dall'hub IoT:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```
