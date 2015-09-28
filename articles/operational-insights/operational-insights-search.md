<properties
   pageTitle="Ricerca di dati in Operational Insights"
   description="È possibile usare la funzionalità di ricerca log in Microsoft Azure Operational Insights per trovare i dati desiderati."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/10/2015"
   ms.author="banders" />

# Ricerca di dati in Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Un elemento fondamentale di Microsoft Azure Operational Insights è la funzionalità di ricerca log, che permette di combinare e correlare qualsiasi dato di computer da più origini nell'ambiente. Anche le soluzioni sono basate sulla ricerca log, per offrire metriche specifiche per una particolare area problematica.

Nella pagina Search è possibile creare una query e quindi, durante la ricerca, filtrare i risultati usando controlli facet. È anche possibile creare query avanzate per trasformare, filtrare e creare report sui risultati.

Le query di ricerca log più comuni sono visualizzate nella maggior parte delle pagine delle soluzioni. In tutta la console di Operational Insights è possibile fare clic sui riquadri o analizzare altri elementi per visualizzarne i dettagli usando la funzionalità di ricerca log.

In questa esercitazione verranno esaminati alcuni esempi per analizzare tutti gli elementi di base quando si usa la ricerca log.

Si inizierà con esempi semplici e pratici, tali esempi verranno quindi compilati in modo da acquisire informazioni sui casi d'uso pratici relativi all'uso della sintassi per estrarre le informazioni desiderate dai dati.

Durante le ricerche log in Operational Insights, si useranno le tecniche seguenti:

- Usare filtri di base
- Usare filtri aggiuntivi
- Modificare i risultati della ricerca
- Usare il comando measure
- Usare le funzioni max e min con il comando measure
- Usare la funzione avg con il comando measure
- Usare il comando where

