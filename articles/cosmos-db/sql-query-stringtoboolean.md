---
title: StringToBoolean nel linguaggio di query del database Cosmos di AzureStringToBoolean in Azure Cosmos DB query language
description: Informazioni sulla funzione di sistema SQL StringToBoolean nel database Cosmos di Azure.Learn about SQL system function StringToBoolean in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296542"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Restituisce l'espressione convertita in un valore booleano. Se l'espressione non può essere convertita, restituisce undefined.  
  
## <a name="syntax"></a>Sintassi
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È un'espressione stringa da analizzare come espressione booleana.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana o non definita.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente `StringToBoolean` viene illustrato come si comporta in diversi tipi. 
 
 Di seguito sono riportati esempi con input valido.

Gli spazi vuoti sono consentiti solo prima o dopo "true"/"false".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Questo è il set di risultati.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Di seguito sono riportati esempi con input non valido.

 I valori booleani fanno distinzione tra maiuscole e minuscole e devono essere scritti con tutti i caratteri minuscoli, ovvero "true" e "false".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Questo è il set di risultati.  
  
```json
[{}]
``` 

L'espressione passata verrà analizzata come espressione booleana. questi input non restituiscono il tipo Boolean e pertanto restituiscono undefined.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Questo è il set di risultati.  
  
```json
[{}]
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
