---
title: StringToNumber in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL StringToNumber in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f2622958a2315458ccd01da4aea7c6b75941e755
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339564"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
