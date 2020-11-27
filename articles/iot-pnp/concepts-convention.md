---
title: Convenzioni Plug and Play Microsoft Docs
description: Descrizione delle convenzioni Plug and Play prevede che i dispositivi usino quando inviano i dati di telemetria e le proprietà e gestiscono i comandi e gli aggiornamenti delle proprietà.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 86c6ea9dded423e7bd513faf73adfd293f2bd38f
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302614"
---
# <a name="iot-plug-and-play-conventions"></a>Convenzioni di Plug and Play IoT

I dispositivi Plug and Play devono seguire un set di convenzioni quando scambiano messaggi con un hub Internet. I dispositivi Plug and Play usano il protocollo MQTT per comunicare con l'hub Internet.

I dispositivi possono includere [moduli](../iot-hub/iot-hub-devguide-module-twins.md)o essere implementati in un [Modulo IOT Edge](../iot-edge/about-iot-edge.md) ospitato dal runtime di IOT Edge.

Si descrivono i dati di telemetria, le proprietà e i comandi implementati da un dispositivo Plug and Play dispositivo con un _modello_ [DTDL (Digital gemell Definition Language v2)](https://github.com/Azure/opendigitaltwins-dtdl) . Sono disponibili due tipi di modello a cui si fa riferimento in questo articolo:

- **Nessun componente** , ovvero un modello senza componenti. Il modello dichiara i dati di telemetria, le proprietà e i comandi come proprietà di primo livello nella sezione Contents dell'interfaccia principale. Nello strumento Azure Internet Explorer, questo modello viene visualizzato come un singolo _componente predefinito_.
- **Più componenti** : un modello composto da due o più interfacce. Interfaccia principale, che viene visualizzata come _componente predefinito_, con i comandi di telemetria, le proprietà e. Una o più interfacce dichiarate come componenti con dati di telemetria, proprietà e comandi aggiuntivi.

Per ulteriori informazioni, vedere la pagina relativa ai [componenti plug and Play nei modelli](concepts-components.md).

## <a name="identify-the-model"></a>Identificare il modello

Per annunciare il modello implementato, un Plug and Play del dispositivo o del modulo include l'ID del modello nel pacchetto di connessione MQTT aggiungendo `model-id` al `USERNAME` campo.

Per identificare il modello implementato da un dispositivo o un modulo, un servizio può ottenere l'ID modello da:

- Il campo del dispositivo gemello `modelId` .
- Il campo del gemello digitale `$metadata.$model` .
- Notifica di modifica del dispositivo gemello digitale.

## <a name="telemetry"></a>Telemetria

I dati di telemetria inviati da un dispositivo senza componenti non richiedono metadati aggiuntivi. Il sistema aggiunge la `dt-dataschema` Proprietà.

I dati di telemetria inviati da un dispositivo a più componenti devono `$.sub` essere aggiunti come proprietà del messaggio. Il sistema aggiunge le `dt-subject` `dt-dataschema` proprietà e.

## <a name="read-only-properties"></a>Proprietà di sola lettura

### <a name="sample-no-component-read-only-property"></a>Esempio: nessuna proprietà di sola lettura del componente

Un dispositivo o un modulo può inviare qualsiasi JSON valido che segue le regole DTDL V2.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Payload della proprietà segnalato di esempio:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Proprietà di sola lettura di più componenti di esempio

Il dispositivo o il modulo deve aggiungere il `{"__t": "c"}` marcatore per indicare che l'elemento fa riferimento a un componente.

DTDL che fa riferimento a un componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL che definisce il componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Payload della proprietà segnalato di esempio:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Proprietà scrivibili

Il dispositivo o il modulo deve confermare la ricezione della proprietà inviando una proprietà segnalata. La proprietà segnalata deve includere:

- `value` : valore effettivo della proprietà (in genere il valore ricevuto, ma il dispositivo può decidere di segnalare un valore diverso).
- `ac` : codice di riconoscimento che usa un codice di stato HTTP.
- `av` : versione di riconoscimento che fa riferimento all'oggetto `$version` della proprietà desiderata. È possibile trovare questo valore nel payload JSON della proprietà desiderato.
- `ad` -Descrizione facoltativa di riconoscimento.

Quando un dispositivo viene avviato, deve richiedere il dispositivo gemello e verificare la presenza di eventuali aggiornamenti delle proprietà scrivibili. Se la versione di una proprietà scrivibile è aumentata mentre il dispositivo era offline, il dispositivo deve inviare una risposta di proprietà segnalata per confermare che l'aggiornamento è stato ricevuto.

Quando un dispositivo viene avviato per la prima volta, può inviare un valore iniziale per una proprietà segnalata se non riceve una proprietà iniziale desiderata dall'hub. In questo caso, il dispositivo deve `av` essere impostato su `1` . Esempio:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Un dispositivo può usare la proprietà segnalata per fornire altre informazioni all'hub. Ad esempio, il dispositivo potrebbe rispondere con una serie di messaggi in corso, ad esempio:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Quando il dispositivo raggiunge la temperatura di destinazione, invia il messaggio seguente:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Un dispositivo potrebbe segnalare un errore, ad esempio:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Esempio di proprietà non scrivibile di componenti

Quando un dispositivo riceve più proprietà segnalate in un singolo payload, può inviare le risposte alle proprietà segnalate tra più payload.

Un dispositivo o un modulo può inviare qualsiasi JSON valido che segue le regole DTDL V2:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Esempio di payload della proprietà desiderata:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Primo payload della proprietà segnalato di esempio:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Esempio di payload di proprietà segnalato secondo:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Proprietà di esempio di più componenti scrivibili

Il dispositivo o il modulo deve aggiungere il `{"__t": "c"}` marcatore per indicare che l'elemento fa riferimento a un componente.

Il marcatore viene inviato solo per gli aggiornamenti alle proprietà definite in un componente. Gli aggiornamenti alle proprietà definite nel componente predefinito non includono il marcatore, vedere la [proprietà di esempio nessuna componente scrivibile](#sample-no-component-writable-property)

Quando un dispositivo riceve più proprietà segnalate in un singolo payload, può inviare le risposte alle proprietà segnalate tra più payload.

Il dispositivo o il modulo deve confermare di aver ricevuto le proprietà inviando le proprietà segnalate:

DTDL che fa riferimento a un componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL che definisce il componente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Esempio di payload della proprietà desiderata:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Primo payload della proprietà segnalato di esempio:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Esempio di payload di proprietà segnalato secondo:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Comandi:

Nessuna interfaccia di componente usa il nome del comando senza prefisso.

In un dispositivo o un modulo, più interfacce componente utilizzano i nomi di comando con il formato seguente: `componentName*commandName` .

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le informazioni sulle convenzioni Plug and Play, Ecco alcune risorse aggiuntive:

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK per dispositivi per C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST](/rest/api/iothub/device)
- [Componenti del modello](./concepts-components.md)