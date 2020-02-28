---
title: Riferimento rapido KQL
description: Elenco di funzioni KQL utili e relative definizioni con esempi di sintassi.
author: yossi-karp
ms.author: v-yokarp
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: c5d331b809d3f9f7db8d8c36add8b283bf0b9e39
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664865"
---
# <a name="kql-quick-reference"></a>Riferimento rapido KQL

Questo articolo illustra un elenco di funzioni e le relative descrizioni che consentono di iniziare a usare il linguaggio di query kusto.

| Operatore/funzione                               | Descrizione                           | Sintassi                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtro/ricerca/condizione**                      |**_Trovare i dati rilevanti filtrando o cercando_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | Filtra in base a un predicato specifico           | `T | where Predicate`                         |
| [dove Contains/has](/azure/kusto/query/whereoperator)        | `Contains`: ricerca eventuali corrispondenze di sottostringhe <br> `Has`: ricerca di una parola specifica (prestazioni migliori)  | `T | where col1 contains/has "[search term]"`|
| [search](/azure/kusto/query/searchoperator)                    | Cerca il valore in tutte le colonne della tabella | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](/azure/kusto/query/takeoperator)                        | Restituisce il numero di record specificato. Usare per testare una query<br>**_Nota_** : `_take`_ e `_limit`_ sono sinonimi. | `T | take NumberOfRows` |
| [caso](/azure/kusto/query/casefunction)                        | Aggiunge un'istruzione Condition, simile a if/then/ElseIf in altri sistemi. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator)                | Produce una tabella con la combinazione distinta delle colonne specificate della tabella di input. | `distinct [ColumnName], [ColumnName]` |
| **Data/ora**                                   |**_Operazioni che usano funzioni di data e ora_**               |                          |
|[fa](/azure/kusto/query/agofunction)                           | Restituisce l'offset dell'ora rispetto al tempo di esecuzione della query. Ad esempio, `ago(1h)` è un'ora prima della lettura del clock corrente. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Restituisce i dati in [vari formati di data](/azure/kusto/query/format-datetimefunction#supported-formats). | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction)                          | Arrotonda tutti i valori in un intervallo di tempo e li raggruppa | `bin(value,roundTo)` |
| **Crea/Rimuovi colonne**                   |**_Aggiungere o rimuovere colonne in una tabella_** |                                                    |
| [stampa](/azure/kusto/query/printoperator)                      | Restituisce una singola riga con una o più espressioni scalari | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](/azure/kusto/query/projectoperator)                  | Consente di selezionare le colonne da includere nell'ordine specificato | `T | project ColumnName [= Expression] [, ...]` <br> Oppure <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator)         | Seleziona le colonne da escludere dall'output | `T | project-away ColumnNameOrPattern [, ...]` |
| [extend](/azure/kusto/query/extendoperator)                    | Crea una colonna calcolata e la aggiunge al set di risultati | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Ordina e aggrega DataSet**                 |**_Ristrutturare i dati ordinando o raggruppando i dati in modi significativi_**|                  |
| [sort](/azure/kusto/query/sortoperator)                        | Ordina le righe della tabella di input in base a una o più colonne in ordine crescente o decrescente | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](/azure/kusto/query/topoperator)                          | Restituisce le prime N righe del set di dati quando il set di dati viene ordinato utilizzando `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](/azure/kusto/query/summarizeoperator)              | Raggruppa le righe in base alle colonne del gruppo di `by` e calcola le aggregazioni in ogni gruppo | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | Conta i record nella tabella di input (ad esempio, T)<br>Questo operatore è abbreviato per `summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator)                        | Unisce le righe di due tabelle per formare una nuova tabella tramite la corrispondenza dei valori delle colonne specificate da ogni tabella. Supporta una gamma completa di tipi di join: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter``rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](/azure/kusto/query/unionoperator)                      | Accetta due o più tabelle e restituisce tutte le relative righe | `[T1] | union [T2], [T3], …` |
| [range](/azure/kusto/query/rangeoperator)                      | Genera una tabella con una serie aritmetica di valori | `range columnName from start to stop step step` |
| **Formattare i dati**                                 | **_Ristrutturare i dati per l'output in modo utile_** | |
| [ricerca](/azure/kusto/query/lookupoperator)                    | Estende le colonne di una tabella dei fatti con i valori cercati in una tabella delle dimensioni | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [MV-Espandi](/azure/kusto/query/mvexpandoperator)               | Converte le matrici dinamiche in righe (espansione multivalore) | `T | mv-expand Column` |
| [analizzare](/azure/kusto/query/parseoperator)                      | valuta un'espressione stringa e analizza il relativo valore in una o più colonne calcolate. Da usare per la strutturazione di dati non strutturati. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [serie make](/azure/kusto/query/make-seriesoperator)          | Crea una serie di valori aggregati specificati lungo un asse specificato | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [consentono](/azure/kusto/query/letstatement)                         | Associa un nome a espressioni che possono fare riferimento al relativo valore associato. I valori possono essere espressioni lambda per creare funzioni ad hoc come parte della query. Utilizzare `let` per creare espressioni su tabelle i cui risultati sono simili a una nuova tabella. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Generale**                                     | **_Operazioni e funzioni varie_** | |
| [invoke](/azure/kusto/query/invokeoperator)                    | Esegue la funzione nella tabella che riceve come input. | `T | invoke function([param1, param2])` |
| [valutare il plug-in](/azure/kusto/query/evaluateoperator)     | Valuta le estensioni del linguaggio di query (plug-in) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualizzazione**                               | **_Operazioni che visualizzano i dati in formato grafico_** | |
| [rendering](/azure/kusto/query/renderoperator) | Esegue il rendering dei risultati come output grafico | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
