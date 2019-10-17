---
title: Clausola ORDER BY in Azure Cosmos DB
description: Informazioni sulla clausola SQL ORDER BY per Azure Cosmos DB. Usare SQL come linguaggio di query JSON Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 14f61d14b59dca4bcf2e0f4b93e918f101a61833
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326844"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Clausola ORDER BY in Azure Cosmos DB

La clausola ORDER BY facoltativa specifica l'ordinamento dei risultati restituiti dalla query.

## <a name="syntax"></a>Sintassi
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argomenti
  
- `<sort_specification>`  
  
   Specifica una proprietà o espressione in cui ordinare il set di risultati della query. È possibile specificare una colonna di ordinamento come nome o alias di proprietà.  
  
   È possibile specificare più proprietà. I nomi delle proprietà devono essere univoci. La sequenza delle proprietà di ordinamento nella clausola ORDER BY definisce l'organizzazione del set di risultati ordinato. Ovvero, il set di risultati viene ordinato in base alla prima proprietà e quindi l'elenco così ordinato viene ordinato in base alla seconda proprietà e così via.  
  
   I nomi di proprietà a cui viene fatto riferimento nella clausola ORDER BY devono corrispondere a una proprietà nell'elenco di selezione o a una proprietà definita nella raccolta specificata nella clausola FROM senza ambiguità.  
  
- `<sort_expression>`  
  
   Specifica una o più proprietà o espressioni in cui ordinare il set di risultati della query.  
  
- `<scalar_expression>`  
  
   Vedere la sezione [Espressioni scalari](sql-query-scalar-expressions.md) per informazioni dettagliate.  
  
- `ASC | DESC`  
  
   Specifica che i valori presenti nella colonna specificata devono essere ordinati in ordine crescente o decrescente. ASC (crescente) esegue l'ordinamento dal valore più basso a quello più alto. DESC (decrescente) esegue l'ordinamento dal valore più alto a quello più basso. L'ordinamento predefinito è ASC (crescente). I valori Null vengono considerati come i valori più bassi possibile.  
  
## <a name="remarks"></a>Osservazioni  
  
   Per la clausola ORDER BY è necessario che i criteri di indicizzazione includano un indice per i campi da ordinare. Il runtime di query di Azure Cosmos DB supporta l'ordinamento in base a un nome di proprietà e non alle proprietà calcolate. Azure Cosmos DB supporta più proprietà ORDER BY. Per eseguire una query con più proprietà ORDER BY, è necessario definire un [indice composto](index-policy.md#composite-indexes) sui campi da ordinare.

## <a name="examples"></a>esempi

Ad esempio, ecco una query che recupera le famiglie in ordine crescente di nome della città residente:

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

La query seguente recupera la famiglia `id` in ordine di data di creazione dell'elemento. Item `creationDate` è un numero che rappresenta l' *ora del periodo*o il tempo trascorso da Jan. 1, 1970 in secondi.

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

È inoltre possibile ordinare in base a più proprietà. Una query che ordina in base a più proprietà richiede un [indice composto](index-policy.md#composite-indexes). Considerare la query seguente:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Questa query recupera la famiglia `id` in ordine crescente in base al nome della città. Se più elementi hanno lo stesso nome di città, la query viene ordinata in base al `creationDate` in ordine decrescente.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Clausola SELECT](sql-query-select.md)
- [Clausola limite OFFSET](sql-query-offset-limit.md)
