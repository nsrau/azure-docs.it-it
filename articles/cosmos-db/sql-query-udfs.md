---
title: Funzioni definite dall'utente (UDF) in Azure Cosmos DBUser-defined functions (UDFs) in Azure Cosmos DB
description: Informazioni sulle funzioni definite dall'utente in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614326"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funzioni definite dall'utente (UDF) in Azure Cosmos DBUser-defined functions (UDFs) in Azure Cosmos DB

L'API SQL fornisce il supporto per le funzioni definite dall'utente (UDF). Con le funzioni definite dall'utente scalari, è possibile passare zero o molti argomenti e restituire un singolo risultato dell'argomento. L'API controlla ogni argomento per essere valori JSON legali.  

L'API estende la sintassi SQL per supportare la logica dell'applicazione personalizzata tramite funzioni definite dall'utente. È possibile registrare funzioni definite dall'utente con l'API SQL e farvi riferimento nelle query SQL. In effetti, le UDF sono progettate espressamente per essere chiamate dalle query. Come corollario, le funzioni definite dall'utente non hanno accesso all'oggetto di contesto come altri tipi JavaScript, ad esempio stored procedure e trigger. Le query sono di sola lettura e possono essere eseguite in repliche primarie o secondarie. Le funzioni definite dall'utente, a differenza di altri tipi JavaScript, sono progettate per essere eseguite su repliche secondarie.

Nell'esempio seguente viene registrato un UDF in un contenitore di elementi nel database Cosmos. Nell'esempio viene creata una `REGEX_MATCH`funzione definita dall'utente il cui nome è . Accetta due valori stringa `input` JSON `pattern`e , e controlla se il primo corrisponde al `string.match()` modello specificato nel secondo utilizzando la funzione di JavaScript.

## <a name="examples"></a>Esempi

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