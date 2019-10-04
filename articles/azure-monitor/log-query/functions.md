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
ms.openlocfilehash: 75beb7b66863efd2fb3679f034a3663dca4a6d2f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076709"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Uso delle funzioni nelle query di log di Monitoraggio di Azure

Per usare una query di log con un'altra query è possibile salvarla come funzione. Questa possibililità consente di semplificare query complesse suddividendole in più parti e di riusare codice comune con più query.

## <a name="create-a-function"></a>Crea una funzione

Per creare una funzione con Log Analytics nel portale di Azure, fare clic su **Salva** e quindi fornire le informazioni riportate nella tabella seguente.

| Impostazione | DESCRIZIONE |
|:---|:---|
| Name           | Nome visualizzato della query in **Esplora Query**. |
| Salva con nome        | Funzione |
| Alias della funzione | Nome breve per usare la funzione in altre query. Non può contenere spazi e deve essere univoco. |
| Category       | Una categoria per organizzare le funzioni e le query salvate in **Esplora Query**. |

> [!NOTE]
> Una funzione in Monitoraggio di Azure non può contenere un'altra funzione.




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
