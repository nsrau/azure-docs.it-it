---
title: Payload della telemetria, della proprietà e del comando in Azure IoT Central | Microsoft Docs
description: I modelli di dispositivo IoT Central di Azure consentono di specificare i dati di telemetria, le proprietà e i comandi di un dispositivo devono implementare. Comprendere il formato dei dati che un dispositivo può scambiare con IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 9e5288bb177d5827f05003e4561bc79240a71b59
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427863"
---
# <a name="telemetry-property-and-command-payloads"></a>Payload di telemetria, proprietà e comandi

_Questo articolo si applica agli sviluppatori di dispositivi._

Un modello di dispositivo in IoT Central di Azure è un progetto che definisce:

* Telemetria inviato da un dispositivo a IoT Central.
* Proprietà sincronizzate da un dispositivo con IoT Central.
* Comandi che IoT Central chiama su un dispositivo.

Questo articolo descrive, per gli sviluppatori di dispositivi, i payload JSON che i dispositivi inviano e ricevono per la telemetria, le proprietà e i comandi definiti in un modello di dispositivo.

L'articolo non descrive tutti i possibili tipi di dati di telemetria, proprietà e payload dei comandi, ma gli esempi illustrano tutti i tipi di chiave.

Ogni esempio mostra un frammento di codice del modello di funzionalità del dispositivo (DCM) che definisce il tipo e i payload JSON di esempio per illustrare il modo in cui il dispositivo deve interagire con l'applicazione IoT Central.

> [!NOTE]
> IoT Central accetta qualsiasi JSON valido, ma può essere usato solo per le visualizzazioni Se corrisponde a una definizione in DCM. È possibile esportare i dati che non corrispondono a una definizione, vedere [esportare i dati delle cose in destinazioni in Azure](howto-export-data.md).

Il file JSON che definisce il DCM usa il [linguaggio DTDL (Digital Twin Definition Language) V1](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/dtdlv1.md). Questa specifica include la definizione del `@id` formato della proprietà.

Per il codice del dispositivo di esempio che mostra alcuni payload in uso, vedere l'applicazione [creare e connettere un'applicazione client all'applicazione azure IOT Central (Node.js)](tutorial-connect-device-nodejs.md) e [creare e connettere un'applicazione client alle esercitazioni di Azure IOT Central Application (Python)](tutorial-connect-device-python.md) .

## <a name="view-raw-data"></a>Visualizzare dati non elaborati

IoT Central consente di visualizzare i dati non elaborati inviati da un dispositivo a un'applicazione. Questa vista è utile per la risoluzione dei problemi relativi al payload inviato da un dispositivo. Per visualizzare i dati non elaborati inviati da un dispositivo:

1. Passare al dispositivo dalla pagina **dispositivi** .

1. Selezionare la scheda **dati non elaborati** :

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Visualizzazione Dati non elaborati":::

    In questa visualizzazione è possibile selezionare le colonne da visualizzare e impostare un intervallo di tempo da visualizzare. La colonna **Dati senza modello** contiene i dati del dispositivo che non corrispondono ad alcuna definizione di proprietà o di telemetria nel modello di dispositivo.

## <a name="telemetry"></a>Telemetria

### <a name="primitive-types"></a>Tipi primitivi

Questa sezione illustra alcuni esempi di tipi di dati di telemetria primitivi che un dispositivo trasmette a un'applicazione IoT Central.

Il frammento di codice seguente da un DCM Mostra la definizione di un `boolean` tipo di telemetria:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come illustrato nell'esempio seguente:

```json
{ "BooleanTelemetry": true }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `string` tipo di telemetria:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come illustrato nell'esempio seguente:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `integer` tipo di telemetria:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come illustrato nell'esempio seguente:

```json
{ "IntegerTelemetry": 23 }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `double` tipo di telemetria:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come illustrato nell'esempio seguente:

