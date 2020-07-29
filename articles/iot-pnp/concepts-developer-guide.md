---
title: Guida per gli sviluppatori-anteprima Plug and Play Microsoft Docs
description: Descrizione delle Plug and Play per gli sviluppatori
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef221ea068f2786a4a84f20a29e80dd7176f06c6
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337416"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>Guida per gli sviluppatori dell'anteprima Plug and Play

Il Plug and Play anteprima consente di creare dispositivi intelligenti che annunciano le proprie funzionalità alle applicazioni Azure Internet. I dispositivi Plug and Play non richiedono la configurazione manuale quando un cliente li connette alle applicazioni con funzionalità abilitate per la Plug and Play.

In questa guida vengono descritti i passaggi di base necessari per creare un dispositivo che segue le [convenzioni plug and Play](concepts-convention.md)di Internet delle cose e le API REST disponibili che è possibile usare per interagire con il dispositivo.

Per creare un dispositivo Plug and Play, attenersi alla procedura seguente:

1. Verificare che il dispositivo usi il protocollo MQTT o MQTT over WebSocket per connettersi all'hub di Azure.
1. Creare un modello [DTDL (Digital Gemini Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) per descrivere il dispositivo. Per altre informazioni, vedere informazioni [sui componenti nei modelli plug and Play](concepts-components.md).
1. Aggiornare il dispositivo per annunciare `model-id` come parte della connessione del dispositivo.
1. Implementare la telemetria, le proprietà e i comandi usando le [convenzioni plug and Play](concepts-convention.md)

Quando l'implementazione del dispositivo è pronta, usare [Esplora risorse di Azure](howto-use-iot-explorer.md) per verificare che il dispositivo segua le convenzioni plug and Play.

> [!Tip]
> Tutti i frammenti di codice in questo articolo usano C#, ma i concetti sono applicabili a tutti gli SDK disponibili per C, Python, node e Java.

## <a name="model-id-announcement"></a>Annuncio ID modello

Per annunciare l'ID modello, il dispositivo deve includerlo nelle informazioni di connessione:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Il nuovo `ClientOptions` Overload è disponibile in tutti i `DeviceClient` metodi utilizzati per inizializzare una connessione.

L'annuncio dell'ID modello è stato aggiunto alle versioni successive degli SDK

|SDK|Versione|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Nodo|1.17.0|
|Python|2.1.4|

## <a name="implement-telemetry-properties-and-commands"></a>Implementare la telemetria, le proprietà e i comandi

Come descritto in [informazioni sui componenti nei modelli plug and Play](concepts-components.md), i generatori di dispositivi devono decidere se vogliono usare i componenti per descrivere i dispositivi. Quando si usano i componenti, i dispositivi devono seguire le regole descritte in questa sezione.

### <a name="telemetry"></a>Telemetria

I modelli senza componenti non richiedono alcuna proprietà speciale.

Quando si usano i componenti, i dispositivi devono impostare una proprietà del messaggio con il nome del componente:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Proprietà di sola lettura

I modelli senza componenti non richiedono un costrutto speciale:

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

Quando si usano i componenti, è necessario creare le proprietà all'interno del nome del componente:

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

Queste proprietà possono essere impostate dal dispositivo o aggiornate dalla soluzione. Se la soluzione aggiorna una proprietà, il client riceve una notifica come callback in `DeviceClient` . Per seguire le convenzioni Plug and Play, il dispositivo deve informare il servizio che la proprietà è stata ricevuta correttamente.

#### <a name="report-a-writable-property"></a>Segnala una proprietà scrivibile

Quando un dispositivo segnala una proprietà scrivibile, deve includere i `ack` valori definiti nelle convenzioni.

Per segnalare una proprietà scrivibile senza componenti:

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

Per segnalare una proprietà scrivibile da un componente, il dispositivo gemello deve includere un marcatore:

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

Nei modelli senza componenti viene visualizzata la singola proprietà e viene creato il report `ack` con la versione ricevuta:

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

I modelli con componenti ricevono le proprietà desiderate di cui è stato eseguito il wrapper con il nome del componente e devono segnalare la `ack` Proprietà segnalata:

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

Il dispositivo gemello per i componenti Mostra le sezioni desiderate e segnalate come indicato di seguito:

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

I modelli senza componenti ricevono il nome del comando così come è stato richiamato dal servizio.

I modelli con componenti riceveranno il nome del comando con prefisso il componente e il `*` separatore.

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

## <a name="interact-with-the-device"></a>Interagire con il dispositivo 

Internet delle cose Plug and Play consente di usare i dispositivi che hanno annunciato l'ID modello con l'hub Internet delle cose. Ad esempio, è possibile accedere direttamente alle proprietà e ai comandi di un dispositivo.

Per usare un Plug and Play dispositivo connesso all'hub Internet delle cose, usare l'API REST dell'hub Internet delle cose o uno degli SDK del linguaggio Internet delle cose. Gli esempi seguenti usano l'API REST dell'hub Internet. La versione corrente dell'API è `2020-05-31-preview` . Accodare `?api-version=2020-05-31` le chiamate a Rest pi.

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

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come modellare i dispositivi, di seguito sono riportate alcune risorse aggiuntive:

- [Digital Gemini Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK per dispositivi per C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST](https://docs.microsoft.com/rest/api/iothub/device)
- [Componenti del modello](./concepts-components.md)
