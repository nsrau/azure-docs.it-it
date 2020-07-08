---
title: StringToNull in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL StringToNull in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78296440"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Restituisce l'espressione convertita in null. Se l'espressione non può essere convertita, restituisce undefined.  
  
## <a name="syntax"></a>Sintassi
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da analizzare come espressione null.
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione null o non definita.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato il comportamento di `StringToNull` tra tipi diversi. 

Di seguito sono riportati esempi con input valido.

 Gli spazi vuoti sono consentiti solo prima o dopo "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Questo è il set di risultati.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Di seguito sono riportati esempi con input non valido.

Null fa distinzione tra maiuscole e minuscole e deve essere scritto con caratteri minuscoli, ovvero "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Questo è il set di risultati.  
  
```json
[{}]
```  

L'espressione passata verrà analizzata come espressione null; questi input non restituiscono il tipo null e pertanto restituiscono undefined.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
