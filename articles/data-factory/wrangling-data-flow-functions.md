---
title: Funzionalità di trasformazione del flusso di dati in Azure Data Factory
description: Panoramica delle funzioni flusso di dati disponibili in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: c56c52193f433571f16e4acf7bd6e7b89641b26f
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233951"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Funzioni di trasformazione nell'attività del flusso di dati

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La verifica del flusso di dati in Azure Data Factory ti permette di eseguire operazioni di preparazione dei dati agile senza codice e di litigare a livello di cloud. La verifica del flusso di dati si integra con [Power query online](/powerquery-m/power-query-m-reference) e rende disponibili le funzioni Power query M per data wrangling tramite l'esecuzione di Spark. 

> [!NOTE]
> Il flusso di dati in corso è attualmente avilable in anteprima pubblica

Attualmente non tutte le funzioni Power Query M sono supportate per data wrangling nonostante siano disponibili durante la creazione. Quando si compilano i flussi di dati in corso, verrà visualizzato il messaggio di errore seguente se una funzione non è supportata:

`The Wrangling Data Flow is invalid. Expression.Error: The transformation logic is not supported. Please try a simpler expression.`

Di seguito è riportato un elenco delle funzioni di Power Query M supportate.

## <a name="column-management"></a>Gestione delle colonne

