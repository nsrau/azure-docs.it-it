---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 46cb129d18e082f836a688b95111c10c8e191b01
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511466"
---
## <a name="model-id-announcement"></a>Annuncio ID modello

Per annunciare l'ID modello, il dispositivo deve includerlo nelle informazioni di connessione:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Il nuovo `ClientOptions` Overload è disponibile in tutti i `DeviceClient` metodi utilizzati per inizializzare una connessione.

> [!TIP]
> Per i moduli e IoT Edge, usare `ModuleClient` al posto di `DeviceClient` .

## <a name="dps-payload"></a>Payload del servizio Device Provisioning

I dispositivi che usano il [servizio Device provisioning (DPS)](../articles/iot-dps/about-iot-dps.md) possono includere l'oggetto `modelId` da usare durante il processo di provisioning usando il payload JSON seguente.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementare la telemetria, le proprietà e i comandi

Come descritto in [informazioni sui componenti nei modelli plug and Play](../articles/iot-pnp/concepts-components.md), i generatori di dispositivi devono decidere se vogliono usare i componenti per descrivere i dispositivi. Quando si usano i componenti, i dispositivi devono seguire le regole descritte in questa sezione.

### <a name="telemetry"></a>Telemetria

Un componente predefinito non richiede alcuna proprietà speciale.

Quando si usano i componenti annidati, i dispositivi devono impostare una proprietà del messaggio con il nome del componente:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Proprietà di sola lettura

Per segnalare una proprietà del componente predefinito non è necessario alcun costrutto speciale:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Quando si utilizzano i componenti annidati, è necessario creare le proprietà all'interno del nome del componente:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Proprietà scrivibili

Queste proprietà possono essere impostate dal dispositivo o aggiornate dalla soluzione. Se la soluzione aggiorna una proprietà, il client riceve una notifica come callback in `DeviceClient` o `ModuleClient` . Per seguire le convenzioni Plug and Play, il dispositivo deve informare il servizio che la proprietà è stata ricevuta correttamente.

#### <a name="report-a-writable-property"></a>Segnala una proprietà scrivibile

Quando un dispositivo segnala una proprietà scrivibile, deve includere i `ack` valori definiti nelle convenzioni.

Per segnalare una proprietà scrivibile dal componente predefinito:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Per segnalare una proprietà scrivibile da un componente annidato, il dispositivo gemello deve includere un marcatore:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Sottoscrivere gli aggiornamenti delle proprietà desiderate

I servizi possono aggiornare le proprietà desiderate che attivano una notifica nei dispositivi connessi. Questa notifica include le proprietà desiderate aggiornate, incluso il numero di versione che identifica l'aggiornamento. I dispositivi devono rispondere con lo stesso `ack` messaggio delle proprietà segnalate.

Un componente predefinito Visualizza la singola proprietà e crea l'oggetto segnalato `ack` con la versione ricevuta:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Il dispositivo gemello Mostra la proprietà nelle sezioni desiderate e segnalate:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Un componente annidato riceve le proprietà desiderate di cui è stato eseguito il wrapper con il nome del componente e deve segnalare la `ack` Proprietà segnalata:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Il dispositivo gemello per un componente annidato Mostra le sezioni desiderate e segnalate come indicato di seguito:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Comandi:

Un componente predefinito riceve il nome del comando così come è stato richiamato dal servizio.

Un componente annidato riceve il nome del comando preceduto dal nome del componente e dal `*` separatore.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Payload di richiesta e risposta

I comandi usano i tipi per definire i payload di richiesta e risposta. Un dispositivo deve deserializzare il parametro di input in ingresso e serializzare la risposta. Nell'esempio seguente viene illustrato come implementare un comando con tipi complessi definiti nei payload:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Nei frammenti di codice seguenti viene illustrato il modo in cui un dispositivo implementa questa definizione di comando, inclusi i tipi utilizzati per abilitare la serializzazione e la deserializzazione:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> I nomi di richiesta e risposta non sono presenti nei payload serializzati trasmessi in rete.
