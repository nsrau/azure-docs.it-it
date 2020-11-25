---
author: baanders
description: file di inclusione per le opzioni del filtro di route di Azure Digital gemelli
ms.service: digital-twins
ms.topic: include
ms.date: 11/18/2020
ms.author: baanders
ms.openlocfilehash: 261c5fa47cddcc527e7c0a18fbd18aad9320ed4b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018963"
---
| Nome filtro | Descrizione | Filtra schema testo | Valori supportati | 
| --- | --- | --- | --- |
| True/false | Consente la creazione di una route senza filtro o la disabilitazione di una route in modo che non venga inviato alcun evento | `<true/false>` | `true` = Route abilitata senza filtro <br> `false` = la route è disabilitata |
| Tipo | [Tipo di eventi](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) che passano attraverso l'istanza di dispositivi gemelli digitali | `type = '<eventType>'` | Ecco i possibili valori dei tipi di evento: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Source (Sorgente) | Nome dell'istanza di Azure Digital Twins | `source = '<hostname>'`| Ecco i possibili valori di hostname: <br> **Per le notifiche**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **Per la telemetria**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Subject | Descrizione dell'evento nel contesto dell'origine evento precedente | `subject = '<subject>'` | Ecco i possibili valori oggetto: <br>**Per le notifiche**: l'oggetto è `<twinid>` <br> o un formato URI per gli oggetti, che sono identificati in modo univoco da più parti o ID:<br>`<twinid>/relationships/<relationshipid>`<br> **Per la telemetria**: l'oggetto è il percorso del componente (se i dati di telemetria vengono emessi da un componente gemello), ad esempio `comp1.comp2` . Se i dati di telemetria non vengono emessi da un componente, il relativo campo soggetto è vuoto. |
| Schema dei dati | ID modello DTDL | `dataschema = '<model-dtmi-ID>'` | **Per la telemetria**: lo schema di dati è l'ID modello del dispositivo gemello o il componente che genera i dati di telemetria. Ad esempio: `dtmi:example:com:floor4;2` <br>**Per le notifiche**: è possibile accedere allo schema dei dati nel corpo della notifica in `$body.$metadata.$model`|
| Tipo di contenuto | Tipo di contenuto del valore dati | `datacontenttype = '<contentType>'` | Il tipo di contenuto è `application/json` |
| Versione spec | Versione dello schema di eventi in uso | `specversion = '<version>'` | La versione deve essere `1.0` . Indica la versione dello schema CloudEvents 1,0 |
| Corpo della notifica | Fare riferimento a qualsiasi proprietà nel `data` campo di una notifica | `$body.<property>` | Vedere [*procedura: comprendere i dati degli eventi*](../articles/digital-twins/how-to-interpret-event-data.md) per esempi di notifiche. `data`È possibile fare riferimento a qualsiasi proprietà nel campo usando`$body`

Si noti che è possibile aggiungere più filtri a una richiesta simile alla seguente: 

```json  
{
    "endpointName": "dt-endpoint", 
    "filter": "true", 
    "filter": "source = 'ADT-resource.api.wus2.digitaltwins.azure.net/myFloorID'", 
    "filter": "type = 'Microsoft.DigitalTwins.Twin.Delete'", 
    "filter": "specversion = '1.0'"
}
```

I tipi di dati seguenti sono supportati come valori restituiti dai riferimenti ai dati precedenti:

| Tipo di dati | Esempio |
|-|-|-|
|**Stringa**| `STARTS_WITH($body.$metadate.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Integer**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

Quando si definiscono i filtri di route, sono supportati gli operatori seguenti:

|Famiglia|Operatori|Esempio|
|-|-|-|
|Logico|AND, OR, ()|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Confronto|<, <=, >, >=, =,! =|`$body.temperature <= 5.5`

Quando si definiscono i filtri di route, sono supportate le funzioni seguenti:

|Funzione|Descrizione|Esempio|
|--|--|--|
|STARTS_WITH (x, y)|Restituisce true se il valore `x` inizia con la stringa `y` .|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x, y) | Restituisce true se il valore `x` termina con la stringa `y` .|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| Restituisce true se il valore `x` contiene la stringa `y` .|`CONTAINS(subject, '<twinID>')`|

Quando si implementa o si aggiorna un filtro, la modifica potrebbe richiedere alcuni minuti per essere riflessa nella pipeline di dati.