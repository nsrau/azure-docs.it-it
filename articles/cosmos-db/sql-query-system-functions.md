---
title: Funzioni di sistema nel linguaggio di query cosmos di AzureSystem functions in Azure Cosmos DB query language
description: Informazioni sulle funzioni di sistema SQL predefinite e definite dall'utente in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870531"
---
# <a name="system-functions-azure-cosmos-db"></a>Funzioni di sistema (Azure Cosmos DB)

 In Cosmos DB sono disponibili molte funzioni SQL predefinite. Le categorie di funzioni predefinite sono elencate di seguito.  
  
|Gruppo di funzioni|Descrizione|Operazioni|  
|--------------|-----------------|-----------------| 
|[Funzioni di matrice](sql-query-array-functions.md)|Le funzioni di matrice eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o matrice. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md) [, ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funzioni di data e ora](sql-query-date-time-functions.md)|Le funzioni di data e ora consentono di ottenere la data e l'ora UTC correnti in due forme; un timestamp numerico il cui valore è l'epoca Unix in millisecondi o come stringa conforme al formato ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Funzioni matematiche](sql-query-mathematical-functions.md)|Le funzioni matematiche eseguono un calcolo basato in genere su valori di input passati come argomenti e restituiscono un valore numerico. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [CEILING](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [DEGREES](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [POWER](sql-query-power.md), [RADIANS](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [SQUARE](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Funzioni spaziali](sql-query-spatial-functions.md)|Le funzioni spaziali eseguono un'operazione su un valore di input di oggetto spaziale e restituiscono un valore numerico o booleano. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Funzioni stringa](sql-query-string-functions.md)|Le funzioni stringa eseguono un'operazione su un valore di stringa di input e restituiscono una stringa, il valore numerico o booleano. | [CONCAT](sql-query-concat.md), CONTAINS , [INDEX_OF](sql-query-index-of.md) [ENDSWITH](sql-query-contains.md), INDEX_OF , [LEFT](sql-query-left.md), [LENGTH](sql-query-length.md), [LOWER](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [REPLACE](sql-query-replace.md), [REPLICATE](sql-query-replicate.md), [REVERSE](sql-query-reverse.md), [RIGHT](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), TRIM , [ENDSWITH](sql-query-endswith.md) [UPPER](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[Funzioni di controllo del tipo](sql-query-type-checking-functions.md)|Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione nell'ambito delle query SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md) [, IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md) [, IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Funzioni definite dall'utente (UDF) integrate e definite dall'utente

Se si utilizza attualmente una funzione definita dall'utente (UDF) per la quale è ora disponibile una funzione incorporata, la funzione incorporata corrispondente sarà più veloce da eseguire e più efficiente.

## <a name="built-in-versus-ansi-sql-functions"></a>Funzioni SQL integrate e ANSI

La differenza principale tra le funzioni Cosmos DB e le funzioni ANSI SQL è che le funzioni Cosmos DB sono progettate per funzionare bene con i dati senza schema e a schema misto. Ad esempio, se una proprietà manca o ha `unknown`un valore non numerico come , l'elemento viene ignorato anziché restituire un errore.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni di matrice](sql-query-array-functions.md)
- [Funzioni di data e ora](sql-query-date-time-functions.md)
- [Funzioni matematiche](sql-query-mathematical-functions.md)
- [Funzioni spaziali](sql-query-spatial-functions.md)
- [Funzioni stringa](sql-query-string-functions.md)
- [Funzioni di controllo del tipo](sql-query-type-checking-functions.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)
- [Aggregazioni](sql-query-aggregates.md)
