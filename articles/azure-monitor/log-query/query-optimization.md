---
title: Ottimizzare le query di log in monitoraggio di Azure
description: Procedure consigliate per l'ottimizzazione delle query di log in monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/28/2019
ms.openlocfilehash: e5c3da94cf2440b30dc59fe20bc51a34095f7d5f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269052"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Ottimizzare le query di log in monitoraggio di Azure
Log di monitoraggio di Azure usa [Esplora dati di Azure (ADX)](/azure/data-explorer/) per archiviare i dati di log ed eseguire query per l'analisi di tali dati. Crea, gestisce e gestisce i cluster ADX per l'utente e li ottimizza per il carico di lavoro di analisi dei log. Quando si esegue una query, questa viene ottimizzata e indirizzata al cluster ADX appropriato che archivia i dati dell'area di lavoro. Sia i log di monitoraggio di Azure che Azure Esplora dati usano molti meccanismi di ottimizzazione automatica delle query. Sebbene le ottimizzazioni automatiche forniscano un incremento significativo, in alcuni casi è possibile migliorare notevolmente le prestazioni di esecuzione delle query. Questo articolo illustra le considerazioni sulle prestazioni e alcune tecniche per risolverle.

La maggior parte delle tecniche è comune per le query che vengono eseguite direttamente nei log di Azure Esplora dati e di monitoraggio di Azure, anche se sono presenti alcune considerazioni univoche sui log di monitoraggio di Azure illustrate qui. Per altri suggerimenti sull'ottimizzazione di Azure Esplora dati, vedere procedure consigliate per le [query](/azure/kusto/query/best-practices).

Query ottimizzate:

- Esecuzione più veloce, riduzione della durata complessiva dell'esecuzione della query.
- Hanno una minore probabilità di essere limitate o rifiutate.

È necessario prestare particolare attenzione alle query che vengono usate per l'utilizzo ricorrente e di tipo imprevisto, ad esempio dashboard, avvisi, app per la logica e Power BI. L'impatto di una query inefficace in questi casi è sostanziale.

## <a name="query-performance-pane"></a>Riquadro prestazioni query
Dopo aver eseguito una query in Log Analytics, fare clic sulla freccia rivolta verso il basso sopra i risultati della query per visualizzare il riquadro prestazioni query che mostra i risultati di diversi indicatori di prestazioni per la query. Questi indicatori di prestazioni sono descritti nella sezione seguente.

