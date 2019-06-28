---
title: Clausola OFFSET limite in Azure Cosmos DB
description: Informazioni sulla clausola OFFSET limite per Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342615"
---
# <a name="offset-limit-clause"></a>Clausola OFFSET limite

La clausola OFFSET LIMIT è una clausola facoltativa per ignorare quindi richiedere un numero di valori dalla query. Il numero OFFSET e il conteggio di limite sono necessari nella clausola OFFSET limite.

Quando il limite di OFFSET viene utilizzato in combinazione con una clausola ORDER BY, il set di risultati viene generato eseguendo skip e accettano i valori ordinati. Se non esiste una clausola ORDER BY viene utilizzata, si verificherà in un ordine deterministico dei valori.

## <a name="syntax"></a>Sintassi
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argomenti

- `<offset_amount>`

   Specifica il numero intero di elementi che devono ignorare i risultati della query.

- `<limit_amount>`
  
   Specifica il numero intero di elementi che deve includere i risultati della query

## <a name="remarks"></a>Note
  
  Nella clausola OFFSET limite sono necessari sia il numero OFFSET e il conteggio di limite. Se facoltativo `ORDER BY` viene utilizzata la clausola, il set di risultati viene prodotta eseguendo l'operazione FETCH sui valori ordinati. In caso contrario, la query restituirà un ordine fisso di valori. Attualmente questa clausola è supportata per le query all'interno di una singola partizione, query tra partizioni ancora non supportano questa azione.

## <a name="examples"></a>Esempi

Ad esempio, ecco una query che ignora il primo valore e restituisce il secondo valore (in ordine di nome della città di residenza):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

I risultati sono:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Ecco una query che ignora il primo valore e restituisce il secondo valore (senza ordinamento):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

I risultati sono:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Clausola SELECT](sql-query-select.md)
- [Clausola ORDER BY](sql-query-order-by.md)
