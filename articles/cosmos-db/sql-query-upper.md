---
title: UPPER nel linguaggio di query del database Cosmos di Azure
description: Informazioni sulla funzione di sistema SQL MAIUSC in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303971"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Restituisce un'espressione stringa dopo aver convertito i caratteri minuscoli in caratteri maiuscoli.  

La funzione di sistema UPPER non utilizza l'indice. Se si prevede di eseguire frequenti confronti senza distinzione tra maiuscole e minuscole, la funzione di sistema MAIUSC può consumare una quantità significativa di RU. In questo caso, invece di utilizzare la funzione di sistema MAIUSC per normalizzare i dati ogni volta per i confronti, è possibile normalizzare l'uso di maiuscole e minuscole al momento dell'inserimento. Quindi una query come SELECT , FROM c WHERE UPPER(c.name) , 'BOB' diventa semplicemente SELECT , FROM c WHERE c.name , 'BOB'.

## <a name="syntax"></a>Sintassi
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È un'espressione stringa.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente mostra `UPPER` come utilizzare in una query  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Questo è il set di risultati.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
