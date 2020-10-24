---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: bef7807c0df580a6763a69619cdaa3d9d29f72e6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521390"
---
Sono disponibili anche le risorse seguenti:

- [Documentazione di riferimento per Python SDK](/python/api/azure-iot-hub/azure.iot.hub?preserve-view=true&view=azure-python)
- [Esempi client del servizio](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Esempi di dispositivi gemelli digitali](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>Esempi client del servizio hub Internet

Questa sezione illustra gli esempi di Python che usano il client del servizio hub Internet delle cose e le classi **IoTHubRegistryManager** e **CloudToDeviceMethod** . Usare la classe **IoTHubRegistryManager** per interagire con lo stato del dispositivo con i dispositivi gemelli. È anche possibile usare la classe **IoTHubRegistryManager** per [eseguire query sulle registrazioni dei dispositivi](../articles/iot-hub/iot-hub-devguide-query-language.md) nell'hub Internet delle cose. Usare la classe **CloudToDeviceMethod** per chiamare i comandi nel dispositivo. Il modello [DTDL](../articles/iot-pnp/concepts-digital-twin.md) per il dispositivo consente di definire le proprietà e i comandi implementati dal dispositivo. Nei frammenti di codice la `device_id` variabile include l'ID del dispositivo Plug and Play dispositivo registrato con l'hub Internet.

### <a name="get-the-device-twin-and-model-id"></a>Ottenere il dispositivo gemello e l'ID modello

Per ottenere il dispositivo gemello e l'ID del modello del Plug and Play dispositivo connesso all'hub Internet delle cose:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Aggiornamento dispositivo gemello

Il frammento di codice seguente illustra come aggiornare la `targetTemperature` Proprietà in un dispositivo. L'esempio Mostra come è necessario ottenere i dispositivi gemelli `etag` prima di aggiornarli. La proprietà è definita nel componente predefinito del dispositivo:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Il frammento di codice seguente mostra come aggiornare la `targetTemperature` proprietà su un componente. L'esempio Mostra come è necessario ottenere i dispositivi gemelli `ETag` prima di aggiornarli. La proprietà è definita nel componente **thermostat1** :

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Per una proprietà in un componente, la patch della proprietà ha un aspetto simile all'esempio seguente:

```json
{
"thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Chiama comando

Il frammento di codice seguente mostra come richiamare il `getMaxMinReport` comando definito in un componente predefinito:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

Il frammento di codice seguente mostra come chiamare il `getMaxMinReport` comando su un componente. Il comando è definito nel componente **thermostat1** :

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>Esempi di dispositivi gemelli per hub Internet

Usare la classe **DigitalTwinClient** per interagire con lo stato del dispositivo con i dispositivi gemelli digitali. Il modello [DTDL](../articles/iot-pnp/concepts-digital-twin.md) per il dispositivo consente di definire le proprietà e i comandi implementati dal dispositivo.

La `device_id` variabile include l'ID del dispositivo Plug and Play dispositivo registrato con l'hub Internet.

### <a name="get-the-digital-twin-and-model-id"></a>Ottenere il gemello digitale e l'ID modello

Per ottenere il dispositivo digitale gemello e l'ID del modello del Plug and Play dispositivo connesso all'hub Internet delle cose:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Aggiornare un dispositivo gemello digitale

Il frammento di codice seguente illustra come aggiornare la `targetTemperature` Proprietà in un dispositivo. La proprietà è definita nel componente predefinito del dispositivo:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

Il frammento di codice seguente mostra come aggiornare la `targetTemperature` proprietà su un componente. La proprietà è definita nel componente **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Chiama comando

Il frammento di codice seguente mostra come richiamare il `getMaxMinReport` comando definito in un componente predefinito:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

Il frammento di codice seguente mostra come chiamare il `getMaxMinReport` comando su un componente. Il comando è definito nel componente **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Leggere i dati di telemetria del dispositivo

I dispositivi Plug and Play i dispositivi inviano i dati di telemetria definiti nel modello DTDL all'hub Internet. Per impostazione predefinita, l'hub Internet instrada i dati di telemetria a un endpoint di hub eventi in cui è possibile utilizzarlo. Per altre informazioni, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a diversi endpoint](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Il frammento di codice seguente mostra come leggere i dati di telemetria dall'endpoint predefinito di hub eventi. Il codice in questo frammento di codice viene tratto dalla Guida introduttiva dell'hub Internet [per inviare dati di telemetria da un dispositivo a un hub Internet e leggerlo con un'applicazione back-end](../articles/iot-hub/quickstart-send-telemetry-python.md):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

Il seguente output del codice precedente Mostra i dati di telemetria relativi alla temperatura inviati dal **termostato** di nessun componente plug and Play dispositivo che ha solo il componente predefinito. La `dt-dataschema` proprietà di sistema Mostra l'ID modello:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

Il seguente output del codice precedente Mostra i dati di telemetria relativi alla temperatura inviati dal dispositivo **TemperatureController** plug and Play Internet. La `dt-subject` proprietà di sistema Mostra il nome del componente che ha inviato i dati di telemetria. In questo esempio, i due componenti sono `thermostat1` e `thermostat2` come definito nel modello DTDL. La `dt-dataschema` proprietà di sistema Mostra l'ID modello:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Leggere le notifiche di modifica del dispositivo gemello

È possibile configurare l'hub Internet per generare notifiche di modifica del dispositivo gemello per il routing a un endpoint supportato. Per altre informazioni, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi > eventi non di telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Il codice illustrato nel frammento di codice Python precedente genera l'output seguente quando l'hub Internet genera le notifiche di modifica del dispositivo gemello per un dispositivo termostato senza componenti. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

Il codice illustrato nel frammento di codice Python precedente genera l'output seguente quando l'hub Internet genera le notifiche di modifica del dispositivo gemello per un dispositivo con componenti. Questo esempio mostra l'output quando un dispositivo del sensore di temperatura con un componente del termostato genera notifiche. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Leggere le notifiche di modifica del dispositivo gemello digitale

È possibile configurare l'hub Internet per generare notifiche di modifica del dispositivo gemello digitale per il routing a un endpoint supportato. Per altre informazioni, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi > eventi non di telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Il codice illustrato nel frammento di codice Python precedente genera l'output seguente quando l'hub Internet genera notifiche di modifica del gemello digitale per un dispositivo termostato senza componenti. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

Il codice illustrato nel frammento di codice Python precedente genera l'output seguente quando l'hub Internet genera le notifiche di modifica del gemello digitale per un dispositivo con componenti. Questo esempio mostra l'output quando un dispositivo del sensore di temperatura con un componente del termostato genera notifiche. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
