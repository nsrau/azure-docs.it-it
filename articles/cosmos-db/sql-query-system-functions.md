---
title: Funzioni di sistema nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 98a087f02035545b828cfcf11c58d03d02a5e76a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349181"
---
# <a name="system-functions-azure-cosmos-db"></a>Funzioni di sistema (Azure Cosmos DB)

 In Cosmos DB sono disponibili molte funzioni SQL predefinite. Le categorie di funzioni predefinite sono elencate di seguito.  
  
|Gruppo di funzioni|Descrizione|Operazioni|  
|--------------|-----------------|-----------------| 
|[Funzioni di matrice](sql-query-array-functions.md)|Le funzioni di matrice eseguono un'operazione su un valore di input di matrice e restituiscono un valore numerico, booleano o matrice. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funzioni di data e ora](sql-query-date-time-functions.md)|Le funzioni di data e ora consentono di ottenere la data e l'ora UTC correnti in due formati; timestamp numerico il cui valore è l'EPOCH UNIX in millisecondi o come stringa conforme al formato ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Funzioni matematiche](sql-query-mathematical-functions.md)|Le funzioni matematiche eseguono un calcolo basato in genere su valori di input passati come argomenti e restituiscono un valore numerico. | [ABS](sql-query-abs.md), [ARCCOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [CEILING](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [DEGREES](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [POWER](sql-query-power.md), [RADIANTI](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [SQUARE](sql-query-square.md), [TAN](sql-query-tan.md), [TRONCA](sql-query-trunc.md) |
|[Funzioni spaziali](sql-query-spatial-functions.md)|Le funzioni spaziali eseguono un'operazione su un valore di input di oggetto spaziale e restituiscono un valore numerico o booleano. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Funzioni stringa](sql-query-string-functions.md)|Le funzioni stringa eseguono un'operazione su un valore di stringa di input e restituiscono una stringa, il valore numerico o booleano. | [Concat](sql-query-concat.md), [Contains](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [Left](sql-query-left.md), [length](sql-query-length.md), [Lower](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [Replace](sql-query-replace.md), [replicate](sql-query-replicate.md), [Reverse](sql-query-reverse.md), [right](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [ STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [substring](sql-query-substring.md), [ToString,](sql-query-tostring.md) [Trim](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[Funzioni di controllo del tipo](sql-query-type-checking-functions.md)|Le funzioni di controllo del tipo consentono di controllare il tipo di un'espressione nell'ambito delle query SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Funzioni predefinite rispetto a quelle definite dall'utente (UDF)

Se attualmente si usa una funzione definita dall'utente (UDF) per cui è ora disponibile una funzione predefinita, la funzione predefinita corrispondente sarà più veloce da eseguire e più efficiente.

## <a name="built-in-versus-ansi-sql-functions"></a>Funzioni predefinite rispetto a quelle SQL ANSI

La differenza principale tra funzioni Cosmos DB e funzioni SQL ANSI è che le funzioni Cosmos DB sono progettate per funzionare correttamente con dati senza schema e con schema misto. Se ad esempio una proprietà è mancante o ha un valore non numerico come `unknown`, l'elemento viene ignorato anziché restituire un errore.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Azure Cosmos DB](introduction.md)
- [Funzioni di matrice](sql-query-array-functions.md)
- [Funzioni di data e ora](sql-query-date-time-functions.md)
- [Funzioni matematiche](sql-query-mathematical-functions.md)
- [Funzioni spaziali](sql-query-spatial-functions.md)
- [Funzioni stringa](sql-query-string-functions.md)
- [Funzioni di controllo del tipo](sql-query-type-checking-functions.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)
- [Aggregati](sql-query-aggregates.md)
