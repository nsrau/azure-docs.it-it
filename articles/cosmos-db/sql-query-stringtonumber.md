---
title: StringToNumber nel linguaggio di query di Azure Cosmos DBStringToNumber in Azure Cosmos DB query language
description: Informazioni sulla funzione di sistema SQL StringToNumber in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296423"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Restituisce l'espressione convertita in un numero. Se l'espressione non può essere convertita, restituisce undefined.  
  
## <a name="syntax"></a>Sintassi
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È un'espressione stringa da analizzare come espressione Number JSON. I numeri in JSON devono essere un numero intero o una virgola mobile. Per informazioni dettagliate sul formato JSON, vedere [json.org](https://json.org/)  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica o non definita.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente `StringToNumber` viene illustrato come si comporta in diversi tipi. 

Gli spazi vuoti sono consentiti solo prima o dopo il numero.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Questo è il set di risultati.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

In JSON un numero valido deve essere un numero intero o un numero a virgola mobile.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Questo è il set di risultati.  
  
```json
{{}}
```  

L'espressione passata verrà analizzata come espressione Number. questi input non restituiscono il tipo Number e pertanto restituiscono undefined. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Questo è il set di risultati.  
  
```json
{{}}
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
