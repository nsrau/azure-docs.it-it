---
title: Funzioni definite dall'utente (UDF) in Azure Cosmos DB
description: Informazioni sulle funzioni definite dall'utente in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81011124"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funzioni definite dall'utente (UDF) in Azure Cosmos DB

L'API SQL fornisce il supporto per le funzioni definite dall'utente (UDF). Con le funzioni definite dall'utente scalari, è possibile passare zero o molti argomenti e restituire un singolo argomento. L'API verifica che ogni argomento sia un valore JSON valido.  

## <a name="udf-use-cases"></a>Casi d'uso UDF

L'API estende la sintassi SQL per supportare la logica dell'applicazione personalizzata usando le funzioni definite dall'utente. È possibile registrare le funzioni definite dall'utente con l'API SQL e farvi riferimento nelle query SQL. A differenza di stored procedure e trigger, le funzioni definite dall'utente sono di sola lettura.

Con le funzioni definite dall'utente è possibile estendere il linguaggio di query di Azure Cosmos DB. Le funzioni definite dall'utente sono un ottimo modo per esprimere la logica di business complessa nella proiezione di una query.

Tuttavia, è consigliabile evitare le UDF nei casi seguenti:

- Esiste già una [funzione di sistema](sql-query-system-functions.md) equivalente in Azure Cosmos DB. Le funzioni di sistema utilizzeranno sempre un minor numero di ur rispetto alla funzione definita dall'utente equivalente.
- La funzione definita dall'utente è l'unico `WHERE` filtro nella clausola della query. Le funzioni UDF non utilizzano l'indice, pertanto la valutazione della funzione definita dall'utente richiede il caricamento di documenti. La combinazione di predicati di filtro aggiuntivi che utilizzano l'indice, in combinazione con una `WHERE` funzione definita dall'utente, nella clausola ridurrà il numero di documenti elaborati dalla funzione definita dall'utente.

Se è necessario utilizzare la stessa funzione UDF più volte in una query, è necessario fare riferimento alla funzione definita dall'utente in una [sottoquery](sql-query-subquery.md#evaluate-once-and-reference-many-times), consentendo di utilizzare un'espressione di join per valutare la funzione definita dall'utente una volta, ma fare riferimento più volte.

## <a name="examples"></a>Esempi

Nell'esempio seguente viene registrata una funzione definita dall'utente in un contenitore di elementi nel database Cosmos. Nell'esempio viene creata una funzione definita dall' `REGEX_MATCH`utente il cui nome è. Accetta due valori stringa JSON, `input` e `pattern`e controlla se il primo corrisponde al modello specificato nel secondo usando la funzione di `string.match()` JavaScript.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

A questo punto, usare questa funzione definita dall'utente in una proiezione di query. È necessario qualificare le funzioni definite dall'utente con `udf.` il prefisso con distinzione tra maiuscole e minuscole quando vengono chiamate da query.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

I risultati sono:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

È possibile usare la funzione definita dall'utente `udf.` qualificata con il prefisso all'interno di un filtro, come nell'esempio seguente:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

I risultati sono:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

In sostanza, le funzioni definite dall'utente sono espressioni scalari valide che è possibile usare sia per le proiezioni che per i filtri.

Per ampliare le potenzialità delle funzioni definite dall'utente, vedere un altro esempio con la logica condizionale:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Nell'esempio seguente viene esercitata la funzione definita dall'utente:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

I risultati sono:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Se le proprietà a cui fanno riferimento i parametri UDF non sono disponibili nel valore JSON, il parametro viene considerato come non definito e la chiamata della funzione definita dall'utente viene ignorata. Analogamente, se il risultato della funzione definita dall'utente non è definito, non è incluso nel risultato.

Come illustrato negli esempi precedenti, le funzioni definite dall'utente integrano la potenza del linguaggio JavaScript con l'API SQL. Le funzioni definite dall'utente forniscono un'interfaccia programmabile avanzata per la logica condizionale e procedurale complessa con l'ausilio delle funzionalità di runtime JavaScript predefinite. L'API SQL fornisce gli argomenti alle UDF per ogni elemento di origine in corrispondenza della fase di elaborazione della clausola WHERE o SELECT corrente. Il risultato viene incorporato facilmente nella pipeline di esecuzione complessiva. In sintesi, le funzioni definite dall'utente sono ottimi strumenti per la logica di business complessa nell'ambito delle query.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni di sistema](sql-query-system-functions.md)
- [Aggregazioni](sql-query-aggregates.md)