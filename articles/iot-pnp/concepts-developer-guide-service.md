---
title: Guida per gli sviluppatori di servizi-Plug and Play Internet | Microsoft Docs
description: Descrizione delle Plug and Play per gli sviluppatori di servizi
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2a61eefc9c065253bdac11665f0135e493584c0d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945108"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guida per gli sviluppatori del servizio Plug and Play

Internet delle cose Plug and Play consente di creare dispositivi intelligenti che annunciano le proprie funzionalità alle applicazioni Azure Internet. I dispositivi Plug and Play non richiedono la configurazione manuale quando un cliente li connette alle applicazioni con funzionalità abilitate per la Plug and Play.

Internet delle cose Plug and Play consente di usare i dispositivi che hanno annunciato l'ID modello con l'hub Internet delle cose. Ad esempio, è possibile accedere direttamente alle proprietà e ai comandi di un dispositivo.

Per usare un Plug and Play dispositivo connesso all'hub Internet delle cose, usare uno degli SDK del servizio Internet delle cose o l'API REST dell'hub Internet:

## <a name="service-sdks"></a>SDK per servizi

Per interagire con i dispositivi e i moduli, usare gli SDK dei servizi di Azure. Ad esempio, è possibile usare gli SDK del servizio per leggere e aggiornare le proprietà dei dispositivi gemelli e richiamare i comandi. Le lingue supportate includono C#, Java, Node.js e Python.

Gli SDK dei servizi consentono di accedere alle informazioni sul dispositivo da una soluzione, ad esempio un'applicazione desktop o Web. Gli SDK dei servizi includono due spazi dei nomi e modelli a oggetti che è possibile usare per recuperare l'ID modello:

- Client del servizio hub Internet. Questo servizio espone l'ID modello come proprietà del dispositivo gemello.

- Client del servizio digitale gemelli. La nuova API dei gemelli digitali opera sui costrutti del modello [DTDL (Digital gemelle Definition Language)](concepts-digital-twin.md) , ad esempio componenti, proprietà e comandi. Le API dei dispositivi gemelli digitali semplificano la creazione di soluzioni Plug and Play per i generatori di soluzioni.

