---
title: StringToNumber in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL StringToNumber in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
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
   Espressione stringa da analizzare come espressione numerica JSON. I numeri in JSON devono essere un numero intero o un punto a virgola mobile. Per informazioni dettagliate sul formato JSON, vedere [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica o non definita.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato il comportamento di `StringToNumber` tra tipi diversi. 

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

L'espressione passata verrà analizzata come espressione numerica. questi input non restituiscono il tipo Number e pertanto restituiscono undefined. 

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

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
