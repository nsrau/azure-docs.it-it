---
title: Funzioni definite dall'utente (UDF) in Azure Cosmos DBUser-defined functions (UDFs) in Azure Cosmos DB
description: Informazioni sulle funzioni definite dall'utente in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011124"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funzioni definite dall'utente (UDF) in Azure Cosmos DBUser-defined functions (UDFs) in Azure Cosmos DB

L'API SQL fornisce il supporto per le funzioni definite dall'utente (UDF). Con le funzioni definite dall'utente scalari, è possibile passare zero o molti argomenti e restituire un singolo risultato dell'argomento. L'API controlla ogni argomento per essere valori JSON legali.  

## <a name="udf-use-cases"></a>Casi d'uso UDF

L'API estende la sintassi SQL per supportare la logica dell'applicazione personalizzata tramite funzioni definite dall'utente. È possibile registrare funzioni definite dall'utente con l'API SQL e farvi riferimento nelle query SQL. A differenza delle stored procedure e dei trigger, le funzioni definite dall'utente sono di sola lettura.

Usando le funzioni definite dall'utente, è possibile estendere il linguaggio di query di Azure Cosmos DB. Le funzioni definite dall'utente sono un ottimo modo per esprimere logica di business complessa nella proiezione di una query.

Tuttavia, si consiglia di evitare le funzioni definite dall'utente quando:However, we recommending avoiding UDFs when:

- Una [funzione](sql-query-system-functions.md) di sistema equivalente esiste già in Azure Cosmos DB. Le funzioni di sistema utilizzeranno sempre meno RU rispetto alla funzione definita dall'utente equivalente.
- La funzione definita dall'utente `WHERE` è l'unico filtro nella clausola della query. UDF non utilizzano l'indice, pertanto la valutazione della Funzione definita dall'utente richiederà il caricamento dei documenti. La combinazione di predicati di filtro aggiuntivi che utilizzano `WHERE` l'indice, in combinazione con una funzione definita dall'utente, nella clausola ridurrà il numero di documenti elaborati dalla funzione definita dall'utente.

Se è necessario utilizzare più volte la stessa funzione definita dall'utente in una query, è necessario fare riferimento alla fDU in una [sottoquery,](sql-query-subquery.md#evaluate-once-and-reference-many-times)consentendo di utilizzare un'espressione JOIN per valutare la funzione definita dall'utente una sola volta, ma farvi riferimento più volte.

## <a name="examples"></a>Esempi

Nell'esempio seguente viene registrato un UDF in un contenitore di elementi nel database Cosmos. Nell'esempio viene creata una `REGEX_MATCH`funzione definita dall'utente il cui nome è . Accetta due valori stringa `input` JSON `pattern`e , e controlla se il primo corrisponde al `string.match()` modello specificato nel secondo utilizzando la funzione di JavaScript.

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

A questo punto, utilizzare questa funzione definita dall'utente in una proiezione di query. È necessario qualificare le funzioni `udf.` definite dall'utente con il prefisso con distinzione tra maiuscole e minuscole quando vengono chiamate dall'interno delle query.

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

È possibile utilizzare la funzione `udf.` definita dall'utente qualificata con il prefisso all'interno di un filtro, come nell'esempio seguente:You can use the UDF qualified with the prefix inside a filter, as in the following example:

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

In sostanza, le funzioni definite dall'utente sono espressioni scalari valide che è possibile utilizzare sia nelle proiezioni che nei filtri.

Per espandere la potenza delle funzioni definite dall'utente, esaminare un altro esempio con logica condizionale:To expand on the power of UDFs, look at another example with conditional logic:

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

The following example exercises the UDF:

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

Se le proprietà a cui fanno riferimento i parametri UDF non sono disponibili nel valore JSON, il parametro viene considerato non definito e la chiamata UDF viene ignorata. Analogamente, se il risultato della funzione definita dall'utente non è definito, non viene incluso nel risultato.

Come illustrato negli esempi precedenti, le funzioni definite dall'utente integrano la potenza del linguaggio JavaScript con l'API SQL. Le funzioni definite dall'utente forniscono un'interfaccia programmabile avanzata per eseguire complesse procedure e logica condizionale con l'aiuto delle funzionalità di runtime JavaScript incorporate. L'API SQL fornisce gli argomenti alle funzioni definite dall'utente per ogni elemento di origine nella fase corrente della clausola WHERE o SELECT dell'elaborazione. Il risultato è incorporato senza soluzione di continuità nella pipeline di esecuzione complessiva. In sintesi, le funzioni definite dall'utente sono ottimi strumenti per eseguire una logica di business complessa come parte delle query.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni di sistema](sql-query-system-functions.md)
- [Aggregazioni](sql-query-aggregates.md)