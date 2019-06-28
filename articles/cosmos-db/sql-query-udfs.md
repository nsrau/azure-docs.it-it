---
title: Funzioni definite dall'utente (UDF) in Azure Cosmos DB
description: Informazioni sulle funzioni definite dall'utente in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342834"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funzioni definite dall'utente (UDF) in Azure Cosmos DB

L'API SQL offre il supporto per le funzioni definite dall'utente (UDF). Con funzioni scalari definite dall'utente, è possibile passare zero o più argomenti e restituiscono un risultato singolo argomento. L'API verifica ogni argomento di essere valore JSON legale.  

L'API estende la sintassi SQL per il supporto per la logica dell'applicazione personalizzata tramite funzioni definite dall'utente. È possibile registrare funzioni definite dall'utente con l'API SQL e farvi riferimento nella query SQL. In effetti, le UDF sono progettate espressamente per essere chiamate dalle query. A corollario, funzioni definite dall'utente non ha accesso all'oggetto di contesto, come altri tipi di JavaScript, ad esempio stored procedure e trigger. Le query sono di sola lettura e possono eseguire in repliche primarie o secondarie. Funzioni definite dall'utente, a differenza di altri tipi di JavaScript, progettate per l'esecuzione nelle repliche secondarie.

Nell'esempio seguente registra una funzione definita dall'utente in un contenitore di elementi nel database di Cosmos DB. Nell'esempio viene creata una funzione definita dall'utente il cui nome è `REGEX_MATCH`. Accetta due valori di stringa JSON, `input` e `pattern`, e controlla se i primi corrisponde al modello specificato nel secondo utilizzo di JavaScript `string.match()` (funzione).

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

A questo punto, è possibile usare questa UDF in una proiezione di query. Funzioni definite dall'utente è necessario qualificare con il prefisso distinzione maiuscole/minuscole `udf.` durante la chiamata dall'interno delle query.

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

È possibile usare la funzione definita dall'utente qualificato con il `udf.` prefisso all'interno di un filtro, come nell'esempio seguente:

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

In pratica, le UDF sono espressioni scalari valide che è possibile usare in entrambe le proiezioni e filtri.

Per ampliare la potenza delle UDF, esaminiamo un altro esempio con la logica condizionale:

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

Nell'esempio seguente usa la funzione definita dall'utente:

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

Se la proprietà definita per la funzione definita dall'utente i parametri non sono disponibili nel valore JSON, il parametro verrà considerato come undefined e la chiamata di funzione definita dall'utente è stata ignorata. Analogamente, se il risultato della UDF è undefined, non è incluso nel risultato.

Come mostrano negli esempi precedenti, le UDF integrano la potenza del linguaggio JavaScript con l'API SQL. Funzioni definite dall'utente forniscono un'interfaccia programmabile avanzata per eseguire operazioni complessa logica condizionale e procedurale con l'aiuto del runtime JavaScript integrate. L'API SQL fornisce gli argomenti alle UDF per ogni elemento di origine nel server di corrente WHERE o clausola SELECT fase dell'elaborazione. Il risultato è perfettamente incorporato nella pipeline di esecuzione complessivo. In breve, le UDF sono un ottimo strumento per eseguire la logica di business complessa come parte delle query.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni di sistema](sql-query-system-functions.md)
- [funzioni di aggregazione](sql-query-aggregates.md)