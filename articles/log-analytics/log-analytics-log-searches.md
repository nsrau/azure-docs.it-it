<properties
	pageTitle="Ricerche nei log in Log Analytics | Microsoft Azure"
	description="Le ricerche nei log permettono di combinare e correlare i dati del computer provenienti da più origini nell'ambiente corrente."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Ricerche nei log in Log Analytics

Un elemento fondamentale di Log Analytics è la funzionalità di ricerca nei log, che permette di combinare e correlare i dati del computer da più origini all'interno dell'ambiente. Anche le soluzioni sono basate sulla ricerca log, per offrire metriche specifiche per una particolare area problematica.

Nella pagina Search è possibile creare una query e quindi, durante la ricerca, filtrare i risultati usando controlli facet. È anche possibile creare query avanzate per trasformare, filtrare e creare report sui risultati.

Le query di ricerca log più comuni sono visualizzate nella maggior parte delle pagine delle soluzioni. In tutta la console OMS è possibile fare clic sui riquadri o analizzare altri elementi per visualizzarne i dettagli usando la funzionalità di ricerca nei log.

In questa esercitazione verranno esaminati alcuni esempi per analizzare tutti gli elementi di base quando si usa la ricerca log.

Si inizierà con esempi semplici e pratici, tali esempi verranno quindi compilati in modo da acquisire informazioni sui casi d'uso pratici relativi all'uso della sintassi per estrarre le informazioni desiderate dai dati.

Dopo aver acquisito familiarità con le tecniche di ricerca, vedere il [riferimento alla ricerca nei log di Log Analytics](log-analytics-search-reference.md).

## Usare filtri di base

La prima parte di una query di ricerca, quella prima di un carattere di barra verticale "|", è sempre un *filtro*. Può essere considerata come una clausola WHERE in TSQL, perché determina *quale* subset di dati estrarre dall'archivio dati di OMS. Per la ricerca in un archivio dati è importante specificare le caratteristiche dei dati da estrarre, è quindi normale che una query inizi con la clausola WHERE.

I filtri più semplici che è possibile usare sono *parole chiave*, ad esempio 'error' o 'timeout' o un nome di computer. Questi tipi di query semplici restituiscono in genere diverse forme di dati all'interno dello stesso set di risultati. Questo perché Log Analytics include diversi *tipi* di dati nel sistema.


