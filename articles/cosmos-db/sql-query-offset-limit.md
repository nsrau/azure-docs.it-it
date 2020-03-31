---
title: Clausola OFFSET LIMIT in Azure Cosmos DB
description: Informazioni su come usare la clausola OFFSET LIMIT per ignorare e accettare alcuni valori durante l'esecuzione di query in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771578"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Clausola OFFSET LIMIT in Azure Cosmos DB

La clausola OFFSET LIMIT è una clausola facoltativa da ignorare e quindi accettare un certo numero di valori dalla query. Il conteggio OFFSET e il conteggio LIMIT sono necessari nella clausola OFFSET LIMIT.

Quando OFFSET LIMIT viene utilizzato insieme a una clausola ORDER BY, il set di risultati viene prodotto eseguendo skip e assumendo i valori ordinati. Se non viene utilizzata alcuna clausola ORDER BY, verrà determinato un ordine deterministico di valori.

## <a name="syntax"></a>Sintassi
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argomenti

- `<offset_amount>`

   Specifica il numero intero di elementi che i risultati della query devono ignorare.

- `<limit_amount>`
  
   Specifica il numero intero di elementi che i risultati della query devono includere

## <a name="remarks"></a>Osservazioni
  
  Sia `OFFSET` il conteggio `LIMIT` che il `OFFSET LIMIT` conteggio sono necessari nella clausola. Se viene `ORDER BY` utilizzata una clausola facoltativa, il set di risultati viene prodotto eseguendo il salto sui valori ordinati. In caso contrario, la query restituirà un ordine fisso di valori.

  L'addebito RU `OFFSET LIMIT` di una query con aumenterà con l'aumento del numero di termini da offset. Per le query con più pagine di risultati, è in genere consigliabile usare i token di continuazione. I token di continuazione sono un "segnalibro" per la posizione in cui la query può essere ripresa in un secondo momento. Se si `OFFSET LIMIT`utilizza , non vi è alcun "segnalibro". Se si desidera restituire la pagina successiva della query, è necessario iniziare dall'inizio.
  
  È consigliabile utilizzare per i `OFFSET LIMIT` casi in cui si desidera ignorare completamente i documenti e salvare le risorse client. Ad esempio, è `OFFSET LIMIT` necessario utilizzare se si desidera passare al 1000esimo risultato della query e non è necessario visualizzare i risultati da 1 a 999. Nel back-end, `OFFSET LIMIT` ancora carica ogni documento, inclusi quelli che vengono ignorati. Il vantaggio in termini di prestazioni è un risparmio nelle risorse client evitando l'elaborazione di documenti non necessari.

## <a name="examples"></a>Esempi

Ad esempio, ecco una query che ignora il primo valore e restituisce il secondo valore (in ordine di nome della città residente):

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

Ecco una query che ignora il primo valore e restituisce il secondo valore (senza ordinare):Here's a query that skips the first value and returns the second value (without ordering):

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
