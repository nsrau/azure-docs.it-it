---
title: Connettere il codice C# di un dispositivo Plug and Play IoT di esempio con componenti a un hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice C# di un dispositivo Plug and Play IoT di esempio che usa più componenti e si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 72b85388bf80a6ed2b9617b606940c243c5711f1
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945533"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Esercitazione: Connettere un'applicazione di un dispositivo Plug and Play IoT con più componenti in esecuzione in Windows a un hub IoT (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Questa esercitazione illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con componenti, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta in C# ed è inclusa in Azure IoT SDK per dispositivi per C#. Un integratore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per completare questa esercitazione in Windows, installare il software seguente nell'ambiente Windows locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonare il repository dell'SDK con il codice di esempio

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione di un dispositivo Plug and Play IoT di esempio in esecuzione in Windows all'hub IoT (C#)](quickstart-connect-device-csharp.md), il repository è già stato clonato.

Clonare gli esempi dagli esempi di Azure IoT per il repository GitHub per C#. Aprire un prompt dei comandi in una cartella di propria scelta. Eseguire questo comando per clonare il repository GitHub degli [esempi di Microsoft Azure IoT per .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp):

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

In questa guida di avvio rapido verrà usato come dispositivo Plug and Play IoT un dispositivo termoregolatore di esempio scritto in C#. Per eseguire il dispositivo di esempio:

1. Aprire il file di progetto *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* in Visual Studio 2019.

1. In Visual Studio passare a **Progetto > TemperatureController Properties (Proprietà TemperatureController) > Debug**. Aggiungere quindi al progetto le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Il valore annotato quando è stato completato il passaggio [Configurare l'ambiente](set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Il valore annotato quando è stato completato il passaggio [Configurare l'ambiente](set-up-environment.md) |


1. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Questi messaggi indicano che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questa istanza di Visual Studio perché sarà necessaria per verificare il funzionamento dell'esempio di servizio.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un dispositivo termoregolatore Plug and Play IoT. Il modello implementato da questo esempio usa [più componenti](concepts-components.md). Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definisce i dati di telemetria, le proprietà e i comandi implementati.

Il codice del dispositivo si connette all'hub IoT usando il metodo `CreateFromConnectionString` standard. Il dispositivo invia l'ID modello del modello DTDL implementato nella richiesta di connessione. Un dispositivo che invia un ID modello è un dispositivo Plug and Play IoT:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

L'ID modello viene archiviato nel codice come mostrato nel frammento seguente:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

Quando il dispositivo si connette all'hub IoT, il codice registra i gestori dei comandi. Il comando `reboot` viene definito nel componente predefinito. Il comando `getMaxMinReport` viene definito in ognuno dei due componenti del termostato:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

Sono disponibili gestori distinti per gli aggiornamenti delle proprietà desiderati sui due componenti del termostato:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

Il codice di esempio invia dati di telemetria da ogni componente del termostato:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

Il metodo `SendTemperatureTelemetryAsync` usa la classe `PnpHhelper` per creare messaggi per ogni componente:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

La classe `PnpHelper` contiene altri metodi di esempio che è possibile usare con un modello a più componenti.

Usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria e le proprietà dei due componenti del termostato:

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Dispositivo a più componenti in Azure IoT Explorer":::

È anche possibile usare lo strumento Azure IoT Explorer per chiamare i comandi in uno dei due componenti del termostato o nel componente predefinito.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come connettere un dispositivo Plug and Play IoT con componenti a un hub IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](concepts-developer-guide-device-csharp.md)