### Per eseguire una ricerca semplice
1. Nel portale di OMS, fare clic su **Ricerca log**. ![riquadro di ricerca](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. Nel campo delle query digitare `error` e quindi fare clic su **Search**. ![errore di ricerca](./media/log-analytics-log-searches/oms-search-error.png) Ad esempio, la query per `error` nell'immagine seguente ha restituito 100.000 record **Event** (raccolti da Log Management), 18 record **ConfigurationAlert** (generati da Configuration Assessment) e 12 record **ConfigurationChange** (acquisiti da Change Tracking). ![risultati della ricerca](./media/log-analytics-log-searches/oms-search-results01.png)  

Questi filtri non sono realmente tipi o classi di oggetti. *Type* è semplicemente un tag o una proprietà o una stringa/nome/categoria, collegata a una parte dei dati. Alcuni documenti nel sistema vengono contrassegnati come **Type:ConfigurationAlert** e alcuni vengono contrassegnati come **Type:Perf** o **Type:Event** e così via. In ogni risultato della ricerca, documento, record o voce vengono visualizzate tutte le proprietà non elaborate e i relativi valori per ciascuno di tali dati ed è possibile usare i nomi dei campi per specificare nel filtro quando si desidera recuperare solo i record il cui campo presenta il valore specificato.

*Type* è in realtà solo un campo che contiene tutti i record, non è diverso dagli altri campi. Ciò è stato stabilito in base al valore del campo Type. Quel record avrà una forma o un formato diversi. Tra l'altro, **Type=Perf** o **Type=Event** è anche la sintassi necessaria per eseguire una query su eventi o dati sulle prestazioni.

È possibile usare i due punti (:) o un segno di uguale (=) dopo il nome del campo e prima del valore. **Type:Event** e **Type=Event** hanno un significato equivalente ed è possibile scegliere lo stile preferito.

Se i record Type=Perf hanno un campo denominato "CounterName", è quindi possibile scrivere una query simile a `Type=Perf CounterName="% Processor Time"`.

Questa query fornirà solo i dati sulle prestazioni in cui il nome del contatore delle prestazioni è "% Processor Time".

### Per ricercare i dati sulle prestazioni del tempo del processore
- Nel campo della query di ricerca, digitare `Type=Perf CounterName="% Processor Time"`

È inoltre possibile essere più specifici e usare **InstanceName=\_'Total'** nella query, che rappresenta un contatore delle prestazioni di Windows. È possibile anche selezionare un facet e un altro **field:value**. Il filtro viene aggiunto automaticamente al filtro dell'utente nella barra di query. È possibile visualizzare il risultato nell'immagine seguente. Viene indicato dove è possibile fare clic per aggiungere **InstanceName:'\_Total'** alla query senza digitare nulla.

![facet di ricerca](./media/log-analytics-log-searches/oms-search-facet.png)

La query diventa `Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

In questo esempio non è necessario specificare **Type=Perf** per ottenere questo risultato. Dato che i campi CounterName e InstanceName esistono solo per record di Type=Perf, la query è abbastanza specifica per restituire gli stessi risultati della query precedente, più lunga:
```
CounterName="% Processor Time" InstanceName="_Total"
```

Questo perché tutti i filtri nella query vengono considerati come se fossero collegati dall'operatore *AND*. In effetti, quanti più campi vengono aggiunti ai criteri, tanto più i risultati saranno di numero inferiore ma più specifici e complessi.

Ad esempio, la query `Type=Event EventLog="Windows PowerShell"` è identica a `Type=Event AND EventLog="Windows PowerShell"`. Restituisce tutti gli eventi a cui è stato effettuato l'accesso e che sono stati raccolti dal registro eventi di Windows PowerShell. Se si aggiunge un filtro più volte selezionando ripetutamente lo stesso facet, il problema è puramente descrittivo, potrebbe creare confusione nella barra di ricerca, ma restituisce comunque gli stessi risultati perché l'operatore AND implicito è sempre presente.

È possibile invertire facilmente l'operatore AND implicito usando un operatore NOT in modo esplicito. ad esempio:

`Type:Event NOT(EventLog:"Windows PowerShell")` o l'equivalente `Type=Event EventLog!="Windows PowerShell"` restituisce tutti gli eventi di tutti gli altri log DIVERSI dal log di Windows PowerShell.

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

![risultati della ricerca](./media/log-analytics-log-searches/oms-search-results03.png)

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
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### Operatori booleani
Con i campi di data/ora e numerici, è possibile cercare i valori usando *maggiore di*, *minore di* e *minore o uguale a*. È possibile usare operatori semplici come >, < , >=, <= , != nella barra di ricerca della query.


È possibile eseguire una query su un registro eventi specifico per uno specifico periodo di tempo. Ad esempio, le ultime 24 ore viene espresso con la seguente espressione mnemonica.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### Per eseguire la ricerca usando un operatore booleano
- Nel campo della query di ricerca, digitare `EventLog=System TimeGenerated>NOW-24HOURS"` ![ricerca con valore booleano](./media/log-analytics-log-searches/oms-search-boolean.png)

Nonostante sia possibile controllare graficamente l'intervallo di tempo, e la maggior parte delle volte si desideri farlo, l'inclusione di un filtro temporale direttamente nella query presenta dei vantaggi. Ad esempio, è efficace con i dashboard, in cui è possibile sostituire l'intervallo di tempo per ogni riquadro, indipendentemente dal selettore temporale *globale* nella pagina del dashboard. Per altre informazioni, vedere l'argomento relativo alle [questioni di tempo nel dashboard](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Quando si filtra in base al tempo si ottengono risultati per l'*intersezione* dei due periodi di tempo: quello specificato nel portale di OMS (S1) e quello specificato nella query (S2).

![intersezione](./media/log-analytics-log-searches/oms-search-intersection.png)

Ciò significa che se non c'è intersezione tra i periodi di tempo, ad esempio se nel portale di OMS è stata scelta la **settimana corrente** e nella query è stata definita l'**ultima settimana**, non viene visualizzato alcun risultato.

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


>[AZURE.NOTE] La sintassi dell'intervallo da usare è il separatore di due punti (:), field:value, e *non* il segno di uguale (=). Racchiudere le estremità inferiore e superiore dell'intervallo tra parentesi quadre e separarle con due punti (..).

## Modificare i risultati della ricerca

Quando si esegue una ricerca di dati, è opportuno ridefinire la query di ricerca e avere un buon livello di controllo sui risultati. Quando i risultati vengono recuperati, è possibile applicare comandi per trasformarli.

I comandi nelle ricerche di Log Analytics *devono* seguire il carattere di barra verticale (|). Un filtro deve essere sempre la prima parte di una stringa di query. Definisce il set di dati che si sta usando e quindi "invia" i risultati in un comando. È quindi possibile usare il carattere di barra verticale per aggiungere altri comandi. Il funzionamento è simile a quello della pipeline di Windows PowerShell.

Il linguaggio di ricerca di Log Analytics segue in genere lo stile e le linee guida di PowerShell per semplificare la curva di apprendimento dei professionisti IT grazie a tale somiglianza.

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
- Nel campo della query di ricerca, digitare `Type=Event EventID=2110 | Top 1` ![top di ricerca](./media/log-analytics-log-searches/oms-search-top.png)

Nell'immagine precedente, sono presenti 988 record con EventID=2110. I campi, i facet e i filtri a sinistra mostrano sempre informazioni sui risultati restituiti *dalla parte filtro* della query, ovvero la parte prima di qualsiasi carattere di barra verticale. Il riquadro **Risultati** restituisce il risultato più recente, in quanto il comando di esempio ha dato una forma e trasformato i risultati.

### Selezionare

Il comando SELECT si comporta come Select-Object in PowerShell. Restituisce i risultati filtrati che non presentano tutte le relative proprietà originali. Al contrario, seleziona solo le proprietà specificate.

#### Per eseguire una ricerca usando il comando select

1. Nella ricerca, digitare `Type=Event` e quindi fare clic su **Search**.
2. Fare clic su **+ show more** in uno dei risultati per visualizzare tutte le proprietà dei risultati.
3. Selezionare alcune proprietà in modo esplicito, la query viene modificata in `Type=Event | Select Computer,EventID,RenderedDescription`. ![select di ricerca](./media/log-analytics-log-searches/oms-search-select.png)

Si tratta di comando particolarmente utile quando si desidera controllare l'output di ricerca e scegliere solo le parti di dati davvero importanti per l'esplorazione, che spesso non corrispondono al record completo. Il comando è utile anche quando record di tipo diverso hanno *alcune* proprietà comuni, ma non *tutte*. È possibile generare un output simile a una tabella o che funziona bene quando esportato in un file CSV e quindi modificato in Excel.



## Usare il comando measure

MEASURE è uno dei comandi più versatili nelle ricerche di Log Analytics. Consente di applicare *funzioni* statistiche ai dati e di aggregare i risultati raggruppati in base a un determinato campo. Esistono più funzioni statistiche supportate dal comando Measure.

### Measure count()

La prima funzione statistica da usare e una delle più semplici da comprendere è la funzione *count()*.

Nei risultati di una query di ricerca, ad esempio `Type=Event`, i filtri, anche denominati facet, vengono visualizzati sul lato sinistro dei risultati della ricerca. I filtri visualizzano una distribuzione di valori in base a un determinato campo, per i risultati della ricerca eseguita.

![measure count di ricerca](./media/log-analytics-log-searches/oms-search-measure-count01.png)

Ad esempio, nell'immagine precedente viene visualizzato il campo **Computer**. Tale campo indica che in quasi 3 milioni di eventi nei risultati, sono presenti 20 valori univoci e distinti per il campo **Computer** in questi record. Nel riquadro vengono visualizzati solo i primi 5 valori, che corrispondono ai 5 valori più comuni scritti nel campo **Computer**, ordinati in base al numero di documenti che contengono quel valore specifico in quel campo. Nell'immagine è possibile vedere che, di quasi 3 milioni di eventi, 880.000 provengono dal computer DM, 602.000 dal computer DE e così via.


Poiché nel riquadro vengono visualizzati solo i primi 5 valori, in che modo è possibile visualizzare tutti i valori?

Per visualizzare tutti i valori è possibile usare il comando measure con la funzione count(). Per questa funzione non viene usato alcun parametro. È sufficiente specificare il campo in base al quale si desidera eseguire il raggruppamento: in questo caso il campo **Computer**:

`Type=Event | Measure count() by Computer`

![measure count di ricerca](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

Tuttavia, **Computer** è semplicemente un campo usato *in* tutti i dati: non è coinvolto nessun database relazionale e non esiste alcun oggetto **Computer** separato altrove. Solo i valori presenti *nei* dati possono descrivere quale entità ha generato i dati e altre caratteristiche e aspetti dei dati, da qui il termine *facet*. Tuttavia, è possibile eseguire il raggruppamento anche in base ad altri campi. Poiché i risultati originali di quasi 3 milioni di eventi inviati al comando mesure presentano anche un campo denominato **EventID**, è possibile applicare la stessa tecnica per eseguire il raggruppamento in base a tale campo e ottenere un conteggio di eventi per EventID:

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

![measure count start di ricerca](./media/log-analytics-log-searches/oms-search-measure-max01.png)

Se si desidera visualizzare il valore massimo per tutti gli avvisi di un determinato computer comune, quindi raggrupparli per campo, è possibile usare

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![measure max computer di ricerca](./media/log-analytics-log-searches/oms-search-measure-max02.png)

La maggior parte dei computer che presentano record **Avvisi** hanno almeno un avviso critico e il computer Bacc ha un avviso con la gravità massima.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![measure max time generated computer di ricerca](./media/log-analytics-log-searches/oms-search-measure-max03.png)

Questa funzione è adatta ad essere usata con i numeri, ma può essere usata anche con i campi di data/ora. È utile per verificare l'ultima data/ora o la data/ora più recente relativa a tutti i dati indicizzati per ciascun computer. Ad esempio, quando è stata segnalata la modifica più recente alla configurazione dalla soluzione che tiene traccia delle modifiche per ogni computer?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## Usare la funzione avg con il comando measure

La funzione statistica Avg() usata con il comando measure consente di calcolare il valore medio per alcuni campi e di raggruppare i risultati in base allo stesso campo o ad un campo diverso. Questa funzione è utile in diversi casi, ad esempio con i dati sulle prestazioni.

Si inizierà con i dati sulle prestazioni. Si noti che OMS attualmente raccoglie i contatori delle prestazioni sia per i computer Windows che Linux.

Per la ricerca di *tutti* i dati sulle prestazioni, la query più semplice è:

```
Type=Perf
```

![avg start di ricerca](./media/log-analytics-log-searches/oms-search-avg01.png)

Si noti che Log Analytics mostra due prospettive. La prima è quella delle metriche, che include i grafici per i contatori delle prestazioni. L'altra è quella dei log, che include i record effettivi alla base dei grafici.

![avg start di ricerca](./media/log-analytics-log-searches/oms-search-avg02.png)

Nell'immagine precedente, sono presenti due set di campi contrassegnati che indicano quanto segue:

- Il primo set identifica il nome del contatore delle prestazioni di Windows, il nome dell'oggetto e il nome dell'istanza nel filtro della query. Questi sono i campi che probabilmente verranno usati in genere come facet/filtri
- **CounterValue** è il valore effettivo del contatore.
- **TimeGenerated** è 21:00, nel formato 24 ore. Rappresenta l'aggregazione per il periodo orario dalle 20:00 alle 21:00.

Dopo aver acquisito informazioni sulla forma del record Perf e su altre tecniche di ricerca, è possibile usare il comando measure Avg() per aggregare questo tipo di dati numerici.

Un semplice esempio viene riportato di seguito:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![avg samplevalue di ricerca](./media/log-analytics-log-searches/oms-search-avg03.png)

In questo esempio, viene selezionato il contatore delle prestazioni CPU Total Time e la media viene calcolata in base a Computer. Per limitare i risultati alle ultime sei ore, è possibile usare il controllo del filtro temporale o specificarlo nella query come indicato di seguito:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### Per eseguire la ricerca tramite la funzione avg con il comando measure
- Nella casella della query di ricerca, digitare `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`


È possibile aggregare e correlare i dati *tra* computer. Ad esempio, si supponga di disporre di un set di host in un certo tipo di farm in cui ogni nodo è uguale a qualsiasi altro nodo, tutti i nodi eseguono lo stesso tipo di lavoro e il carico è approssimativamente bilanciato. È possibile ottenere tutti i contatori contemporaneamente con la query seguente e ottenere le medie per l'intera farm. È possibile iniziare scegliendo i computer con l'esempio seguente:

```
Type=Perf AND (Computer="SERVER1.contoso.com" OR Computer="SERVER2.contoso.com" OR Computer="SERVER3.contoso.com")
```

Ora che si dispone dei computer, è possibile selezionare due indicatori delle prestazioni chiave (KPI): % CPU Usage e % Free Disk Space. Quindi, quella parte della query diventa:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Ora è possibile aggiungere computer e contatori con l'esempio seguente:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="SERVER1.contoso.com" OR Computer="SERVER2.contoso.com" OR Computer="SERVER3.contoso.com")
```

Poiché è stata effettuata una selezione molto specifica, il comando **measure Avg()** può restituire la media non in base al computer ma, all'interno della farm, eseguendo il raggruppamento per CounterName. Ad esempio:

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="SERVER1.contoso.com" OR Computer="SERVER2.contoso.com" OR Computer="SERVER3.contoso.com") | Measure Avg(CounterValue) by CounterName
```

Ciò consente una visualizzazione utile e compatta di un paio di indicatori di prestazioni chiave dell'ambiente.

![avg grouping di ricerca](./media/log-analytics-log-searches/oms-search-avg04.png)


È possibile usare facilmente questo comando in un dashboard. Per altre informazioni sull'uso dei dashboard, vedere l'articolo relativo alla [creazione di un dashboard personalizzato in Log Analytics](log-analytics-dashboards.md).

![avg dashboard di ricerca](./media/log-analytics-log-searches/oms-search-avg05.png)

### Usare la funzione sum con il comando measure

La funzione sum è simile ad altre funzioni del comando measure. È possibile vedere un esempio su come usare la funzione sum nell'argomento relativo alla [ricerca di Log W3C IIS in Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

È possibile usare Max() e Min() con numeri, intervalli di data/ora e stringhe di testo. Le stringhe di testo sono ordinate in ordine alfabetico e vengono visualizzate la prima e l'ultima.

Tuttavia, è possibile usare Sum() con elementi diversi dai campi numerici. Questo vale anche per Avg().

### Usare la funzione percentile con il comando measure

La funzione percentile è simile a Avg() e Sum() perché può essere usata unicamente per i campi numerici. In un campo numerico è possibile usare qualsiasi percentile compreso tra 1 e 99. È possibile usare sia il comando **percentile** che il comando **pct**. Di seguito sono riportati alcuni esempi:

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## Usare il comando where

Il comando where funziona come un filtro, ma può essere applicato nella pipeline per filtrare ulteriormente i risultati aggregati prodotti da un comando Measure, differentemente dai risultati non elaborati che vengono filtrati all'inizio di una query.

ad esempio:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

È possibile aggiungere un altro carattere di barra verticale "|" e il comando where per visualizzare solo i computer il cui utilizzo medio della CPU è superiore all'80%, con l'esempio seguente:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Se si ha familiarità con Microsoft System Center - Operations Manager, è possibile considerare il comando where in termini di Management Pack. Se l'esempio fosse una regola, la prima parte della query sarebbe l'origine dati e il comando where sarebbe il rilevamento della condizione.

È possibile usare la query come un riquadro in **My Dashboard**, come un monitoraggio di ordinamenti, per verificare se sono presenti CPU di computer sovrautilizzate. Per altre informazioni sui dashboard, vedere l'articolo relativo alla [creazione di un dashboard personalizzato in Log Analytics](log-analytics-dashboards.md). È possibile creare e usare i dashboard anche tramite l'app per dispositivi mobili. Per altre informazioni, vedere la pagina relativa all'[app OMS per dispositivi mobili](http://www.windowsphone.com/it-IT/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). Nei due riquadri in basso dell'immagine seguente, il monitoraggio viene visualizzato come un elenco e come un numero. Essenzialmente, è auspicabile che il numero sia sempre zero e l'elenco sia sempre vuoto. In caso contrario, indica una condizione di avviso. Se necessario, è possibile usarlo per individuare quali computer sono sotto pressione.

![dashboard mobile](./media/log-analytics-log-searches/oms-search-mobile.png)

## Usare l'operatore IN

Gli operatori *IN* e *NOT IN* permettono di usare le sottoricerche, ovvero ricerche che includono un'altra ricerca come argomento. Sono racchiuse tra parentesi graffe {} all'interno di un'altra ricerca *primaria* o *esterna*. Il risultato di una sottoricerca, costituito spesso da un elenco di risultati distinti, viene quindi usato come argomento nella ricerca primaria.

È possibile usare le sottoricerche per la corrispondenza con subset di dati che non è possibile descrivere direttamente in un'espressione di ricerca, ma che possono essere generati da una ricerca. Ad esempio, se si vuole usare una sola ricerca per trovare tutti gli eventi da *computer privi di aggiornamenti della sicurezza*, è necessario progettare prima di tutto una sottoricerca che identifichi i *computer privi di aggiornamenti della sicurezza* per poi trovare gli eventi appartenenti a tali host.

Per specificare i *computer attualmente privi dei necessari aggiornamenti della sicurezza* è possibile usare la query seguente:

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![esempio di ricerca con IN](./media/log-analytics-log-searches/oms-search-in01-revised.png)

Dopo aver ottenuto l'elenco, la ricerca può essere usata come ricerca interna per inserire l'elenco dei computer in una ricerca esterna, o primaria, che cerca gli eventi relativi a tali computer. A questo scopo, racchiudere tra parentesi la ricerca interna e inserire i risultati come possibili valori per un filtro o un campo nella ricerca esterna usando l'operatore IN. La query sarà simile alla seguente:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![esempio di ricerca con IN](./media/log-analytics-log-searches/oms-search-in02-revised.png)


Si noti anche il filtro temporale usato nella ricerca interna perché System Update Assessment acquisisce uno snapshot di tutti i computer ogni 24 ore. È possibile rendere più leggera e precisa la query interna cercando soltanto un giorno. La ricerca esterna usa invece la selezione della data/ora nell'interfaccia utente e recupera gli eventi degli ultimi sette giorni. Per altre informazioni sugli operatori temporali, vedere la sezione [Operatori booleani](#boolean-operators).

Dato che i risultati della ricerca interna vengono usati solo come valore filtro per quella esterna, è comunque possibile applicare i comandi alla ricerca esterna. Ad esempio, è ancora possibile raggruppare gli eventi mostrati sopra con un altro comando measure:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![esempio di ricerca con IN](./media/log-analytics-log-searches/oms-search-in03-revised.png)


In genere, la query interna deve essere eseguita rapidamente perché Log Analytics prevede timeout sul lato servizio e anche per ottenere una quantità ridotta di risultati. Se la query interna restituisce più risultati, l'elenco dei risultati viene troncato e questo può portare a risultati non corretti restituiti dalla ricerca esterna.

La ricerca interna attualmente deve fornire risultati *aggregati*. In altre parole deve contenere un comando *measure*, perché attualmente non è possibile inserire risultati non elaborati in una ricerca esterna.

Può essere presente un solo operatore IN e deve essere l'ultimo filtro nella query. Non è possibile usare gli operatori OR con più operatori IN. Questo di fatto impedisce l'esecuzione di più sottoricerche. Tenere quindi presente che per ogni ricerca esterna può essere presente una sola ricerca interna o sottoricerca.

Nonostante queste limitazioni, l'operatore IN permette di eseguire molti tipi di ricerche correlate e consente di definire qualcosa di simile ai gruppi, ad esempio computer, utenti, file o qualunque cosa contengano i campi dei dati. Di seguito sono riportati altri esempi:

**Tutti gli aggiornamenti mancanti nei computer in cui è disabilitato l'aggiornamento automatico**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**Tutti gli eventi di errore dai computer che eseguono SQL Server, in cui viene eseguito SQL Assessment**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**Tutti gli eventi di sicurezza dai computer che sono controller di dominio, in cui viene eseguito AD Assessment**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**Quali altri account hanno eseguito l'accesso agli stessi computer a cui si è connesso l'account BACONLAND\\jochan?**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## Usare il comando distinct

Come suggerisce il nome, questo comando restituisce un elenco di valori distinct per un campo. È estremamente semplice ma molto utile. È possibile ottenere lo stesso risultato con il comando measure count(), come illustrato di seguito.

```
Type=Event | Measure count() by Computer
```

![esempio di comando di ricerca DISTINCT](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

Tuttavia, per ottenere semplicemente un elenco di valori distinct e non il conteggio dei documenti che li contengono, il comando distinct fornisce un output più chiaro e leggibile con una sintassi più breve, come illustrato di seguito.

```
Type=Event | Distinct Computer
```
![esempio di comando di ricerca DISTINCT](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## Usare la funzione countdistinct con il comando measure
La funzione countdistinct conta il numero di valori distinct in ogni gruppo. Può essere usata, ad esempio, per contare il numero di computer univoci che creano report per ogni tipo:

```
* | measure countdistinct(Computer) by Type
```

![OMS-countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## Usare il comando measure interval
La raccolta di dati sulle prestazioni quasi in tempo reale permette di raccogliere e visualizzare qualsiasi contatore delle prestazioni in Log Analytics. La semplice query **Type:Perf** restituisce migliaia di grafici delle metriche basati sul numero di contatori e server nell'ambiente di Log Analytics. L'aggregazione delle metriche su richiesta permette di esaminare tutte le metriche nell'ambiente a livello generale e di approfondire i dati più granulari in base alle esigenze.

Si supponga di voler conoscere l'utilizzo medio della CPU in tutti i computer. Esaminare l'utilizzo medio della CPU di ogni computer potrebbe non essere utile perché i risultati potrebbero essere livellati. Per ottenere informazioni più dettagliate, è possibile aggregare il risultato in blocchi di intervalli di tempo più piccoli ed esaminare una serie temporale da varie dimensioni. Ad esempio, per calcolare la media oraria di utilizzo della CPU tra tutti i computer, è possibile procedere come indicato di seguito:

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![misura intervallo medio](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

Per impostazione predefinita, questi risultati vengono visualizzati in un grafico a linee interattivo a più serie. Il grafico supporta l'attivazione e la disattivazione delle serie con ridimensionamento dell'asse Y, lo zoom e il passaggio del puntatore. L'opzione di visualizzazione tabella è ancora disponibile per visualizzare i dati non elaborati, se necessario.

È anche possibile raggruppare in base ad altri campi. In questo esempio vengono esaminati tutti i contatori % per un computer specifico e si vuole conoscere il percentile 70 orario di ogni contatore:

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
Un aspetto da sottolineare è che queste query non sono limitate ai contatori delle prestazioni. Possono essere applicate a qualsiasi metrica. In questo esempio si esaminano i log di IIS W3C. Si vuole conoscere il tempo massimo impiegato in un intervallo di cinque minuti per l'elaborazione di ogni richiesta:

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### Usare più aggregazioni in una query
È possibile specificare più clausole di aggregazione in un comando measure. Ognuna di esse può essere associata a un alias in modo indipendente. Se non viene associata a un alias, il nome del campo risultante sarà la funzione di aggregazione che è stata usata, ad esempio "avg(CounterValue)" per avg(CounterValue).

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS-multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

Di seguito è riportato un altro esempio:
 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## Passaggi successivi

Per altre informazioni sulle ricerche nei log:

- Usare [Campi personalizzati in Log Analytics](log-analytics-custom-fields.md) per estendere le ricerche nei log.
- Vedere il [riferimento alla ricerca nei log di Log Analytics](log-analytics-search-reference.md) per visualizzare tutti i campi di ricerca e i facet disponibili in Log Analytics.

<!---HONumber=AcomDC_0504_2016-->