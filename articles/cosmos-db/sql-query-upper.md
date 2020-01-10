---
title: Lingua di query superiore in Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL superiore in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9809a138a5e28eb069d545d39cfda815c915bd78
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728889"
---
# <a name="upper-azure-cosmos-db"></a>SUPERIORE (Azure Cosmos DB)
 Restituisce un'espressione stringa dopo aver convertito i caratteri minuscoli in caratteri maiuscoli.  

La funzione di sistema superiore non utilizza l'indice. Se si prevede di eseguire confronti frequenti senza distinzione tra maiuscole e minuscole, la funzione di sistema superiore può utilizzare una quantità significativa di ur. In tal caso, anziché utilizzare la funzione di sistema superiore per normalizzare i dati ogni volta per i confronti, è possibile normalizzare la combinazione di maiuscole e minuscole al momento dell'inserimento. Quindi, una query come SELECT * FROM c dove UPPER (c. Name) =' BOB ' diventa semplicemente SELECT * FROM c WHERE c.name =' BOB '.

## <a name="syntax"></a>Sintassi
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come utilizzare `UPPER` in una query  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Questo è il set di risultati.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
