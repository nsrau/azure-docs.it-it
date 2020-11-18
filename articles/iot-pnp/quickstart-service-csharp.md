---
title: 'Avvio rapido: Interagire con un dispositivo Plug and Play IoT connesso alla soluzione Azure IoT (C#) | Microsoft Docs'
description: 'Avvio rapido: Usare C# per connettersi e interagire con un dispositivo Plug and Play IoT connesso alla soluzione Azure IoT.'
author: ericmitt
ms.author: ericmitt
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 018edea9a013df55b32ee22020be7226eab27319
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421550"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-c"></a>Avvio rapido: Interagire con un dispositivo Plug and Play IoT connesso alla soluzione (C#)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

Plug and Play IoT semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare C# per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per completare questa guida di avvio rapido in Windows, è necessario che nel computer di sviluppo sia installato il software seguente:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonare il repository dell'SDK con il codice di esempio

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione per dispositivi Plug and Play IoT di esempio in esecuzione in Windows all'hub IoT (C#)](quickstart-connect-device-csharp.md), il repository è già stato clonato.

Clonare gli esempi dal repository GitHub degli esempi di Azure IoT per C#. Aprire un prompt dei comandi in una cartella di propria scelta. Eseguire questo comando per clonare il repository GitHub degli [esempi di Microsoft Azure IoT per .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp):

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

In questa guida di avvio rapido verrà usato come dispositivo Plug and Play IoT un dispositivo termostato di esempio scritto in C#. Per eseguire il dispositivo di esempio:

1. Aprire il file di progetto *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* in Visual Studio 2019.

1. In Visual Studio passare a **Progetto > Thermostat Properties (Proprietà termostato) > Debug**. Aggiungere quindi al progetto le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](set-up-environment.md) |

1. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Questi messaggi indicano che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questa istanza di Visual Studio perché sarà necessaria per verificare il funzionamento dell'esempio di servizio.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In [Configurare l'ambiente per le esercitazioni e le guide di avvio rapido di Plug and Play IoT](set-up-environment.md) sono state create due variabili di ambiente per configurare l'esempio da connettere all'hub IoT e al dispositivo:

* **IOTHUB_CONNECTION_STRING**: stringa di connessione dell'hub IoT annotata in precedenza.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

In questo argomento di avvio rapido si userà una soluzione IoT di esempio in C# per interagire con il dispositivo di esempio appena configurato.

1. In un'altra istanza di Visual Studio aprire il progetto *azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. In Visual Studio passare a **Progetto > Thermostat Properties (Proprietà termostato) > Debug**. Aggiungere quindi al progetto le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](set-up-environment.md) |

1. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

### <a name="get-device-twin"></a>Recuperare il dispositivo gemello

Il frammento di codice seguente mostra come l'applicazione del servizio recupera il dispositivo gemello:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> In questo esempio viene usato lo spazio dei nomi **Microsoft.Azure.Devices.Client** dal **client del servizio hub IoT**. Per altre informazioni sulle API, tra cui l'API gemelli digitali, vedere la [guida per sviluppatori di servizi](concepts-developer-guide-service.md).

Questo codice genera l'output seguente:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

Questo frammento di codice mostra come usare una *patch* per aggiornare le proprietà tramite il dispositivo gemello:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Questo codice genera l'output seguente dal dispositivo quando il servizio aggiorna la proprietà `targetTemperature`:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Richiamare un comando

Il frammento di codice seguente mostra come chiamare un comando:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Questo codice genera l'output seguente dal dispositivo quando il servizio chiama il comando `getMaxMinReport`:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a una soluzione IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](concepts-developer-guide-device-csharp.md)
