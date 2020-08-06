---
author: baanders
description: file di inclusione per le opzioni del filtro di route di Azure Digital gemelli
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: f9db7c489bb1c64a1649bba74d08fef5f0ec97b8
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801484"
---
| Nome filtro | Descrizione | Filtra schema testo | Valori supportati | 
| --- | --- | --- | --- |
| Tipo | [Tipo di eventi](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) che passano attraverso l'istanza di dispositivi gemelli digitali | `type = '<eventType>'` | Ecco i possibili valori dei tipi di evento: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Origine | Nome dell'istanza di Azure Digital Twins | `source = '<hostname>'`| Ecco i possibili valori di hostname: <br> **Per le notifiche**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Per la telemetria**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Oggetto | Descrizione dell'evento nel contesto dell'origine evento precedente | `subject = '<subject>'` | Ecco i possibili valori oggetto: <br>**Per le notifiche**: l'oggetto è`<twinid>` <br> o un formato URI per gli oggetti, che sono identificati in modo univoco da più parti o ID:<br>`<twinid>/relationships/<relationshipid>`<br> **Per la telemetria**: l'oggetto è il percorso del componente (se i dati di telemetria vengono emessi da un componente gemello), ad esempio `comp1.comp2` . Se i dati di telemetria non vengono emessi da un componente, il relativo campo soggetto è vuoto. |
| Schema dei dati | ID modello DTDL | `dataschema = '<model-dtmi-ID>'` | **Per la telemetria**: lo schema di dati è l'ID modello del dispositivo gemello o il componente che genera i dati di telemetria. Ad esempio: `dtmi:example:com:floor4;2` <br>**Per le notifiche**: schema dati non supportato|
| Tipo di contenuto | Tipo di contenuto del valore dati | `datacontenttype = '<contentType>'` | Il tipo di contenuto è`application/json` |
| Versione spec | Versione dello schema di eventi in uso | `specversion = '<version>'` | La versione deve essere `1.0` . Indica la versione dello schema CloudEvents 1,0 |
| True/false | Consente la creazione di una route senza filtro o la disabilitazione di una route | `<true/false>` | `true`= Route abilitata senza filtro <br> `false`= la route è disabilitata |

È anche possibile combinare i filtri usando le operazioni seguenti:

| Nome filtro | Filtra schema testo | Esempio | 
| --- | --- | --- |
| E/O | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| E/O | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Operazioni annidate | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> Durante l'anteprima, è supportata solo l'uguaglianza di stringhe (=,! =).

Quando si implementa o si aggiorna un filtro, la modifica potrebbe richiedere alcuni minuti per essere riflessa nella pipeline di dati.
