---
title: Clausola ORDER BY in Azure Cosmos DB
description: Informazioni sulla clausola SQL ORDER BY per Azure Cosmos DB. Usare SQL come linguaggio di query JSON del database cosmos di Azure.Use SQL as an Azure Cosmos DB JSON query language.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188739"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Clausola ORDER BY in Azure Cosmos DB

La clausola facoltativa ORDER BY specifica l'ordinamento per i risultati restituiti dalla query.

## <a name="syntax"></a>Sintassi
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argomenti
  
- `<sort_specification>`  
  
   Specifica una proprietà o espressione in cui ordinare il set di risultati della query. Una colonna di ordinamento può essere specificata come nome o alias di proprietà.  
  
   È possibile specificare più proprietà. I nomi delle proprietà devono essere univoci. La sequenza delle proprietà di ordinamento nella clausola ORDER BY definisce l'organizzazione del set di risultati ordinato. Ovvero, il set di risultati viene ordinato in base alla prima proprietà e quindi l'elenco così ordinato viene ordinato in base alla seconda proprietà e così via.  
  
   I nomi di proprietà a cui viene fatto riferimento nella clausola ORDER BY devono corrispondere a una proprietà nell'elenco di selezione o a una proprietà definita nella raccolta specificata nella clausola FROM senza ambiguità.  
  
- `<sort_expression>`  
  
   Specifica una o più proprietà o espressioni in base alle quali ordinare il set di risultati della query.  
  
- `<scalar_expression>`  
  
   Vedere la sezione [Espressioni scalari](sql-query-scalar-expressions.md) per informazioni dettagliate.  
  
- `ASC | DESC`  
  
   Specifica che i valori nella colonna specificata devono essere ordinati in ordine crescente o decrescente. ASC consente di ordinare i valori dal più piccolo al più grande. DESC consente di ordinare i valori dal più grande al più piccolo. ASC è l'ordinamento predefinito. I valori Null vengono considerati i valori in assoluto più piccoli.  
  
## <a name="remarks"></a>Osservazioni  
  
   La `ORDER BY` clausola richiede che i criteri di indicizzazione includano un indice per i campi da ordinare. Il runtime di query Cosmos DB di Azure supporta l'ordinamento in base a un nome di proprietà e non con le proprietà calcolate. Azure Cosmos DB `ORDER BY` supporta più proprietà. Per eseguire una query con più proprietà ORDER BY, è necessario definire un [indice composito](index-policy.md#composite-indexes) sui campi da ordinare.

> [!Note]
> Se le proprietà da ordinare potrebbero non essere definite per alcuni documenti e si desidera recuperarle in una query ORDER BY, è necessario includere in modo esplicito questo percorso nell'indice. I criteri di indicizzazione predefiniti non consentono il recupero dei documenti in cui la proprietà di ordinamento non è definita. [Esaminare query di esempio su documenti con alcuni campi mancanti.](#documents-with-missing-fields)

## <a name="examples"></a>Esempi

Ad esempio, ecco una query che recupera le famiglie in ordine crescente del nome della città residente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

I risultati sono:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

La query seguente `id`recupera gli s della famiglia in ordine di data di creazione dell'elemento. Item `creationDate` è un numero che rappresenta il tempo di *epoca*o il tempo trascorso dal 1 gennaio 1970 in secondi.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

I risultati sono:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Inoltre, è possibile ordinare in base a più proprietà. Una query che ordina in base a più proprietà richiede un [indice composito.](index-policy.md#composite-indexes) Si consideri la query seguente:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Questa query recupera `id` la famiglia in ordine crescente del nome della città. Se più elementi hanno lo stesso nome di `creationDate` città, la query ordinerà in ordine decrescente.

## <a name="documents-with-missing-fields"></a>Documenti con campi mancanti

Le `ORDER BY` query con che vengono eseguite su contenitori con i criteri di indicizzazione predefiniti non restituiranno documenti in cui la proprietà di ordinamento non è definita. Se si desidera includere i documenti in cui la proprietà sort non è definita, è necessario includere in modo esplicito questa proprietà nei criteri di indicizzazione.

Ad esempio, ecco un contenitore con un criterio di indicizzazione `"/*"`che non include in modo esplicito alcun percorso oltre a:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se si esegue una `lastName` query `Order By` che include nella clausola, `lastName` i risultati includeranno solo i documenti per i quali è definita una proprietà. Non è stato definito un `lastName` percorso incluso `lastName` esplicito per in modo che tutti i documenti senza a non verranno visualizzati nei risultati della query.

Di seguito è riportata una query che esegue l'ordinamento in base a due documenti, uno dei quali non dispone di un:Here `lastName` `lastName` is a query that sorts by on two documents, one of which does a defined:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

I risultati includono solo il `lastName`documento che ha un:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Se si aggiornano i criteri di indicizzazione del `lastName`contenitore per includere in modo esplicito un percorso per , nei risultati della query verranno inclusi documenti con una proprietà di ordinamento non definita. È necessario definire in modo esplicito il percorso per portare a questo valore scalare (e non oltre). È necessario `?` utilizzare il carattere nella definizione del percorso nei `lastName` criteri di indicizzazione per assicurarsi di indicizzare in modo esplicito la proprietà e nessun percorso annidato aggiuntivo oltre.

Di seguito è riportato un criterio di indicizzazione `lastName` di esempio che consente di visualizzare documenti con un elemento non definito nei risultati della query:Here is a sample indexing policy which allows you to have documents with an undefined appear in the query results:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se si esegue di nuovo la `lastName` stessa query, i documenti mancanti vengono visualizzati per primi nei risultati della query:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

I risultati sono:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Se si modifica l'ordinamento `DESC`in `lastName` , i documenti mancanti vengono visualizzati per ultimi nei risultati della query:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

I risultati sono:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Indexing policies in Azure Cosmos DB](index-policy.md) (Criteri di indicizzazione in Azure Cosmos DB)
- [Clausola OFFSET LIMIT](sql-query-offset-limit.md)
