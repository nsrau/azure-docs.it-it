---
title: Guida per gli sviluppatori di dispositivi (Node.js)-Plug and Play Internet delle cose | Microsoft Docs
description: Descrizione delle Plug and Play per gli sviluppatori di dispositivi Node.js
author: rido-min
ms.author: rmpablos
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 12b09cd76a3bda265a3eb610c95fb008af1f3914
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580081"
---
# <a name="iot-plug-and-play-device-developer-guide-nodejs"></a>Guida per gli sviluppatori di Plug and Play per dispositivi (Node.js)

[!INCLUDE [iot-pnp-device-devguide-selector.md](../../includes/iot-pnp-device-devguide-selector.md)]

[!INCLUDE [iot-pnp-device-devguide-intro.md](../../includes/iot-pnp-device-devguide-intro.md)]

## <a name="model-id-announcement"></a>Annuncio ID modello

Per annunciare l'ID modello, il dispositivo deve includerlo nelle informazioni di connessione:

```nodejs
const modelIdObject = { modelId: 'dtmi:com:example:Thermostat;1' };
const client = Client.fromConnectionString(deviceConnectionString, Protocol);
await client.setOptions(modelIdObject);
await client.open();
```

> [!TIP]
> Per i moduli e IoT Edge, usare `ModuleClient` al posto di `Client` .

## <a name="dps-payload"></a>Payload del servizio Device Provisioning

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

```nodejs
async function sendTelemetry(deviceClient, data, index, componentName) {
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

### <a name="read-only-properties"></a>Proprietà di sola lettura

Per segnalare una proprietà del componente predefinito non è necessario alcun costrutto speciale:

```nodejs
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat = createReportPropPatch({
  maxTempSinceLastReboot: 38.7
});

deviceTwin.properties.reported.update(patchThermostat, function (err) {
  if (err) throw err;
});
```

Il dispositivo gemello viene aggiornato con la proprietà segnalata successiva:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Quando si utilizzano i componenti annidati, le proprietà devono essere create all'interno del nome del componente.:

```nodejs
helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat1Info = helperCreateReportedPropertiesPatch({
  maxTempSinceLastReboot: 38.7,
}, 'thermostat1');

deviceTwin.properties.reported.update(patchThermostat1Info, function (err) {
  if (err) throw err;
});
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

Queste proprietà possono essere impostate dal dispositivo o aggiornate dalla soluzione. Se la soluzione aggiorna una proprietà, il client riceve una notifica come callback in `Client` o `ModuleClient` . Per seguire le convenzioni Plug and Play, il dispositivo deve informare il servizio che la proprietà è stata ricevuta correttamente.

#### <a name="report-a-writable-property"></a>Segnala una proprietà scrivibile

Quando un dispositivo segnala una proprietà scrivibile, deve includere i `ack` valori definiti nelle convenzioni.

Per segnalare una proprietà scrivibile dal componente predefinito:

```nodejs
patch = {
  targetTemperature:
    {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
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

```nodejs
patch = {
  thermostat1: {
    '__t' : 'c',
    targetTemperature: {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
  }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
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

```nodejs
const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
};

desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};
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

```nodejs
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
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

```nodejs
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  
  // ...

  case 'thermostat1*reboot': {
    await response.send(200, 'reboot response');
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};

client.onDeviceMethod('thermostat1*reboot', commandHandler);
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

```nodejs
class TemperatureSensor {

  // ...

  getMaxMinReportObject() {
    return {
      maxTemp: this.maxTemp,
      minTemp: this.minTemp,
      avgTemp: this.cumulativeTemperature / this.numberOfTemperatureReadings,
      endTime: (new Date(Date.now())).toISOString(),
      startTime: this.startTime
    };
  }
}

// ...

const deviceTemperatureSensor = new TemperatureSensor();

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await response.send(200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};
```

> [!Tip]
> I nomi di richiesta e risposta non sono presenti nei payload serializzati trasmessi in rete.

[!INCLUDE [iot-pnp-device-devguide-summary.md](../../includes/iot-pnp-device-devguide-summary.md)]