Dopo aver acquisito familiarità con le tecniche di ricerca, è possibile esaminare le sezioni [Riferimento alla sintassi di ricerca](#search-syntax-reference) e [Riferimenti al campo Ricerca e ai facet](#search-field-and-facet-reference).

## Usare filtri di base

La prima cosa da sapere è che la prima parte di una query di ricerca, prima di un carattere di barra verticale "|" è sempre un *filtro*. Può essere considerato come una clausola WHERE in TSQL: determina *quali* subset di dati estrarre dall'archivio dati di Operational Insights. Per la ricerca in un archivio dati è importante specificare le caratteristiche dei dati che si desidera estrarre, pertanto è normale che una query inizi con la clausola WHERE.

I filtri più semplici che è possibile usare sono *parole chiave*, ad esempio 'error' o 'timeout' o un nome di computer. Questi tipi di query semplici restituiscono in genere diverse forme di dati all'interno dello stesso set di risultati. Questo perché Operational Insights presenta diversi *tipi* di dati nel sistema.


### Per eseguire una ricerca semplice
1. Nel portale di Operational Insights fare clic su **Search Data Explorer**. ![riquadro di ricerca](./media/operational-insights-search/overview-search.png)
2. Nel campo delle query digitare `error` e quindi fare clic su **Search**. ![errore di ricerca](./media/operational-insights-search/search-error.png) Ad esempio, la query per `error` nell'immagine seguente ha restituito 100.000 record **Event** (raccolti da Log Management), 18 record **ConfigurationAlert** (generati da Configuration Assessment) e 12 record **ConfigurationChange** (acquisiti da Change Tracking). ![risultati della ricerca](./media/operational-insights-search/results01.png)

Questi filtri non sono realmente tipi o classi di oggetti. *Type* è semplicemente un tag o una proprietà o una stringa/nome/categoria, collegata a una parte dei dati. Alcuni documenti nel sistema vengono contrassegnati come **Type:ConfigurationAlert** e alcuni vengono contrassegnati come **Type:PerfHourly** o **Type:Event** e così via. In ogni risultato della ricerca, documento, record o voce vengono visualizzate tutte le proprietà non elaborate e i relativi valori per ciascuno di tali dati ed è possibile usare i nomi dei campi per specificare nel filtro quando si desidera recuperare solo i record il cui campo presenta il valore specificato.

*Type* è in realtà solo un campo che contiene tutti i record, non è diverso dagli altri campi. Ciò è stato stabilito in base al valore del campo Type. Quel record avrà una forma o un formato diversi. Tra l'altro, **Type=PerfHourly** o **Type=Event** è anche la sintassi che è necessario conoscere per eseguire una query su aggregazioni di dati o eventi relativi alle prestazioni che si verificano ogni ora.

È possibile usare i due punti (:) o un segno di uguale (=) dopo il nome del campo e prima del valore. **Type:Event** e **Type=Event** sono equivalenti nel significato, è possibile scegliere lo stile desiderato.

Pertanto, se i record Type=PerfHourly hanno un campo denominato 'CounterName', è quindi possibile scrivere una query simile a `Type=PerfHourly CounterName="% Processor Time"`.

Questa query fornirà solo i dati sulle prestazioni in cui il nome del contatore delle prestazioni è "% Processor Time".

### Per ricercare i dati sulle prestazioni del tempo del processore
- Nel campo della query di ricerca, digitare `Type=PerfHourly CounterName="% Processor Time"`

È inoltre possibile essere più specifici e usare **InstanceName=\_'Total'** nella query, che rappresenta un contatore delle prestazioni di Windows. È possibile anche selezionare un facet e un altro **field:value**. Il filtro viene aggiunto automaticamente al filtro dell'utente nella barra di query. È possibile visualizzare il risultato nell'immagine seguente. Viene indicato dove è possibile fare clic per aggiungere **InstanceName:'\_Total'** alla query senza digitare nulla.

![facet di ricerca](./media/operational-insights-search/search-facet.png)

La query diventa `Type=PerfHourly CounterName=”% Processor Time” InstanceName=”_Total”`

In questo esempio, non è necessario specificare **Type=PerfHourly** per ottenere questo risultato. Poiché i campi CounterName e InstanceName esistono solo per record di Type=PerfHourly, la query è abbastanza specifica per restituire gli stessi risultati della query precedente, più lunga: ```
CounterName=”% Processor Time” InstanceName=”_Total”
```

Questo perché tutti i filtri nella query vengono considerati come se fossero collegati dall'operatore *AND*. In effetti, quanti più campi vengono aggiunti ai criteri, tanto più i risultati saranno di numero inferiore ma più specifici e complessi.

Ad esempio, la query `Type=Event EventLog="Windows PowerShell"` è identica a `Type=Event AND EventLog="Windows PowerShell"`. Restituisce tutti gli eventi a cui è stato effettuato l'accesso e che sono stati raccolti dal registro eventi di Windows PowerShell. Se si aggiunge un filtro più volte selezionando ripetutamente lo stesso facet, il problema è puramente descrittivo, potrebbe creare confusione nella barra di ricerca, ma restituisce comunque gli stessi risultati perché l'operatore AND implicito è sempre presente.

È possibile invertire facilmente l'operatore AND implicito usando un operatore NOT in modo esplicito. Ad esempio:

`Type:Event NOT(EventLog:"Windows PowerShell")` o l'equivalente `Type=Event EventLog!="Windows PowerShell"` restituiscono tutti gli eventi di tutti gli altri log che NON sono il log di Windows PowerShell.

In alternativa, è possibile usare altri operatori booleani, ad esempio 'OR'. La query seguente restituisce i record per cui EventLog è Application OR System.

```
EventLog=Application OR EventLog=System
```

Usando la query precedente, si otterranno le voci per entrambi i log nello stesso set di risultati.

Tuttavia, se si rimuove l'operatore OR lasciando l'operatore implicito AND, la query seguente non produrrà alcun risultato perché non è presente una voce del registro eventi che appartiene a entrambi i log. Ogni voce del registro eventi è stata scritta solo in uno dei due log.

```
EventLog=Application EventLog=System
```


## Usare filtri aggiuntivi

La query seguente restituisce le voci per 2 registri eventi per tutti i computer che hanno inviato dati.

```
EventLog=Application OR EventLog=System
```

![risultati della ricerca](./media/operational-insights-search/search-results03.png)

Se si seleziona uno dei campi o filtri la query viene limitata a un computer specifico, escludendo tutti gli altri. La query risultante sarà simile alla seguente.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Questa query equivale alla seguente, a causa dell'operatore AND implicito.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Ogni query viene considerata nell'ordine esplicito seguente. Notare le parentesi.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Come il campo del registro eventi, è possibile recuperare i dati solo per un set di computer specifici aggiungendo OR. Ad esempio:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

Analogamente ,la query seguente restituisce **% CPU Time** solo per i due computer selezionati.

```
CounterName=”% Processor Time”  AND InstanceName=”_Total” AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### Operatori booleani
Con i campi di data/ora e numerici, è possibile cercare i valori usando *maggiore di*, *minore di* e *minore o uguale a*. È possibile usare operatori semplici come >, < , >=, <= , != nella barra di ricerca della query.


È possibile eseguire una query su un registro eventi specifico per uno specifico periodo di tempo. Ad esempio, le ultime 24 ore viene espresso con la seguente espressione mnemonica.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### Per eseguire la ricerca usando un operatore booleano
- Nel campo della query di ricerca, digitare `EventLog=System TimeGenerated>NOW-24HOURS"` ![ricerca con valore booleano](./media/operational-insights-search/search-boolean.png)

Nonostante sia possibile controllare graficamente l'intervallo di tempo, e la maggior parte delle volte si desideri farlo, l'inclusione di un filtro temporale direttamente nella query presenta dei vantaggi. Ad esempio, è efficace con i dashboard, in cui è possibile sostituire l'intervallo di tempo per ogni riquadro, indipendentemente dal selettore temporale *globale* nella pagina del dashboard. Per altre informazioni, vedere l'argomento relativo alle [questioni di tempo nel dashboard](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Quando si applica il filtro per tempo, si ottengono risultati per l'*intersezione* dei due periodi di tempo: quello specificato nel portale di Operational Insights (S1) e quello specificato nella query (S2).

![intersezione](./media/operational-insights-search/intersection.png)

Ciò significa che se i periodi di tempo non si intersecano, ad esempio nel portale di Operational Insights è stato scelto **questa settimana** e nella query è stato definito **la settimana scorsa**, non esiste alcuna intersezione e non viene visualizzato alcun risultato.

Gli operatori di confronto usati per il campo TimeGenerated sono utili anche in altre situazioni. Ad esempio, con i campi numerici.

Ad esempio, poiché gli avvisi di Configuration Assessment presentano i seguenti valori di gravità:

- 0 = Informazioni
- 1 = Avviso
- 2 = Avviso critico

È possibile eseguire una query per avvisi e avvisi critici ed escludere gli avvisi informativi con la query seguente:

```
Type=ConfigurationAlert  Severity>=1
```


È inoltre possibile usare le query di intervallo. Ciò significa che è possibile specificare l'inizio e la fine dell'intervallo dei valori in una sequenza. Ad esempio, se si desidera conoscere gli eventi del registro eventi di Operations Manager dove EventID è maggiore o uguale a 2100 ma non maggiore di 2199, la query seguente restituirà tali eventi.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE]La sintassi dell'intervallo da usare è il separatore di due punti (:), field:value, e *non* il segno di uguale (=). Racchiudere le estremità inferiore e superiore dell'intervallo tra parentesi quadre e separarle con due punti (..).

## Modificare i risultati della ricerca

Quando si esegue una ricerca di dati, è opportuno ridefinire la query di ricerca e avere un buon livello di controllo sui risultati. Quando i risultati vengono recuperati, è possibile applicare comandi per trasformarli.

I comandi nelle ricerche di Operational Insights *devono* seguire il carattere di barra verticale (|). Un filtro deve essere sempre la prima parte di una stringa di query. Definisce il set di dati che si sta usando e quindi "invia" i risultati in un comando. È quindi possibile usare il carattere di barra verticale per aggiungere altri comandi. Il funzionamento è simile a quello della pipeline di Windows PowerShell.

In generale, il linguaggio di ricerca di Operational Insights tenta di seguire lo stile e le linee guida di Powershell per renderli simili per i professionisti IT e per semplificare la curva di apprendimento.

I comandi hanno nomi di verbi, pertanto è possibile stabilire facilmente quali azioni eseguono.

### Ordina

Il comando sort consente di definire l'ordinamento in base a uno o più campi. Anche se non viene usato, per impostazione predefinita, viene applicato un ordine decrescente di tempo. I risultati più recenti sono sempre nella parte superiore dei risultati della ricerca. Ciò significa che quando si esegue una ricerca con `Type=Event EventID=1234`, in realtà la query che viene eseguita automaticamente è:

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

Questo perché si tratta del tipo di esperienza con cui si ha familiarità nei log, ad esempio, nel Visualizzatore eventi di Windows.

È possibile usare il comando Sort per modificare il modo in cui vengono restituiti i risultati. Negli esempi riportati di seguito viene illustrato il funzionamento di questo comando.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Gli esempi semplici sopra riportati illustrano il funzionamento dei comandi: i comandi cambiano la forma dei risultati restituiti dal filtro.

### Limit e top
Un altro comando meno noto è LIMIT. Limit è un verbo di tipo PowerShell. Il comando Limit è, dal punto di vista funzionale, identico al comando TOP. Le query seguenti restituiscono gli stessi risultati.

```
Type=Event EventID=2110 | Limit 1
```

```
Type=Event EventID=2110 | Top 1
```


#### Per eseguire la ricerca usando il comando top
- Nel campo della query di ricerca, digitare `Type=Event EventID=2110 | Top 1` ![top di ricerca](./media/operational-insights-search/search-top.png)

Nell'immagine precedente, sono presenti 988 record con EventID=2110. I campi, i facet e i filtri a sinistra mostrano sempre informazioni sui risultati restituiti *dalla parte filtro* della query, ovvero la parte prima di qualsiasi carattere di barra verticale. Il riquadro **Risultati** restituisce il risultato più recente, in quanto il comando di esempio ha dato una forma e trasformato i risultati.

### Selezionare

Il comando SELECT si comporta come Select-Object in PowerShell. Restituisce i risultati filtrati che non presentano tutte le relative proprietà originali. Al contrario, seleziona solo le proprietà specificate.

#### Per eseguire una ricerca usando il comando select

1. Nella ricerca, digitare `Type=Event` e quindi fare clic su **Search**.
2. Fare clic su **+ show more** in uno dei risultati per visualizzare tutte le proprietà dei risultati.
3. Selezionare alcune proprietà in modo esplicito, la query viene modificata in `Type=Event | Select Computer,EventID,RenderedDescription`. ![select di ricerca](./media/operational-insights-search/search-select.png)

Si tratta di comando particolarmente utile quando si desidera controllare l'output di ricerca e scegliere solo le parti di dati davvero importanti per l'esplorazione, che spesso non corrispondono al record completo. Il comando è utile anche quando record di tipo diverso hanno *alcune* proprietà comuni, ma non *tutte*. È possibile generare un output simile a una tabella o che funziona bene quando esportato in un file CSV e quindi modificato in Excel.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

## Usare il comando measure

MEASURE è uno dei comandi più versatili nelle ricerche di Operational Insights. Consente di applicare *funzioni* statistiche ai dati e di aggregare i risultati raggruppati in base a un determinato campo. Esistono più funzioni statistiche supportate dal comando Measure.

### Measure count()

La prima funzione statistica da usare e una delle più semplici da comprendere è la funzione *count()*.

Nei risultati di una query di ricerca, ad esempio `Type=Event`, i filtri, anche denominati facet, vengono visualizzati sul lato sinistro dei risultati della ricerca. I filtri visualizzano una distribuzione di valori in base a un determinato campo, per i risultati della ricerca eseguita.

![measure count di ricerca](./media/operational-insights-search/search-measure-count01.png)

Ad esempio, nell'immagine precedente viene visualizzato il campo **Computer**. Tale campo indica che in quasi 3 milioni di eventi nei risultati, sono presenti 20 valori univoci e distinti per il campo **Computer** in questi record. Nel riquadro vengono visualizzati solo i primi 5 valori, che corrispondono ai 5 valori più comuni scritti nel campo **Computer**, ordinati in base al numero di documenti che contengono quel valore specifico in quel campo. Nell'immagine è possibile vedere che, di quasi 3 milioni di eventi, 880.000 provengono dal computer DM, 602.000 dal computer DE e così via.


Poiché nel riquadro vengono visualizzati solo i primi 5 valori, in che modo è possibile visualizzare tutti i valori?

Per visualizzare tutti i valori è possibile usare il comando measure con la funzione count(). Per questa funzione non viene usato alcun parametro. È sufficiente specificare il campo in base al quale si desidera eseguire il raggruppamento: in questo caso il campo **Computer**:

`Type=Event | Measure count() by Computer`

![measure count di ricerca](./media/operational-insights-search/search-measure-count-computer.png)

Tuttavia, **Computer** è semplicemente un campo usato *in* tutti i dati: non è coinvolto nessun database relazionale e non esiste alcun oggetto **Computer** separato altrove. Solo i valori presenti *nei* dati possono descrivere quale entità ha generato i dati e altre caratteristiche e aspetti dei dati, da qui il termine *facet*. Tuttavia, è possibile eseguire il raggruppamento anche in base ad altri campi. Poiché i risultati originali di quasi 3 milioni di eventi inviati al comando measure presentano anche un campo denominato **EventID**, è possibile applicare la stessa tecnica per eseguire il raggruppamento in base a tale campo e ottenere un conteggio di eventi per EventID:

```
Type=Event | Measure count() by EventID
```

Se non si è interessati al conteggio record effettivo che contiene un valore specifico, ma si desidera invece solo un elenco di valori, è possibile aggiungere un comando *Select* alla fine e selezionare la prima colonna:

```
Type=Event | Measure count() by EventID | Select EventID
```

Quindi, è possibile ottenere risultati più complessi e ordinare in precedenza i risultati nella query o semplicemente selezionare le colonne nella griglia.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### Per eseguire la ricerca con measure count

- Nel campo della query di ricerca, digitare `Type=Event | Measure count() by EventID`
- Aggiungere `| Select EventID` alla fine della query.
- Infine, aggiungere `| Sort EventID asc` alla fine della query.


Esistono due aspetti importanti da notare e mettere in evidenza:

In primo luogo, i risultati visualizzati non sono più i risultati originali non elaborati. Sono invece risultati aggregati, essenzialmente gruppi di risultati. Questo non è un problema, ma è importante sapere che si interagisce con una forma di dati che è molto diversa dalla forma originale non elaborata che viene creata in tempo reale come risultato della funzione di statistica o di aggregazione.

In secondo luogo, **Measure count** restituisce attualmente solo i primi 100 risultati distinti. Questo limite non si applica alle altre funzioni statistiche. Pertanto, in genere è necessario usare un filtro più preciso per cercare elementi specifici, prima di applicare measure count().

## Usare le funzioni max e min con il comando measure

Esistono vari scenari in cui è utile usare **Measure Max()** e **Measure Min()**. Tuttavia, poiché ciascuna funzione è opposta all'altra, verrà illustrata la funzione Max() e l'utente sperimenterà autonomamente la funzione Min().

Se si esegue una query per gli avvisi di Configuration Assessment,tali avvisi hanno una proprietà **Severity** che può essere 0, 1 o 2, valori che rappresentano informazioni, avvisi e avvisi critici. Ad esempio:

```
Type=ConfigurationAlert
```

![measure count start di ricerca](./media/operational-insights-search/search-measure-max01.png)

Se si desidera visualizzare il valore massimo per tutti gli avvisi di un determinato computer comune, quindi raggrupparli per campo, è possibile usare

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![measure max computer di ricerca](./media/operational-insights-search/search-measure-max02.png)

La maggior parte dei computer che presentano record **Avvisi** hanno almeno un avviso critico e il computer Bacc ha un avviso con la gravità massima.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![measure max time generated computer di ricerca](./media/operational-insights-search/search-measure-max03.png)

Questa funzione è adatta ad essere usata con i numeri, ma può essere usata anche con i campi di data/ora. È utile per verificare l'ultima data/ora o la data/ora più recente relativa a tutti i dati indicizzati per ciascun computer. Ad esempio, quando la modifica alla configurazione più recente è stata riportata dalla soluzione di traccia delle modifiche per ciascuna macchina?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## Usare la funzione avg con il comando measure

La funzione statistica Avg() usata con il comando measure consente di calcolare il valore medio per alcuni campi e di raggruppare i risultati in base allo stesso campo o ad un campo diverso. Questa funzione è utile in diversi casi, ad esempio con i dati sulle prestazioni.

Si inizierà con i dati sulle prestazioni. Notare tuttavia che Operational Insights raccoglie attualmente solo alcuni contatori delle prestazioni relativi all'infrastruttura, per gli host di Virtual Machine Manager e Hyper-V, come parte della soluzione Capacity Management.

Per la ricerca di *tutti* i dati sulle prestazioni, la query più semplice è:

```
Type=PerfHourly
```

![avg start di ricerca](./media/operational-insights-search/search-avg01.png)

La prima cosa da notare è che Operational Insights consente di visualizzare i grafici dei contatori delle prestazioni. Nella parte inferiore dei risultati, vengono visualizzati i record effettivi, dietro i grafici.

![avg start di ricerca](./media/operational-insights-search/search-avg02.png)

Nell'immagine precedente, sono presenti due set di campi contrassegnati che indicano quanto segue:

- il primo set identifica il nome del contatore delle prestazioni di Windows, il nome dell'oggetto e il nome dell'istanza nel filtro di query. Questi sono i campi che probabilmente verranno usati in genere come facet/filtri
- **SampleValue** è il valore effettivo del contatore
- nella query, **Type=PerfHourly** è un'aggregazione oraria
- **TimeGenerated** è 21:00, nel formato 24 ore. Rappresenta l'aggregazione per il periodo orario dalle 20:00 alle 21:00.
- **SampleCount** è l'aggregazione, calcolata mediante 12 campioni (uno ogni 5 minuti)
- **min**, **max** e **Percentile95** per il periodo orario sono, in questo esempio relativo alla memoria di una macchina virtuale, 6144 (MB)
- **SampleValue** è un'aggregazione oraria e viene compilata con la *media* per il periodo orario e viene usata per tracciare i grafici delle prestazioni

Dopo aver acquisito informazioni sulla forma del record PerfHourly e su altre tecniche di ricerca, è possibile usare measure Avg() per aggregare questo tipo di dati numerici.

Un semplice esempio viene riportato di seguito:

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(SampleValue) by Computer
```

![avg samplevalue di ricerca](./media/operational-insights-search/search-avg03.png)

In questo esempio, viene selezionato il contatore delle prestazioni CPU Total Time e la media viene calcolata in base a Computer. Poiché **SampleValue** è già una media, in realtà viene eseguita una query per una media di una media. A questo punto è corretto usare Type=PerfHourly. È consigliabile usare sempre un filtro in TimeGenerated per limitare l'operazione a un set di dati piccolo o recente, ad esempio le ultime 4 ore, non 7 giorni.

La query precedente diventa:

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer
```

### Per eseguire la ricerca tramite la funzione avg con il comando measure
1. Nella casella della query di ricerca, digitare `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer`
2. La media recente sarà in genere maggiore.
3. Calcolare la media dei valori orari massimi modificando la query di ricerca con `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(Max) by Computer`

È possibile aggregare e correlare i dati *tra* computer. Ad esempio, si supponga di disporre di un set di host in un certo tipo di farm in cui ogni nodo è uguale a qualsiasi altro nodo, tutti i nodi eseguono lo stesso tipo di lavoro e il carico è approssimativamente bilanciato. È possibile ottenere tutti i contatori contemporaneamente con la query seguente e ottenere le medie per l'intera farm. È possibile iniziare scegliendo i computer con l'esempio seguente:

```
Type=PerfHourly AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Ora che si dispone dei computer, è possibile selezionare due indicatori delle prestazioni chiave (KPI): % CPU Usage e % Free Disk Space. Quindi, quella parte della query diventa:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Ora è possibile aggiungere computer e contatori con l'esempio seguente:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Poiché è stata effettuata una selezione molto specifica, il comando **measure Avg()** può restituire la media non in base al computer ma, all'interno della farm, eseguendo il raggruppamento per CounterName. Ad esempio:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”) | Measure Avg(SampleValue) by CounterName
```

Ciò consente una visualizzazione utile e compatta di un paio di indicatori di prestazioni chiave dell'ambiente.

![avg grouping di ricerca](./media/operational-insights-search/search-avg04.png)


È possibile usare facilmente questo comando in un dashboard. Per altre informazioni sull'uso dei dashboard, vedere [Dashboard di Operational Insights](operational-insights-use-dashboards).

![avg dashboard di ricerca](./media/operational-insights-search/search-avg05.png)

### Usare la funzione sum con il comando measure

La funzione sum è simile ad altre funzioni del comando measure. È possibile vedere un esempio su come usare la funzione sum nell'argomento relativo alla [ricerca di Log W3C IIS in Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

È possibile usare Max() e Min() con numeri, intervalli di data/ora e stringhe di testo. Le stringhe di testo sono ordinate in ordine alfabetico e vengono visualizzate la prima e l'ultima.

Tuttavia, è possibile usare Sum() con elementi diversi dai campi numerici. Questo vale anche per Avg().

## Usare il comando where

Il comando where funziona come un filtro, ma può essere applicato nella pipeline per filtrare ulteriormente i risultati aggregati prodotti da un comando Measure, differentemente dai risultati non elaborati che vengono filtrati all'inizio di una query.

Ad esempio:

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer
```

È possibile aggiungere un altro carattere di barra verticale "|" e il comando Where per visualizzare solo i computer la cui CPU media è superiore all'80%, con l'esempio seguente:

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer | Where AVGCPU>80
```

Se si ha familiarità con Microsoft System Center - Operations Manager, è possibile considerare il comando where in termini di Management Pack. Se l'esempio fosse una regola, la prima parte della query sarebbe l'origine dati e il comando where sarebbe il rilevamento della condizione.

È possibile usare la query come un riquadro in **My Dashboard**, come un monitoraggio di ordinamenti, per verificare se sono presenti CPU di computer sovrautilizzate. Per altre informazioni sui dashboard, vedere [Dashboard di Operational Insights](operational-insights-use-dashboards). È possibile creare e usare i dashboard anche tramite l'app per dispositivi mobili. Per altre informazioni vedere [App per dispositivi mobili di Azure Operational Insights](http://www.windowsphone.com/it-IT/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). Nei due riquadri in basso dell'immagine seguente, il monitoraggio viene visualizzato come un elenco e come un numero. Essenzialmente, è auspicabile che il numero sia sempre zero e l'elenco sia sempre vuoto. In caso contrario, indica una condizione di avviso. Se necessario, è possibile usarlo per individuare quali computer sono sotto pressione.

![dashboard mobile](./media/operational-insights-search/search-mobile.png)

## Riferimento alla sintassi di ricerca

Nella sezione di riferimento seguente relativa al linguaggio di ricerca vengono descritte le opzioni della sintassi di query generale che è possibile usare quando si ricercano dati e si filtrano espressioni per restringere la ricerca. Vengono inoltre descritti i comandi che è possibile usare per intervenire sui dati recuperati.

È possibile ottenere informazioni sui campi restituiti nelle ricerche e sui facet che consentono di analizzare categorie di dati simili in [Riferimenti al campo Ricerca e ai facet](#Search-field-and-facet-reference).

### Sintassi di query generale

Sintassi:

filterExpression | command1 | command2 …

L'espressione di filtro (**filterExpression**) definisce la condizione "where" per la query. I comandi si applicano ai risultati restituiti dalla query. Più comandi devono essere separati dal carattere di barra verticale (|).

#### Esempi di sintassi generale

Esempi:

	system

Questa query restituisce risultati che contengono la parola "system" in qualsiasi campo che è stato indicizzato per la ricerca full-text o di termini.

>[AZURE.NOTE]Non tutti i campi sono indicizzati in questo modo, ma in genere lo sono i campi di testuali più comuni (ad esempio nomi e descrizioni).

	system error

Questa query restituisce risultati che contengono le parole "system" e "error".

	system error | sort ManagementGroupName, TimeGenerated desc | top 10

Questa query restituisce risultati che contengono le parole "system" e "error". Quindi ordina i risultati in base al campo **ManagementGroupName** (in ordine crescente) e in base al campo **TimeGenerated** (in ordine decrescente). Visualizza solo i primi dieci risultati.

>[AZURE.IMPORTANT]Tutti i nomi dei campi e i valori dei campi di tipo stringa e testo fanno distinzione tra maiuscole e minuscole.

### Espressione di filtro

Nelle sottosezioni seguenti vengono illustrate le espressioni di filtro.

#### Valori letterali stringa

Un valore letterale stringa è qualsiasi stringa non riconosciuta dal parser come una parola chiave o come un tipo di dati predefiniti (ad esempio, un numero o una data).

Esempi:

	These all are string literals


Questa query cerca i risultati che contengono occorrenze di tutte le cinque parole. Per eseguire una ricerca di stringa complessa, racchiudere il valore letterale di stringa tra virgolette, ad esempio:

	" Windows Server"

Restituisce solo i risultati con corrispondenze esatte per "Windows Server"

#### Numeri

Il parser supporta la sintassi di numeri interi decimali e a virgola mobile per i campi numerici.

Esempi:

	Type:PerfHourly 0.5


	HTTP 500

#### Data/ora

Tutti i dati nel sistema presentano una proprietà **TimeGenerated** che rappresenta la data e l'ora originali del record. Alcuni tipi di dati possono inoltre avere più campi di data e ora, (ad esempio **LastModified**).

Il selettore di grafico/ora della sequenza temporale in Operational Insights mostra una distribuzione dei risultati nel tempo (secondo la query corrente in esecuzione), in base al campo **TimeGenerated**. I campi di data e ora hanno un formato di stringa specifico che può essere usato nelle query per limitare la query a un determinato intervallo di tempo. È inoltre possibile usare la sintassi per fare riferimento a intervalli di tempo relativi (ad esempio, "tra 3 giorni fa e 2 ore fa").

Sintassi:

	yyyy-mm-ddThh:mm:ss.dddZ


	yyyy-mm-ddThh:mm:ss.ddd


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm


	yyyy-mm-dd



Esempio:

	TimeGenerated:2013-10-01T12:20

Il comando precedente restituisce solo i record con un valore **TimeGenerated** corrispondente alle 12:20 del 1° ottobre 2013. È improbabile che il comando fornisca risultati, ma viene usato per comprendere il concetto.

Il parser supporta anche il valore di data e ora mnemonico, NOW.

Esempio:


Anche in questo caso, è improbabile che vengano restituiti risultati, in quanto i dati non producono risultati attraverso il sistema in modo così rapido.

Questi esempi sono blocchi predefiniti da usare per le date relative e assolute. Nelle tre sezioni successive verrà illustrato come usare tali blocchi in filtri più avanzati con esempi che usano intervalli di date relative.

#### Operatori matematici di data/ora

Usare gli operatori matematici di data/ora per eseguire l'offset o arrotondare il valore di data/ora usando semplici calcoli di data/ora.

Sintassi:

	datetime/unit


	datetime[+|-]count unit




<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th>Operatore</th>
		<th>Descrizione</th>
	</tr>
	<tr>
		<td>
		<p>/</p>
		</td>
		<td>
		<p>La data/ora viene arrotondata all'unità specificata. </p>
		<p>Esempio: NOW/DAY arrotonda la data/ora corrente a mezzanotte del giorno corrente. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>+ o -</p>
		</td>
		<td>
		<p>Esegue l'offset della data/ora in base al numero specificato di unità</p>
		<p>Esempi:&#160; </p>
		<ul>
			<li class="unordered">NOW+1HOUR esegue l'offset della data/ora corrente un'ora più avanti.<br><br></li>
			<li class="unordered">2013-10-01T12:00-10DAYS esegue l'offset del valore data indietro di 10 giorni.</li>
		</ul>
		</td>
	</tr>
</table>





È possibile concatenare gli operatori matematici di data/ora, ad esempio:

	NOW+1HOUR-10MONTHS/MINUTE

Nella tabella seguente vengono elencate le unità di data/ora supportate.

Unità di data/ora|Descrizione
---|---
YEAR, YEARS|Viene arrotondata all'anno corrente o viene eseguito l'offset in base al numero di anni specificato.
MONTH, MONTHS|Viene arrotondata al mese corrente o viene eseguito l'offset in base al numero di mesi specificato.
DAY, DAYS, DATE|Viene arrotondata al giorno corrente del mese o viene eseguito l'offset in base al numero di giorni specificato.
HOUR, HOURS|Viene arrotondata all'ora corrente o viene eseguito l'offset in base al numero di ore specificato.
MINUTE, MINUTES|Viene arrotondata al minuto corrente o viene eseguito l'offset in base al numero di minuti specificato.
SECOND, SECONDS|Viene arrotondata al secondo corrente o viene eseguito l'offset in base al numero di secondi specificato.
MILLISECOND, MILLISECONDS, MILLI, MILLIS|Viene arrotondata al millisecondo corrente o viene eseguito l'offset in base al numero di millisecondi specificato.


#### Facet di campo

Usando i facet di campo, è possibile specificare la condizione di ricerca per campi specifici e i relativi valori esatti, anziché scrivere query di "testo libero" per diversi termini dell'indice. Questa sintassi è già stata usata in vari esempi nei paragrafi precedenti. Vengono ora forniti esempi più complessi.

**Sintassi**

*field:value*

*field=value*

**Descrizione**

Cerca il valore specifico nel campo. Il valore può essere un valore letterale stringa, un numero o una data/ora.

Esempio:


	TimeGenerated:NOW


	ObjectDisplayName:"server01.contoso.com"


	SampleValue:0.3

**Sintassi**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**Descrizione**

Fornisce i confronti.

Esempio:

	TimeGenerated>NOW+2HOURS


**Sintassi**

*field:[from..to]*

**Descrizione**

Fornisce il facet di intervallo.

Esempio:

	TimeGenerated:[NOW..NOW+1DAY]


	SampleValue:[0..2]
#### Operatori logici

I linguaggi di query supportano gli operatori logici (AND, OR e NOT) e i relativi alias di tipo C, rispettivamente (&&, || e !). È possibile usare le parentesi per raggruppare questi operatori.

Esempi:

	system OR error


	Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
È possibile omettere l'operatore logico per gli argomenti di filtro di primo livello. In questo caso, viene usato l'operatore AND.


Espressione di filtro|Equivalente a
---|---
system error|system AND error
system "Windows Server" OR Severity:1|system AND ("Windows Server" OR Severity:1)



### Comandi:

I comandi si applicano ai risultati restituiti dalla query. Usare il carattere di barra verticale (|) per applicare un comando ai risultati recuperati. Più comandi devono essere separati dal carattere di barra verticale (|).

>[AZURE.NOTE]I nomi dei comandi possono essere scritti in lettere maiuscole o minuscole, a differenza dei nomi dei campi e dei dati.

#### Ordina

Sintassi:

	sort field1 asc|desc, field2 asc|desc, …

Ordina i risultati in base a determinati campi. I prefissi asc/desc sono facoltativi. Se vengono omessi, viene usato l'ordinamento "asc". Se una query non usa il comando **Sort** in modo esplicito, Sort **TimeGenerated** desc è il comportamento predefinito e verranno sempre restituiti prima i risultati più recenti.

#### Top/Limit

Sintassi:

	top number


	limit number
Limita la risposta ai primi N risultati.

Esempio:

	Type:Alert errors detected | top 10

Restituisce i primi 10 risultati corrispondenti.

#### Skip

Sintassi:

	skip number

Ignora il numero di risultati elencati.

Esempio:

	Type:Alert errors detected | top 10 | skip 200

Restituisce i primi 10 risultati corrispondenti a partire dal risultato 200.

#### Selezionare

Sintassi:

	select field1, field2, ...

Limita i risultati ai campi selezionati.

Esempio:

	Type:Alert errors detected | select Name, Severity

Limita i campi dei risultati restituiti a **Name** e **Severity**.

#### Measure

Il comando **measure** viene usato per applicare funzioni statistiche ai risultati della ricerca non elaborati. Questo comando è molto utile per ottenere visualizzazioni di tipo *group-by* dei dati. Quando si usa il comando **measure**, Operational Insights visualizza una tabella con risultati aggregati.

Sintassi:

	measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]


	measure aggregateFunction([aggregatedField])  interval interval

Aggrega i risultati per **groupField** e calcola i valori di misura aggregati tramite l'oggetto **aggregatedField**.


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Funzione statistica di misura </th> <th>Descrizione </th> </tr> <tr> <td> <p><em>aggregateFunction</em> </p> <p></p> </td> <td> <p>Nome della funzione di aggregazione (senza distinzione tra maiuscole e minuscole). Sono supportate le funzioni di aggregazione seguenti:</p> <ul> <li class="unordered">COUNT<br><br></li> <li class="unordered">MAX<br><br></li> <li class="unordered">MIN<br><br></li> <li class="unordered">SUM<br><br></li> <li class="unordered">AVG<br><br></li> <li class="unordered">STDDEV<br><br></li> </ul> </td> </tr> <tr> <td> <p><em>aggregatedField</em> </p> </td> <td> <p>Campo di aggregazione. Questo campo è facoltativo per la funzione di aggregazione COUNT, ma deve essere un campo numerico esistente per SUM, MAX, MIN, AVG o STDDEV.</p> </td> </tr> <tr> <td> <p><em>fieldAlias</em> </p> </td> <td> <p>Alias (facoltativo) per il valore aggregato calcolato. Se non specificato, il nome del campo sarà <em>AggregatedValue.</em></p> </td> </tr> <tr> <td> <p><em>groupField</em> </p> </td> <td> <p>Nome del campo in base al quale viene raggruppato il set di risultati. </p> </td> </tr> <tr> <td> <p><em>Interval</em> </p> </td> <td> <p>Intervallo di tempo nel formato: </p> <p><em>nnnNAME</em> </p> <p></p> <p>Dove: </p> <p>nnn è il numero intero positivo</p> <p><em>NAME</em> è il nome dell'intervallo</p> <p>I nomi di intervallo supportati includono (con distinzione tra maiuscole e minuscole): </p> <ul> <li class="unordered">MILLISECOND[S]<br><br></li> <li class="unordered">SECOND[S]<br><br></li> <li class="unordered">MINUTE[S]<br><br></li> <li class="unordered">HOUR[S]<br><br></li> <li class="unordered">DAY[S]<br><br></li> <li class="unordered">MONTH[S]<br><br></li> <li class="unordered">YEAR[S]<br></li> </ul> </td> </tr> </table>



L'opzione intervallo può essere usata solo nei campi del gruppo data/ora (ad esempio **TimeGenerated** e **TimeCreated**). Questo non viene applicato dal servizio, ma attualmente un campo senza data/ora passato al back-end causa un errore di runtime. Quando viene implementata la convalida dello schema, l'API del servizio rifiuta le query che usano campi senza data/ora per l'aggregazione di intervalli. L'implementazione corrente di **Measure** supporta il raggruppamento di intervalli solo per la funzione di aggregazione **Count**.

Se la clausola BY viene omessa, ma viene specificato un intervallo (come una seconda sintassi), per impostazione predefinita viene usato il campo **TimeGenerated**.

Esempi:

**Esempio 1**

	Type:Alert | measure count() as Count by ObjectId

*Spiegazione*

Raggruppa gli avvisi per **ObjectID** e calcola il numero di avvisi per ogni gruppo. Il valore aggregato viene restituito come campo **Count** (alias).

**Esempio 2**

	Type:Alert | measure count() interval 1HOUR

*Spiegazione*

Raggruppa gli avvisi per intervalli di 1 ora tramite il campo **TimeGenerated** e restituisce il numero di avvisi in ogni intervallo.

**Esempio 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*Spiegazione*

Uguale all'esempio precedente, ma con un alias del campo aggregato (**AlertsPerHour**).

**Esempio 4**

	* | measure count() by TimeCreated interval 5DAYS

*Spiegazione*

Raggruppa i risultati per intervalli di 5 giorni tramite il campo **TimeCreated** e restituisce il numero di risultati in ogni intervallo.

**Esempio 5**

	Type:Alert | measure max(Severity) by WorkflowName

*Spiegazione*

Raggruppa gli avvisi in base al nome del carico di lavoro e restituisce il valore di gravità massima degli avvisi per ogni flusso di lavoro.

**Esempio 6**

	Type:Alert | measure min(Severity) by WorkflowName

*Spiegazione*

Uguale all'esempio precedente, ma con la funzione di aggregazione **Min**.

**Esempio 7**

	Type:PerfHourly | measure avg(SampleValue) by ObjectId

*Spiegazione*

Raggruppa le prestazioni orarie per ID oggetto e calcola la media (avg).

**Esempio 8**

	Type:PerfHourly | measure sum(SampleValue) by ObjectId

*Spiegazione*

Uguale all'esempio precedente, ma usa **Sum**.

**Esempio 9**

	Type:PerfHourly | measure stddev(SampleValue) by ObjectId

*Spiegazione*

Uguale all'esempio precedente, ma usa **STDDEV**.

**Esempio 10**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*Spiegazione*

Ottiene i primi cinque flussi di lavoro con il numero massimo di avvisi.

#### Where

Sintassi:

**where** AggregatedValue>20

Può essere usata solo dopo un comando **Measure** per filtrare ulteriormente i risultati aggregati prodotti dalla funzione di aggregazione **Measure**.

Esempi:

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) as MAXCPU by

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) by RootObjectName | where (AggregatedValue>50 and AggregatedValue<90)




