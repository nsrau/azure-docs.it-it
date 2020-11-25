---
title: Funzioni di aggregazione in Azure Cosmos DB
description: Informazioni sulla sintassi della funzione di aggregazione SQL, sui tipi di funzioni di aggregazione supportate da Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: tisande
ms.openlocfilehash: f04590e78b5f1ea9d5e00c9f3d42c2fc32bebc5f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96001780"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funzioni di aggregazione in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Le funzioni di aggregazione eseguono un calcolo su un set di valori nella `SELECT` clausola e restituiscono un singolo valore. Ad esempio, la query seguente restituisce il numero di elementi all'interno del `Families` contenitore:

## <a name="examples"></a>Esempio

Quando `COUNT()` si usa, è possibile usare qualsiasi espressione scalare valida, ad esempio `1` , come input.

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

È inoltre possibile restituire solo il valore scalare dell'aggregazione utilizzando la parola chiave VALUE. Ad esempio, la query seguente restituisce il numero di valori come un singolo numero:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

I risultati sono:

```json
    [ 2 ]
```

È inoltre possibile combinare le aggregazioni con i filtri. Ad esempio, la query seguente restituisce il numero di elementi con lo stato dell'indirizzo di `WA` .

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

L'API SQL supporta le funzioni di aggregazione seguenti. `SUM` e `AVG` operano su valori numerici, e `COUNT` , e `MIN` `MAX` funzionano su numeri, stringhe, valori booleani e valori null.

| Funzione | Descrizione |
|-------|-------------|
| COUNT | Restituisce il numero di elementi nell'espressione. |
| SUM   | Restituisce la somma dei valori nell'espressione. |
| MIN   | Restituisce il valore minimo nell'espressione. |
| MAX   | Restituisce il valore massimo dell'espressione. |
| MEDIA   | Restituisce la media dei valori nell'espressione. |

È anche possibile aggregare i risultati di un'iterazione di matrice.

> [!NOTE]
> Nella Esplora dati del portale di Azure le query di aggregazione possono aggregare risultati parziali su una sola pagina di query. L'SDK genera un singolo valore cumulativo in tutte le pagine. Per eseguire query di aggregazione usando il codice, è necessario .NET SDK 1.12.0, .NET Core SDK 1.1.0 o Java SDK 1.9.5 o versione successiva.

## <a name="remarks"></a>Commenti

Queste funzioni di sistema di aggregazione trarranno vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy). Se si prevede di eseguire una `COUNT` ,,, `SUM` `MIN` `MAX` o `AVG` su una proprietà, è necessario [includere il percorso pertinente nei criteri di indicizzazione](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni di sistema](sql-query-system-functions.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)