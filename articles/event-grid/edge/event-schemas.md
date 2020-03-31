---
title: Schemi di eventi - Azure Event Grid IoT Edge Documenti Microsoft
description: Event schemas in Event Grid on IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242459"
---
# <a name="event-schemas"></a>Schemi degli eventi

Il modulo Griglia di eventi accetta e recapita eventi in formato JSON. Attualmente sono supportati tre schemi da Griglia di eventi: -

* **EventGridSchema (SchemaDi Anni)**
* **Schema Personalizzato**
* **CloudEventSchema**

È possibile configurare lo schema a cui un autore deve essere conforme durante la creazione dell'argomento. Se non specificato, il valore predefinito è **EventGridSchema**. Gli eventi non conformi allo schema previsto verranno rifiutati.

I sottoscrittori possono anche configurare lo schema in cui desiderano recapitare gli eventi. Se non specificato, l'impostazione predefinita è lo schema dell'argomento.
Attualmente lo schema di distribuzione del sottoscrittore deve corrispondere allo schema di input dell'argomento. 

## <a name="eventgrid-schema"></a>Schema EventGrid

Lo schema EventGrid è costituito da un set di proprietà obbligatorie a cui un'entità di pubblicazione deve essere conforme. Ogni server di pubblicazione deve popolare i campi di primo livello.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>Proprietà dello schema EventGrid

Tutti gli eventi contano i seguenti dati di primo livello:

| Proprietà | Type | Obbligatoria | Descrizione |
| -------- | ---- | ----------- |-----------
| argomento | string | No | Deve corrispondere all'argomento in cui viene pubblicato. Griglia di eventi lo popola con il nome dell'argomento in cui viene pubblicato se non specificato. |
| subject | string | Sì | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Sì | Event type for this event source, for example, BlobCreated. |
| eventTime | string | Sì | Ora di generazione dell'evento in base all'ora UTC del provider. |
| ID | string | No | Identificatore univoco dell'evento. |
| data | object | No | Utilizzato per acquisire dati di evento specifici dell'entità di pubblicazione. |
| dataVersion | string | Sì | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | No | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

### <a name="example--eventgrid-schema-event"></a>Esempio: evento dello schema EventGrid

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>Schema CustomEvent

Nello schema personalizzato non sono presenti proprietà obbligatorie che vengono applicate come lo schema EventGrid.In custom schema, there are no mandatory properties that are enforced like the EventGrid schema. L'entità di pubblicazione può controllare completamente lo schema degli eventi. Fornisce la massima flessibilità e consente scenari in cui si dispone di un sistema basato su eventi già in atto e si desidera riutilizzare gli eventi esistenti e/o non si desidera essere legati a uno schema specifico.

### <a name="custom-schema-properties"></a>Proprietà dello schema personalizzate

Nessuna proprietà obbligatoria. Spetta all'entità di pubblicazione determinare il payload.

### <a name="example--custom-schema-event"></a>Esempio: evento dello schema personalizzatoExample - Custom Schema Event

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>Schema CloudEvent

Oltre agli schemi precedenti, Griglia di eventi supporta in modo nativo gli eventi nello [schema JSON CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents è una specifica aperta per la descrizione dei dati degli eventi. Semplifica l'interoperabilità fornendo uno schema di eventi comune per la pubblicazione e l'utilizzo di eventi. Fa parte di [CNCF](https://www.cncf.io/) e attualmente disponibile versione è 1.0-rc1.

### <a name="cloudevent-schema-properties"></a>Proprietà dello schema CloudEvent

Fare riferimento alla [specifica CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) sulle proprietà obbligatorie della busta.

### <a name="example--cloud-event"></a>Esempio: evento cloud
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
