---
title: Query di ricerca nei log di Monitoraggio di Azure | Microsoft Docs
description: Questo articolo fornisce un'esercitazione per iniziare a usare il comando search nelle query di log di Monitoraggio di Azure.
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
ms.date: 08/06/2018
ms.author: bwren
ms.openlocfilehash: b118740f3a57e168c5dfb071c199bcf424bd5113
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295567"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Query di ricerca nei log di Monitoraggio di Azure
Le query di log di Monitoraggio di Azure possono iniziare con un nome di tabella o un comando search. Questa esercitazione illustra le query basate sul comando search. Ogni metodo offre vantaggi specifici.

Le query basate su tabella iniziano definendo l'ambito della query e quindi tendono a essere più efficienti rispetto alle query di ricerca. Le query di ricerca sono meno strutturate, quindi più appropriate per la ricerca di un valore specifico in tabelle o colonne. Il comando **search** consente di analizzare tutte le colonne in una determinata tabella o in tutte le tabelle, per cercare il valore specificato. La quantità di dati elaborati può essere estremamente elevata, quindi queste query possono richiedere più tempo per il completamento e possono restituire set di risultati molto grandi.

## <a name="search-a-term"></a>Cercare un termine
Il comando **search** viene in genere usato per cercare un termine specifico. Nell'esempio seguente vengono analizzate tutte le colonne di tutte le tabelle per cercare il termine "error":

```Kusto
search "error"
| take 100
```

Sebbene siano facili da usare, le query senza ambito come quella illustrata in precedenza non sono efficienti ed è probabile che restituiscano numerosi risultati non rilevanti. È preferibile eseguire la ricerca nella tabella pertinente o anche in una colonna specifica.

### <a name="table-scoping"></a>Impostazione di una tabella come ambito
Per cercare un termine di ricerca in una tabella specifica, aggiungere `in (table-name)` subito dopo l'operatore **search**:

```Kusto
search in (Event) "error"
| take 100
```

oppure in più tabelle:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Impostazione di una tabella e di una colonna come ambito
Per impostazione predefinita, il comando **search** valuta tutte le colonne nel set di dati. Per eseguire la ricerca solo in una colonna specifica, usare questa sintassi:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Se si usa `==` invece di `:`, i risultati includeranno i record in cui la colonna *Source* contiene l'esatto valore "error" con l'esatta combinazione di maiuscole e minuscole. Con ':' includerà i record in cui *origine* dispone di valori, ad esempio "codice di errore 404" o "Error".

## <a name="case-sensitivity"></a>Distinzione tra maiuscole e minuscole
Per impostazione predefinita, la ricerca di termini non fa distinzione tra maiuscole e minuscole, quindi una ricerca di "dns" potrebbe restituire risultati come "DNS", "dns" o "Dns". Per eseguire una ricerca con distinzione tra maiuscole e minuscole, usare l'opzione `kind`:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Usare i caratteri jolly
Il comando **search** supporta i caratteri jolly, all'inizio, alla fine o al centro di un termine.

Per cercare i termini che iniziano con "win":
```Kusto
search in (Event) "win*"
| take 100
```

Per cercare i termini che finiscono con ".com":
```Kusto
search in (Event) "*.com"
| take 100
```

Per cercare i termini che contengono "www":
```Kusto
search in (Event) "*www*"
| take 100
```

Per cercare i termini che iniziano con "corp" e finiscono con ".com", ad esempio "corp.mydomain.com":

```Kusto
search in (Event) "corp*.com"
| take 100
```

È anche possibile ottenere tutti gli elementi di una tabella usando solo un carattere jolly: `search in (Event) *`, ma ciò equivarrebbe a scrivere semplicemente `Event`.

> [!TIP]
> Sebbene sia possibile usare `search *` per ottenere tutte le colonne di ogni tabella, è consigliabile impostare sempre tabelle specifiche come ambito delle query. Le query senza ambito potrebbero richiedere tempo per il completamento e potrebbero restituire un numero eccessivo di risultati.

## <a name="add-and--or-to-search-queries"></a>Aggiungere *and*/*or* alle query di ricerca
Usare **and** per cercare i record che contengono più termini:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Usare **or** per ottenere i record che contengono almeno uno dei termini:

```Kusto
search in (Event) "error" or "register"
| take 100
```

In presenza di più condizioni di ricerca, è possibile combinarle nella stessa query usando le parentesi:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

I risultati di questo esempio sarebbero record che contengono il termine "error" e che contengono anche "register" o qualcosa che inizia con "marshal".

## <a name="pipe-search-queries"></a>Inviare query di ricerca con pipe
Esattamente come qualsiasi altro comando, **search** consente l'invio di pipe per filtrare, ordinare e aggregare i risultati della ricerca. Ad esempio, per ottenere il numero di record di *Event* che contengono "win":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Passaggi successivi

- Vedere altre esercitazioni nel [sito sul linguaggio di query Kusto](/azure/kusto/query/).
