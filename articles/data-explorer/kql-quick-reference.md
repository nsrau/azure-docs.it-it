---
title: Riferimento rapido KQL
description: Un elenco di funzioni KQL utili e le relative definizioni con esempi di sintassi.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139080"
---
# <a name="kql-quick-reference"></a>Riferimento rapido KQL

Questo articolo illustra un elenco di funzioni e le relative descrizioni per iniziare a usare Kusto Query Language.

| Operatore/Funzione                               | Descrizione                           | Sintassi                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtro/Ricerca/Condizione**                      |**_Trovare dati pertinenti filtrando o cercando_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | Filtri su un predicato specificoFilters on a specific predicate           | `T | where Predicate`                         |
| [dove contiene/ha](/azure/kusto/query/whereoperator)        | `Contains`: cerca qualsiasi corrispondenza di sottostringa <br> `Has`: cerca una parola specifica (prestazioni migliori)  | `T | where col1 contains/has "[search term]"`|
| [ricerca](/azure/kusto/query/searchoperator)                    | Cerca il valore in tutte le colonne della tabella | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](/azure/kusto/query/takeoperator)                        | Restituisce il numero specificato di record. Utilizzare per testare una query<br>**_Nota_** `_take`: `_limit`_ e _ sono sinonimi. | `T | take NumberOfRows` |
| [case](/azure/kusto/query/casefunction)                        | Aggiunge un'istruzione condition, simile a if/then/elseif in altri sistemi. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [Distinti](/azure/kusto/query/distinctoperator)                | Produce una tabella con la combinazione distinta delle colonne fornite della tabella di input | `distinct [ColumnName], [ColumnName]` |
| **Data/Ora**                                   |**_Operazioni che utilizzano funzioni di data e ora_**               |                          |
|[Fa](/azure/kusto/query/agofunction)                           | Restituisce l'offset temporale relativo al momento dell'esecuzione della query. Ad esempio, `ago(1h)` è un'ora prima della lettura dell'orologio corrente. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Restituisce i dati in [vari formati di data.](/azure/kusto/query/format-datetimefunction#supported-formats) | `format_datetime(datetime , format)` |
| [Bin](/azure/kusto/query/binfunction)                          | Arrotonda tutti i valori in un intervallo di tempo e li raggruppa | `bin(value,roundTo)` |
| **Crea/Rimuovi colonne**                   |**_Aggiungere o rimuovere colonne in una tabella_** |                                                    |
| [Stampare](/azure/kusto/query/printoperator)                      | Restituisce una singola riga con una o più espressioni scalari | `print [ColumnName =] ScalarExpression [',' ...]` |
| [Progetto](/azure/kusto/query/projectoperator)                  | Seleziona le colonne da includere nell'ordine specificato | `T | project ColumnName [= Expression] [, ...]` <br> Oppure <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator)         | Seleziona le colonne da escludere dall'output | `T | project-away ColumnNameOrPattern [, ...]` |
| [Estendere](/azure/kusto/query/extendoperator)                    | Crea una colonna calcolata e la aggiunge al set di risultati | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Ordina e aggrega set di dati**                 |**_Ristrutturare i dati ordinandoli o raggruppandoli in modo significativo_**|                  |
| [Sorta](/azure/kusto/query/sortoperator)                        | Ordina le righe della tabella di input in base a una o più colonne in ordine crescente o decrescente | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [In alto](/azure/kusto/query/topoperator)                          | Restituisce le prime N righe del set di dati quando il set di dati viene ordinato`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Riassumere](/azure/kusto/query/summarizeoperator)              | Raggruppa le righe `by` in base alle colonne del gruppo e calcola le aggregazioni su ciascun gruppo | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | Conta i record nella tabella di input (ad esempio, T)<br>Questo operatore è l'abbreviazione di`summarize count() `| `T | count` |
| [Unirsi](/azure/kusto/query/joinoperator)                        | Unisce le righe di due tabelle per formare una nuova tabella abbinando i valori delle colonne specificate da ogni tabella. Supporta una gamma completa di `flouter` `inner`tipi `innerunique` `leftanti`di `leftantisemi` `leftouter`join: `rightanti` `rightantisemi`, `rightouter`, , , , , , `leftsemi`, , , , ,`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Unione](/azure/kusto/query/unionoperator)                      | Accetta due o più tabelle e restituisce tutte le relative righe | `[T1] | union [T2], [T3], …` |
| [Gamma](/azure/kusto/query/rangeoperator)                      | Genera una tabella con una serie aritmetica di valori | `range columnName from start to stop step step` |
| **Formattare i dati**                                 | **_Ristrutturare i dati per l'output in modo utile_** | |
| [Ricerca](/azure/kusto/query/lookupoperator)                    | Estende le colonne di una tabella dei fatti con valori ricercati in una tabella delle dimensioni | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](/azure/kusto/query/mvexpandoperator)               | Trasforma le matrici dinamiche in righe (espansione multivalore) | `T | mv-expand Column` |
| [Parse](/azure/kusto/query/parseoperator)                      | valuta un'espressione stringa e analizza il relativo valore in una o più colonne calcolate. Utilizzare per strutturare dati non strutturati. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](/azure/kusto/query/make-seriesoperator)          | Crea serie di valori aggregati specificati lungo un asse specificato | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [Lasciare](/azure/kusto/query/letstatement)                         | Associa un nome alle espressioni che possono fare riferimento al relativo valore associato. I valori possono essere espressioni lambda per creare funzioni ad hoc come parte della query. Consente `let` di creare espressioni su tabelle i cui risultati hanno l'aspetto di una nuova tabella. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Generale**                                     | **_Operazioni e funzioni varie_** | |
| [invoke](/azure/kusto/query/invokeoperator)                    | Esegue la funzione nella tabella che riceve come input. | `T | invoke function([param1, param2])` |
| [valutare pluginName](/azure/kusto/query/evaluateoperator)     | Valuta le estensioni del linguaggio di query (plugin) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualizzazione**                               | **_Operazioni che visualizzano i dati in un formato grafico_** | |
| [rendere](/azure/kusto/query/renderoperator) | Esegue il rendering dei risultati come output grafico | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
