---
title: LOWER nel linguaggio di query del database Cosmos di Azure
description: Informazioni sulla funzione di sistema LOWER SQL in Azure Cosmos DB per restituire un'espressione stringa dopo la conversione di dati di caratteri maiuscoli in lettere minuscole
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302271"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Database Cosmos di Azure)
 Restituisce un'espressione stringa dopo la conversione di dati in caratteri maiuscoli in caratteri minuscoli.  

La funzione di sistema LOWER non utilizza l'indice. Se si prevede di eseguire frequenti confronti senza distinzione tra maiuscole e minuscole, la funzione di sistema LOWER può consumare una quantità significativa di RU. In questo caso, invece di utilizzare la funzione di sistema LOWER per normalizzare i dati ogni volta per i confronti, è possibile normalizzare l'uso di maiuscole e minuscole al momento dell'inserimento. Quindi una query come SELECT , FROM c WHERE LOWER(c.name) , 'bob' diventa semplicemente SELECT e FROM c WHERE c.name : 'bob'.

## <a name="syntax"></a>Sintassi
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È un'espressione stringa.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene `LOWER` illustrato come utilizzare in una query.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Questo è il set di risultati.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