| Piattaforma | Client del servizio hub Internet | Client Servizi gemelli digitali |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [Documentazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices) <br/> [Esempi](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [Esempi](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Documentazione](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Esempi](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [Esempi](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Documentazione](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Esempio](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Documentazione](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [Esempio](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [Documentazione](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Esempio](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Documentazione](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [Esempio](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="iot-hub-service-client-examples"></a>Esempi client del servizio hub Internet

Questa sezione illustra gli esempi di C# che usano il client del servizio hub Internet delle cose e le classi **RegistryManager** e **ServiceClient** . Usare la classe **RegistryManager** per interagire con lo stato del dispositivo con i dispositivi gemelli. È anche possibile usare la classe **RegistryManager** per [eseguire query sulle registrazioni dei dispositivi](..\iot-hub\iot-hub-devguide-query-language.md) nell'hub Internet delle cose. Usare la classe **ServiceClient** per chiamare i comandi nel dispositivo. Il modello [DTDL](concepts-digital-twin.md) per il dispositivo consente di definire le proprietà e i comandi implementati dal dispositivo. Nei frammenti di codice la `deviceTwinId` variabile include l'ID del dispositivo Plug and Play dispositivo registrato con l'hub Internet.

### <a name="get-the-device-twin-and-model-id"></a>Ottenere il dispositivo gemello e l'ID modello

Per ottenere il dispositivo gemello e l'ID del modello del Plug and Play dispositivo connesso all'hub Internet delle cose:

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(parameters.HubConnectionString);

Twin twin = await registryManager.GetTwinAsync(deviceTwinId);
Console.WriteLine($"Device twin: \n{JsonConvert.SerializeObject(twin, Formatting.Indented)}");
Console.WriteLine($"Model ID: {twin.ModelId}.");
```

### <a name="update-device-twin"></a>Aggiornamento dispositivo gemello

Il frammento di codice seguente illustra come aggiornare la `targetTemperature` Proprietà in un dispositivo. L'esempio Mostra come è necessario ottenere i dispositivi gemelli `ETag` prima di aggiornarli. La proprietà è definita nel componente predefinito del dispositivo:

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired["targetTemperature"] = desiredTargetTemperature;

Console.WriteLine($"Update the targetTemperature property to {desiredTargetTemperature}.");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);
```

Il frammento di codice seguente mostra come aggiornare la `targetTemperature` proprietà su un componente. L'esempio Mostra come è necessario ottenere i dispositivi gemelli `ETag` prima di aggiornarli. La proprietà è definita nell'interfaccia **Thermostat1** :

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

var twinPatch = CreatePropertyPatch("targetTemperature", desiredTargetTemperature, "thermostat1");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);

// ...

private static Twin CreatePropertyPatch(string propertyName, object propertyValue, string componentName)
{
    var twinPatch = new Twin();
    twinPatch.Properties.Desired[componentName] = new
    {
        __t = "c"
    };
    twinPatch.Properties.Desired[componentName][propertyName] = JsonConvert.SerializeObject(propertyValue);
    return twinPatch;
}
```

Per una proprietà in un componente, la patch della proprietà ha un aspetto simile all'esempio seguente:

```json
{
"sampleComponentName":
  {
    "__t": "c",
    "samplePropertyName": 20
  }
}
```

### <a name="call-command"></a>Chiama comando

Il frammento di codice seguente mostra come richiamare il `getMaxMinReport` comando definito in un componente predefinito:

```csharp
ServiceClient serviceClient = ServiceClient.CreateFromConnectionString(parameters.HubConnectionString);

var commandInvocation = new CloudToDeviceMethod("getMaxMinReport") { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
  CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);

  Console.WriteLine($"Command getMaxMinReport was invoked." +
      $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine($"Unable to execute command getMaxMinReport on {deviceTwinId}.";
}
```

Il frammento di codice seguente mostra come chiamare il `getMaxMinReport` comando su un componente. Il comando è definito nell'interfaccia **Thermostat1** :

```csharp
// Create command name to invoke for component. The command is formatted as <component name>*<command name>
string commandToInvoke = "thermostat1*getMaxMinReport";
var commandInvocation = new CloudToDeviceMethod(commandToInvoke) { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
    CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);
    Console.WriteLine($"Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
}
```

## <a name="digital-twin-examples"></a>Esempi di dispositivi gemelli digitali

Usare la classe **DigitalTwinClient** per interagire con lo stato del dispositivo con i dispositivi gemelli digitali. Il modello [DTDL](concepts-digital-twin.md) per il dispositivo consente di definire le proprietà e i comandi implementati dal dispositivo.

Questa sezione illustra gli esempi di C# che usano l'API Digital Gemells. I frammenti di codice seguenti usano le classi seguenti per rappresentare il gemello digitale del termostato e dei dispositivi del controller di temperatura:

```csharp
using Microsoft.Azure.Devices.Serialization;
using Newtonsoft.Json;
using System;

namespace Microsoft.Azure.Devices.Samples
{
  internal class ThermostatTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new ThermostatMetadata Metadata { get; set; }

    [JsonProperty("maxTempSinceLastReboot")]
    public double? MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public double? TargetTemperature { get; set; }
  }

  internal class ThermostatMetadata : DigitalTwinMetadata
  {
    [JsonProperty("maxTempSinceLastReboot")]
    public ReportedPropertyMetadata MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public WritableProperty TargetTemperature { get; set; }
  }

  internal class ReportedPropertyMetadata
  {
    [JsonProperty("lastUpdateTime")]
    public DateTimeOffset LastUpdateTime { get; set; }
  }

  internal class TemperatureControllerTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new TemperatureControllerMetadata Metadata { get; set; }

    [JsonProperty("serialNumber")]
    public string SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public ThermostatTwin Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public ThermostatTwin Thermostat2 { get; set; }
  }

  internal class TemperatureControllerMetadata : DigitalTwinMetadata
  {
    [JsonProperty("serialNumber")]
    public ReportedPropertyMetadata SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public WritableProperty Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public WritableProperty Thermostat2 { get; set; }
  }
}
```

La `digitalTwinId` variabile include l'ID del dispositivo Plug and Play dispositivo registrato con l'hub Internet.

### <a name="get-the-digital-twin-and-model-id"></a>Ottenere il gemello digitale e l'ID modello

Per ottenere il dispositivo digitale gemello e l'ID del modello del Plug and Play dispositivo connesso all'hub Internet delle cose:

```csharp
DigitalTwinClient digitalTwinClient = DigitalTwinClient.CreateFromConnectionString(parameters.HubConnectionString);
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
ThermostatTwin thermostatTwin = getDigitalTwinResponse.Body;
Console.WriteLine($"Model ID: {thermostatTwin.Metadata.ModelId}.");
Console.WriteLine($"Digital Twin: \n{JsonConvert.SerializeObject(thermostatTwin, Formatting.Indented)}");
```

### <a name="update-digital-twin"></a>Aggiornare un dispositivo gemello digitale

Il frammento di codice seguente illustra come aggiornare la `targetTemperature` Proprietà in un dispositivo. La proprietà è definita nel componente predefinito del dispositivo:

```csharp
var updateOperation = new UpdateOperationsUtility();

int desiredTargetTemperature = 60;

// Get the current value of the targetTemperature property
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
double? currentTargetTemperature = getDigitalTwinResponse.Body.TargetTemperature;

// Has the targetTemperature property previously been set?
if (currentTargetTemperature != null)
{
  // Update the existing property
  // Prepend the property path with a '/'
  updateOperation.AppendReplacePropertyOp($"/targetTemperature", desiredTargetTemperature);
}
else
{
  // Add a new property
  // Prepend the property path with a '/'
  updateOperation.AppendAddPropertyOp($"/targetTemperature", desiredTargetTemperature);
}

// Update the targetTemperature property on the digital twin
HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

Il frammento di codice seguente mostra come aggiornare la `targetTemperature` proprietà su un componente. La proprietà è definita nel componente **Thermostat1** :

```csharp
int desiredTargetTemperature = 60;

var updateOperation = new UpdateOperationsUtility();

// Look at when the property was updated and what was it set to.
HttpOperationResponse<TemperatureControllerTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
  .GetDigitalTwinAsync<TemperatureControllerTwin>(digitalTwinId);

ThermostatTwin thermostat1 = getDigitalTwinResponse.Body.Thermostat1;

if (thermostat1 != null)
{
  // Thermostat1 is present in the TemperatureController twin. You can add/replace the component-level property "targetTemperature"
  double? currentComponentTargetTemperature = getDigitalTwinResponse.Body.Thermostat1.TargetTemperature;
  if (currentComponentTargetTemperature != null)
  {
      DateTimeOffset targetTemperatureDesiredLastUpdateTime = getDigitalTwinResponse.Body.Thermostat1.Metadata.TargetTemperature.LastUpdateTime;

      // The property path to be replaced should be prepended with a '/'
      updateOperation.AppendReplacePropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
  else
  {
      // The property path to be added should be prepended with a '/'
      updateOperation.AppendAddPropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
}
else
{
    // Thermostat1 is not present in the TemperatureController twin. Add the component.
    var componentProperty = new Dictionary<string, object> { { "targetTemperature", desiredTargetTemperature }, { "$metadata", new object() } };

    // The property path to be replaced should be prepended with a '/'
    updateOperation.AppendAddComponentOp("/thermostat1", componentProperty);
}

HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

### <a name="call-command"></a>Chiama comando

Il frammento di codice seguente mostra come richiamare il `getMaxMinReport` comando definito in un componente predefinito:

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
  HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
    .InvokeCommandAsync(digitalTwinId, "getMaxMinReport", JsonConvert.SerializeObject(since));

  Console.WriteLine($"Command getMaxMinReport was invoked. \nDevice returned status: {invokeCommandResponse.Body.Status}." +
    $"\nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
  if (e.Response.StatusCode == HttpStatusCode.NotFound)
  {
    Console.WriteLine($"Unable to execute command getMaxMinReport on {digitalTwinId}.");
  }
}
```

Il frammento di codice seguente mostra come chiamare il `getMaxMinReport` comando su un componente. Il comando è definito nell'interfaccia **Thermostat1** :

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
    HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
        .InvokeComponentCommandAsync(digitalTwinId, "thermostat1", "getMaxMinReport", JsonConvert.SerializeObject(since));

    Console.WriteLine("Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {invokeCommandResponse.Body.Status}. \nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
    if (e.Response.StatusCode == HttpStatusCode.NotFound)
    {
        Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
    }
}
```

## <a name="rest-api"></a>API REST

Gli esempi seguenti usano l'API REST dell'hub Internet per interagire con un dispositivo Plug and Play. La versione corrente dell'API è `2020-09-30` . Accodare `?api-version=2020-09-30` le chiamate a Rest pi.

> [!NOTE]
> I moduli gemelli non sono attualmente supportati dall' `digitalTwins` API.

Se il dispositivo termostato viene chiamato `t-123` , si ottengono tutte le proprietà di tutte le interfacce implementate dal dispositivo con una chiamata Get dell'API REST:

```REST
GET /digitalTwins/t-123
```

Questa chiamata includerà la proprietà JSON `$metadata.$model` con l'ID modello annunciato dal dispositivo.

Tutte le proprietà di tutte le interfacce sono accessibili con il `GET /DigitalTwin/{device-id}` modello API REST in cui `{device-id}` è l'identificatore del dispositivo:

```REST
GET /digitalTwins/{device-id}
```

È possibile chiamare direttamente i comandi del dispositivo Plug and Play. Se il `Thermostat` componente nel `t-123` dispositivo ha un `restart` comando, è possibile chiamarlo con una chiamata all'API REST:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Più in generale, i comandi possono essere chiamati tramite questo modello API REST:

- `device-id`: identificatore per il dispositivo.
- `component-name`: nome dell'interfaccia dalla sezione Implements nel modello di funzionalità del dispositivo.
- `command-name`: nome del comando.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="read-device-telemetry"></a>Leggere i dati di telemetria del dispositivo

I dispositivi Plug and Play i dispositivi inviano i dati di telemetria definiti nel modello DTDL all'hub Internet. Per impostazione predefinita, l'hub Internet instrada i dati di telemetria a un endpoint di hub eventi in cui è possibile utilizzarlo. Per altre informazioni, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a diversi endpoint](../iot-hub/iot-hub-devguide-messages-d2c.md).

Il frammento di codice seguente mostra come leggere i dati di telemetria dall'endpoint predefinito di hub eventi. Il codice in questo frammento di codice viene tratto dalla Guida introduttiva dell'hub Internet [per inviare dati di telemetria da un dispositivo a un hub Internet e leggerlo con un'applicazione back-end](../iot-hub/quickstart-send-telemetry-dotnet.md):

```csharp
await using EventHubConsumerClient consumer = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, connectionString, EventHubName);

Console.WriteLine("Listening for messages on all partitions");

try
{
    await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync(cancellationToken))
    {
        Console.WriteLine("Message received on partition {0}:", partitionEvent.Partition.PartitionId);

        string data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
        Console.WriteLine("\t{0}:", data);

        Console.WriteLine("Application properties (set by device):");
        foreach (var prop in partitionEvent.Data.Properties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }

        Console.WriteLine("System properties (set by IoT Hub):");
        foreach (var prop in partitionEvent.Data.SystemProperties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }
    }
}
catch (TaskCanceledException)
{
    // This is expected when the token is signaled; it should not be considered an
    // error in this scenario.
}
```

Il seguente output del codice precedente Mostra i dati di telemetria relativi alla temperatura inviati dal **termostato** di nessun componente plug and Play dispositivo che ha solo il componente predefinito. La `dt-dataschema` proprietà di sistema Mostra l'ID modello:

```cmd/sh
Message received on partition 1:
        { "temperature": 25.5 }:
Application properties (set by device):
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:30:58
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:Thermostat;1
        content-type: application/json
        content-encoding: utf-8
```

Il seguente output del codice precedente Mostra i dati di telemetria relativi alla temperatura inviati dal dispositivo **TemperatureController** plug and Play Internet. La `dt-subject` proprietà di sistema Mostra il nome del componente che ha inviato i dati di telemetria. In questo esempio, i due componenti sono `thermostat1` e `thermostat2` come definito nel modello DTDL. La `dt-dataschema` proprietà di sistema Mostra l'ID modello:

```cmd/sh
Message received on partition 1:
        {"temperature":11.1}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat1
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
Message received on partition 1:
        {"temperature":41.2}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat2
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-device-twin-change-notifications"></a>Leggere le notifiche di modifica del dispositivo gemello

È possibile configurare l'hub Internet per generare notifiche di modifica del dispositivo gemello per il routing a un endpoint supportato. Per altre informazioni, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi > eventi non di telemetria](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Il codice illustrato nel frammento di codice C# precedente genera l'output seguente quando l'hub Internet genera le notifiche di modifica del dispositivo gemello per un dispositivo termostato senza componenti. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Message received on partition 1:
        {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":9.6,"$metadata":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z"}},"$version":2}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:17:41.7408552+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:17:41
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 61394e8ba7d
        content-type: application/json
        content-encoding: utf-8
