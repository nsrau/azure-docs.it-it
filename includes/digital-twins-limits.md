---
author: baanders
description: file di inclusione per i limiti dei dispositivi digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 2ea607b22bfa1eebdf6b63adcd14a5d1bb1ca9d0
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304099"
---
### <a name="functional-limits"></a>Limiti funzionali

La tabella seguente elenca i limiti funzionali dei dispositivi gemelli digitali di Azure nell'anteprima corrente.

| Area | Funzionalità | Limite predefinito | Regolabile? |
| --- | --- | --- | --- |
| Risorsa di Azure | Numero di istanze di dispositivi gemelli digitali di Azure in un'area, per sottoscrizione | 10 | Sì |
| Gemelli digitali | Numero di gemelli in un'istanza di dispositivi gemelli digitali di Azure | 200.000 | Sì |
| Gemelli digitali | Numero di relazioni in ingresso per un singolo gemello | 5\.000 | No |
| Gemelli digitali | Numero di relazioni in uscita da un singolo gemello | 5\.000 | No |
| Routing. | Numero di endpoint per una singola istanza di Azure Digital Twins | 6 | No |
| Routing. | Numero di route per una singola istanza di Azure Digital Twins | 6 | Sì |
| Modelli | Numero di modelli all'interno di una singola istanza di Azure Digital Twins | 10,000 | Sì |
| Modelli | Numero di modelli che possono essere caricati in una singola chiamata API | 250 | No |
| Modelli | Numero di elementi restituiti in una singola pagina | 100 | No |
| Query | Numero di elementi restituiti in una singola pagina | 100 | No |
| Query | Numero di `AND`  /  `OR` espressioni in una query | 50 | Sì |
| Query | Numero di elementi di matrice in una `IN`  /  `NOT IN` clausola | 50 | Sì |
| Query | Numero di caratteri in una query | 8\.000 | Sì |
| Query | Numero di `JOINS` in una query | 5 | Sì |

### <a name="rate-limits"></a>Limiti di velocità

Questa tabella riflette i limiti di velocità delle diverse API.

| API | Funzionalità | Limite predefinito | Regolabile? |
| --- | --- | --- | --- |
| API modelli | Numero di richieste al secondo | 100 | Sì |
| API gemelli digitali | Numero di richieste al secondo | 1\.000 | Sì |
| API di query | Numero di richieste al secondo | 500 | Sì |
| API di query | [Unità di query](../articles/digital-twins/concepts-query-units.md) al secondo | 4.000 | Sì |
| API route di eventi | Numero di richieste al secondo | 100 | Sì |

### <a name="other-limits"></a>Altri limiti

I limiti per i tipi di dati e i campi nei documenti di DTDL per i modelli di dispositivi gemelli digitali di Azure sono disponibili nella relativa documentazione specifica in GitHub: [*Digital Gemini Definition Language (DTDL)-versione 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Per informazioni dettagliate sulla latenza delle query e altre linee guida sulla scrittura di query durante [*l'anteprima, vedere Procedura: eseguire una query sul grafico gemello*](../articles/digital-twins/how-to-query-graph.md).