---
title: Operatori utili nelle query di log di Monitoraggio di Azure | Microsoft Docs
description: Funzioni comuni da usare per diversi scenari nelle query dei log di Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: d11445c3f31f9aced6fdb9783575d10a026de1f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424139"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Operatori utili nelle query di log di Monitoraggio di Azure

La tabella di seguito riporta alcune funzioni comuni da usare per diversi scenari nelle query dei log di Monitoraggio di Azure.

## <a name="useful-operators"></a>Operatori utili

Categoria                                |Funzione di analisi pertinente
----------------------------------------|----------------------------------------
Alias di colonna e selezione            |`project`, `project-away`, `extend`
Costanti e tabelle temporanee          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Operatori di stringa e confronto         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Funzioni stringa comuni                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Funzioni matematiche comuni                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Testo di analisi                            |`extract()`, `extractjson()`, `parse`, `split()`
Limitazione dell'output                         |`take`, `limit`, `top`, `sample`
Funzioni di data                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Raggruppamento e aggregazioni                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Join e unioni                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Ordinamento                             |`sort`, `order` 
Oggetto dinamico (JSON e matrice)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Operatori logici                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Machine learning                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Passaggi successivi

- Seguire una lezione sulla [scrittura di query di log in Monitoraggio di Azure](get-started-queries.md).
