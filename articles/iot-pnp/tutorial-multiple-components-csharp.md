---
title: Connettere il codice C# di un dispositivo Plug and Play IoT (anteprima) di esempio con componenti a un hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice C# di un dispositivo Plug and Play IoT (anteprima) di esempio che usa più componenti e si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 67b71399332fb29a277381a8c2806dbe7fb31d85
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552121"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Esercitazione: Connettere un'applicazione di un dispositivo Plug and Play IoT con più componenti in esecuzione in Windows a un hub IoT (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Questa esercitazione illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con componenti e interfaccia radice, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta in C# ed è inclusa in Azure IoT SDK per dispositivi per C#. Un integratore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione in Windows, installare il software seguente nell'ambiente Windows locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Per interagire con il dispositivo di esempio nella seconda parte di questa esercitazione, usare lo strumento**Azure IoT Explorer**. [Scaricare e installare la versione di Azure IoT Explorer più recente](./howto-use-iot-explorer.md) per il sistema operativo in uso.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire il comando seguente per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questa esercitazione:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> È anche possibile usare lo strumento Azure IoT Explorer per trovare la stringa di connessione dell'hub IoT.

Eseguire il comando seguente per ottenere la _stringa di connessione del dispositivo_ aggiunto all'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questa esercitazione:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Scaricare il codice

In questa esercitazione viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare Azure IoT Hub SDK per dispositivi per C#.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire il comando seguente per clonare il repository GitHub di [SDK e librerie di Azure IoT per C#](https://github.com/Azure/azure-iot-sdk-csharp) in tale percorso:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-csharp.git
```

## <a name="build-the-code"></a>Compilare il codice

Aprire il file della soluzione **azureiot.sln** in Visual Studio 2019 e impostare il progetto **TemperatureController** come progetto di avvio. In **Esplora soluzioni** è possibile trovare il file di progetto in **iothub > device > samples**.

È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

## <a name="run-the-device-sample"></a>Eseguire l'esempio del dispositivo

Creare una variabile di ambiente definita **IOTHUB_DEVICE_CONNECTION_STRING** per archiviare la stringa di connessione del dispositivo annotata in precedenza.

Per verificare l'esecuzione del codice in Visual Studio in Windows, aggiungere un punto di interruzione alla funzione `main` nel file program.cs.

Il dispositivo è ora pronto a ricevere comandi e aggiornamenti delle proprietà e ha iniziato a inviare dati di telemetria all'hub. Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un dispositivo termoregolatore Plug and Play IoT. Il modello implementato da questo esempio usa [più componenti](concepts-components.md). Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definisce i dati di telemetria, le proprietà e i comandi implementati.

Il codice del dispositivo si connette all'hub IoT usando il metodo `CreateFromConnectionString` standard. Il dispositivo invia l'ID modello del modello DTDL implementato nella richiesta di connessione. Un dispositivo che invia un ID modello è un dispositivo Plug and Play IoT:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
      ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
      s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

L'ID modello viene archiviato nel codice come mostrato nel frammento seguente:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Quando il dispositivo si connette all'hub IoT, il codice registra i gestori dei comandi. Il comando `reboot` viene definito nell'interfaccia radice. Il comando `getMaxMinReport` viene definito in ognuno dei due componenti del termostato:

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

Sono disponibili gestori distinti per gli aggiornamenti delle proprietà desiderati sui due componenti del termostato:

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

Il codice di esempio invia dati di telemetria da ogni componente del termostato:

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

Il metodo `SendTemperature` usa la classe `PnpHhelper` per creare messaggi per ogni componente:

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

La classe `PnpHelper` contiene altri metodi di esempio che è possibile usare con un modello a più componenti.

Usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria e le proprietà dei due componenti del termostato:

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Dispositivo a più componenti in Azure IoT Explorer":::

È anche possibile usare lo strumento Azure IoT Explorer per chiamare i comandi in uno dei due componenti del termostato o nell'interfaccia radice.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come connettere un dispositivo Plug and Play IoT con componenti a un hub IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per gli sviluppatori alla modellazione di Plug and Play IoT (anteprima)](concepts-developer-guide.md)
