---
title: Funzioni nelle query di log di Monitoraggio di Azure | Microsoft Docs
description: Questo articolo descrive come usare le funzioni per chiamare una query da un'altra query di log in Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 678a6f0dc19d966f3d15e713008c19c8fbb96f5e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024365"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Uso delle funzioni nelle query di log di Monitoraggio di Azure

Per usare una query di log con un'altra query è possibile salvarla come funzione. Questa possibililità consente di semplificare query complesse suddividendole in più parti e di riusare codice comune con più query.

## <a name="create-a-function"></a>Creare una funzione

Creare una funzione con Log Analytics nel portale di Azure facendo clic su **Salva** e quindi specificando le informazioni richieste nella tabella seguente.

| Impostazione | Descrizione |
|:---|:---|
| Nome           | Nome visualizzato della query in **Esplora Query**. |
| Salva con nome        | Funzione |
| Alias della funzione | Nome breve per usare la funzione in altre query. Non può contenere spazi e deve essere univoco. |
| Category       | Una categoria per organizzare le funzioni e le query salvate in **Esplora Query**. |




## <a name="use-a-function"></a>Usare una funzione
Per usare una funzione, includere il relativo alias in un'altra query. Può essere usata come qualsiasi altra tabella.

## <a name="function-parameters"></a>Parametri di funzione 
È possibile aggiungere parametri a una funzione in modo da poter fornire valori per determinate variabili quando viene chiamata. L'unico modo per creare una funzione con parametri è l'uso di un modello di Gestione risorse. Per un esempio, vedere [Gestione risorse esempi di modelli per le query di log in monitoraggio di Azure](../samples/resource-manager-log-queries.md#parameterized-function) .

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

- [Operazioni con stringhe](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)
- [Operazioni con data e ora](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#date-and-time-operations)
- [Funzioni di aggregazione](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)
- [Aggregazioni avanzate](/azure/data-explorer/write-queries#advanced-aggregations)
- [Strutture dei dati e JSON](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#json-and-data-structures)
- [Join](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)
- [Grafici](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#charts)
