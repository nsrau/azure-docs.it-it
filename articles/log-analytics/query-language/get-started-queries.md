---
title: Introduzione alle query in Azure Log Analytics| Microsoft Docs
description: Questo articolo fornisce un'esercitazione per iniziare a scrivere query in Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 548c94ce502da8c6a8d208daafb5b0fb624de1e1
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603938"
---
# <a name="get-started-with-queries-in-log-analytics"></a>Introduzione alle query in Log Analytics


> [!NOTE]
> Prima di seguire questa esercitazione, è consigliabile completare l'esercitazione [Introduzione al portale di analisi](get-started-analytics-portal.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In questa esercitazione si apprenderà come scrivere query di Azure Log Analytics. Si apprenderà come:

- Comprendere la struttura delle query
- Ordinare i risultati di query
- Filtrare i risultati di query
- Specificare un intervallo di tempo
- Selezionare i campi da includere nei risultati
- Definire e usare campi personalizzati
- Aggregare e raggruppare i risultati


## <a name="writing-a-new-query"></a>Scrittura di una nuova query
Le query possono iniziare con un nome di tabella o con il comando *search*. È consigliabile iniziare con un nome di tabella, perché definisce un chiaro ambito per la query e consente di migliorare sia le prestazioni di query che la pertinenza dei risultati.

> [!NOTE]
> Il linguaggio di query di Azure Log Analytics fa distinzione tra maiuscole e minuscole. Le parole chiave del linguaggio sono in genere scritte in lettere minuscole. Quando si usano nomi di tabelle o di colonne in una query, assicurarsi di usare la giusta combinazione di maiuscole e minuscole, come indicato nel riquadro dello schema.

### <a name="table-based-queries"></a>Query basate su tabella
Azure Log Analytics organizza i dati in tabelle, ognuna composta da più colonne. Tutte le tabelle e le colonne vengono visualizzate nel riquadro dello schema, nel portale di analisi. Identificare una tabella di interesse ed esaminarne i dati:

```KQL
SecurityEvent
| take 10
```

La query precedente restituisce 10 risultati dalla tabella *SecurityEvent*, senza alcun ordine specifico. Si tratta di un modo molto comune per esaminare a grandi linee una tabella e comprenderne struttura e contenuto. Ecco come è fatta la query:

* La query inizia con il nome di tabella *SecurityEvent*, che definisce l'ambito della query.
* Il carattere di barra verticale, o pipe, (|) separa i comandi, in modo che l'output del primo comando sia l'input del comando successivo. È possibile inoltrare tramite pipe un numero qualsiasi di elementi.
* La barra verticale è seguita dal comando **take**, che restituisce un numero specifico di record arbitrari dalla tabella.

Sarebbe stato possibile eseguire la query anche senza aggiungere `| take 10`. La query sarebbe comunque valida, ma potrebbe restituire fino a 10.000 risultati.

### <a name="search-queries"></a>Query di ricerca
Le query di ricerca sono meno strutturate e in genere più appropriate per trovare record che includono un valore specifico in una delle relative colonne:

```KQL
search in (SecurityEvent) "Cryptographic"
| take 10
```

Questa query cerca nella tabella *SecurityEvent* i record che contengono il termine "Cryptographic". Di questi record, ne vengono restituiti e visualizzati 10. Se si omette la parte `in (SecurityEvent)` e si esegue solamente il comando `search "Cryptographic"`, la ricerca viene eseguita in *tutte* le tabelle e quindi richiede più tempo ed è meno efficiente.

> [!NOTE]
> Per impostazione predefinita, viene usato un intervallo di tempo corrispondente alle _ultime 24 ore_. Per usare un intervallo diverso, usare il controllo di selezione di data e ora (che si trova vicino al pulsante *Vai*) o aggiungere alla query un filtro di intervallo di tempo esplicito.

## <a name="sort-and-top"></a>Sort e top
Sebbene **take** sia utile per ottenere pochi record, i risultati vengono selezionati e visualizzati senza alcun ordine particolare. Per ottenere una visualizzazione ordinata, è possibile usare il comando **sort**, per ordinare i risultati in base alla colonna preferita:

```
SecurityEvent   
| sort by TimeGenerated desc
```

In questo modo, tuttavia, possono venire restituiti troppi risultati e la query potrebbe richiedere tempo. La query precedente ordina l'*intera* tabella SecurityEvent in base alla colonna TimeGenerated. Il portale di analisi limita quindi la visualizzazione per mostrare solo 10.000 record. Questo approccio non è ovviamente ottimale.

Il modo migliore per ottenere solo i 10 record più recenti consiste nell'usare **top**, che consente di ordinare l'intera tabella sul lato server e quindi restituisce i primi record:

```KQL
SecurityEvent
| top 10 by TimeGenerated
```

L'ordine decrescente rappresenta il tipo di ordinamento predefinito, quindi in genere l'argomento **desc** viene omesso. L'output avrà un aspetto simile al seguente:

