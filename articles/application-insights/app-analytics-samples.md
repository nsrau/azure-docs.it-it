<properties 
	pageTitle="Esempi di query in Application Insights - Analytics" 
	description="Esempi di query in Application Insights - Analytics, lo strumento di ricerca avanzato per Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>

# Esempi di Analytics per Application Insights Analytics

[Analytics](app-analytics.md) consente di eseguire query importanti sui dati di telemetria dell'app raccolti da [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query usato da Analytics. È inoltre disponibile un'[introduzione al linguaggio](app-analytics-tour.md) utile per la fase iniziale.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Procedure consigliate per le query 

Esistono diverse operazioni consigliate e sconsigliate cui è possibile attenersi per un'esecuzione più rapida delle query.

OPERAZIONI CONSIGLIATE:

-	Usare prima i filtri data/ora. Application Insights - Analytics è ottimizzato per l'uso dei filtri data/ora.
-	Inserire i filtri previsti immediatamente dopo i filtri data/ora per escludere la maggior parte dei dati all'inizio della query
-	Verificare che la maggior parte dei filtri vengono visualizzati all'inizio della query, prima di iniziare a usare 'extend' 
-	Per la ricerca di token completi, preferire la parola chiave 'has' a 'contains'. 'has' offre migliori prestazioni poiché non richiede la ricerca di sottostringhe.
-	Preferire la ricerca in una colonna specifica all'uso di '*' (ricerca full-text in tutte le colonne)
-	Quando si usa join, selezionare soltanto le colonne richieste da entrambi i lati del join, ciò consentirà di ridurre il recupero del payload da un computer all'altro

OPERAZIONI SCONSIGLIATE:

-	Provare nuove query senza 'limit [numero piccolo]' o 'count' alla fine. L'esecuzione di query non associate su set di dati sconosciuti può causare la restituzione di diversi GB di risultati al client, rallentando i tempi di risposta e occupando il cluster.
-	Se vengono applicate conversioni (JSON, stringa, ecc.) su record 1B+, modificare la query per ridurre la quantità di dati inseriti nella conversione





<a name="activities"></a>
## Individuare le sessioni in base agli eventi start e stop

Si supponga di disporre di un log eventi in cui alcuni eventi indicano l'inizio o la fine di un'attività estesa o di una sessione.

|Nome|city|SessionId|Timestamp|
|---|---|---|---|
|Inizia|Londra|2817330|2015-12-09T10:12:02.32|
|Game|Londra|2817330|2015-12-09T10:12:52.45|
|Inizia|Manchester|4267667|2015-12-09T10:14:02.23|
|Arresto|Londra|2817330|2015-12-09T10:23:43.18|
|Annulla|Manchester|4267667|2015-12-09T10:27:26.29|
|Arresto|Manchester|4267667|2015-12-09T10:28:31.72|

Poiché ogni evento include un valore SessionId, è necessario individuare gli eventi start e stop con lo stesso ID.