```json
{ "DoubleTelemetry": 56.78 }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `dateTime` tipo di telemetria:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come illustrato nell'esempio seguente `DateTime` : i tipi devono essere conformi allo standard ISO 8061:

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `duration` tipo di telemetria:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come nell'esempio seguente: le durate devono essere conformi alla durata ISO 8601:

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Tipi complessi

Questa sezione illustra alcuni esempi di tipi di dati di telemetria complessi che un dispositivo trasmette a un'applicazione IoT Central.

Il frammento di codice seguente da un DCM Mostra la definizione di un `geopoint` tipo di telemetria:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come nell'esempio seguente. IoT Central Visualizza il valore come pin su una mappa:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `Enum` tipo di telemetria:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come nell'esempio seguente. I valori possibili sono `0` , `1` e `2` che vengono visualizzati in IOT Central come `Item1` , `Item2` e `Item3` :

```json
{ "EnumTelemetry": 1 }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `Object` tipo di telemetria. Questo oggetto include tre campi con i tipi `dateTime` , `integer` , e `Enum` :

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@id": "<element id>",
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come nell'esempio seguente. `DateTime` i tipi devono essere conformi allo standard ISO 8061. I valori possibili per `Property3` sono `0` , e sono `1` visualizzati in IOT Central come `Item1` , `Item2` e `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `vector` tipo di telemetria:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

Un client del dispositivo deve inviare i dati di telemetria come JSON, come illustrato nell'esempio seguente:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Tipi di evento e stato

Questa sezione illustra alcuni esempi di eventi di telemetria e indica che un dispositivo invia a un'applicazione IoT Central.

Il frammento di codice seguente di un DCM Mostra la definizione di un `integer` tipo di evento:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

Un client dispositivo deve inviare i dati dell'evento come JSON simile all'esempio seguente:

```json
{ "IntegerEvent": 74 }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `integer` tipo di stato:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

Un client del dispositivo deve inviare lo stato come JSON simile all'esempio seguente. I valori di stato Integer possibili sono `1` , `2` o `3` :

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Proprietà

> [!NOTE]
> I formati di payload per le proprietà si applicano alle applicazioni create in o dopo il 07/14/2020.

### <a name="primitive-types"></a>Tipi primitivi

Questa sezione illustra alcuni esempi di tipi di proprietà primitivi inviati da un dispositivo a un'applicazione IoT Central.

Il frammento di codice seguente da un DCM Mostra la definizione di un `boolean` tipo di proprietà:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean"
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello:

```json
{ "BooleanProperty": false }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `boolean` tipo di proprietà:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long"
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello:

```json
{ "LongProperty": 439 }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `date` tipo di proprietà:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date"
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello. `Date` i tipi devono essere conformi allo standard ISO 8061:

```json
{ "DateProperty": "2020-05-17" }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `duration` tipo di proprietà:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration"
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello: le durate devono essere conformi alla durata ISO 8601:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `float` tipo di proprietà:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float"
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello:

```json
{ "FloatProperty": 1.9 }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `string` tipo di proprietà:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string"
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello:

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Tipi complessi

Questa sezione illustra alcuni esempi di tipi di proprietà complessi inviati da un dispositivo a un'applicazione IoT Central.

Il frammento di codice seguente da un DCM Mostra la definizione di un `geopoint` tipo di proprietà:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint"
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello:

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `Enum` tipo di proprietà:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello. I valori possibili sono `0` , `1` e che vengono visualizzati in IOT Central come `Item1` , `Item2` e `Item3` :

```json
{ "EnumProperty": 1 }
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `Object` tipo di proprietà. Questo oggetto dispone di due campi con tipi `string` e `integer` :

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello:

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

