---
title: Convenzioni Plug and Play Microsoft Docs
description: Descrizione delle convenzioni Plug and Play prevede che i dispositivi usino quando inviano i dati di telemetria e le proprietà e gestiscono i comandi e gli aggiornamenti delle proprietà.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 126673391b49f884a51521d462060c425a314667
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475669"
---
# <a name="iot-plug-and-play-conventions"></a>Convenzioni di Plug and Play IoT

I dispositivi Plug and Play anteprima dovrebbero seguire un set di convenzioni quando scambiano messaggi con un hub Internet. I dispositivi Plug and Play anteprima usano il protocollo MQTT per comunicare con l'hub Internet.

Si descrivono i dati di telemetria, le proprietà e i comandi implementati da un dispositivo Plug and Play dispositivo con un _modello_ [DTDL (Digital gemell Definition Language v2)](https://github.com/Azure/opendigitaltwins-dtdl) . Sono disponibili due tipi di modello a cui si fa riferimento in questo articolo:

- **Nessun componente** , ovvero un modello senza componenti. Il modello dichiara i dati di telemetria, le proprietà e i comandi come proprietà di primo livello nella sezione Contents dell'interfaccia principale.
- **Più componenti** : un modello composto da due o più interfacce. Interfaccia principale con i dati di telemetria, le proprietà e i comandi. Una o più interfacce dichiarate come componenti con dati di telemetria, proprietà e comandi aggiuntivi.

Per ulteriori informazioni, vedere la pagina relativa ai [componenti plug and Play nei modelli](concepts-components.md).

## <a name="identify-the-model"></a>Identificare il modello

Per annunciare il modello implementato, un Plug and Play dispositivo include l'ID del modello nel pacchetto di connessione MQTT aggiungendo `model-id` al `USERNAME` campo.

Per identificare il modello implementato da un dispositivo, un servizio può ottenere l'ID modello da:

- Il campo del dispositivo gemello `modelId` .
- Il campo del gemello digitale `$metadata.$model` .
- Notifica di modifica del dispositivo gemello digitale.

## <a name="telemetry"></a>Telemetria

I dati di telemetria inviati da un dispositivo senza componenti non richiedono metadati aggiuntivi. Il sistema aggiunge la `dt-dataschema` Proprietà.

I dati di telemetria inviati da un dispositivo a più componenti devono `$.sub` essere aggiunti come proprietà del messaggio. Il sistema aggiunge le `dt-subject` `dt-dataschema` proprietà e.

## <a name="read-only-properties"></a>Proprietà di sola lettura

### <a name="sample-no-component-read-only-property"></a>Esempio: nessuna proprietà di sola lettura del componente

Un dispositivo può inviare qualsiasi JSON valido che segue le regole DTDL V2.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Esempio di payload**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Proprietà di sola lettura di più componenti di esempio

Il dispositivo deve aggiungere il `{"__t": "c"}` marcatore per indicare che l'elemento fa riferimento a un componente.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Proprietà segnalata**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Proprietà scrivibili

Il dispositivo deve confermare la ricezione della proprietà inviando una proprietà segnalata. La proprietà segnalata deve includere:

- `value`: valore ricevuto dal dispositivo.
- `ac`: codice di riconoscimento che usa un codice di stato HTTP.
- `av`: versione di riconoscimento che fa riferimento all'oggetto `$version` della proprietà desiderata.
- `ad`-Descrizione facoltativa di riconoscimento.

### <a name="sample-no-component-writable-property"></a>Esempio di proprietà non scrivibile di componenti

Un dispositivo può inviare qualsiasi JSON valido che segue le regole DTDL V2:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Proprietà desiderata**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Proprietà segnalata**

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
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Proprietà di esempio di più componenti scrivibili

Il dispositivo deve aggiungere il `{"__t": "c"}` marcatore per indicare che l'elemento fa riferimento a un componente.

Il marcatore viene inviato solo per gli aggiornamenti a livello di componente, quindi i dispositivi non devono verificare la presenza di questo flag.

Il dispositivo deve confermare la ricezione della proprietà inviando una proprietà segnalata:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Proprietà desiderata**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Proprietà segnalata**

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
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Comandi

Nessuna interfaccia di componente usa il nome del comando senza prefisso.

In un dispositivo, più interfacce componente utilizzano i nomi di comando con il formato seguente: `componentName*commandName` .

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le informazioni sulle convenzioni Plug and Play, Ecco alcune risorse aggiuntive:

- [Digital Gemini Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK per dispositivi per C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST](https://docs.microsoft.com/rest/api/iothub/device)
- [Componenti del modello](./concepts-components.md)
