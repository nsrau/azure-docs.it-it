---
title: Informazioni sui gemelli digitali Plug and Play IoT
description: Informazioni sul modo in cui l'Plug and Play usa i gemelli digitali
author: prashmo
ms.author: prashmo
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ea523045875e0abc9e14924c7bb388ea2cfcc2db
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046469"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>Informazioni sui gemelli digitali Plug and Play IoT

Un dispositivo Plug and Play implementa un modello descritto dallo schema [DTDL (Digital Twin Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) . Un modello descrive il set di componenti, proprietà, comandi e messaggi di telemetria che possono essere presenti in un determinato dispositivo. Un dispositivo gemello e un dispositivo gemello digitale vengono inizializzati la prima volta che un Plug and Play dispositivo si connette a un hub.

Il Plug and Play di Internet delle cose USA DTDL versione 2. Per altre informazioni su questa versione, vedere la specifica [DTDL (Digital Twin Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) in GitHub.

DTDL non è esclusivo per gli Internet delle cose Plug and Play. Altri servizi Internet, ad esempio i dispositivi [gemelli digitali di Azure](../digital-twins/overview.md), lo usano per rappresentare interi ambienti, ad esempio edifici e reti energetiche. Per altre informazioni, vedere [comprendere i modelli gemelli nei dispositivi gemelli digitali di Azure](../digital-twins/concepts-models.md).

Questo articolo descrive come i componenti e le proprietà sono rappresentati nelle sezioni *desiderate* e *segnalate* di un dispositivo gemello. Viene inoltre descritto il mapping di questi concetti al gemello digitale corrispondente.

Il dispositivo Plug and Play per gli elementi di questo articolo che implementa il [modello di controller di temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) con componente [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) .

## <a name="device-twins-and-digital-twins"></a>Dispositivi gemelli e dispositivi gemelli digitali

I dispositivi gemelli sono documenti JSON che archiviano le informazioni sullo stato del dispositivo, inclusi metadati, configurazioni e condizioni. Per altre informazioni, vedere [comprendere e usare dispositivi gemelli nell'hub](../iot-hub/iot-hub-devguide-device-twins.md). I compilatori di dispositivi e soluzioni possono continuare a usare lo stesso set di API e SDK per dispositivi gemelli per implementare dispositivi e soluzioni usando le convenzioni Plug and Play.

Le API gemelle digitali operano su costrutti di alto livello nel linguaggio DTDL (Digital Gemini Definition Language), ad esempio componenti, proprietà e comandi. Le API dei dispositivi gemelli digitali semplificano la creazione di soluzioni Plug and Play per i generatori di soluzioni.

In un dispositivo gemello, lo stato di una proprietà scrivibile viene suddiviso nelle sezioni desiderate e segnalate. Tutte le proprietà di sola lettura sono disponibili all'interno della sezione segnalata.

In un dispositivo gemello digitale è presente una visualizzazione unificata dello stato corrente e desiderato della proprietà. Lo stato di sincronizzazione per una determinata proprietà viene archiviato nella sezione componente predefinito corrispondente `$metadata` .

### <a name="digital-twin-json-format"></a>Formato JSON digitale gemello

Quando viene rappresentato come oggetto JSON, un dispositivo gemello digitale include i campi seguenti:

| Nome campo | Descrizione |
| --- | --- |
| `$dtId` | Stringa fornita dall'utente che rappresenta l'ID del dispositivo gemello digitale |
| `{propertyName}` | Valore di una proprietà in JSON |
| `$metadata.$model` | Opzionale ID dell'interfaccia del modello che caratterizza il gemello digitale |
| `$metadata.{propertyName}.desiredValue` | [Solo per le proprietà scrivibili] Valore desiderato della proprietà specificata. |
| `$metadata.{propertyName}.desiredVersion` | [Solo per le proprietà scrivibili] Versione del valore desiderato gestito dall'hub Internet|
| `$metadata.{propertyName}.ackVersion` | [Obbligatorio, solo per le proprietà scrivibili] La versione riconosciuta dal dispositivo che implementa il gemello digitale, deve essere maggiore o uguale alla versione desiderata |
| `$metadata.{propertyName}.ackCode` | [Obbligatorio, solo per le proprietà scrivibili] `ack` Codice restituito dall'app per dispositivi che implementa il dispositivo gemello digitale |
| `$metadata.{propertyName}.ackDescription` | [Facoltativo, solo per le proprietà scrivibili] `ack` Descrizione restituita dall'app per dispositivo che implementa il gemello digitale |
| `$metadata.{propertyName}.lastUpdateTime` | L'hub Internet gestisce il timestamp dell'ultimo aggiornamento della proprietà da parte del dispositivo. I timestamp sono in formato UTC e codificati nel formato ISO8601 aaaa-MM-GGThh: MM: SS. mmmZ |
| `{componentName}` | Oggetto JSON che contiene i valori e i metadati della proprietà del componente. |
| `{componentName}.{propertyName}` | Valore della proprietà del componente in JSON |
| `{componentName}.$metadata` | Informazioni sui metadati per il componente. |

#### <a name="device-twin-sample"></a>Esempio di dispositivo gemello

Il frammento di codice seguente mostra un Plug and Play del dispositivo gemello formattato come oggetto JSON:

```json
{
    "deviceId": "sample-device",
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {
        "desired": {
            "thermostat1": {
                "__t": "c",
                "targetTemperature": 21.8
            },
            "$metadata": {...},
            "$version": 4
        },
        "reported": {
            "serialNumber": "alwinexlepaho8329",
            "thermostat1": {
                "maxTempSinceLastReboot": 25.3,
                "__t": "c",
                "targetTemperature": {
                    "value": 21.8,
                    "ac": 200,
                    "ad": "Successfully executed patch",
                }
            },
            "$metadata": {...},
            "$version": 11
        }
    }
}
```

#### <a name="digital-twin-sample"></a>Esempio di dispositivo gemello digitale

Il frammento di codice seguente mostra il gemello digitale formattato come oggetto JSON:

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 21.8,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 21.8,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="properties"></a>Proprietà

Le proprietà sono campi dati che rappresentano lo stato di un'entità (ad esempio le proprietà in molti linguaggi di programmazione orientati a oggetti).

#### <a name="read-only-property"></a>Proprietà di sola lettura

Schema:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

In questo esempio, `alwinexlepaho8329` è il valore corrente della proprietà di sola `serialNumber` lettura indicata dal dispositivo.
I frammenti di codice seguenti mostrano la rappresentazione JSON side-by-side della `serialNumber` proprietà:

:::row:::
   :::column span="":::
      **Dispositivo gemello**

```json
"properties": {
    "reported": {
        "serialNumber": "alwinexlepaho8329"
    }
}
```

   :::column-end:::
   :::column span="":::
      **Gemelli digitali**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Proprietà scrivibile

Si immagini che il dispositivo abbia anche la seguente proprietà scrivibile nel componente predefinito:

```json
{
    "@type": "Property",
    "name": "fanSpeed",
    "displayName": "Fan Speed",
    "writable": true,
    "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Dispositivo gemello**

```json
{
    "properties": {
        "desired": {
            "fanSpeed": 2.0,
        },
        "reported": {
            "fanSpeed": {
                "value": 3.0,
                "ac": 200,
                "av": 1,
                "ad": "Successfully executed patch version 1"
            }
        }
    },
}
```

   :::column-end:::
   :::column span="":::
      **Gemelli digitali**

```json
{
    "fanSpeed": 3.0,
    "$metadata": {
        "fanSpeed": {
            "desiredValue": 2.0,
            "desiredVersion": 2,
            "ackVersion": 1,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch version 1",
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

In questo esempio, `3.0` è il valore corrente della `fanSpeed` proprietà indicata dal dispositivo. `2.0` valore desiderato impostato dalla soluzione. Il valore desiderato e lo stato di sincronizzazione di una proprietà a livello di radice vengono impostati all'interno del livello radice `$metadata` per un gemello digitale. Quando il dispositivo torna online, può applicare questo aggiornamento e restituire il valore aggiornato.

### <a name="components"></a>Componenti

I componenti consentono di compilare l'interfaccia del modello come assembly di altre interfacce.
Prendere in considerazione l'interfaccia del [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) , definita come modello.
Questa interfaccia ora può essere incorporata come componente thermostat1 (e un altro componente thermostat2) quando si definisce il [modello di controller di temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

In un dispositivo gemello, un componente viene identificato dal `{ "__t": "c"}` marcatore. In un dispositivo gemello digitale, la presenza di `$metadata` contrassegna un componente.

In questo esempio `thermostat1` è un componente con due proprietà:

- `maxTempSinceLastReboot` è una proprietà di sola lettura.
- `targetTemperature` è una proprietà scrivibile che è stata sincronizzata correttamente dal dispositivo. Il valore desiderato e lo stato di sincronizzazione di queste proprietà si trovano nell'oggetto del componente `$metadata` .

I frammenti di codice seguenti mostrano la rappresentazione in formato JSON affiancato del `thermostat1` componente:

:::row:::
   :::column span="":::
      **Dispositivo gemello**

```json
"properties": {
    "desired": {
        "thermostat1": {
            "__t": "c",
            "targetTemperature": 21.8
        },
        "$metadata": {
        },
        "$version": 4
    },
    "reported": {
        "thermostat1": {
            "maxTempSinceLastReboot": 25.3,
            "__t": "c",
            "targetTemperature": {
                "value": 21.8,
                "ac": 200,
                "ad": "Successfully executed patch",
                "av": 4
            }
        },
        "$metadata": {
        },
        "$version": 11
    }
}
```

   :::column-end:::
   :::column span="":::
      **Gemelli digitali**

```json
"thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.8,
            "desiredVersion": 4,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        },
        "maxTempSinceLastReboot": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>API gemelle digitali

I dispositivi gemelli digitali di Azure sono forniti con **Get Digital Twin**, **Update Digital Twin**, **Invoke Component Command** e **Invoke Command** per la gestione dei dispositivi gemelli digitali. È possibile usare le [API REST](/rest/api/iothub/service/digitaltwin) direttamente o tramite un [SDK del servizio](../iot-pnp/libraries-sdks.md).

## <a name="digital-twin-change-events"></a>Eventi di modifica del gemello digitale

Quando sono abilitati gli eventi di modifica del gemello digitale, viene attivato un evento ogni volta che viene modificato il valore attuale o desiderato del componente o della proprietà. Gli eventi di modifica di dispositivi gemelli digitali vengono generati nel formato di [patch JSON](http://jsonpatch.com/) . Gli eventi corrispondenti vengono generati nel formato dispositivo gemello se sono abilitati gli eventi di modifica gemelli.

Per informazioni su come abilitare il routing per gli eventi di dispositivi e dispositivi gemelli digitali, vedere [usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a diversi endpoint](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Per informazioni sul formato del messaggio, vedere [creare e leggere i messaggi dell'hub](../iot-hub/iot-hub-devguide-messages-construct.md)Internet.

Ad esempio, l'evento di modifica del gemello digitale seguente viene attivato quando `targetTemperature` viene impostato dalla soluzione:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

Quando il dispositivo segnala che è stata applicata la modifica desiderata precedente, viene attivato l'evento di modifica del dispositivo digitale gemello seguente:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> I messaggi di notifica delle modifiche gemelle vengono raddoppiati quando sono attivati nella notifica di modifica di dispositivi e dispositivi gemelli digitali.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese informazioni sui dispositivi gemelli digitali, di seguito sono riportate alcune risorse aggiuntive:

- [Come usare le API per i dispositivi gemelli Plug and Play digitali](howto-manage-digital-twin.md)
- [Interagire con un dispositivo dalla soluzione](quickstart-service-node.md)
- [API REST Digital Twin](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)