* Selezione: [Table. SelectColumns](/powerquery-m/table-selectcolumns)
* Rimozione: [Table. RemoveColumns](/powerquery-m/table-removecolumns)
* Ridenominazione: [Table. RenameColumns](/powerquery-m/table-renamecolumns), [Table. PrefixColumns](/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Riordino: [Table. ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtro di riga

Usare la funzione M [Table. SelectRows](/powerquery-m/table-selectrows) per filtrare le condizioni seguenti:

* Uguaglianza e disuguaglianza
* Confronti numerici, di testo e di data (ma non DateTime)
* Informazioni numeriche, ad esempio [Number. equamente](/powerquery-m/number-iseven) / [Odd](/powerquery-m/number-iseven)
* Contenimento di testo tramite [Text. Contains](/powerquery-m/text-contains), [Text. StartsWith](/powerquery-m/text-startswith)o [Text. EndsWith](/powerquery-m/text-endswith)
* Intervalli di date che includono tutte le [funzioni di data](/powerquery-m/date-functions)IsIn' 
* Combinazioni di queste con le condizioni and, or o not

## <a name="adding-and-transforming-columns"></a>Aggiunta e trasformazione di colonne

Le funzioni M seguenti aggiungono o trasformano colonne: [Table. AddColumn](/powerquery-m/table-addcolumn), [Table. TransformColumns](/powerquery-m/table-transformcolumns), [Table. ReplaceValue](/powerquery-m/table-replacevalue), [Table. DuplicateColumn](/powerquery-m/table-duplicatecolumn). Di seguito sono riportate le funzioni di trasformazione supportate.

* Aritmetica numerica
* Concatenazione di testo
* Data andTime aritmetica (operatori aritmetici, [date. AddDays](/powerquery-m/date-adddays), [date. AddMonths](/powerquery-m/date-addmonths), [date. AddQuarters](/powerquery-m/date-addquarters), [date. AddWeeks](/powerquery-m/date-addweeks), [date. AddYears](/powerquery-m/date-addyears))
* Le durate possono essere usate per operazioni aritmetiche di data e ora, ma devono essere trasformate in un altro tipo prima della scrittura in un sink (operatori aritmetici, [#duration](/powerquery-m/sharpduration), [Duration. Days](/powerquery-m/duration-days), [Duration. hours](/powerquery-m/duration-hours), [Duration. minutes](/powerquery-m/duration-minutes), [Duration. seconds](/powerquery-m/duration-seconds), [Duration. TotalDays](/powerquery-m/duration-totaldays), [Duration. TotalHours](/powerquery-m/duration-totalhours), [Duration. TotalMinutes](/powerquery-m/duration-totalminutes), [Duration. TotalSeconds](/powerquery-m/duration-totalseconds))    
* La maggior parte delle funzioni numeriche standard, scientifiche e trigonometriche (tutte le funzioni in [operazioni](/powerquery-m/number-functions#operations), [arrotondamento](/powerquery-m/number-functions#rounding)e [trigonometria](/powerquery-m/number-functions#trigonometry) *tranne* numero. fattoriale, numero. permutazioni e numero. combinazioni)
* Sostituzione ([Replacer. ReplaceText](/powerquery-m/replacer-replacetext), [Replacer. ReplaceValue](/powerquery-m/replacer-replacevalue), [Text. Replace](/powerquery-m/text-replace), [Text. Remove](/powerquery-m/text-remove))
* Estrazione del testo posizionale ([Text. PositionOf](/powerquery-m/text-positionof), [Text. length](/powerquery-m/text-length), [Text. Start](/powerquery-m/text-start), [Text. end](/powerquery-m/text-end), [Text. Middle](/powerquery-m/text-middle), [Text. ReplaceRange](/powerquery-m/text-replacerange), [Text. RemoveRange](/powerquery-m/text-removerange))
* Formattazione del testo di base ([Text. Lower](/powerquery-m/text-lower), [Text. Upper](/powerquery-m/text-upper), [Text. Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [end](/powerquery-m/text-trimend), [Text. PadStart](/powerquery-m/text-padstart) / [end](/powerquery-m/text-padend), [Text. Reverse](/powerquery-m/text-reverse))
* Funzioni di data/ora ([date. Day](/powerquery-m/date-day), [date. month](/powerquery-m/date-month), [date. Year](/powerquery-m/date-year) [time. hour](/powerquery-m/time-hour), [time. minute](/powerquery-m/time-minute), [time. Second](/powerquery-m/time-second), [date. DayOfWeek](/powerquery-m/date-dayofweek), [date. DayOfYear](/powerquery-m/date-dayofyear), [date. DaysInMonth](/powerquery-m/date-daysinmonth))
* Espressioni if (ma i rami devono avere tipi corrispondenti)
* Filtri di riga come colonna logica
* Costanti Number, text, Logical, date e DateTime

<a name="mergingjoining-tables"></a>Unione/Unione di tabelle
----------------------
* Power Query genererà un join annidato (Table. NestedJoin; gli utenti possono anche scrivere manualmente [Table. AddJoinColumn](/powerquery-m/table-addjoincolumn)).
    Gli utenti devono quindi espandere la colonna join annidata in un join non annidato (Table. ExpandTableColumn, non supportato in nessun altro contesto).
* La tabella della funzione M   [. join](/powerquery-m/table-join) può essere scritta direttamente per evitare la necessità di un passaggio di espansione aggiuntivo, ma l'utente deve assicurarsi che non siano presenti nomi di colonna duplicati tra le tabelle unite in join
* Tipi di join supportati:   [inner](/powerquery-m/joinkind-inner),   [LeftOuter](/powerquery-m/joinkind-leftouter),   [RightOuter](/powerquery-m/joinkind-rightouter),   [FullOuter](/powerquery-m/joinkind-fullouter)
* Sia   [value. Equals](/powerquery-m/value-equals) che   [value. NullableEquals](/powerquery-m/value-nullableequals) sono supportati come operatori di confronto per l'uguaglianza delle chiavi

## <a name="group-by"></a>Group by

Utilizzare [Table. Group](/powerquery-m/table-group) per aggregare i valori.
* Deve essere usato con una funzione di aggregazione
* Funzioni di aggregazione supportate: list.   [Sum](/powerquery-m/list-sum),   [List. Count](/powerquery-m/list-count),   [List. Average](/powerquery-m/list-average),   [List. min](/powerquery-m/list-min),   [List. max](/powerquery-m/list-max),   [List. StandardDeviation](/powerquery-m/list-standarddeviation),   [List. First](/powerquery-m/list-first), List.   [Last](/powerquery-m/list-last)

## <a name="sorting"></a>Ordinamento

Utilizzare [Table. Sort](/powerquery-m/table-sort) per ordinare i valori.

## <a name="reducing-rows"></a>Riduzione di righe

Mantieni e Rimuovi top, Mantieni intervallo (funzioni M corrispondenti, solo conteggi di supporto, non condizioni [: Table. firstn](/powerquery-m/table-firstn), [Table. Skip](/powerquery-m/table-skip), [Table. RemoveFirstN](/powerquery-m/table-removefirstn), [Table. Range](/powerquery-m/table-range), [Table. Minnesota](/powerquery-m/table-minn), [Table. MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Funzioni non supportate note

| Funzione | Stato |
| -- | -- |
| Table.PromoteHeaders | Non supportata. Lo stesso risultato può essere ottenuto impostando la "prima riga come intestazione" nel set di dati. |
| Table.CombineColumns | Si tratta di uno scenario comune che non è supportato direttamente, ma è possibile ottenerlo aggiungendo una nuova colonna che concatena due colonne specificate.  Ad esempio, Table. AddColumn (RemoveEmailColumn, "Name", each [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | Questa operazione è supportata nella maggior parte dei casi. Gli scenari seguenti non sono supportati: trasformazione di una stringa in un tipo di valuta, trasformazione di una stringa in un tipo time, trasformazione di una stringa in un tipo di percentuale. |
| Table.NestedJoin | La semplice operazione di join comporterà un errore di convalida. Per il corretto funzionamento, le colonne devono essere espanse. |
| Table.Distinct | La rimozione di righe duplicate non è supportata. |
| Table.RemoveLastN | La rimozione delle righe in basso non è supportata. |
| Table.RowCount | Non supportato, ma è possibile ottenerlo aggiungendo una colonna personalizzata contenente il valore 1, quindi aggregando tale colonna con List. Sum. Table. Group è supportato. | 
| Gestione degli errori a livello di riga | La gestione degli errori a livello di riga non è attualmente supportata. Per filtrare, ad esempio, i valori non numerici di una colonna, un approccio consiste nel trasformare la colonna di testo in un numero. Ogni cella che non riesce a trasformare sarà in uno stato di errore e dovrà essere filtrata. Questo scenario non è possibile nel flusso di dati. |
| Table.Transpose | Non supportate |
| Table.Pivot | Non supportate |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un flusso di dati](wrangling-data-flow-tutorial.md)in confronto.
