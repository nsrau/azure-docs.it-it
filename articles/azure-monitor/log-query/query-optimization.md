---
title: Ottimizzare le query di log in Monitoraggio di AzureOptimize log queries in Azure Monitor
description: Procedure consigliate per l'ottimizzazione delle query di log in Monitoraggio di Azure.Best practices for optimizing log queries in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 29d5213b8eecd94ed8c8ce565972c9f98872a362
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411439"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Ottimizzare le query di log in Monitoraggio di AzureOptimize log queries in Azure Monitor
Log di monitoraggio di Azure usa [Azure Data Explorer (ADX)](/azure/data-explorer/) per archiviare i dati di log ed eseguire query per l'analisi di tali dati. Crea, gestisce e gestisce automaticamente i cluster ADX e li ottimizza per il carico di lavoro dell'analisi dei log. Quando si esegue una query, questa viene ottimizzata e instradata al cluster ADX appropriato in cui sono archiviati i dati dell'area di lavoro. Sia i log di monitoraggio di Azure che Azure Data Explorer usano molti meccanismi di ottimizzazione automatica delle query. Mentre le ottimizzazioni automatiche forniscono un incremento significativo, sono in alcuni casi in cui è possibile migliorare notevolmente le prestazioni delle query. In questo articolo vengono illustrate le considerazioni sulle prestazioni e diverse tecniche per correggerle.

La maggior parte delle tecniche sono comuni alle query che vengono eseguite direttamente in Azure Data Explorer e nei log di Monitoraggio di Azure, anche se sono disponibili diverse considerazioni univoche dei log di Monitoraggio di Azure che vengono illustrate di seguito. Per altri suggerimenti per l'ottimizzazione di Azure Data Explorer, vedere Procedure consigliate per le [query.](/azure/kusto/query/best-practices)

Le query ottimizzate:

- Eseguire più velocemente, ridurre la durata complessiva dell'esecuzione della query.
- Hanno minori possibilità di essere limitati o respinti.

È necessario prestare particolare attenzione alle query usate per l'utilizzo ricorrente e intenso, ad esempio dashboard, avvisi, app per la logica e Power BI. L'impatto di una query inefficace in questi casi è notevole.

## <a name="query-performance-pane"></a>Riquadro delle prestazioni delle query
Dopo aver eseguito una query in Log Analytics, fare clic sulla freccia in giù sopra i risultati della query per visualizzare il riquadro delle prestazioni della query che mostra i risultati di diversi indicatori di prestazioni per la query. Questi indicatori di prestazioni sono descritti nella sezione seguente.

