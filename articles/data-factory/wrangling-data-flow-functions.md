---
title: Funzionalità di trasformazione del flusso di dati in Azure Data Factory
description: Panoramica delle funzioni flusso di dati disponibili in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 3274641f7b118e13b3ed727f609ce7471fd66b54
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682296"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Funzioni di trasformazione nell'attività del flusso di dati

La verifica del flusso di dati in Azure Data Factory ti permette di eseguire operazioni di preparazione dei dati agile senza codice e di litigare a livello di cloud. La verifica del flusso di dati si integra con [Power query online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) e rende disponibili le funzioni Power query M per data wrangling tramite l'esecuzione di Spark. 

Attualmente non tutte le funzioni Power Query M sono supportate per data wrangling nonostante siano disponibili durante la creazione. Quando si compilano i flussi di dati in corso, verrà visualizzato il messaggio di errore seguente se una funzione non è supportata:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Di seguito è riportato un elenco delle funzioni di Power Query M supportate.

## <a name="column-management"></a>Gestione delle colonne

* Selezione: [Table. SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Rimozione: [Table. RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Ridenominazione: [Table. RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table. PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Riordino: [Table. ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtro di riga

Usare la funzione M [Table. SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) per filtrare le condizioni seguenti:

* Uguaglianza e disuguaglianza
* Confronti numerici, di testo e di data (ma non DateTime)
* Informazioni numeriche, ad esempio [Number.](https://docs.microsoft.com/powerquery-m/number-iseven)/[dispari](https://docs.microsoft.com/powerquery-m/number-iseven)
* Contenimento di testo tramite [Text. Contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text. StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)o [Text. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Intervalli di date che includono tutte le [funzioni di data](https://docs.microsoft.com/powerquery-m/date-functions)IsIn' 
* Combinazioni di queste con le condizioni and, or o not

## <a name="adding-and-transforming-columns"></a>Aggiunta e trasformazione di colonne

Le funzioni M seguenti aggiungono o trasformano colonne: [Table. AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table. TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table. ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table. DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Di seguito sono riportate le funzioni di trasformazione supportate.

* Aritmetica numerica
* Concatenazione di testo
* Data andTime aritmetica (operatori aritmetici, [date. AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [date. AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [date. AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [date. AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [date. AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Le durate possono essere usate per l'aritmetica di data e ora, ma devono essere trasformate in un altro tipo prima della scrittura in un sink (operatori aritmetici, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Duration. Days](https://docs.microsoft.com/powerquery-m/duration-days), [Duration. hours](https://docs.microsoft.com/powerquery-m/duration-hours), [Duration. minutes](https://docs.microsoft.com/powerquery-m/duration-minutes) [, Duration. seconds](https://docs.microsoft.com/powerquery-m/duration-seconds), [Duration. TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Duration. TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Duration. TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [Duration. TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* La maggior parte delle funzioni numeriche standard, scientifiche e trigonometriche (tutte le funzioni in [operazioni](https://docs.microsoft.com/powerquery-m/number-functions#operations), [arrotondamento](https://docs.microsoft.com/powerquery-m/number-functions#rounding)e [trigonometria](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *tranne* numero. fattoriale, numero. permutazioni e numero. combinazioni)
* Sostituzione ([Replacer. ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [Replacer. ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text. Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text. Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Estrazione del testo posizionale ([Text. PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text. length](https://docs.microsoft.com/powerquery-m/text-length), [Text. Start](https://docs.microsoft.com/powerquery-m/text-start), [Text. end](https://docs.microsoft.com/powerquery-m/text-end), [Text. Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text. ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text. RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Formattazione del testo di base ([Text. Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text. Upper](https://docs.microsoft.com/powerquery-m/text-upper), [text. Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[end](https://docs.microsoft.com/powerquery-m/text-trimend), [Text. PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[end](https://docs.microsoft.com/powerquery-m/text-padend), [Text. Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Funzioni di data/ora ([date. Day](https://docs.microsoft.com/powerquery-m/date-day), [date. month](https://docs.microsoft.com/powerquery-m/date-month), [date. Year](https://docs.microsoft.com/powerquery-m/date-year) [time. hour](https://docs.microsoft.com/powerquery-m/time-hour), [time. minute](https://docs.microsoft.com/powerquery-m/time-minute), [time. Second](https://docs.microsoft.com/powerquery-m/time-second), [date. DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [date. DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [date. DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Espressioni if (ma i rami devono avere tipi corrispondenti)
* Filtri di riga come colonna logica
* Costanti Number, text, Logical, date e DateTime

<a name="mergingjoining-tables"></a>Unione/Unione di tabelle
----------------------
* Power Query genererà un join annidato (Table. NestedJoin; gli utenti possono anche scrivere manualmente [Table. AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Gli utenti devono quindi espandere la colonna join annidata in un join non annidato (Table. ExpandTableColumn, non supportato in nessun altro contesto).
* La tabella della funzione M [. join](https://docs.microsoft.com/powerquery-m/table-join) può essere scritta direttamente per evitare la necessità di un passaggio di espansione aggiuntivo, ma l'utente deve assicurarsi che non siano presenti nomi di colonna duplicati tra le tabelle unite in join
* Tipi di join supportati: [inner](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Sia [value. Equals](https://docs.microsoft.com/powerquery-m/value-equals) che [value. NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) sono supportati come operatori di confronto per l'uguaglianza delle chiavi

## <a name="group-by"></a>Raggruppa per

Utilizzare [Table. Group](https://docs.microsoft.com/powerquery-m/table-group) per aggregare i valori.
* Deve essere usato con una funzione di aggregazione
* Funzioni di aggregazione supportate: [Table. RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List. Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List. Count](https://docs.microsoft.com/powerquery-m/list-count), [List. Average](https://docs.microsoft.com/powerquery-m/list-average), [List. min](https://docs.microsoft.com/powerquery-m/list-min), [List. max](https://docs.microsoft.com/powerquery-m/list-max), [List. StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List. First](https://docs.microsoft.com/powerquery-m/list-first), List. [Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>ordinamento

Utilizzare [Table. Sort](https://docs.microsoft.com/powerquery-m/table-sort) per ordinare i valori.

## <a name="reducing-rows"></a>Riduzione di righe

Mantieni e Rimuovi top, Mantieni intervallo (funzioni M corrispondenti, solo conteggi di supporto, non condizioni [: Table. firstn](https://docs.microsoft.com/powerquery-m/table-firstn), [Table. Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table. RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table. Range](https://docs.microsoft.com/powerquery-m/table-range), [Table. Minnesota](https://docs.microsoft.com/powerquery-m/table-minn), [Table. MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functionality"></a>Funzionalità note non supportate

Di seguito sono riportate le funzioni non supportate. Questo elenco non è esaustivo ed è soggetto a modifiche.
* Unire le colonne (si può ottenere con AddColumn)
* Dividi colonna
* Accoda query
* ' Utilizza la prima riga come intestazioni ' è utilizza intestazioni come prima riga '

## <a name="next-steps"></a>Passaggi successivi
