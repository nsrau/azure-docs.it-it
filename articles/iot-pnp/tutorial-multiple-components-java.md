---
title: Connettere il codice Java di un dispositivo Plug and Play IoT di esempio con componenti a un hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice Java di un dispositivo Plug and Play IoT di esempio che usa più componenti e si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: fce6477313cb7fb917c0fb81d03a73eec4714915
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046401"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-java"></a>Esercitazione: Connettere un'applicazione di un dispositivo Plug and Play IoT di esempio con più componenti a un hub IoT (Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Questa esercitazione rapido illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con più componenti, connetterla all'hub IoT e usare l'interfaccia della riga di comando di Azure per visualizzare i dati di telemetria inviati. L'applicazione di esempio è scritta in Java ed è inclusa in Azure IoT SDK per dispositivi per Java. Un integratore di soluzioni può usare l'interfaccia della riga di comando di Azure per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

Questa esercitazione illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con componenti, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta in Java ed è inclusa in Azure IoT SDK per dispositivi per Java. Un integratore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per completare questa esercitazione in Windows, installare il software seguente nell'ambiente Windows locale:

* Java SE Development Kit 8. In [Supporto a lungo termine di Java per Azure e Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) selezionare **Java 8** nella sezione **Supporto a lungo termine**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Scaricare il codice

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione di un dispositivo Plug and Play IoT di esempio in esecuzione in Windows a un hub IoT (Java)](quickstart-connect-device-java.md), il repository è già stato clonato.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire il comando seguente per clonare il repository GitHub di [SDK e librerie di Azure IoT per Java](https://github.com/Azure/azure-iot-sdk-java) in questo percorso:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Il completamento di questa operazione richiederà alcuni minuti.

## <a name="build-the-code"></a>Compilare il codice

In Windows passare alla cartella radice del repository dell'SDK Java clonato. Eseguire il comando seguente per creare le dipendenze:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Eseguire l'esempio del dispositivo

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Per eseguire l'applicazione di esempio, passare alla cartella *\device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample* ed eseguire il comando seguente:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un dispositivo termoregolatore Plug and Play IoT. Il modello implementato da questo esempio usa [più componenti](concepts-components.md). Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definisce i dati di telemetria, le proprietà e i comandi implementati.

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

L'ID modello viene archiviato nel codice come mostrato nel frammento seguente:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Quando il dispositivo si connette all'hub IoT, il codice registra i gestori dei comandi.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Sono disponibili gestori distinti per gli aggiornamenti delle proprietà desiderati sui due componenti del termostato:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

Il codice di esempio invia dati di telemetria da ogni componente del termostato:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

Il metodo `sendTemperatureReading` usa la classe `PnpHhelper` per creare messaggi per ogni componente:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

La classe `PnpHelper` contiene altri metodi di esempio che è possibile usare con un modello a più componenti.

Usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria e le proprietà dei due componenti del termostato:

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Dispositivo a più componenti in Azure IoT Explorer":::

È anche possibile usare lo strumento Azure IoT Explorer per chiamare i comandi in uno dei due componenti del termostato o nel componente predefinito.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come connettere un dispositivo Plug and Play IoT con componenti a un hub IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](concepts-developer-guide-device-csharp.md)