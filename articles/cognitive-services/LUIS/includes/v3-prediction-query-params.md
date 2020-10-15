---
title: Parametri della stringa di query dell'API V3
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 14380dd781fc1eebfe7edb0a816ff8af0f2f17b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91309430"
---
I parametri della stringa di query dell'API V3 includono:

|Query parameter (Parametro di query)|Nome del portale LUIS|Type|Versione|Predefinito|Scopo|
|--|--|--|--|--|--|
|`log`|Salva log|boolean|V2 & V3|false|Archivia query nel file di log. Il valore predefinito è false.|
|`query`|-|string|Solo V3|Nessun valore predefinito: è obbligatorio nella richiesta GET|**Nella versione V2**, l'espressione da stimare si trova nel `q` parametro. <br><br>**Nella V3**la funzionalità viene passata nel `query` parametro.|
|`show-all-intents`|Includi punteggi per tutti gli Intent|boolean|Solo V3|false|Restituisce tutti gli Intent con il punteggio corrispondente nell'oggetto **PREDICTION. Intent** . Gli Intent vengono restituiti come oggetti in un `intents` oggetto padre. Questo consente l'accesso a livello di codice senza la necessità di trovare l'intento in una matrice: `prediction.intents.give` . Nella versione V2 questi sono stati restituiti in una matrice. |
|`verbose`|Includi ulteriori dettagli sulle entità|boolean|V2 & V3|false|**Nella versione V2**, quando è impostato su true, vengono restituiti tutti gli Intent stimati. Se sono necessari tutti gli intenti previsti, usare il parametro V3 di `show-all-intents` .<br><br>**In V3**, questo parametro fornisce solo i dettagli relativi ai metadati dell'entità della stima delle entità.  |
|`timezoneOffset`|-|string|V2|-|Fuso orario applicato alle entità datetimeV2.|
|`datetimeReference`|-|string|V3|-|[Fuso orario](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) applicato alle entità datetimeV2. Sostituisce `timezoneOffset` dalla versione V2.|