---
title: Clausola ORDER BY in Azure Cosmos DB
description: Informazioni sulla clausola SQL ORDER BY per Azure Cosmos DB. Usa SQL come un linguaggio di query JSON di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342456"
---
# <a name="order-by-clause"></a>Clausola ORDER BY

La clausola ORDER BY facoltativa specifica l'ordinamento dei risultati restituiti dalla query.

## <a name="syntax"></a>Sintassi
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argomenti
  
- `<sort_specification>`  
  
   Specifica una proprietà o espressione in cui ordinare il set di risultati della query. Una colonna di ordinamento può essere specificata come un alias del nome o una proprietà.  
  
   È possibile specificare più proprietà. I nomi delle proprietà devono essere univoci. La sequenza delle proprietà di ordinamento nella clausola ORDER BY definisce l'organizzazione del set di risultati ordinato. Ovvero, il set di risultati viene ordinato in base alla prima proprietà e quindi l'elenco così ordinato viene ordinato in base alla seconda proprietà e così via.  
  
   I nomi delle proprietà a cui fa riferimento la clausola ORDER BY devono corrispondere a una proprietà nell'elenco di selezione o a una proprietà definita nella raccolta specificata nella clausola FROM senza ambiguità.  
  
- `<sort_expression>`  
  
   Specifica uno o più proprietà o le espressioni in cui ordinare il set di risultati di query.  
  
- `<scalar_expression>`  
  
   Vedere la sezione [Espressioni scalari](sql-query-scalar-expressions.md) per informazioni dettagliate.  
  
- `ASC | DESC`  
  
   Specifica che i valori presenti nella colonna specificata devono essere ordinati in ordine crescente o decrescente. ASC (crescente) esegue l'ordinamento dal valore più basso a quello più alto. DESC (decrescente) esegue l'ordinamento dal valore più alto a quello più basso. L'ordinamento predefinito è ASC (crescente). I valori Null vengono considerati come i valori più bassi possibile.  
  
## <a name="remarks"></a>Note  
  
   La clausola ORDER BY richiede che i criteri di indicizzazione includano un indice per i campi da ordinare. Il runtime di query di Azure Cosmos DB supporta l'ordinamento con un nome di proprietà e non in base a proprietà calcolate. Azure Cosmos DB supporta più proprietà ORDER BY. Per eseguire una query con più proprietà ORDER BY, è necessario definire un [indice composto](index-policy.md#composite-indexes) nei campi da ordinare.

## <a name="examples"></a>Esempi

Ad esempio, ecco una query che recupera le famiglie in ordine crescente di nome della città di residenza:

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

La query seguente recupera famiglia `id`s nell'ordine della loro data di creazione di elementi. Elemento `creationDate` è un numero che rappresenta il *come valore epoch time*, o tempo trascorso dopo il 1 gennaio 1970 in secondi.

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

Inoltre, è possibile ordinare dal più proprietà. Una query che Ordina dal più proprietà richiede un [indice composto](index-policy.md#composite-indexes). Considerare la query seguente:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Questa query recupera la famiglia `id` in ordine crescente del nome della città. Se più elementi hanno lo stesso nome di città, la query verrà ordinare in base il `creationDate` in ordine decrescente.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Clausola SELECT](sql-query-select.md)
- [Clausola OFFSET limite](sql-query-offset-limit.md)
