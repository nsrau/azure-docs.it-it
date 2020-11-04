---
title: StringToBoolean in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL StringToBoolean in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2ad7ca9e2e50395effcc50e776eee3f1740fbb7a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337915"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce l'espressione convertita in un valore booleano. Se l'espressione non può essere convertita, restituisce undefined.  
  
## <a name="syntax"></a>Sintassi
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da analizzare come espressione booleana.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana o undefined.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato il comportamento di `StringToBoolean` tra tipi diversi. 
 
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

 I valori booleani fanno distinzione tra maiuscole e minuscole e devono essere scritti con caratteri minuscoli, ad esempio "true" e "false".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Questo è il set di risultati.  
  
```json
[{}]
``` 

L'espressione passata verrà analizzata come espressione booleana. questi input non restituiscono il tipo booleano e pertanto restituiscono undefined.

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

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