```

Il codice illustrato nel frammento di codice C# precedente genera l'output seguente quando l'hub Internet genera le notifiche di modifica del dispositivo gemello per un dispositivo con componenti. Questo esempio mostra l'output quando un dispositivo del sensore di temperatura con un componente del termostato genera notifiche. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Message received on partition 1:
        {"version":5,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":9.6},"$metadata":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","thermostat1":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","__t":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"}}},"$version":4}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:27:59.5159720+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:27:59
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 615051f364e
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-digital-twin-change-notifications"></a>Leggere le notifiche di modifica del dispositivo gemello digitale

È possibile configurare l'hub Internet per generare notifiche di modifica del dispositivo gemello digitale per il routing a un endpoint supportato. Per altre informazioni, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi > eventi non di telemetria](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Il codice illustrato nel frammento di codice C# precedente genera l'output seguente quando l'hub Internet genera notifiche di modifica dei dispositivi gemelli digitali per un dispositivo termostato senza componenti. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-06T10:39:16.0209836Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":34.9}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:39:16.0209836+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:39:16
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 6169857bf8c
        content-type: application/json-patch+json
        content-encoding: utf-8
```

Il codice illustrato nel frammento di codice C# precedente genera l'output seguente quando l'hub Internet genera notifiche di modifica di dispositivi gemelli digitali per un dispositivo con componenti. Questo esempio mostra l'output quando un dispositivo del sensore di temperatura con un componente del termostato genera notifiche. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-06T10:41:44.8312666Z"}},"maxTempSinceLastReboot":29.1}}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:41:44.8312666+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:41:44
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 616f108f0e3
        content-type: application/json-patch+json
        content-encoding: utf-8
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come modellare i dispositivi, di seguito sono riportate alcune risorse aggiuntive:

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK per dispositivi per C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST](https://docs.microsoft.com/rest/api/iothub/device)
- [Componenti del modello](./concepts-components.md)
- [Installare e usare gli strumenti di creazione di DTDL](howto-use-dtdl-authoring-tools.md)
