---
title: Funzioni di aggregazione in Azure Cosmos DBAggregate functions in Azure Cosmos DB
description: Informazioni sulla sintassi delle funzioni di aggregazione SQL, sui tipi di funzioni di aggregazione supportate da Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464462"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funzioni di aggregazione in Azure Cosmos DBAggregate functions in Azure Cosmos DB

Le funzioni di aggregazione eseguono `SELECT` un calcolo su un set di valori nella clausola e restituiscono un singolo valore. Ad esempio, la query seguente restituisce `Families` il numero di elementi all'interno del contenitore:

## <a name="examples"></a>Esempi

```sql
    SELECT COUNT(1)
    FROM Families f
```

I risultati sono:

```json
    [{
        "$1": 2
    }]
```

È inoltre possibile restituire solo il valore scalare dell'aggregazione usando la parola chiave VALUE. Ad esempio, la query seguente restituisce il numero di valori come un singolo numero:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

I risultati sono:

```json
    [ 2 ]
```

È inoltre possibile combinare aggregazioni con filtri. Ad esempio, la query seguente restituisce il `WA`conteggio degli elementi con lo stato dell'indirizzo di .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

I risultati sono:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Tipi di funzioni di aggregazione

L'API SQL supporta le funzioni di aggregazione seguenti. `SUM`e `AVG` operano su `COUNT`valori `MIN`numerici e , e `MAX` funzionano su numeri, stringhe, valori booleani e valori Null.

| Funzione | Descrizione |
|-------|-------------|
| COUNT | Restituisce il numero di elementi nell'espressione. |
| SUM   | Restituisce la somma dei valori nell'espressione. |
| MIN   | Restituisce il valore minimo nell'espressione. |
| MAX   | Restituisce il valore massimo dell'espressione. |
| MEDIA   | Restituisce la media dei valori nell'espressione. |

È inoltre possibile eseguire l'aggregazione sui risultati di un'iterazione di matrice.

> [!NOTE]
> In Esplora dati del portale di Azure le query di aggregazione possono aggregare risultati parziali in una sola pagina di query. L'SDK produce un singolo valore cumulativo in tutte le pagine. Per eseguire query di aggregazione tramite codice, è necessario .NET SDK 1.12.0, .NET Core SDK 1.1.0 o Java SDK 1.9.5 o versione successiva.

## <a name="remarks"></a>Osservazioni

Queste funzioni di sistema di aggregazione trarranno vantaggio da un [indice di intervallo.](index-policy.md#includeexclude-strategy) Se si prevede `COUNT`di `SUM` `MIN`eseguire `MAX`un' operazione , , , o `AVG` su una proprietà, è necessario [includere il percorso pertinente nei criteri di indicizzazione.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni di sistema](sql-query-system-functions.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)