```AIQL
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on activityId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

Usare [`let`](app-analytics-syntax.md#let-statements) per assegnare un nome a una proiezione della tabella ridotta il più possibile prima di passare al join. [`Project`](app-analytics-queries.md#project-operator) consente di modificare i nomi di timestamp per fare in modo che vengano visualizzate nel risultato sia l'ora di inizio che l'ora di fine. Consente inoltre di selezionare ulteriori colonne da visualizzare nel risultato. [`join`](app-analytics-queries.md#join-operator) stabilisce una corrispondenza tra start e stop della stessa attività creando una riga per ogni attività. Infine, `project` aggiunge una colonna per visualizzare la durata dell'attività.


|city|SessionId|StartTime|StopTime|Durata|
|---|---|---|---|---|
|Londra|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### Individuare le sessioni senza ID sessione

Si supponga che gli eventi start e stop non includano un ID sessione in cui individuare la corrispondenza. È tuttavia indicato un indirizzo IP del client in cui è stata eseguita la sessione. Se si presuppone che ogni indirizzo client esegua una sola sessione alla volta, è possibile associare ogni evento start al successivo evento stop proveniente dallo stesso indirizzo IP.

```AIQL
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize argmin(duration, *) by bin(StartTime,1s), ClientIp
```

Il join consentirà di associare ogni ora di inizio a tutte le ore di fine provenienti dallo stesso indirizzo IP del client. Di conseguenza, vengono prima rimosse le corrispondenze con le ore di fine precedenti.

Si procede quindi a raggruppare in base a ora di inizio e IP per creare un gruppo per ogni sessione. È necessario specificare una funzione `bin` per il parametro StartTime. In caso contrario, Analytics userà automaticamente bin di 1 ora che assoceranno alcune ore di inizio a ore di fine errate.

`argmin` rileva la riga con la durata minore in ogni gruppo, mentre il parametro `*` passa tutte le altre colonne, inserendo il prefisso "min\_" in ogni nome di colonna.


![](./media/app-analytics-samples/040.png)

È quindi possibile aggiungere un codice per conteggiare le durate in contenitori di dimensioni adatte. Poiché si preferisce usare il grafico a barre, si esegue la divisione per `1s` per convertire gli intervalli di tempo in numeri.


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Prepare for display in a bar chart:
    | sort by duration asc 


![](./media/app-analytics-samples/050.png)


### Esempio reale

```AIQL

Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  	 
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## Rappresentare sessioni simultanee nel tempo in un grafico

Si supponga di disporre di una tabella delle attività con le relative ore di inizio e di fine. Si desidera visualizzare un grafico che mostra quante sessioni vengono eseguite simultaneamente in ogni momento.

Di seguito è riportato un esempio di input denominato `X`:

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Poiché si desidera creare un grafico con contenitori di 1 minuto, si creerà un elemento che potrà essere calcolato, a ogni intervallo di 1 minuto, per ogni attività in esecuzione.

Di seguito è riportato un risultato intermedio:

     X | extend samples = range(bin(StartTime, 1m), Stop, 1m)

`range` genera una matrice di valori agli intervalli specificati:

|SessionId | StartTime | StopTime | esempi|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Tuttavia, anziché mantenere tali matrici, si esegue l'[espansione](app-analytics-queries.md#mvexpand-operator):

    X | mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)


|SessionId | StartTime | StopTime | esempi|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | 10:01:00|
| a | 10:03:33 | 10:06:31 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| a | 10:03:33 | 10:06:31 | 10:03:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| a | 10:03:33 | 10:06:31 | 10:04:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|
|...||||

Si potrà ora raggrupparle per ora, conteggiando le occorrenze di ogni attività:

```
X
| mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* È necessario specificare todatetime() perché `mvexpand` produce una colonna di tipo dinamico.
* È necessario specificare bin() perché, per valori numerici e date, il riepilogo applica sempre una funzione bin con intervallo predefinito se non specificato. 


| count\_SessionId | esempi|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|

Questi dati possono essere rappresentati come grafico a barre o un grafico di tempo.


## Tipi di join

Il tipo di operatore join viene specificato con la parola chiave kind. Analytics supporta ora sei tipi di operatore join: inner join con deduplicazione lato sinistro (valore predefinito), inner join standard, left outer join, right outer join, full outer join e left anti join.
 
Tipo di join predefinito (nessun tipo specificato) Le due tabelle di esempio che seguono descrivono il funzionamento del join:
 
Tabella X


|Chiave |Value1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

Tabella Y

|Chiave |Value2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 

 
Il join predefinito esegue un inner join dopo la deduplicazione del lato sinistro della chiave di join (la deduplicazione mantiene il primo record). Se si considera l'istruzione seguente:

    X | join Y on Key 

l'effettivo lato sinistro del join (tabella X dopo la deduplicazione) sarà:

|Chiave |Value1 
|---|---
|a |1 
|b |2 
|c |4 

e il risultato del join sarà:

|Chiave |Value1 |Chiave |Value2 
|---|---|---|---
|b |2 |b |10 
|c |4 |c |20 
|c |4 |c |30 

Si noti che le chiavi 'a' e 'd' non vengono visualizzate nell'output poiché non sono presenti chiavi corrispondenti su entrambi i lati destro e sinistro.
 
Questa è stata la prima implementazione di join supportata dalla versione iniziale di Analytics. Tale implementazione è utile negli scenari di analisi di log/traccia tipici in cui si desidera correlare due eventi (ognuno dei quali corrispondente a un determinato criterio di filtro) con lo stesso ID correlazione e ottenere tutte le occorrenze del fenomeno ricercato, ignorando le occorrenze multiple dei record di traccia interessati.
 
### Inner join (kind=inner) 

Si tratta dell'inner join standard del linguaggio SQL. Il record di output viene generato ogni volta che un record sul lato sinistro ha la stessa chiave di join del record sul lato destro.
 
    X | join kind=inner Y on Key 

il risultato è il seguente:

|Chiave |Value1 |Chiave |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 

Si noti che per (b,10) proveniente dal lato destro è stato eseguito il join due volte: con entrambi (b,2) e (b,3) a sinistra. Anche per (c,4) a sinistra è stato eseguito il join due volte: con entrambi (c,20) e (c,30) a destra.

### Left outer join (kind=leftouter) 

Il risultato di un left outer join per le tabelle X e Y contiene sempre tutti i record della tabella di sinistra (X), anche se la condizione di join non rileva alcun record corrispondente nella tabella di destra (Y).
 
Si consideri quanto segue:

    X | join kind=leftouter Y on Key 

Risultato:

|Chiave |Value1 |Chiave |Value2 
|---|---|---|---
|a |1 |Null |Null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
 
### Right outer join (kind=rightouter) 

Simile a left outer join, ma il trattamento delle tabelle è invertito.
 
Si consideri quanto segue:

    X | join kind=rightouter Y on Key 

Risultato:


|Chiave |Value1 |Chiave |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|Null |Null |d |40 
 
### Full outer join (kind=fullouter) 

Teoricamente, un full outer join corrisponde all'applicazione di entrambi left outer join e right outer join. Se i record nelle tabelle di cui è stato eseguito il join non corrispondono, il set di risultati includerà valori NULL per ogni colonna della tabella che non contiene una riga corrispondente. I record corrispondenti, verrà generata una singola riga nel set di risultati (contenente i campi popolati da entrambe le tabelle).
 
Si consideri quanto segue:

    X | join kind=fullouter Y on Key 

Risultato:

|Chiave |Value1 |Chiave |Value2 
|---|---|---|---
|a |1 |Null |Null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|Null |Null |d |40 
 
### Left anti join (kind=leftanti) 

Left anti join restituisce tutti i record del lato sinistro che non corrispondono ad alcun record del lato destro.
 
    X | join kind=leftanti Y on Key 
 
Risultato:

|Chiave |Value1 
|---|---
|a |1 
 
Anti-join definisce la query "NOT IN".



## Join con mapping di dizionario inline

```AIQL

let TeamFoundationJobResult = range i from 1 to 1 step 1 
  | extend recordsJson = "[ 
    { 'key': -1, 'value': 'None'}, 
    { 'key': 0, 'value': 'Succeeded'}, 
    { 'key': 1, 'value': 'PartiallySucceeded'}, 
    { 'key': 2, 'value': 'Failed'}, 
    { 'key': 3, 'value': 'Stopped'}, 
    { 'key': 4, 'value': 'Killed'}, 
    { 'key': 5, 'value': 'Blocked'}, 
    { 'key': 6, 'value': 'ExtensionNotFound'}, 
    { 'key': 7, 'value': 'Inactive'}, 
    { 'key': 8, 'value': 'Disabled'}, 
    { 'key': 9, 'value': 'JobInitializationError'} 
  ]" 
  | mvexpand record = parsejson(replace("'", """, recordsJson)) 
  | project Result = toint(record.key), ResultString = tostring(record.value); 
JobHistory 
  | where PreciseTimeStamp > ago(1h)  
  | where Service  <> "AX" 
  | where Plugin has "Analytics"  
  | sort by PreciseTimeStamp desc 
  | join kind=leftouter TeamFoundationJobResult on Result 
  | extend ExecutionTimeSpan = totimespan(ExecutionTime) 
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage  
```





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->