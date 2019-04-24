---
title: Join nelle query di log di Monitoraggio di Azure | Microsoft Docs
description: Questo articolo include una lezione sull'utilizzo dei join nelle query di log di Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 2ea5b4e3af6591e6e25a863998baa7cecb3e29e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60520099"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Join nelle query di log di Monitoraggio di Azure

> [!NOTE]
> È consigliabile completare [Introduzione a Azure Monitor Log Analitica](get-started-portal.md) e [query di log di monitoraggio di Azure](get-started-queries.md) prima di completare questa lezione.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I join consentono di analizzare nella stessa query i dati provenienti da più tabelle. Uniscono le righe di due set di dati associando i valori di determinate colonne.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

In questo esempio viene creato un join tra un primo set di dati, che filtra tutti gli eventi di accesso, e un secondo set di dati, che filtra tutti gli eventi di disconnessione. Le colonne previste sono _Computer_, _Account_, _TargetLogonId_ e _TimeGenerated_. I set di dati siano correlati in base a una colonna condivisa, _TargetLogonId_. L'output è costituito da un singolo record per correlazione, che include sia l'ora di accesso sia l'ora di disconnessione.

Se i due set di dati avessero colonne con gli stessi nomi, a quelle del set di dati a destra verrebbe assegnato un numero di indice. Pertanto, in questo esempio i risultati mostrerebbero _TargetLogonId_ con i valori dalla tabella di sinistra e  _TargetLogonId1_ con i valori dalla tabella di destra. In questo caso la seconda colonna _TargetLogonId1_ è stata rimossa tramite l'operatore `project-away`.

> [!NOTE]
> Per migliorare le prestazioni, mantenere solo le colonne rilevanti dei set di dati sottoposti a join usando l'operatore `project`.


Usare la sintassi seguente per creare un join tra due set di dati lasciando la chiave di join con un nome diverso tra le due tabelle:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Tabelle di ricerca
Un uso comune dei join consiste nel mapping statico dei valori tramite `datatable` che può essere utile per migliorare l'aspetto dei risultati. Ad esempio, per arricchire i dati degli eventi di sicurezza con il nome dell'evento per ogni ID evento.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Join con una datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Tipi di join
Specificare il tipo di join con l'argomento _kind_. Ogni tipo esegue una diversa corrispondenza tra i record di determinate tabelle, come descritto nella tabella seguente.

| Tipo di join | DESCRIZIONE |
|:---|:---|
| innerunique | Questa è la modalità di join predefinita. Prima vengono trovati i valori della colonna corrispondente nella tabella di sinistra e vengono rimossi i valori duplicati  e dopo il set di valori univoci viene confrontato con la tabella di destra. |
| interno | Nei risultati vengono inclusi solo i record corrispondenti in entrambe le tabelle. |
| leftouter | Nei risultati vengono inclusi tutti i record nella tabella di sinistra e i record corrispondenti nella tabella di destra. Le proprietà di output senza corrispondenza contengono valori null.  |
| leftanti | Nei risultati vengono inclusi i record nella tabella di sinistra che non hanno corrispondenze con quella di destra. La tabella dei risultati include solo le colonne della tabella di sinistra. |
| leftsemi | Nei risultati vengono inclusi i record nella tabella di sinistra che hanno corrispondenze con quella di destra. La tabella dei risultati include solo le colonne della tabella di sinistra. |


## <a name="best-practices"></a>Procedure consigliate

Per prestazioni ottimali, prendere in considerazione i punti seguenti:

- Usare un filtro temporale in ogni tabella per ridurre i record che devono essere valutati per il join.
- Usare `where` e `project` per ridurre i numeri di righe e colonne nelle tabelle di input prima del join.
- Se una tabella è sempre più piccola dell'altra, usarla come lato sinistro del join.


## <a name="next-steps"></a>Passaggi successivi
Vedere altre lezioni per l'utilizzo di query di log di Monitoraggio di Azure:

- [Operazioni con stringhe](string-operations.md)
- [Funzioni di aggregazione](aggregations.md)
- [Aggregazioni avanzate](advanced-aggregations.md)
- [Strutture dei dati e JSON](json-data-structures.md)
- [Scrittura di query avanzate](advanced-query-writing.md)
- [Grafici](charts.md)