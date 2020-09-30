---
title: Guida per gli sviluppatori di dispositivi (Python)-Plug and Play Microsoft Docs
description: Descrizione delle Plug and Play per gli sviluppatori di dispositivi Python
author: rido-min
ms.author: rmpablos
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4062716a0f2746ef37fa37a0b071871d4b18b0a7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580492"
---
# <a name="iot-plug-and-play-device-developer-guide-python"></a>Guida per gli sviluppatori di Plug and Play per dispositivi (Python)

[!INCLUDE [iot-pnp-device-devguide-selector.md](../../includes/iot-pnp-device-devguide-selector.md)]

[!INCLUDE [iot-pnp-device-devguide-intro.md](../../includes/iot-pnp-device-devguide-intro.md)]

## <a name="model-id-announcement"></a>Annuncio ID modello

Per annunciare l'ID modello, il dispositivo deve includerlo nelle informazioni di connessione:

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> Per i moduli e IoT Edge, usare `IoTHubModuleClient` al posto di `IoTHubDeviceClient` .

## <a name="dps-payload"></a>Payload DPS

I dispositivi che usano il [servizio Device provisioning (DPS)](../iot-dps/about-iot-dps.md) possono includere l'oggetto `modelId` da usare durante il processo di provisioning usando il payload JSON seguente.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementare la telemetria, le proprietà e i comandi

Come descritto in [informazioni sui componenti nei modelli plug and Play](concepts-components.md), i generatori di dispositivi devono decidere se vogliono usare i componenti per descrivere i dispositivi. Quando si usano i componenti, i dispositivi devono seguire le regole descritte in questa sezione.

### <a name="telemetry"></a>Telemetria

Un componente predefinito non richiede alcuna proprietà speciale.

Quando si usano i componenti annidati, i dispositivi devono impostare una proprietà del messaggio con il nome del componente:

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

### <a name="read-only-properties"></a>Proprietà di sola lettura

Per segnalare una proprietà del componente predefinito non è necessario alcun costrutto speciale:

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Quando si utilizzano i componenti annidati, è necessario creare le proprietà all'interno del nome del componente:

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>Proprietà scrivibili

Queste proprietà possono essere impostate dal dispositivo o aggiornate dalla soluzione. Se la soluzione aggiorna una proprietà, il client riceve una notifica come callback in `IoTHubDeviceClient` o `IoTHubModuleClient` . Per seguire le convenzioni Plug and Play, il dispositivo deve informare il servizio che la proprietà è stata ricevuta correttamente.

#### <a name="report-a-writable-property"></a>Segnala una proprietà scrivibile

Quando un dispositivo segnala una proprietà scrivibile, deve includere i `ack` valori definiti nelle convenzioni.

Per segnalare una proprietà scrivibile dal componente predefinito:

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Per segnalare una proprietà scrivibile da un componente annidato, il dispositivo gemello deve includere un marcatore:

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Sottoscrivere gli aggiornamenti delle proprietà desiderate

I servizi possono aggiornare le proprietà desiderate che attivano una notifica nei dispositivi connessi. Questa notifica include le proprietà desiderate aggiornate, incluso il numero di versione che identifica l'aggiornamento. I dispositivi devono rispondere con lo stesso `ack` messaggio delle proprietà segnalate.

Un componente predefinito Visualizza la singola proprietà e crea l'oggetto segnalato `ack` con la versione ricevuta:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
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

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
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

### <a name="commands"></a>Comandi

Un componente predefinito riceve il nome del comando così come è stato richiamato dal servizio.

Un componente annidato riceve il nome del comando preceduto dal nome del componente e dal `*` separatore.

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
```

#### <a name="request-and-response-payloads"></a>Payload di richiesta e risposta

I comandi usano i tipi per definire i payload di richiesta e risposta. Un dispositivo deve deserializzare il parametro di input in ingresso e serializzare la risposta. Nell'esempio seguente viene illustrato come implementare un comando con tipi complessi definiti nei payload:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Nei frammenti di codice seguenti viene illustrato il modo in cui un dispositivo implementa questa definizione di comando, inclusi i tipi utilizzati per abilitare la serializzazione e la deserializzazione:

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> I nomi di richiesta e risposta non sono presenti nei payload serializzati trasmessi in rete.

[!INCLUDE [iot-pnp-device-devguide-summary.md](../../includes/iot-pnp-device-devguide-summary.md)]
