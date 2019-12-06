---
title: Clausola limite OFFSET in Azure Cosmos DB
description: Informazioni su come usare la clausola di limite di OFFSET per ignorare e prendere alcuni valori durante l'esecuzione di query in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 68515c51862ada0b1aa794c09b3a6730504a57ee
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873251"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Clausola limite OFFSET in Azure Cosmos DB

La clausola LIMIT OFFSET è una clausola facoltativa da ignorare, quindi è necessario un certo numero di valori dalla query. Il numero di OFFSET e il numero di limiti sono necessari nella clausola limite di OFFSET.

Quando si utilizza il limite di OFFSET insieme a una clausola ORDER BY, il set di risultati viene prodotto tramite Skip e Take sui valori ordinati. Se non viene utilizzata alcuna clausola ORDER BY, verrà generato un ordine deterministico dei valori.

## <a name="syntax"></a>Sintassi
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argomenti

- `<offset_amount>`

   Specifica il numero intero di elementi che i risultati della query devono ignorare.

- `<limit_amount>`
  
   Specifica il numero intero di elementi che devono essere inclusi nei risultati della query

## <a name="remarks"></a>Osservazioni
  
  Nella clausola limite OFFSET sono necessari sia il numero di OFFSET che il numero di limiti. Se viene utilizzata una clausola di `ORDER BY` facoltativa, il set di risultati viene prodotto facendo ignorare i valori ordinati. In caso contrario, la query restituirà un ordine fisso di valori. Attualmente questa clausola è supportata solo per le query all'interno di una singola partizione. le query tra partizioni non le supportano ancora.

## <a name="examples"></a>esempi

Ad esempio, di seguito viene illustrata una query che ignora il primo valore e restituisce il secondo valore (in ordine di nome della città residente):

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