![Riquadro delle prestazioni delle query](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Indicatori di prestazioni delle query

Per ogni query eseguita sono disponibili i seguenti indicatori di prestazioni delle query:

- [CPU totale](#total-cpu): Calcolo complessivo utilizzato per elaborare la query in tutti i nodi di calcolo. Rappresenta il tempo utilizzato per l'elaborazione, l'analisi e il recupero dei dati. 

- [Dati utilizzati per](#data-used-for-processed-query)la query elaborata: dati complessivi a cui è stato effettuato l'accesso per elaborare la query. Influenzato dalle dimensioni della tabella di destinazione, dall'intervallo di tempo utilizzato, dai filtri applicati e dal numero di colonne a cui viene fatto riferimento.

- [Intervallo di tempo della query elaborata:](#time-span-of-the-processed-query)divario tra i dati più recenti e quelli meno recenti a cui è stato effettuato l'accesso per elaborare la query. Influenzato dall'intervallo di tempo esplicito specificato per la query.

- [Età dei dati elaborati](#age-of-processed-data): Il divario tra ora e i dati meno recenti a cui si è avuto accesso per elaborare la query. Influenza fortemente l'efficienza del recupero dei dati.

- [Numero di aree di lavoro](#number-of-workspaces): numero di aree di lavoro a cui è stato effettuato l'accesso durante l'elaborazione della query a causa di una selezione implicita o esplicita.

- [Numero di aree](#number-of-regions): numero di aree a cui è stato eseguito l'accesso durante l'elaborazione delle query a causa della selezione implicita o esplicita delle aree di lavoro. Le query a più aree sono molto meno efficienti e gli indicatori di prestazioni presentano una copertura parziale.

- [Parallelismo](#parallelism): Indica quanto il sistema è stato in grado di eseguire questa query su più nodi. Rilevante solo per le query con un consumo elevato della CPU. Influenzato dall'utilizzo di funzioni e operatori specifici.


## <a name="total-cpu"></a>CPU totale
CPU di calcolo effettiva investita per elaborare la query in tutti i nodi di elaborazione delle query. Poiché la maggior parte delle query vengono eseguite su un numero elevato di nodi, questo sarà in genere molto più grande della durata effettivamente richiesta per l'esecuzione della query. 

Il tempo di elaborazione delle query viene impiegato per:
- Recupero dei dati: il recupero dei dati obsoleti richiederà più tempo rispetto al recupero dei dati recenti.
- Elaborazione dei dati – logica e valutazione dei dati. 

Oltre al tempo trascorso nei nodi di elaborazione delle query, è disponibile ulteriore tempo da parte dei log di Monitoraggio di Azure per: autenticare l'utente e verificare che sia autorizzato ad accedere a questi dati, individuare l'archivio dati, analizzare la query e allocare i nodi di elaborazione delle query. Questo tempo non è incluso nel tempo totale CPU della query.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Filtraggio precoce dei record prima dell'utilizzo di funzioni ad alta CPU

Alcuni dei comandi e delle funzioni di query sono pesanti nel consumo della CPU. Ciò è particolarmente vero per i comandi che analizzano JSON e XML o estraggono espressioni regolari complesse. Tale analisi può avvenire in modo esplicito tramite funzioni [parse_json()](/azure/kusto/query/parsejsonfunction) o [parse_xml()](/azure/kusto/query/parse-xmlfunction) o in modo implicito quando si fa riferimento a colonne dinamiche.

Queste funzioni consumano CPU in proporzione al numero di righe che stanno elaborando. L'ottimizzazione più efficiente consiste nell'aggiungere le condizioni in cui nelle prime fasi della query è possibile filtrare il maggior numero possibile di record prima che venga eseguita la funzione che richiede un utilizzo intensivo della CPU.

Ad esempio, le query seguenti producono esattamente lo stesso risultato, ma la seconda è di gran lunga la più efficiente della condizione [where](/azure/kusto/query/whereoperator) prima dell'analisi esclude molti record:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Evitare di usare clausole where valutate

Le query che contengono clausole [where](/azure/kusto/query/whereoperator) su una colonna valutata anziché su colonne fisicamente presenti nel set di dati perdono efficienza. Il filtro sulle colonne valutate impedisce alcune ottimizzazioni del sistema quando vengono gestiti set di dati di grandi dimensioni.
Ad esempio, le query seguenti producono esattamente lo stesso risultato, ma la seconda è più efficiente della condizione [where](/azure/kusto/query/whereoperator) che fa riferimento alla colonna predefinita

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>Usare comandi di aggregazione e dimenazioni efficaci per riepilogare e unire

Mentre alcuni comandi di aggregazione come [max(),](/azure/kusto/query/max-aggfunction) [sum()](/azure/kusto/query/sum-aggfunction), [count()](/azure/kusto/query/count-aggfunction)e [avg()](/azure/kusto/query/avg-aggfunction) hanno un impatto ridotto sulla CPU a causa della loro logica, altri sono più complessi e includono euristiche e stime che consentono loro di essere eseguiti in modo efficiente. Ad esempio, [dcount()](/azure/kusto/query/dcount-aggfunction) utilizza l'algoritmo HyperLogLog per fornire una stima di chiusura al conteggio distinto di set di dati di grandi dimensioni senza contare effettivamente ogni valore; le funzioni percentili stanno facendo approssimazioni simili utilizzando l'algoritmo percentile rango più vicino. Molti dei comandi includono parametri facoltativi per ridurne l'impatto. Ad esempio, la funzione [makeset()](/azure/kusto/query/makeset-aggfunction) dispone di un parametro facoltativo per definire la dimensione massima impostata, che influisce in modo significativo sulla CPU e sulla memoria.

[I](/azure/kusto/query/joinoperator?pivots=azuremonitor) comandi di join e [riepilogo](/azure/kusto/query/summarizeoperator) possono causare un utilizzo elevato della CPU durante l'elaborazione di un set di dati di grandi dimensioni. La loro complessità è direttamente correlata al numero di valori possibili, denominati *cardinalità*, delle colonne che vengono eseguite come `by` attributi in summarize o as the join. Per la spiegazione e l'ottimizzazione di join e riepilogo, vedere i relativi articoli di documentazione e suggerimenti sull'ottimizzazione.

Ad esempio, le query seguenti producono esattamente lo stesso risultato perché **CounterPath** è sempre mappato uno a uno a **CounterName** e **ObjectName**. Il secondo è più efficiente in quanto la dimensione di aggregazione è più piccola:The second one is more efficient as the aggregation dimension is smaller:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

L'utilizzo della CPU potrebbe essere influenzato anche dal punto in cui le condizioni o le colonne estese che richiedono un utilizzo intensivo dell'elaborazione. Tutti i confronti di stringhe semplici, ad esempio [uguale,](/azure/kusto/query/datatypes-string-operators) e [startswith](/azure/kusto/query/datatypes-string-operators) hanno all'incirca lo stesso impatto sulla CPU, mentre le corrispondenze di testo avanzate hanno un impatto maggiore. In particolare, l'operatore [has](/azure/kusto/query/datatypes-string-operators) è più efficiente dell'operatore [contains.](/azure/kusto/query/datatypes-string-operators) A causa delle tecniche di gestione delle stringhe, è più efficiente cercare stringhe più lunghe di quattro caratteri rispetto alle stringhe brevi.

Ad esempio, le query seguenti producono risultati simili, a seconda dei criteri di denominazione del computer, ma la seconda è più efficiente:For example, the following queries produce similar results, depending on Computer naming policy, but the second one is more efficient:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Questo indicatore presenta solo la CPU del cluster immediato. Nella query su più aree, rappresenterebbe solo una delle aree. Nella query con più aree di lavoro, potrebbe non includere tutte le aree di lavoro.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>Evitare l'analisi XML e JSON completa quando funziona l'analisi delle stringhe
L'analisi completa di un oggetto XML o JSON può consumare risorse di CPU e memoria elevate. In molti casi, quando sono necessari solo uno o due parametri e gli oggetti XML o JSON sono semplici, è più semplice analizzarli come stringhe utilizzando [l'operatore di analisi](/azure/kusto/query/parseoperator) o altre tecniche di analisi del [testo](/azure/azure-monitor/log-query/parse-text). L'aumento delle prestazioni sarà più significativo con l'aumento del numero di record nell'oggetto XML o JSON. È essenziale quando il numero di record raggiunge decine di milioni.

Ad esempio, la query seguente restituirà esattamente gli stessi risultati delle query precedenti senza eseguire l'analisi XML completa. Si noti che fa alcune supposizioni sulla struttura del file XML, ad esempio che FilePath elemento viene dopo FileHash e nessuno di essi dispone di attributi. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>Dati utilizzati per la query elaborata

Un fattore critico nell'elaborazione della query è il volume di dati analizzati e utilizzati per l'elaborazione della query. Azure Data Explorer usa ottimizzazioni aggressive che riducono drasticamente il volume di dati rispetto ad altre piattaforme di dati. Tuttavia, esistono fattori critici nella query che possono influire sul volume di dati utilizzato.

In Registri di monitoraggio di Azure la colonna **TimeGenerated** viene usata per indicizzare i dati. Limitare i valori **TimeGenerated** al più stretto possibile un intervallo consentirà di ottenere un miglioramento significativo delle prestazioni delle query limitando in modo significativo la quantità di dati da elaborare.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Evitare l'uso non necessario degli operatori di ricerca e sindacalità

Un altro fattore che aumenta i dati che vengono processati è l'uso di un numero elevato di tabelle. Ciò si `search *` `union *` verifica in genere quando vengono utilizzati i comandi e . Questi comandi forzano il sistema a valutare ed eseguire la scansione dei dati da tutte le tabelle nell'area di lavoro. In alcuni casi, potrebbero essere presenti centinaia di tabelle nell'area di lavoro. Cercate di evitare il più possibile l'utilizzo di "search" o qualsiasi ricerca senza decanarlo a una tabella specifica.

Ad esempio, le query seguenti producono esattamente lo stesso risultato, ma l'ultima è di gran lunga la più efficiente:For example, the following queries produce exactly the same result but the last one is di gran lunga the most efficient:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Aggiungere filtri anticipati alla query

Un altro metodo per ridurre il volume di dati consiste nell'avere le condizioni [in cui](/azure/kusto/query/whereoperator) le condizioni nelle prime fasi della query. La piattaforma Azure Data Explorer include una cache che consente di sapere quali partizioni includono dati rilevanti per una condizione where specifica. Ad esempio, se `where EventID == 4624` una query contiene quindi distribuirebbe la query solo ai nodi che gestiscono le partizioni con eventi corrispondenti.

Le query di esempio seguenti producono esattamente lo stesso risultato, ma la seconda è più efficiente:The following example queries produce exactly the same result but the second one is more efficient:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Ridurre il numero di colonne recuperate

Poiché Azure Data Explorer è un archivio dati a colonne, il recupero di ogni colonna è indipendente dagli altri. Il numero di colonne recuperate influisce direttamente sull'intero volume di dati. È necessario includere nell'output solo le colonne necessarie [riepilogando](/azure/kusto/query/summarizeoperator) i risultati o [proiettando](/azure/kusto/query/projectoperator) le colonne specifiche. Azure Data Explorer include diverse ottimizzazioni per ridurre il numero di colonne recuperate. Se determina che una colonna non è necessaria, ad esempio se non vi viene fatto riferimento nel comando [summarize,](/azure/kusto/query/summarizeoperator) non la recupererà.

Ad esempio, la seconda query può elaborare tre volte più dati poiché è necessario recuperare non una colonna ma tre:For example, the second query may process three times more data since it needs to fetch not one column but three:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Intervallo di tempo della query elaborata

Tutti i log nei log di Monitoraggio di Azure vengono partizionati in base alla colonna **TimeGenerated.All** logs in Azure Monitor Logs are partitioned according to the TimeGenerated column. Il numero di partizioni a cui si accede è direttamente correlato all'intervallo di tempo. La riduzione dell'intervallo di tempo è il modo più efficiente per assicurare l'esecuzione di una query di prompt.

L'intervallo di tempo può essere impostato usando il selettore dell'intervallo di tempo nella schermata Log Analytics, come descritto in [Log query scope and time range in Azure Monitor Log Analytics](scope.md#time-range). Questo è il metodo consigliato in quanto l'intervallo di tempo selezionato viene passato al back-end utilizzando i metadati della query. 

Un metodo alternativo consiste nell'includere in modo esplicito una condizione [where](/azure/kusto/query/whereoperator) su **TimeGenerated** nella query. È necessario utilizzare questo metodo perché assicura che l'intervallo di tempo sia fisso, anche quando la query viene utilizzata da un'interfaccia diversa.
È necessario assicurarsi che in tutte le parti della query siano disponibili filtri **TimeGenerated.** Quando una query include sottoquery il recupero di dati da varie tabelle o dalla stessa tabella, ognuna deve includere la propria condizione [where.](/azure/kusto/query/whereoperator)

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Assicurarsi che per tutte le sottoquery sia stato definito il filtro TimeGenerated

Ad esempio, nella query seguente, mentre la tabella **Perf** verrà analizzata solo per l'ultimo giorno, la tabella **Heartbeat** verrà analizzata per tutta la cronologia, che potrebbe richiedere fino a due anni:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Un caso comune in cui si verifica un errore di questo tipo è quando [arg_max()](/azure/kusto/query/arg-max-aggfunction) viene utilizzato per trovare l'occorrenza più recente. Ad esempio:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Questo può essere facilmente corretto aggiungendo un filtro temporale nella query interna:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Un altro esempio per questo errore è quando si esegue il filtro dell'ambito di tempo subito dopo [un'unione](/azure/kusto/query/unionoperator?pivots=azuremonitor) su più tabelle. Quando si esegue l'unione, ogni sottoquery deve avere come ambito. È possibile utilizzare [let](/azure/kusto/query/letstatement) istruzione per garantire la coerenza dell'ambito.

Ad esempio, la query seguente analirà tutti i dati nelle tabelle *Heartbeat* e *Perf,* non solo nell'ultimo giorno:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Questa query deve essere corretta come segue:This query should be fixed as follows:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Limitazioni di misurazione dell'intervallo di tempo

La misurazione è sempre maggiore del tempo effettivo specificato. Ad esempio, se il filtro della query è di 7 giorni, il sistema potrebbe eseguire la scansione 7,5 o 8,1 giorni. Ciò è dovuto al fatto che il sistema sta partizionando i dati in blocchi in dimensioni variabili. Per garantire che tutti i record rilevanti vengono analizzati, esegue la scansione dell'intera partizione che potrebbe coprire diverse ore e anche più di un giorno.

Ci sono diversi casi in cui il sistema non può fornire una misurazione accurata dell'intervallo di tempo. Ciò si verifica nella maggior parte dei casi in cui l'estensione della query si estende meno di un giorno o in query con più aree di lavoro.


> [!IMPORTANT]
> Questo indicatore presenta solo i dati elaborati nel cluster immediato. Nella query su più aree, rappresenterebbe solo una delle aree. Nella query con più aree di lavoro, potrebbe non includere tutte le aree di lavoro.

## <a name="age-of-processed-data"></a>Età dei dati trattati
Azure Data Explorer usa diversi livelli di archiviazione: dischi SSD locali in memoria e BLOB di Azure molto più lenti. Più recenti sono i dati, maggiore è la probabilità che vengano archiviati in un livello più performante con latenza inferiore, riducendo la durata e la CPU della query. Oltre ai dati stessi, il sistema dispone anche di una cache per i metadati. Più vecchi sono i dati, minore è la probabilità che i metadati siano nella cache.

Mentre alcune query richiedono l'utilizzo di dati precedenti, ci sono casi in cui i dati obsoleti vengono utilizzati per errore. Ciò si verifica quando le query vengono eseguite senza fornire intervallo di tempo nei metadati e non tutti i riferimenti di tabella includono il filtro nella colonna **TimeGenerated.** In questi casi, il sistema analirà tutti i dati archiviati in tale tabella. Quando la conservazione dei dati è lunga, può coprire lunghi intervalli di tempo e quindi dati che sono vecchi come il periodo di conservazione dei dati.

Tali casi possono essere ad esempio:

- Non impostare l'intervallo di tempo in Log Analytics con una sottoquery che non è limitata. Vedere l'esempio precedente.
- Utilizzo dell'API senza i parametri facoltativi dell'intervallo di tempo.
- Uso di un client che non forza un intervallo di tempo, ad esempio il connettore di Power BI.

Vedere esempi e note nella sezione pervious in quanto sono rilevanti anche in questo caso.

## <a name="number-of-regions"></a>Numero di regioni
Esistono diverse situazioni in cui una singola query può essere eseguita in diverse aree:There are several situations where a single query might be executed across different regions:

- Quando più aree di lavoro sono elencate in modo esplicito e si trovano in aree diverse.
- Quando una query con ambito di risorsa recupera i dati e i dati vengono archiviati in più aree di lavoro che si trovano in aree diverse.

L'esecuzione di query tra aree richiede che il sistema serializzi e trasferisca blocchi di dati intermedi di grandi dimensioni di dati intermedi che in genere sono molto più grandi dei risultati finali della query. Limita inoltre la capacità del sistema di eseguire ottimizzazioni, euristica e utilizzare le cache.
Se non vi è alcun motivo reale per eseguire la scansione di tutte queste aree, è necessario modificare l'ambito in modo che copra un numero inferiore di regioni. Se l'ambito della risorsa è ridotto al minimo ma vengono utilizzate ancora molte aree, è possibile che si verifichi una configurazione errata. Ad esempio, i log di controllo e le impostazioni di diagnostica vengono inviati a aree di lavoro diverse in aree diverse o sono presenti più configurazioni delle impostazioni di diagnostica. 

> [!IMPORTANT]
> Quando una query viene eseguita in più aree, le misurazioni della CPU e dei dati non saranno accurate e rappresenteranno la misurazione solo in una delle aree.

## <a name="number-of-workspaces"></a>Numero di aree di lavoro
Le aree di lavoro sono contenitori logici utilizzati per separare e amministrare i dati di log. Il back-end ottimizza i posizionamenti dell'area di lavoro nei cluster fisici all'interno dell'area selezionata.

L'utilizzo di più aree di lavoro può derivare da: 

- Dove sono elencate in modo esplicito più aree di lavoro.
- Quando una query con ambito di risorsa recupera i dati e i dati vengono archiviati in più aree di lavoro.
 
L'esecuzione tra aree e intercluster delle query richiede che il sistema venga serializzato e trasferito nei blocchi back-end di dati intermedi che in genere sono molto più grandi dei risultati finali della query. Limita inoltre la capacità del sistema di eseguire ottimizzazioni, euristica e utilizzo delle cache.

> [!IMPORTANT]
> In alcuni scenari con più aree di lavoro, le misurazioni della CPU e dei dati non saranno accurate e rappresenteranno la misurazione solo per poche aree di lavoro.

## <a name="parallelism"></a>Parallelismo
Registri di monitoraggio di Azure utilizza cluster di grandi dimensioni di Azure Data Explorer per eseguire query e questi cluster variano in scala, potenzialmente arrivando a decine di nodi di calcolo. Il sistema ridimensiona automaticamente i cluster in base alla logica e alla capacità di posizionamento dell'area di lavoro.

Per eseguire in modo efficiente una query, questa viene partizionata e distribuita ai nodi di calcolo in base ai dati necessari per l'elaborazione. Ci sono alcune situazioni in cui il sistema non può farlo in modo efficiente. Ciò può portare a una lunga durata della query. 

I comportamenti delle query che possono ridurre il parallelismo includono:Query behaviors that can reduce parallelism include:

- Utilizzo delle funzioni di serializzazione e finestra, ad esempio [l'operatore serialize](/azure/kusto/query/serializeoperator), [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction)e le funzioni [di riga.](/azure/kusto/query/rowcumsumfunction) In alcuni di questi casi è possibile usare le serie temporali e le funzioni di analisi utente. La serializzazione inefficiente può verificarsi anche se vengono utilizzati i seguenti operatori non alla fine della query: [range](/azure/kusto/query/rangeoperator), [sort](/azure/kusto/query/sortoperator), [order](/azure/kusto/query/orderoperator), [top](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-    L'utilizzo della funzione di aggregazione [dcount()](/azure/kusto/query/dcount-aggfunction) forza il sistema a disporre di una copia centrale dei valori distinti. Quando la scala dei dati è elevata, è consigliabile usare i parametri facoltativi della funzione dcount per ridurre la precisione.
-    In molti casi, l'operatore [di join](/azure/kusto/query/joinoperator?pivots=azuremonitor) riduce il parallelismo generale. Esaminare il join casuale come alternativa quando le prestazioni sono problematiche.
-    Nelle query con ambito di risorse, i controlli RBAC pre-esecuzione possono indugiare in situazioni in cui è presente un numero molto elevato di assegnazioni RBAC. Ciò può portare a controlli più lunghi che comporterebbero un minore parallelismo. Ad esempio, una query viene eseguita in una sottoscrizione in cui sono presenti migliaia di risorse e ogni risorsa ha molte assegnazioni di ruolo a livello di risorsa, non nella sottoscrizione o nel gruppo di risorse.
-    Se una query sta elaborando piccoli blocchi di dati, il suo parallelismo sarà basso in quanto il sistema non la distribuirà in molti nodi di calcolo.



## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di riferimento per il linguaggio di query Kusto](/azure/kusto/query/).