![Primi 10 elementi](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Where: filtro in base a una condizione
I filtri, come indica il loro nome, filtrano i dati in base a una condizione specifica. Questo è il modo più comune per limitare i risultati di query alle informazioni pertinenti.

Per aggiungere un filtro a una query, usare l'operatore **where** seguito da una o più condizioni. La query seguente, ad esempio, restituisce solo i record *SecurityEvent* in cui _Level_ corrisponde a _8_:

```KQL
SecurityEvent
| where Level == 8
```

Quando si scrivono le condizioni di filtro, è possibile usare le espressioni seguenti:

| Expression | DESCRIZIONE | Esempio |
|:---|:---|:---|
| == | Controllo dell'uguaglianza<br>(fa distinzione tra maiuscole e minuscole) | `Level == 8` |
| =~ | Controllo dell'uguaglianza<br>(non fa distinzione tra maiuscole e minuscole) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Controllo della disuguaglianza<br>(entrambe le espressioni sono identiche) | `Level != 4` |
| *and*, *or* | Operatori richiesti tra le condizioni| `Level == 16 or CommandLine != ""` |

Per filtrare in base a più condizioni, è possibile usare **and**:

```KQL
SecurityEvent
| where Level == 8 and EventID == 4672
```

oppure inoltrare tramite pipe più elementi **where**, uno dopo l'altro:

```KQL
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> I valori possono essere di tipi diversi, quindi potrebbe essere necessario eseguire il cast per effettuare il confronto in base al tipo corretto. Ad esempio, la colonna *Level* di SecurityEvent è di tipo String, quindi è necessario eseguire il cast a un tipo numerico, come *int* o *long*, per poter usare gli operatori numerici: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Specificare un intervallo di tempo

### <a name="time-picker"></a>Controllo di selezione di data e ora
Il controllo di selezione di data e ora si trova nell'angolo in alto a sinistra e indica che viene eseguita una query solo sui record delle ultime 24 ore. Si tratta dell'intervallo di tempo predefinito applicato a tutte le query. Per ottenere solo i record dall'ultima ora, selezionare _Ultima ora_ ed eseguire di nuovo la query.

![Selezione ora](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtro temporale nella query
È anche possibile definire un intervallo di tempo personalizzato aggiungendo un filtro temporale alla query. È consigliabile posizionare il filtro temporale immediatamente dopo il nome della tabella: 

```KQL
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

Nel filtro temporale precedente, `ago(30m)` indica "30 minuti fa", quindi la query restituisce solo i record degli ultimi 30 minuti. Altre unità di tempo includono giorni (2d), minuti (25m) e secondi (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Project ed extend: selezionare e calcolare le colonne
Usare **project** per selezionare colonne specifiche da includere nei risultati:

```KQL
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

L'esempio precedente genera l'output seguente:

![Risultati del comando project di Log Analytics](media/get-started-queries/project.png)

È anche possibile usare **project** per rinominare le colonne e definirne di nuove. L'esempio seguente usa il comando project per questi scopi:

* Selezionare solo le colonne originali *Computer* e *TimeGenerated*.
* Rinominare la colonna *Activity* in *EventDetails*.
* Creare una nuova colonna denominata *EventCode*. La funzione **substring()** viene usata per ottenere solo i primi quattro caratteri del campo Activity.


```KQL
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

Il comando **extend** mantiene tutte le colonne originali nel set di risultati e definisce colonne aggiuntive. La query seguente usa **extend** per aggiungere una colonna *localtime* che contiene un valore TimeGenerated localizzato.

```KQL
SecurityEvent
| top 10 by TimeGenerated
| extend localtime = TimeGenerated-8h
```

## <a name="summarize-aggregate-groups-of-rows"></a>Summarize: aggregare gruppi di righe
Usare **summarize** per identificare i gruppi di record, in base a una o più colonne, e applicarvi aggregazioni. L'uso più comune di **summarize** è con *count*, che restituisce il numero di risultati in ogni gruppo.

La query seguente esamina tutti i record *Perf* dell'ultima ora, li raggruppa in base a *ObjectName* e conta i record in ogni gruppo: 
```KQL
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

In alcuni casi è opportuno definire i gruppi in base a più dimensioni. Ogni combinazione univoca di questi valori definisce un gruppo separato:

```KQL
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Un altro uso comune consiste nell'eseguire calcoli matematici o statistici su ogni gruppo. Ad esempio, di seguito viene calcolata la media di *CounterValue* per ogni computer:

```KQL
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Sfortunatamente, i risultati di questa query non sono significativi perché combinano i valori di diversi contatori delle prestazioni. Per renderli più significativi, è necessario calcolare la media separatamente per ogni combinazione di *CounterName* e *Computer*:

```KQL
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Riepilogare in base a una colonna temporale
Il raggruppamento dei risultati può anche essere basato su una colonna temporale o un altro valore continuo. Un semplice riepilogo di tipo `by TimeGenerated`, tuttavia, creerebbe gruppi per ogni singolo millisecondo nell'intervallo di tempo, poiché si tratta di valori univoci. 

Per creare gruppi basati su valori continui, è consigliabile suddividere l'intervallo in unità gestibili tramite **bin**. La query seguente analizza i record *Perf* che misurano la memoria libera (*Available MBytes*) in un computer specifico. Calcola il valore medio per ogni periodo di 1 ora, negli ultimi 2 giorni:

```KQL
Perf 
| where TimeGenerated > ago(2d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize count() by bin(TimeGenerated, 1h)
```

Per rendere più chiaro l'output, visualizzarlo come un grafico temporale, che mostra la memoria disponibile nel tempo:

![Memoria di Log Analytics nel tempo](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come [scrivere query di ricerca](search-queries.md)