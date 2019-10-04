---
title: Funzioni di aggregazione in Azure Cosmos DB
description: Informazioni sulla sintassi della funzione di aggregazione SQL per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342878"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funzioni di aggregazione in Azure Cosmos DB

Le funzioni di aggregazione eseguono un calcolo su un set di valori nella clausola SELECT e restituiscono un valore singolo. Ad esempio, la query seguente restituisce il conteggio degli elementi all'interno di `Families` contenitore:

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

È anche possibile combinare le aggregazioni con i filtri. Ad esempio, la query seguente restituisce il numero di elementi con lo stato di indirizzo della `WA`.

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

L'API SQL supporta le seguenti funzioni di aggregazione. SUM e AVG SQRT a valori numerici, e COUNT, MIN e MAX funzionano in numeri, stringhe, valori booleani e valori null.

| Funzione | Descrizione |
|-------|-------------|
| COUNT | Restituisce il numero di elementi nell'espressione. |
| SUM   | Restituisce la somma dei valori nell'espressione. |
| MIN   | Restituisce il valore minimo nell'espressione. |
| MAX   | Restituisce il valore massimo nell'espressione. |
| MEDIA   | Restituisce la media dei valori nell'espressione. |

È anche possibile aggregare i risultati di un'iterazione della matrice.

> [!NOTE]
> In Esplora dati del portale di Azure, le query di aggregazione possono aggregare risultati parziali pagina solo una query. il SDK produce un singolo valore cumulativo in tutte le pagine. Per eseguire query di aggregazione tramite codice, è necessario .NET SDK 1.12.0, .NET Core SDK 1.1.0 o SDK per Java 1.9.5 o versione successiva.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni di sistema](sql-query-system-functions.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)