![Riquadro prestazioni query](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Indicatori di prestazioni delle query

Per ogni query eseguita sono disponibili gli indicatori di prestazioni della query seguenti:

- [CPU totale](#total-cpu): calcolo globale usato per elaborare la query in tutti i nodi di calcolo. Rappresenta il tempo usato per l'elaborazione, l'analisi e il recupero dei dati. 

- [Dati usati per la query elaborata](#data-used-for-processed-query): dati complessivi a cui è stato eseguito l'accesso per elaborare la query. Influenzato dalla dimensione della tabella di destinazione, dall'intervallo di tempo utilizzato, dai filtri applicati e dal numero di colonne a cui si fa riferimento.

- [Intervallo di tempo della query elaborata](#time-span-of-the-processed-query): gap tra i dati più recenti e meno recenti a cui è stato eseguito l'accesso per elaborare la query. Influenzato dall'intervallo di tempo esplicito specificato per la query.

- [Età dei dati elaborati](#age-of-processed-data): il gap tra ora e i dati meno recenti a cui è stato eseguito l'accesso per elaborare la query. Influisce fortemente sull'efficienza del recupero dei dati.

- [Numero di aree di lavoro: numero di aree di](#number-of-workspaces)lavoro accessibili durante l'elaborazione della query a causa della selezione implicita o esplicita.

- [Numero di aree: numero di](#number-of-regions)aree a cui è stato eseguito l'accesso durante l'elaborazione delle query in base a una selezione implicita o esplicita delle aree di lavoro. Le query in più aree sono molto meno efficienti e gli indicatori di prestazioni presentano una copertura parziale.

- [Parallelism](#parallelism): indica il grado di esecuzione della query su più nodi da parte del sistema. Pertinente solo per le query con utilizzo elevato della CPU. Influenzato dall'utilizzo di funzioni e operatori specifici.


## <a name="total-cpu"></a>CPU totale
CPU di calcolo effettiva investita per elaborare la query in tutti i nodi di elaborazione delle query. Poiché la maggior parte delle query viene eseguita su un numero elevato di nodi, questo sarà in genere molto più grande del tempo impiegato per l'esecuzione della query. 

Tempo di elaborazione query dedicato a:
- Recupero dati: il recupero dei dati precedenti utilizzerà più tempo del recupero dei dati recenti.
- Elaborazione dei dati: logica e valutazione dei dati. 

Oltre al tempo impiegato nei nodi di elaborazione delle query, è necessario tempo aggiuntivo per i log di monitoraggio di Azure: autenticare l'utente e verificare che sia autorizzato ad accedere a questi dati, individuare l'archivio dati, analizzare la query e allocare l'elaborazione delle query nodi. Questo tempo non è incluso nel tempo totale della CPU della query.

Alcuni comandi e funzioni di query sono pesanti nell'utilizzo della CPU. Questa operazione è particolarmente valida per i comandi che analizzano JSON e XML o estraggono espressioni regolari complesse. Tale analisi può avvenire in modo esplicito tramite [parse_json ()](/azure/kusto/query/parsejsonfunction) o funzioni [parse_xml ()](/azure/kusto/query/parse-xmlfunction) o in modo implicito quando si fa riferimento a colonne dinamiche.

Queste funzioni utilizzano la CPU in proporzione al numero di righe elaborate. L'ottimizzazione più efficiente consiste nell'aggiungere le condizioni WHERE all'inizio della query che possono filtrare il maggior numero possibile di record prima dell'esecuzione della funzione con utilizzo intensivo della CPU.

Ad esempio, le query seguenti producono esattamente lo stesso risultato, ma la seconda è di gran lunga la più efficiente come condizione [where](/azure/kusto/query/whereoperator) prima che l'analisi escluda molti record:

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
```

Le query che contengono clausole [where](/azure/kusto/query/whereoperator) su una colonna valutata anziché su colonne fisicamente presenti nel set di dati perdono l'efficienza. Il filtro sulle colonne valutate impedisce alcune ottimizzazioni del sistema quando vengono gestiti set di dati di grandi dimensioni.
Ad esempio, le query seguenti producono esattamente lo stesso risultato, ma la seconda è più efficiente, perché la condizione [where](/azure/kusto/query/whereoperator) si riferisce a una colonna predefinita

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

Mentre alcuni comandi di aggregazione come [Max ()](/azure/kusto/query/max-aggfunction), [Sum ()](/azure/kusto/query/sum-aggfunction), [Count ()](/azure/kusto/query/count-aggfunction)e [AVG ()](/azure/kusto/query/avg-aggfunction) hanno un basso effetto sulla CPU dovuti alla logica, altri sono più complessi e includono euristiche e stime che ne consentono l'esecuzione in modo efficiente. [DCount ()](/azure/kusto/query/dcount-aggfunction) , ad esempio, usa l'algoritmo HyperLogLog per fornire una stima di chiusura per il conteggio distinto di set di dati di grandi dimensioni senza contare effettivamente ogni valore. le funzioni percentile eseguono approssimazioni simili usando l'algoritmo percentile di rango più vicino. Molti dei comandi includono parametri facoltativi per ridurne l'effetto. Ad esempio, la funzione [maket ()](/azure/kusto/query/makeset-aggfunction) dispone di un parametro facoltativo per definire la dimensione massima del set, che influiscono significativamente sulla CPU e sulla memoria.

I comandi di [join](/azure/kusto/query/joinoperator?pivots=azuremonitor) e [Riepilogo](/azure/kusto/query/summarizeoperator) possono causare un utilizzo elevato della CPU durante l'elaborazione di un set di dati di grandi dimensioni. La relativa complessità è direttamente correlata al numero di valori possibili, denominati *cardinalità*, delle colonne utilizzate come `by` in riepilogo o come attributi di join. Per la spiegazione e l'ottimizzazione di join e riepilogo, vedere gli articoli della documentazione e i suggerimenti per l'ottimizzazione.

Ad esempio, le query seguenti producono esattamente lo stesso risultato perché **CounterPath** è sempre un mapping uno-a-uno con **counterName** e **nomeoggetto**. Il secondo è più efficiente perché la dimensione di aggregazione è più piccola:

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

L'utilizzo della CPU potrebbe anche essere influenzato dalle condizioni di Where o dalle colonne estese che richiedono un calcolo intensivo. Tutti i confronti di stringhe semplici, ad esempio [Equal = =](/azure/kusto/query/datatypes-string-operators) e [StartsWith](/azure/kusto/query/datatypes-string-operators) , presentano approssimativamente lo stesso effetto della CPU mentre le corrispondenze di testo avanzate hanno un maggiore effetto. In particolare, l'operatore [has](/azure/kusto/query/datatypes-string-operators) è più efficiente dell'operatore [Contains](/azure/kusto/query/datatypes-string-operators) . A causa delle tecniche di gestione delle stringhe, è più efficiente cercare stringhe più lunghe di quattro caratteri rispetto alle stringhe brevi.

Ad esempio, le query seguenti producono risultati simili, in base ai criteri di denominazione dei computer, ma la seconda è più efficiente:

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
> Questo indicatore presenta solo la CPU del cluster immediato. In una query in più aree, rappresenta solo una delle aree. In una query con più aree di lavoro potrebbe non includere tutte le aree di lavoro.


## <a name="data-used-for-processed-query"></a>Dati usati per la query elaborata

Un fattore critico nell'elaborazione della query è costituito dal volume di dati analizzati e utilizzati per l'elaborazione delle query. Azure Esplora dati USA ottimizzazioni aggressive che riducono drasticamente il volume di dati rispetto ad altre piattaforme di dati. Tuttavia, nella query sono presenti fattori cruciali che possono influiscono sul volume di dati utilizzato.
Nei log di monitoraggio di Azure, la colonna **TimeGenerated** viene usata come metodo per indicizzare i dati. La limitazione dei valori di **TimeGenerated** a un intervallo più limitato possibile comporta un miglioramento significativo delle prestazioni delle query, limitando significativamente la quantità di dati da elaborare.

Un altro fattore che aumenta i dati elaborati è l'utilizzo di un numero elevato di tabelle. Questa situazione si verifica in genere quando vengono usati `search *` e `union *` comandi. Questi comandi forzano il sistema a valutare e analizzare i dati di tutte le tabelle nell'area di lavoro. In alcuni casi, potrebbero essere presenti centinaia di tabelle nell'area di lavoro. Provare a evitare il più possibile utilizzando "Search *" o qualsiasi ricerca senza l'ambito di una tabella specifica.

Ad esempio, le query seguenti producono esattamente lo stesso risultato, ma l'ultimo è di gran lunga il più efficiente:

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

Un altro metodo per ridurre il volume di dati consiste nel disporre di condizioni [where](/azure/kusto/query/whereoperator) all'inizio della query. La piattaforma Azure Esplora dati include una cache che consente di individuare le partizioni che includono i dati rilevanti per una specifica condizione WHERE. Se, ad esempio, una query contiene `where EventID == 4624`, la query verrà distribuita solo ai nodi che gestiscono le partizioni con eventi corrispondenti.

Le query di esempio seguenti producono esattamente lo stesso risultato, ma la seconda è più efficiente:

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

Poiché Esplora dati di Azure è un archivio dati a colonne, il recupero di ogni colonna è indipendente dagli altri. Il numero di colonne recuperate influisce direttamente sul volume di dati generale. È necessario includere solo le colonne nell'output necessarie [Riepilogando](/azure/kusto/query/summarizeoperator) i risultati o [proiettando](/azure/kusto/query/projectoperator) le colonne specifiche. Azure Esplora dati offre diverse ottimizzazioni per ridurre il numero di colonne recuperate. Se determina che una colonna non è necessaria, ad esempio se non vi viene fatto riferimento nel comando [riepilogate](/azure/kusto/query/summarizeoperator) , non verrà recuperata.

La seconda query, ad esempio, può elaborare tre volte più dati perché è necessario recuperare una colonna, ma tre:

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

Tutti i log nei log di monitoraggio di Azure vengono partizionati in base alla colonna **TimeGenerated** . Il numero di partizioni a cui si accede è direttamente correlato all'intervallo di tempo. La riduzione dell'intervallo di tempo è il modo più efficiente per garantire l'esecuzione di una query di richiesta.

L'intervallo di tempo può essere impostato usando il selettore dell'intervallo di tempo nella schermata Log Analytics come descritto nell' [ambito della query di log e nell'intervallo di tempo in monitoraggio di Azure log Analytics](scope.md#time-range). Si tratta del metodo consigliato perché l'intervallo di tempo selezionato viene passato al back-end usando i metadati della query. 

Un metodo alternativo consiste nel includere in modo esplicito una condizione [where](/azure/kusto/query/whereoperator) in **TimeGenerated** nella query. È consigliabile utilizzare questo metodo per garantire che l'intervallo di tempo sia fisso, anche quando la query viene utilizzata da un'interfaccia diversa.
È necessario assicurarsi che tutte le parti della query dispongano di filtri **TimeGenerated** . Quando in una query sono presenti sottoquery che recuperano dati da diverse tabelle o dalla stessa tabella, ciascuna di esse deve includere la relativa condizione [where](/azure/kusto/query/whereoperator) .

Ad esempio, nella query seguente, mentre la tabella delle **prestazioni** verrà analizzata solo per l'ultimo giorno, la tabella **heartbeat** verrà analizzata per tutta la cronologia, che può essere fino a due anni:

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

Un caso comune in cui si verifica un errore di questo tipo è quando viene usato [ARG_MAX ()](/azure/kusto/query/arg-max-aggfunction) per trovare l'occorrenza più recente. Ad esempio:

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

Questa operazione può essere facilmente corretta aggiungendo un filtro temporale nella query interna:

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

Un altro esempio di questo errore è quando si esegue il filtro dell'ambito temporale subito dopo un' [Unione](/azure/kusto/query/unionoperator?pivots=azuremonitor) su più tabelle. Quando si esegue l'Unione, è necessario definire l'ambito di ogni sottoquery. È possibile utilizzare l'istruzione [Let](/azure/kusto/query/letstatement) per garantire la coerenza dell'ambito.

Ad esempio, la query seguente analizzerà tutti i dati nelle tabelle *heartbeat* e *Perf* , non solo nell'ultimo giorno:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Questa query deve essere corretta nel modo seguente:

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

La misura è sempre maggiore del tempo effettivo specificato. Se, ad esempio, il filtro della query è di 7 giorni, il sistema potrebbe analizzare 7,5 o 8,1 giorni. Questo perché il sistema suddivide i dati in blocchi in dimensioni variabili. Per assicurarsi che tutti i record rilevanti vengano analizzati, analizza l'intera partizione che può coprire diverse ore e persino più di un giorno.

Esistono diversi casi in cui il sistema non è in grado di fornire una misurazione accurata dell'intervallo di tempo. Questa situazione si verifica nella maggior parte dei casi in cui l'intervallo della query è inferiore a un giorno o in query con più aree di lavoro.


> [!IMPORTANT]
> Questo indicatore presenta solo i dati elaborati nel cluster immediato. In una query in più aree, rappresenta solo una delle aree. In una query con più aree di lavoro potrebbe non includere tutte le aree di lavoro.

## <a name="age-of-processed-data"></a>Età dei dati elaborati
Azure Esplora dati usa diversi livelli di archiviazione: in memoria, dischi SSD locali e BLOB di Azure più lenti. Più recenti sono i dati, maggiore è la probabilità che vengano archiviati in un livello più efficiente con latenza inferiore, riducendo la durata e la CPU della query. Oltre ai dati stessi, il sistema dispone anche di una cache per i metadati. Con i dati meno recenti, minore è la probabilità che i metadati si trovino nella cache.

Sebbene alcune query richiedano l'utilizzo di dati obsoleti, esistono casi in cui i dati obsoleti vengono utilizzati per errore. Questo errore si verifica quando le query vengono eseguite senza specificare un intervallo di tempo nei relativi metadati e non tutti i riferimenti alle tabelle includono Filter per la colonna **TimeGenerated** . In questi casi, il sistema analizzerà tutti i dati archiviati in tale tabella. Quando la conservazione dei dati è lunga, può coprire intervalli di tempo prolungati e quindi i dati che hanno un periodo di conservazione dei dati precedente.

Questi casi possono essere ad esempio:

- La mancata impostazione dell'intervallo di tempo in Log Analytics con una sottoquery che non è limitata. Vedere l'esempio precedente.
- Uso dell'API senza i parametri facoltativi dell'intervallo di tempo.
- Uso di un client che non impone un intervallo di tempo, ad esempio il connettore Power BI.

Vedere gli esempi e le note nella sezione precedente, in quanto sono rilevanti anche in questo caso.

## <a name="number-of-regions"></a>Numero di aree
Esistono diverse situazioni in cui una singola query può essere eseguita in aree diverse:

- Quando diverse aree di lavoro sono elencate in modo esplicito e si trovano in aree diverse.
- Quando una query con ambito di risorsa recupera dati e i dati vengono archiviati in più aree di lavoro che si trovano in aree diverse.

Per l'esecuzione di query tra aree diverse è necessario che il sistema esegua la serializzazione e il trasferimento nel back-end di grandi quantità di dati intermedi che in genere sono molto più grandi dei risultati finali della query. Limita inoltre la capacità del sistema di eseguire ottimizzazioni, euristiche e usare cache.
Se non esiste un motivo reale per analizzare tutte queste aree, è necessario modificare l'ambito in modo che ricopra un minor numero di aree. Se l'ambito della risorsa è ridotto a icona ma vengono utilizzate ancora molte aree, potrebbe verificarsi un errore di configurazione. Ad esempio, i log di controllo e le impostazioni di diagnostica vengono inviati a diverse aree di lavoro in aree diverse o sono presenti più configurazioni di impostazioni di diagnostica. 

> [!IMPORTANT]
> Quando una query viene eseguita in diverse aree, le misurazioni di CPU e dati non saranno accurate e rappresenteranno la misurazione solo in una delle aree.

## <a name="number-of-workspaces"></a>Numero di aree di lavoro
Le aree di lavoro sono contenitori logici usati per separare e amministrare i dati dei log. Il back-end ottimizza i posizionamenti dell'area di lavoro nei cluster fisici all'interno dell'area selezionata.

L'uso di più aree di lavoro può essere causato da: 

- Dove sono elencate in modo esplicito diverse aree di lavoro.
- Quando una query con ambito di risorsa recupera dati e i dati vengono archiviati in più aree di lavoro.
 
L'esecuzione di query tra più aree e tra cluster richiede che il sistema esegua la serializzazione e il trasferimento nei blocchi back-end di grandi dimensioni di dati intermedi che in genere sono molto più grandi dei risultati finali della query. Limita inoltre la capacità di sistema di eseguire ottimizzazioni, euristiche e utilizzo di cache.

> [!IMPORTANT]
> In alcuni scenari con più aree di lavoro, le misurazioni di CPU e dati non saranno accurate e rappresenteranno la misurazione solo per alcune delle aree di lavoro.

## <a name="parallelism"></a>Parallelismo
I log di monitoraggio di Azure usano cluster di grandi dimensioni di Azure Esplora dati per eseguire query e questi cluster variano in scala, ottenendo potenzialmente fino a dozzine di nodi di calcolo. Il sistema ridimensiona automaticamente i cluster in base alla logica e alla capacità di posizionamento dell'area di lavoro.

Per eseguire una query in modo efficiente, viene partizionata e distribuita ai nodi di calcolo in base ai dati necessari per l'elaborazione. In alcune situazioni il sistema non è in grado di eseguire questa operazione in modo efficiente. Questo può causare una lunga durata della query. 

I comportamenti di query che possono ridurre il parallelismo includono:

- Utilizzo di funzioni di serializzazione e finestra come l' [operatore Serialize](/azure/kusto/query/serializeoperator), [Next ()](/azure/kusto/query/nextfunction), [Prev ()](/azure/kusto/query/prevfunction)e le funzioni [Row](/azure/kusto/query/rowcumsumfunction) . In alcuni di questi casi è possibile usare le funzioni Time Series e analisi utente. La serializzazione inefficiente può verificarsi anche se vengono usati gli operatori seguenti non alla fine della query: [Range](/azure/kusto/query/rangeoperator), [Sort](/azure/kusto/query/sortoperator), [Order](/azure/kusto/query/orderoperator), [Top](/azure/kusto/query/topoperator), [Top-battitor](/azure/kusto/query/tophittersoperator), [GetSchema](/azure/kusto/query/getschemaoperator).
-   L'utilizzo della funzione di aggregazione [DCount ()](/azure/kusto/query/dcount-aggfunction) impone al sistema la copia centrale dei valori distinti. Quando la scala dei dati è elevata, è consigliabile usare i parametri facoltativi della funzione DCount per ridurre la precisione.
-   In molti casi, l'operatore di [join](/azure/kusto/query/joinoperator?pivots=azuremonitor) abbassa il parallelismo generale. Esaminare il join casuale come alternativa quando le prestazioni sono problematiche.
-   Nelle query con ambito di risorse, i controlli di pre-esecuzione RBAC possono rimanere in situazioni in cui è presente un numero molto elevato di assegnazioni RBAC. Questa operazione può comportare controlli più lunghi che comporterebbero un parallelismo inferiore. Ad esempio, una query viene eseguita su una sottoscrizione in cui sono presenti migliaia di risorse e ogni risorsa ha numerose assegnazioni di ruolo a livello di risorsa, non nella sottoscrizione o nel gruppo di risorse.
-   Se una query elabora piccoli blocchi di dati, il suo parallelismo sarà basso perché il sistema non lo distribuisce in molti nodi di calcolo.



## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di riferimento per il linguaggio di query kusto](/azure/kusto/query/).
