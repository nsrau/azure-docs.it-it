---
title: Lingua di query superiore in Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL superiore in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303971"
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
  
 Set di risultati:  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
