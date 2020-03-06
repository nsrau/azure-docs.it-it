---
title: PIÙ basso nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL più bassa in Azure Cosmos DB per restituire un'espressione stringa dopo la conversione dei dati in caratteri maiuscoli in caratteri minuscoli
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302271"
---
# <a name="lower-azure-cosmos-db"></a>INFERIORE (Azure Cosmos DB)
 Restituisce un'espressione stringa dopo la conversione di dati in caratteri maiuscoli in caratteri minuscoli.  

La funzione di sistema inferiore non utilizza l'indice. Se si prevede di eseguire confronti frequenti senza distinzione tra maiuscole e minuscole, la funzione di sistema inferiore può utilizzare una quantità significativa di ur. In tal caso, anziché utilizzare la funzione di sistema più bassa per normalizzare i dati ogni volta per i confronti, è possibile normalizzare l'involucro al momento dell'inserimento. Quindi, una query come SELECT * FROM c dove LOWER (c. Name) =' Bob ' diventa semplicemente SELECT * FROM c WHERE c.name =' Bob '.

## <a name="syntax"></a>Sintassi
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come utilizzare `LOWER` in una query.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Set di risultati:  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
