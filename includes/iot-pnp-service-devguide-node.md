---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 7bf32de017a5f8ad19eb044ae7dbcdc2eaa96ca5
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521393"
---
Sono disponibili anche le risorse seguenti:

- [ Documentazione di riferimento diNode.js SDK](/javascript/api/azure-iothub?preserve-view=true&view=azure-node-latest)
- [Esempi client del servizio](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Esempi di dispositivi gemelli digitali](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>Esempi client del servizio hub Internet

Questa sezione illustra gli esempi JavaScript che usano il client del servizio hub Internet delle cose e le classi **del registro di sistema** e del **client** . Usare la classe **Registry** per interagire con lo stato del dispositivo con i dispositivi gemelli. È anche possibile usare la classe **Registry** per [eseguire query sulle registrazioni dei dispositivi](../articles/iot-hub/iot-hub-devguide-query-language.md) nell'hub Internet delle cose. Usare la classe **client** per chiamare i comandi nel dispositivo. Il modello [DTDL](../articles/iot-pnp/concepts-digital-twin.md) per il dispositivo consente di definire le proprietà e i comandi implementati dal dispositivo. Nei frammenti di codice la `deviceId` variabile include l'ID del dispositivo Plug and Play dispositivo registrato con l'hub Internet.

### <a name="get-the-device-twin-and-model-id"></a>Ottenere il dispositivo gemello e l'ID modello

Per ottenere il dispositivo gemello e l'ID del modello del Plug and Play dispositivo connesso all'hub Internet delle cose:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Aggiornamento dispositivo gemello

Il frammento di codice seguente illustra come aggiornare la `targetTemperature` Proprietà in un dispositivo. L'esempio Mostra come è necessario ottenere il gemello prima di aggiornarlo. La proprietà è definita nel componente predefinito del dispositivo:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Il frammento di codice seguente mostra come aggiornare la `targetTemperature` proprietà su un componente. L'esempio Mostra come è necessario ottenere il gemello prima di aggiornarlo. La proprietà è definita nel componente **thermostat1** :

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
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

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

Il frammento di codice seguente mostra come chiamare il `getMaxMinReport` comando su un componente. Il comando è definito nel componente **thermostat1** :

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>Esempi di dispositivi gemelli per hub Internet

Usare la classe **DigitalTwinClient** per interagire con lo stato del dispositivo con i dispositivi gemelli digitali. Il modello [DTDL](../articles/iot-pnp/concepts-digital-twin.md) per il dispositivo consente di definire le proprietà e i comandi implementati dal dispositivo.

Questa sezione illustra gli esempi JavaScript che usano l'API dei dispositivi digitali gemelli.

La `digitalTwinId` variabile include l'ID del dispositivo Plug and Play dispositivo registrato con l'hub Internet.

### <a name="get-the-digital-twin-and-model-id"></a>Ottenere il gemello digitale e l'ID modello

Per ottenere il dispositivo digitale gemello e l'ID del modello del Plug and Play dispositivo connesso all'hub Internet delle cose:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Aggiornare un dispositivo gemello digitale

Il frammento di codice seguente illustra come aggiornare la `targetTemperature` Proprietà in un dispositivo. La proprietà è definita nel componente predefinito del dispositivo:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

Il frammento di codice seguente mostra come aggiornare la `targetTemperature` proprietà su un componente. La proprietà è definita nel componente **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Chiama comando

Il frammento di codice seguente mostra come richiamare il `getMaxMinReport` comando definito in un componente predefinito:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

Il frammento di codice seguente mostra come chiamare il `getMaxMinReport` comando su un componente. Il comando è definito nel componente **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Leggere i dati di telemetria del dispositivo

I dispositivi Plug and Play i dispositivi inviano i dati di telemetria definiti nel modello DTDL all'hub Internet. Per impostazione predefinita, l'hub Internet instrada i dati di telemetria a un endpoint di hub eventi in cui è possibile utilizzarlo. Per altre informazioni, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a diversi endpoint](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Il frammento di codice seguente mostra come leggere i dati di telemetria dall'endpoint predefinito di hub eventi. Il codice in questo frammento di codice viene tratto dalla Guida introduttiva dell'hub Internet [per inviare dati di telemetria da un dispositivo a un hub Internet e leggerlo con un'applicazione back-end](../articles/iot-hub/quickstart-send-telemetry-node.md):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

Il seguente output del codice precedente Mostra i dati di telemetria relativi alla temperatura inviati dal dispositivo **TemperatureController** plug and Play Internet. La `dt-subject` proprietà di sistema Mostra il nome del componente che ha inviato i dati di telemetria. In questo esempio, i due componenti sono `thermostat1` e `thermostat2` come definito nel modello DTDL. La `dt-dataschema` proprietà di sistema Mostra l'ID modello:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Leggere le notifiche di modifica del dispositivo gemello

È possibile configurare l'hub Internet per generare notifiche di modifica del dispositivo gemello per il routing a un endpoint supportato. Per altre informazioni, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi > eventi non di telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Il codice illustrato nel frammento di codice JavaScript precedente genera l'output seguente quando l'hub delle cose genera notifiche di modifica del dispositivo gemello per un dispositivo termostato senza componente. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

Il codice illustrato nel frammento di codice JavaScript precedente genera l'output seguente quando l'hub Internet genera le notifiche di modifica del dispositivo gemello per un dispositivo con componenti. Questo esempio mostra l'output quando un dispositivo del sensore di temperatura con un componente del termostato genera notifiche. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Leggere le notifiche di modifica del dispositivo gemello digitale

È possibile configurare l'hub Internet per generare notifiche di modifica del dispositivo gemello digitale per il routing a un endpoint supportato. Per altre informazioni, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi > eventi non di telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Il codice illustrato nel frammento di codice JavaScript precedente genera l'output seguente quando l'hub Internet genera notifiche di modifica del gemello digitale per un dispositivo termostato senza componenti. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

Il codice illustrato nel frammento di codice JavaScript precedente genera l'output seguente quando l'hub Internet genera le notifiche di modifica del gemello digitale per un dispositivo con componenti. Questo esempio mostra l'output quando un dispositivo del sensore di temperatura con un componente del termostato genera notifiche. Le proprietà dell'applicazione `iothub-message-schema` e `opType` forniscono informazioni sul tipo di notifica delle modifiche:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
