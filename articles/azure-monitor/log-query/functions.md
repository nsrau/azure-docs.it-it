---
title: Funzioni nelle query di log di Monitoraggio di Azure | Microsoft Docs
description: Questo articolo descrive come usare le funzioni per chiamare una query da un'altra query di log in Monitoraggio di Azure.
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
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 7eb5f090026b415e3c980116a9317594f4ca8115
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750770"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Uso delle funzioni nelle query di log di Monitoraggio di Azure

> [!NOTE]
> Prima di seguire questa lezione, è consigliabile completare [Introduzione al portale di Analytics](get-started-portal.md) e [Introduzione alle query](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Per usare una query di log con un'altra query è possibile salvarla come funzione. Questa possibililità consente di semplificare query complesse suddividendole in più parti e di riusare codice comune con più query.

## <a name="create-a-function"></a>Creare una funzione

Creare una funzione con Log Analitica nel portale di Azure facendo clic **salvare** quindi fornendo le informazioni nella tabella seguente.

| Impostazione | DESCRIZIONE |
|:---|:---|
| NOME           | Nome visualizzato della query in **Esplora Query**. |
| Salva con nome        | Funzione |
| Alias della funzione | Nome breve per usare la funzione in altre query. Non può contenere spazi e deve essere univoco. |
| Categoria       | Una categoria per organizzare le funzioni e le query salvate in **Esplora Query**. |

> [!NOTE]
> Una funzione in Monitoraggio di Azure non può contenere un'altra funzione.

> [!NOTE]
> Il salvataggio di una funzione è possibile per le query di log di Monitoraggio di Azure, ma attualmente non per le query di Application Insights.



## <a name="use-a-function"></a>Usare una funzione
Per usare una funzione, includere il relativo alias in un'altra query. Può essere usata come qualsiasi altra tabella.

## <a name="example"></a>Esempio
La query di esempio seguente restituisce tutti gli aggiornamenti della sicurezza mancanti segnalati nell'ultimo giorno. Salvare questa query come funzione con l'alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Creare un'altra query e fare riferimento alla funzione _security_updates_last_day_ per cercare gli aggiornamenti della sicurezza necessari correlati a SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Passaggi successivi
Vedere altre lezioni per la scrittura di query di log di Monitoraggio di Azure:

- [Operazioni con stringhe](string-operations.md)
- [Operazioni con data e ora](datetime-operations.md)
- [Funzioni di aggregazione](aggregations.md)
- [Aggregazioni avanzate](advanced-aggregations.md)
- [Strutture dei dati e JSON](json-data-structures.md)
- [Join](joins.md)
- [Grafici](charts.md)
