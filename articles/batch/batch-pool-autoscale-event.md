---
title: Evento di ridimensionamento automatico del pool di Azure Batch
description: Riferimento per l'evento di ridimensionamento automatico del pool di batch, che viene generato dopo l'esecuzione del ridimensionamento automatico del pool. Il contenuto del log esporrà le formule di scalabilità automatica e i risultati della valutazione per il pool.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91852131"
---
# <a name="pool-autoscale-event"></a>Evento di ridimensionamento automatico del pool

 Questo evento viene generato dopo l'esecuzione del ridimensionamento automatico del pool. Il contenuto del log esporrà le formule di scalabilità automatica e i risultati della valutazione per il pool.

 Nell'esempio seguente viene illustrato il corpo di un evento di ridimensionamento automatico del pool per un pool che ha avuto esito negativo a causa di dati di esempio insufficienti.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|Elemento|Type|Note|
|-------------|----------|-----------|
|`id`|string|ID del pool.|
|`timestamp`|Datetime|Timestamp del momento in cui viene eseguita la scalabilità automatica.|
|`formula`|string|Formula definita per la scalabilità automatica.|
|`results`|string|Risultati della valutazione per tutte le variabili usate nella formula.|
|[`error`](#error)|Tipo complesso|Errore dettagliato per la scalabilità automatica.|

###  <a name="error"></a><a name="error"></a> errori

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`code`|string|Identificatore per l'errore di ridimensionamento automatico. I codici sono invariabili e sono progettati per essere usati a livello di codice.|
|`message`|string|Messaggio che descrive l'errore di ridimensionamento automatico, che deve essere adatto per la visualizzazione in un'interfaccia utente.|
|`values`|Array|Elenco di coppie nome-valore che descrivono ulteriori dettagli sull'errore di ridimensionamento automatico.|
