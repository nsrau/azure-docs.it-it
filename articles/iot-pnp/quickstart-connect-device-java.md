---
title: "Avvio rapido: Connettere il codice del dispositivo Java di esempio Plug and Play IoT all'hub IoT di Azure | Microsoft Docs"
description: "Avvio rapido: Compilare ed eseguire il codice del dispositivo di esempio Plug and Play IoT per la connessione a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub."
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 32e610cbd65aafecec9bafb35a78fb8f68279da5
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421601"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-java"></a>Avvio rapido: Connettere un'applicazione per dispositivi Plug and Play IoT in esecuzione in Windows all'hub IoT (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Questa guida di avvio rapido illustra come creare un'applicazione di dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria inviati. L'applicazione di esempio è scritta in Java ed è inclusa in Azure IoT SDK per dispositivi per Java. Un generatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

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

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

Passare alla cartella *\device\iot-device-samples\pnp-device-sample\thermostat-device-sample*.

Per eseguire l'applicazione di esempio, usare il comando seguente:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un semplice dispositivo termostato Plug and Play IoT. Il modello implementato da questo esempio non usa [componenti](concepts-components.md) di Plug and Play IoT. Il [file di modello DTDL per il dispositivo termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definisce i dati di telemetria, le proprietà e i comandi implementati dal dispositivo.

Il codice del dispositivo usa la classe `DeviceClient` standard per la connessione all'hub IoT. Il dispositivo invia l'ID del modello DTDL implementato nella richiesta di connessione. Un dispositivo che invia un ID modello è un dispositivo Plug and Play IoT:

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

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi a un dispositivo e interagire con esso](./quickstart-service-node.md)