---
title: Wrangling data flow transformation functions in Azure Data Factory
description: Panoramica delle funzioni del flusso di dati wrangling disponibili in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e2517ec4a02a5d61fb3ce1d9ca9ffa2b5f4e8bf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287035"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Funzioni di trasformazione nel flusso di dati wrangling

Il flusso di dati wrangling in Azure Data Factory consente di eseguire la preparazione e il wrangling agile senza codice su scala cloud. Wrangling flusso di dati si integra con [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) e rende disponibili le funzioni di Power Query M per il wrangling dei dati tramite l'esecuzione di spark. 

Attualmente non tutte le funzioni di Power Query M sono supportate per il wrangling dei dati nonostante siano disponibili durante la creazione. Durante la compilazione dei flussi di dati wrangling, verrà visualizzato il seguente messaggio di errore se una funzione non è supportata:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Di seguito è riportato un elenco delle funzioni di Power Query M supportate.

## <a name="column-management"></a>Gestione colonne

* Selezione: [Table.SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Rimozione: [Table.RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Ridenominazione: [Table.RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table.PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Riordinamento: [Table.ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtro righe

Utilizzare la funzione M [Table.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) per filtrare in base alle seguenti condizioni:

* Uguaglianza e disuguaglianza
* Confronti numerici, di testo e di data (ma non dateTime)
* Informazioni numeriche come [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Odd](https://docs.microsoft.com/powerquery-m/number-iseven)
* Contenimento del testo [mediante Text.Contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)o [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Intervalli di date che includono tutte le funzioni 'IsIn' [Date](https://docs.microsoft.com/powerquery-m/date-functions)) 
* Combinazioni di questi utilizzando e, o, o non condizioni

## <a name="adding-and-transforming-columns"></a>Aggiunta e trasformazione di colonne

Le funzioni M seguenti aggiungono o trasformano colonne: [Table.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table.TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Di seguito sono riportate le funzioni di trasformazione supportate.

* Aritmetica numerica
* Concatenazione del testo
* Aritmetica e ora (operatori aritmetici, [Date.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date.AddYears , Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Le durate possono essere utilizzate per l'aritmetica di data e ora, ma devono essere trasformate in un altro tipo prima di essere scritte in un sink (operatori aritmetici, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Duration.Days](https://docs.microsoft.com/powerquery-m/duration-days), [Duration.Hours](https://docs.microsoft.com/powerquery-m/duration-hours), [Duration.Minutes](https://docs.microsoft.com/powerquery-m/duration-minutes), [Duration.TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Duration.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds)) . [Duration.Seconds](https://docs.microsoft.com/powerquery-m/duration-seconds)    
* La maggior parte delle funzioni numeriche standard, scientifice e trigonometriche (tutte le funzioni in [Operazioni](https://docs.microsoft.com/powerquery-m/number-functions#operations), [Arrotondamento](https://docs.microsoft.com/powerquery-m/number-functions#rounding)e [Trigonometria](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *ad eccezione* di Number.Factorial, Number.Permutations e Number.Combinations)
* Sostituzione ([Replacer.ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text.Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text.Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Estrazione di testo posizionale ([Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Length](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start](https://docs.microsoft.com/powerquery-m/text-start), [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Formattazione di base del testo ([Text.Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text.Upper](https://docs.microsoft.com/powerquery-m/text-upper), [Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text.Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Funzioni data/ora ([Date.Day](https://docs.microsoft.com/powerquery-m/date-day), [Date.Month](https://docs.microsoft.com/powerquery-m/date-month), [Date.Year](https://docs.microsoft.com/powerquery-m/date-year) [Time.Hour](https://docs.microsoft.com/powerquery-m/time-hour), [Time.Minute](https://docs.microsoft.com/powerquery-m/time-minute), [Time.Second](https://docs.microsoft.com/powerquery-m/time-second), [Date.DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Date.DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Date.DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Se le espressioni (ma i rami devono avere tipi corrispondenti)
* Filtri di riga come colonna logica
* Costanti di tipo numero, testo, logico, data e data/ora

<a name="mergingjoining-tables"></a>Unione/Unione di tabelle
----------------------
* Power Query genererà un join annidato (Table.NestedJoin; gli utenti possono anche scrivere manualmente [Table.AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Gli utenti devono quindi espandere la colonna di join annidata in un join non annidato (Table.ExpandTableColumn, non supportato in nessun altro contesto).
* La funzione M [Table.Join](https://docs.microsoft.com/powerquery-m/table-join) può essere scritta direttamente per evitare la necessità di un passaggio di espansione aggiuntivo, ma l'utente deve assicurarsi che non siano presenti nomi di colonna duplicati tra le tabelle unite in join
* Tipi di join supportati: [Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* [Valore.Equals](https://docs.microsoft.com/powerquery-m/value-equals) e [Value.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) sono supportati come operatori di confronto di uguaglianza delle chiavi

## <a name="group-by"></a>Group by

Utilizzare [Table.Group](https://docs.microsoft.com/powerquery-m/table-group) per aggregare i valori.
* Deve essere utilizzato con una funzione di aggregazione
* Funzioni di aggregazione supportate: [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List.Count](https://docs.microsoft.com/powerquery-m/list-count), [List.Average](https://docs.microsoft.com/powerquery-m/list-average), [List.Min](https://docs.microsoft.com/powerquery-m/list-min), [List.Max](https://docs.microsoft.com/powerquery-m/list-max), [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List.First](https://docs.microsoft.com/powerquery-m/list-first), [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Ordinamento

Utilizzare [Table.Sort](https://docs.microsoft.com/powerquery-m/table-sort) per ordinare i valori.

## <a name="reducing-rows"></a>Riduzione delle righe

Mantieni e rimuovi parte superiore, Mantieni intervallo (funzioni M corrispondenti, solo conteggi di supporto, non condizioni: [Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range](https://docs.microsoft.com/powerquery-m/table-range), [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Funzioni sconosciute non supportate

| Funzione | Stato |
| -- | -- |
| Table.PromoteHeaders | Non supportato. Lo stesso risultato può essere ottenuto impostando "Prima riga come intestazione" nel set di dati. |
| Table.CombineColumns | Si tratta di uno scenario comune che non è supportato direttamente, ma può essere ottenuto aggiungendo una nuova colonna che concatena due colonne date.  Ad esempio, Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | Questa operazione è supportata nella maggior parte dei casi. Gli scenari seguenti non sono supportati: trasformazione della stringa in tipo di valuta, trasformazione della stringa in tipo di tempo, trasformazione della stringa in tipo percentuale. |
| Table.NestedJoin | Basta fare un join si tradurrà in un errore di convalida. Affinché funzioni, è necessario che le colonne vengano espanse. |
| Table.Distinct | La rimozione di righe duplicate non è supportata. |
| Table.RemoveLastN | Rimuovi righe inferiori non è supportato. |
| Table.RowCount | Non supportato, ma può essere ottenuto con una colonna add con tutte le celle vuote (colonna condition può essere utilizzata) e quindi utilizzando group by su tale colonna. Table.Group è supportato. | 
| Gestione degli errori a livello di rigaRow level error handling | La gestione degli errori a livello di riga non è attualmente supportata. Ad esempio, per filtrare i valori non numerici da una colonna, un approccio consiste nel trasformare la colonna di testo in un numero. Ogni cella che non viene transformata si troverà in uno stato di errore e deve essere filtrata. Questo scenario non è possibile nel wrangling del flusso di dati. |
| Table.Transpose | Non supportate |
| Table.Pivot | Non supportate |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare un flusso di [dati di wrangling.](wrangling-data-flow-tutorial.md)