Il frammento di codice seguente da un DCM Mostra la definizione di un `vector` tipo di proprietà:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector"
}
```

Un client del dispositivo deve inviare un payload JSON simile all'esempio seguente come proprietà segnalata nel dispositivo gemello:

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>Tipi di proprietà scrivibile

Questa sezione illustra alcuni esempi di tipi di proprietà scrivibile che un dispositivo riceve da un'applicazione IoT Central.

IoT Central prevede una risposta dal dispositivo agli aggiornamenti delle proprietà scrivibili. Il messaggio di risposta deve includere `ac` i `av` campi e. Il campo `ad` è facoltativo. Per esempi, vedere i frammenti di codice seguenti.

`ac` è un campo numerico che usa i valori riportati nella tabella seguente:

| Valore | Etichetta | Descrizione |
| ----- | ----- | ----------- |
| `'ac': 200` | Completi | L'operazione di modifica della proprietà è stata completata correttamente. |
| `'ac': 202`  o `'ac': 201` | Pending | L'operazione di modifica della proprietà è in sospeso o in corso |
| `'ac': 4xx` | Errore | La modifica della proprietà richiesta non è valida o si è verificato un errore |
| `'ac': 5xx` | Errore | Si è verificato un errore imprevisto nel dispositivo durante l'elaborazione della modifica richiesta. |

`av` numero di versione inviato al dispositivo.

`ad` Descrizione della stringa di opzioni.

Il frammento di codice seguente di un DCM Mostra la definizione di un `string` tipo di proprietà scrivibile:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

Il dispositivo riceve il payload seguente da IoT Central:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

Il dispositivo deve inviare il payload JSON seguente per IoT Central dopo l'elaborazione dell'aggiornamento. Questo messaggio include il numero di versione dell'aggiornamento originale ricevuto da IoT Central. Quando IoT Central riceve questo messaggio, la proprietà viene contrassegnata come **sincronizzata** nell'interfaccia utente:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

Il frammento di codice seguente di un DCM Mostra la definizione di un `Enum` tipo di proprietà scrivibile:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Il dispositivo riceve il payload seguente da IoT Central:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

Il dispositivo deve inviare il payload JSON seguente per IoT Central dopo l'elaborazione dell'aggiornamento. Questo messaggio include il numero di versione dell'aggiornamento originale ricevuto da IoT Central. Quando IoT Central riceve questo messaggio, la proprietà viene contrassegnata come **sincronizzata** nell'interfaccia utente:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Comandi:

### <a name="synchronous-command-types"></a>Tipi di comando sincroni

Il frammento di codice seguente da un DCM Mostra la definizione di un comando sincrono che non ha parametri e che non prevede che il dispositivo restituisca nulla:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "displayName": {
    "en": "SynchronousCommandBasic"
  },
  "name": "SynchronousCommandBasic"
}
```

Il dispositivo riceve un payload vuoto nella richiesta e deve restituire un payload vuoto nella risposta con un `200` codice di risposta http per indicare l'esito positivo.

Il frammento di codice seguente da un DCM Mostra la definizione di un comando sincrono con un parametro integer e che prevede che il dispositivo restituisca un valore integer:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "SynchronousCommandSimple"
  },
  "name": "SynchronousCommandSimple"
}
```

Il dispositivo riceve un valore integer come payload della richiesta. Il dispositivo deve restituire un valore integer come payload della risposta con un `200` codice di risposta http per indicare l'esito positivo.

Il frammento di codice seguente da un DCM Mostra la definizione di un comando sincrono con un parametro dell'oggetto e che prevede che il dispositivo restituisca un oggetto. In questo esempio, entrambi gli oggetti hanno campi di tipo integer e String:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "SynchronousCommandComplex"
  },
  "name": "SynchronousCommandComplex"
}
```

Il frammento di codice seguente mostra un payload della richiesta di esempio inviato al dispositivo:

```json
{ "Field1": 56, "Field2": "A string value" }
```

Il frammento di codice seguente mostra un payload di risposta di esempio inviato dal dispositivo. Usare un `200` codice di risposta http per indicare l'esito positivo:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="asynchronous-command-types"></a>Tipi di comando asincroni

Il frammento di codice seguente da un DCM Mostra la definizione di un comando asincrono. Il comando ha un parametro integer e prevede che il dispositivo restituisca un valore integer:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "asynchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "AsynchronousCommandSimple"
  },
  "name": "AsynchronousCommandSimple"
}
```

Il dispositivo riceve un valore integer come payload della richiesta. Il dispositivo deve restituire un payload di risposta vuoto con un `202` codice di risposta http per indicare che il dispositivo ha accettato la richiesta di elaborazione asincrona.

Al termine dell'elaborazione della richiesta, il dispositivo deve inviare una proprietà a IoT Central simile all'esempio seguente. Il nome della proprietà deve essere uguale al nome del comando:

```json
{
  "AsynchronousCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

In qualità di sviluppatore di dispositivi, ora che si è appreso a conoscere i modelli di dispositivo, i passaggi successivi suggeriti sono la lettura della [connessione ad Azure IOT Central per ottenere](./concepts-get-connected.md) altre informazioni su come registrare i dispositivi con IOT Central e sul modo in cui IOT Central protegge le connessioni del dispositivo.