## Riferimenti al campo Ricerca e ai facet

Quando si usa Ricerca per trovare i dati, i risultati visualizzano vari campi e facet. Tuttavia, alcune delle informazioni visualizzate potrebbero non essere molto descrittive. È possibile usare le informazioni seguenti per comprendere i risultati.

<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th><b>Campo</b></th>
		<th><b>Tipo di ricerca<b></th>
		<th><b>Descrizione</b></th>
	</tr>
	<tr>
		<td>
		<p>TenantId</p>
		</td>
		<td>
		<p>Tutti</p>
		</td>
		<td>
		<p>Usato per la partizione di dati</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeGenerated</p>
		</td>
		<td>
		<p>Tutti</p>
		</td>
		<td>
		<p>Usato per gestire la sequenza temporale, selettori orari (in Ricerca e in altre schermate). Rappresenta quando i dati sono stati generati (in genere nell'agente). Il tempo viene espresso nel formato ISO ed è sempre UTC. Nel caso di 'tipi' basati sulla strumentazione esistente (ad esempio eventi in un log) si tratta in genere del tempo reale in cui la voce/riga/record di log è stata registrata. Per altri tipi prodotti tramite Management Pack o nel cloud, ad esempio raccomandazioni/avvisi/updateagent/e così via, rappresenta il tempo in cui sono stati raccolti nuovi dati con uno snapshot di una configurazione di un certo tipo, o sono stati prodotti una raccomandazione o un avviso</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventID</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>ID evento nel registro eventi di Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLog</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Registro eventi di cui l'evento è stato registrato da Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevelName</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Critico/avviso/informazioni/esito positivo</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevel</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Valore numerico per critico/avviso/informazioni/esito positivo (usare EventLevelName invece di query più semplici o più leggibili)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceSystem</p>
		</td>
		<td>
		<p>Tutti</p>
		</td>
		<td>
		<p>Provenienza dei dati (in termini di modalità di "collegamento" al servizio, ad esempio Operations Manager, Operational Insights (i dati vengono generati nel cloud), Archiviazione di Azure (dati provenienti da WAD) e così via</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nome oggetto delle prestazioni di Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>InstanceName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nome dell'istanza del contatore delle prestazioni di Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>CounteName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nome del contatore delle prestazioni di Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectDisplayName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nome visualizzato dell'oggetto di destinazione tramite una regola di raccolta delle prestazioni in Operations Manager o dell'oggetto individuato da Operational Insights o su cui è stato generato l'avviso</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RootObjectName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nome visualizzato del padre del padre (in una relazione di hosting doppia: ad esempio SqlDatabase ospitato da SqlInstance ospitato da Computer Windows) dell'oggetto di destinazione di una regola di raccolta delle prestazioni in Operations Manager o dell'oggetto individuato da Operational Insights o su cui è stato generato l'avviso</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Computer</p>
		</td>
		<td>
		<p>La maggior parte dei tipi </p>
		</td>
		<td>
		<p>Nome del computer a cui appartengono i dati</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DeviceName</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Nome del computer a cui appartengono i dati (uguale a "Computer")</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DetectionId</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatusRank</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>La classificazione dello stato di minaccia è una rappresentazione numerica dello stato di minaccia e, come per i codici di risposta HTTP, sono stati lasciati spazi tra i numeri (motivo per cui nessuna minaccia è 150, 100 o 0) in modo da poter aggiungere nuovi stati. Quando si esegue un rollup per lo stato di minaccia e lo stato di protezione, viene visualizzato lo stato peggiore in cui il computer è rimasto durante il periodo di tempo selezionato. I numeri vengono usati per classificare i diversi stati, per cui è possibile analizzare il record con il numero più alto.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatus</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Descrizione di ThreatStatus, viene eseguito il mapping 1:1 con ThreatStatusRank</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TypeofProtection</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Prodotto anti-malware rilevato nel computer: nessuno, strumento di rimozione malware Microsoft, Forefront e così via</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ScanDate</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceHealthServiceId</p>
		</td>
		<td>
		<p>ProtectionStatus, RequiredUpdate</p>
		</td>
		<td>
		<p>ID servizio di integrità per l'agente del computer</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>HealthServiceId</p>
		</td>
		<td>
		<p>La maggior parte dei tipi </p>
		</td>
		<td>
		<p>ID servizio di integrità per l'agente del computer</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ManagementGroupName</p>
		</td>
		<td>
		<p>La maggior parte dei tipi </p>
		</td>
		<td>
		<p>Nome del gruppo di gestione per gli agenti di Operations Manager, altrimenti è null o vuoto</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectType</p>
		</td>
		<td>
		<p>ConfigurationObject</p>
		</td>
		<td>
		<p>Tipo (come nel 'tipo'/classe del Management Pack di Operations Manager) per l'oggetto individuato dalla valutazione della configurazione di Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateTitle</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Nome dell'aggiornamento trovato non installato</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>PublishDate</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Quando è stato pubblicato l'aggiornamento su Microsoft Update?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Server</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Nome del computer a cui appartengono i dati (uguale a "Computer")</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Prodotto</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Prodotto a cui si applica l'aggiornamento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateClassification</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Tipo di aggiornamento (rollup aggiornamento, service pack e così via)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBID</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>ID articolo della KB che descrive la procedura consigliata o l'aggiornamento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WorkflowName</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Nome della regola o del monitoraggio che ha generato l'avviso</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Gravità</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Gravità dell'avviso</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Priorità</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Priorità dell'avviso</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>IsMonitorAlert</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Questo avviso viene generato da un monitoraggio (true) o da una regola (false)?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AlertParameters</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML con i parametri dell'avviso di Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Context</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML con il ’contesto’ dell'avviso di Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Carico di lavoro</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Tecnologia o 'carico di lavoro' a cui fa riferimento l'avviso </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AdvisorWorkload</p>
		</td>
		<td>
		<p>Raccomandazione</p>
		</td>
		<td>
		<p>Tecnologia o 'carico di lavoro' a cui fa riferimento la raccomandazione</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Descrizione</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Descrizione dell'avviso (breve)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdate</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Quanti giorni fa (relativo a 'TimeGenerated' di questo record) questo agente ha installato un aggiornamento da WSUS/Microsoft Update?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdateBucket</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>In base a DaysSinceLastUpdate, una categorizzazione in 'bucket orari' del tempo trascorso dall'ultima installazione di aggiornamenti da WSUS/Microsoft Update da parte di un computer</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateEnabled</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Il controllo degli aggiornamenti automatici è abilitato o disabilitato su questo agente?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateValue</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Il controllo degli aggiornamenti automatici è impostato per scaricare automaticamente e installare, solo scaricare o solo controllare?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WindowsUpdateAgentVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Numero di versione dell'agente di Microsoft Update</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WSUSServer</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>A quale server WSUS è destinato questo agente di aggiornamento?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>OSVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Versione del sistema operativo su cui è in esecuzione questo agente di aggiornamento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Nome</p>
		</td>
		<td>
		<p>Recommendation, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nome/titolo della raccomandazione o nome della proprietà di Configuration Assessment di Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Valore</p>
		</td>
		<td>
		<p>ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Valore di una proprietà di Configuration Assessment di Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBLink</p>
		</td>
		<td>
		<p>Raccomandazione</p>
		</td>
		<td>
		<p>URL all'articolo della KB che descrive la procedura consigliata o l'aggiornamento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RecommendationStatus</p>
		</td>
		<td>
		<p>Raccomandazione</p>
		</td>
		<td>
		<p>Le raccomandazioni sono tra i pochi tipi i cui record vengono 'aggiornati', non solo aggiunti all'indice di ricerca. Questo stato cambia se la raccomandazione è attiva/aperta o se Operational Insights rileva che è stata risolta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RenderedDescription</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Descrizione con rendering (testo riutilizzato con parametri popolati) di un evento di Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ParameterXml</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>XML con i parametri della sezione 'data' di un evento di Windows (come illustrato nel Visualizzatore eventi)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventData</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>XML con l'intera sezione 'data' di un evento di Windows (come illustrato nel Visualizzatore eventi)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Source</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Origine del registro eventi che ha generato l'evento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventCategory</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Categoria dell'evento, direttamente dal registro eventi di Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UserName</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Nome utente dell'evento di Windows (in genere, NT AUTHORITY\LOCALSYSTEM)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleValue</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valore medio per l'aggregazione oraria di un contatore delle prestazioni </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Min</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valore minimo nell'intervallo orario di un'aggregazione oraria del contatore delle prestazioni</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Max</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valore massimo nell'intervallo orario di un'aggregazione oraria del contatore delle prestazioni</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Percentile95</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valore del 95° percentile nell'intervallo orario di un'aggregazione oraria del contatore delle prestazioni</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleCount</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Il numero di campioni di contatori delle prestazioni 'non elaborati' usati per produrre questo record di aggregazione oraria</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Threat</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Nome del malware rilevato</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>StorageAccount</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Account di archiviazione di Azure da cui è stato letto il log</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AzureDeploymentID</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>ID distribuzione di Azure del servizio cloud a cui appartiene il log</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Ruolo</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Ruolo del servizio cloud di Azure a cui appartiene il log</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RoleInstance</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Istanza del ruolo di Azure a cui appartiene il log</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sSiteName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Sito Web IIS a cui appartiene il log (notazione metabase); il campo s-sitename nel log originale</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sComputerName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Il campo s-computername nel log originale</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Indirizzo IP del server a cui è stata indirizzata la richiesta HTTP. Il campo s-ip nel log originale</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csMethod</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Metodo HTTP (GET/POST/e così via) usato dal client nella richiesta HTTP. Il metodo cs nel log originale</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>cIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Indirizzo IP del client da cui proviene la richiesta HTTP. Il campo c-ip nel log originale</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserAgent</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Agente utente HTTP dichiarato dal client (browser o altro). L'agente utente cs nel log originale</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Codice di stato HTTP (200/403/500/e così via) restituito dal server al client. Lo stato cs nel log originale</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeTaken</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Periodo di tempo (in millisecondi) impiegato dalla richiesta per il completamento. Il campo timetaken nel log originale</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriStem</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Uri relativo (senza indirizzo host, ad esempio '/search') che è stato richiesto. Il campo cs-uristem nel log originale</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriQuery</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Query dell'URI. Le query dell'URI sono necessarie solo per le pagine dinamiche, ad esempio le pagine ASP, per cui questo campo contiene in genere un trattino per le pagine statiche.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sPort</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Porta del server a cui è stata inviata la richiesta HTTP (e IIS scelto in ascolto)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Nome dell'utente autenticato, se la richiesta è autenticata e non anonima</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csVersion</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Versione del protocollo HTTP usata nella richiesta (ad esempio 'HTTP/1.1')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csCookie</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Informazioni sui cookie</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csReferer</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Ultimo sito visitato dall'utente. Questo sito fornisce un collegamento al sito corrente. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csHost</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Intestazione host (ad esempio 'www.mysite.com') richiesta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scSubStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Codice di errore di stato secondario</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scWin32Status</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Codice di stato Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Byte inviati nella richiesta dal client al server</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Byte restituiti nella risposta dal server al client</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ConfigChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo di modifica (WindowsServices/Software/e così via)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ChangeCategory </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Categoria della modifica (Modificato/Aggiunto/Rimosso)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo di software (Aggiornamento/Applicazione)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nome del software (applicabile solo alle modifiche del software)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Autore </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Fornitore che pubblica il software (applicabile solo alle modifiche del software)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo di modifica applicata a un servizio Windows (State/StartupType/Path/ServiceAccount), applicabile solo alle modifiche del servizio Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDisplayName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nome visualizzato del servizio modificato</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nome del servizio modificato</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcState </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Stato nuovo (corrente) del servizio</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousState</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Stato precedente noto del servizio (applicabile solo se lo stato del servizio è stato modificato)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcStartupType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Service startup type</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousStartupType</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo di avvio del servizio precedente (applicabile solo se il tipo di avvio del servizio è stato modificato)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcAccount </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Account del servizio</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousAccount</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Account del servizio precedente (applicabile solo se l'account del servizio è stato modificato)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Percorso dell'eseguibile per il servizio Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Percorso precedente dell'eseguibile per il servizio Windows (applicabile solo se è stato modificato)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDescription</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Descrizione del servizio</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Precedente </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Stato precedente del software (Installato/Non installato/versione precedente)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Current</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Ultimo stato del software (Installato/Non installato/versione corrente)</p>
		</td>
	</tr>
</table>

## Post di blog - Casi d'uso della ricerca
- [Ricerca nei log di IIS W3C Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)
- [Monitoraggio degli errori del backup di SQL con la ricerca e i dashboard di Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2015/02/21/monitoring-sql-backup-failures-with-azure-operational-insights-search-and-dashboards.aspx)
- [Equivalenti della funzione di ricerca di Operational Insights rispetto alle regole di avviso relative agli eventi del Management Pack di IIS](http://blogs.msdn.com/b/dmuscett/archive/2014/11/05/iis-mp-event-alerting-rules-s-opinsights-searches-equivalents.aspx)
- [Raccolta di query di ricerca utili di Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-searches-collection.aspx)

## Altre risorse:
Stefan Roth ha creato un foglio informativo utile sulla ricerca. Per altre informazioni e per scaricare il foglio informativo, visitare il [blog](http://stefanroth.net/2014/11/05/microsoft-azure-operational-insights-search-data-explorer-cheat-sheet/).

<!---HONumber=Sept15_HO3-->