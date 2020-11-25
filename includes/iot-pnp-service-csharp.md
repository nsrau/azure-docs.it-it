---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 4308dd2b63b33604af83b360e5c1c0f02a3dec27
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487818"
---
Plug and Play IoT semplifica l'Internet delle cose consentendo di interagire con le funzionalità di un dispositivo senza conoscere l'implementazione del dispositivo sottostante. Questa guida di avvio rapido mostra come usare C# per connettersi a un dispositivo Plug and Play IoT connesso alla soluzione e controllarlo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Per completare questa guida di avvio rapido in Windows, è necessario che nel computer di sviluppo sia installato il software seguente:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonare il repository dell'SDK con il codice di esempio

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione per dispositivi Plug and Play IoT di esempio in esecuzione in Windows all'hub IoT (C#)](../articles/iot-pnp/quickstart-connect-device.md), il repository è già stato clonato.

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
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](../articles/iot-pnp/set-up-environment.md) |

1. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

1. Verranno visualizzati messaggi che indicano che il dispositivo ha inviato informazioni e segnalato il proprio stato online. Questi messaggi indicano che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà. Non chiudere questa istanza di Visual Studio perché sarà necessaria per verificare il funzionamento dell'esempio di servizio.

## <a name="run-the-sample-solution"></a>Eseguire la soluzione di esempio

In [Configurare l'ambiente per le esercitazioni e le guide di avvio rapido di Plug and Play IoT](../articles/iot-pnp/set-up-environment.md) sono state create due variabili di ambiente per configurare l'esempio da connettere all'hub IoT e al dispositivo:

* **IOTHUB_CONNECTION_STRING**: stringa di connessione dell'hub IoT annotata in precedenza.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

In questo argomento di avvio rapido si userà una soluzione IoT di esempio in C# per interagire con il dispositivo di esempio appena configurato.

1. In un'altra istanza di Visual Studio aprire il progetto *azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. In Visual Studio passare a **Progetto > Thermostat Properties (Proprietà termostato) > Debug**. Aggiungere quindi al progetto le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](../articles/iot-pnp/set-up-environment.md) |

1. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

### <a name="get-device-twin"></a>Recuperare il dispositivo gemello

Il frammento di codice seguente mostra come l'applicazione del servizio recupera il dispositivo gemello:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> In questo esempio viene usato lo spazio dei nomi **Microsoft.Azure.Devices.Client** dal **client del servizio hub IoT**. Per altre informazioni sulle API, tra cui l'API gemelli digitali, vedere la [guida per sviluppatori di servizi](../articles/iot-pnp/concepts-developer-guide-service.md).

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
