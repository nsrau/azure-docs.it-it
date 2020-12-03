---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: ff1041c941a994784c68d779bd96dec9070ce89a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511331"
---
Questa esercitazione illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con componenti, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta in C# ed è inclusa in Azure IoT SDK per dispositivi per C#. Un integratore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Per completare questa esercitazione in Windows, installare il software seguente nell'ambiente Windows locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonare il repository dell'SDK con il codice di esempio

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione per dispositivi Plug and Play IoT di esempio in esecuzione in Windows all'hub IoT (C#)](../articles/iot-pnp/quickstart-connect-device.md), il repository è già stato clonato.

Clonare gli esempi dal repository GitHub degli esempi di Azure IoT per C#. Aprire un prompt dei comandi in una cartella di propria scelta. Eseguire questo comando per clonare il repository GitHub degli [esempi di Microsoft Azure IoT per .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp):

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
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](../articles/iot-pnp/set-up-environment.md) |


1. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Questi messaggi indicano che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questa istanza di Visual Studio perché sarà necessaria per verificare il funzionamento dell'esempio di servizio.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Esaminare il codice

Questo esempio implementa un dispositivo termoregolatore Plug and Play IoT. Il modello implementato da questo esempio usa [più componenti](../articles/iot-pnp/concepts-components.md). Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definisce i dati di telemetria, le proprietà e i comandi implementati.

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

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Dispositivo a più componenti in Azure IoT Explorer":::

È anche possibile usare lo strumento Azure IoT Explorer per chiamare i comandi in uno dei due componenti del termostato o nel componente predefinito.
