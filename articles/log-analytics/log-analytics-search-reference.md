---
title: Guida di riferimento alla ricerca in Log Analytics di Azure | Microsoft Docs
description: "La Guida di riferimento alla ricerca in Log Analytics descrive il linguaggio di ricerca e fornisce le opzioni della sintassi di query generale che è possibile usare quando si cercano dati e si filtrano espressioni per restringere la ricerca."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 402615a2-bed0-4831-ba69-53be49059718
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 264ea071dc0b15964af07c68cbf0dee896b07a3e
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2017
---
# <a name="log-analytics-search-reference"></a>Guida di riferimento alla ricerca in Log Analytics

>[!NOTE]
> Questo articolo descrive le ricerche log con il linguaggio di query legacy di Log Analytics.  Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), è consigliabile vedere le [informazioni di riferimento sul nuovo linguaggio](https://go.microsoft.com/fwlink/?linkid=856079).

La sezione di riferimento seguente relativa al linguaggio di ricerca descrive le opzioni della sintassi di query generale che è possibile usare quando si ricercano dati e si filtrano espressioni per restringere la ricerca. Vengono inoltre descritti i comandi che è possibile usare per intervenire sui dati recuperati.

È possibile ottenere informazioni sui campi restituiti nelle ricerche e sui facet che consentono di ottenere maggiori informazioni sulle categorie di dati simili nella [sezione Riferimenti al campo Ricerca e ai facet](#search-field-and-facet-reference).

## <a name="general-query-syntax"></a>Sintassi di query generale
La sintassi per l'esecuzione di query generale è la seguente:

```
filterExpression | command1 | command2 …
```

L'espressione di filtro (`filterExpression`) definisce la condizione "where" per la query. I comandi si applicano ai risultati restituiti dalla query. Più comandi devono essere separati dal carattere di barra verticale (|).

### <a name="general-syntax-examples"></a>Esempi di sintassi generale
Esempi:

```
system
```

Questa query restituisce risultati che contengono la parola *system* in qualsiasi campo che è stato indicizzato per la ricerca full-text o di termini.

> [!NOTE]
> Non tutti i campi sono indicizzati in questo modo, ma in genere lo sono i campi di testuali più comuni, ad esempio nomi e descrizioni.
>
>

```
system error
```

Questa query restituisce risultati che contengono le parole *system* ed *error*.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

Questa query restituisce risultati che contengono le parole *system* ed *error*. Quindi ordina i risultati in base al campo *ManagementGroupName* (in ordine crescente) e in base al campo *TimeGenerated* (in ordine decrescente). Visualizza solo i primi dieci risultati.

> [!IMPORTANT]
> Tutti i nomi dei campi e i valori dei campi di tipo stringa e testo fanno distinzione tra maiuscole e minuscole.
>
>

## <a name="filter-expressions"></a>Espressioni filtro
Nelle sottosezioni seguenti vengono illustrate le espressioni di filtro.

### <a name="string-literals"></a>Valori letterali stringa
Un valore letterale stringa è qualsiasi stringa non riconosciuta dal parser come una parola chiave o come un tipo di dati predefiniti (ad esempio, un numero o una data).

Esempi:

```
These all are string literals
```

Questa query cerca i risultati che contengono occorrenze di tutte le cinque parole. Per eseguire una ricerca di stringa complessa, racchiudere il valore letterale di stringa tra virgolette, ad esempio:

```
"Windows Server"
```

Restituisce solo i risultati con corrispondenze esatte per *Windows Server*.

### <a name="numbers"></a>Numeri
Il parser supporta la sintassi di numeri interi decimali e a virgola mobile per i campi numerici.

Esempi:

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="dates-and-times"></a>Date e ore
Tutti i dati nel sistema presentano una proprietà *TimeGenerated* che rappresenta la data e ora originali del record. Alcuni tipi di dati possono inoltre avere campi di data e ora aggiuntivi, ad esempio *LastModified*.

Il selettore di **grafico e ora** della sequenza temporale in Log Analytics di Azure mostra una distribuzione dei risultati nel tempo, secondo la query corrente in esecuzione. Si basa sul campo *TimeGenerated*. I campi di data e ora hanno un formato di stringa specifico che può essere usato nelle query per limitare la query a un determinato intervallo di tempo. È inoltre possibile usare la sintassi per fare riferimento a intervalli di tempo relativi (ad esempio, "tra 3 giorni fa e 2 ore fa").

Di seguito sono riportati i formati validi di sintassi per le date e le ore:

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


ad esempio:

```
TimeGenerated:2013-10-01T12:20
```

Il comando precedente restituisce solo i record con un valore *TimeGenerated* corrispondente alle 12:20 del 1° ottobre 2013.

Il parser supporta anche il valore di data e ora mnemonico, NOW. È improbabile che vengano restituiti risultati, in quanto i dati non producono risultati attraverso il sistema in modo così rapido.

Questi esempi sono blocchi predefiniti da usare per le date relative e assolute. Nelle tre sezioni successive verrà illustrato come usare tali blocchi in filtri più avanzati con esempi che usano intervalli di date relative.

### <a name="datetime-math"></a>Operatori matematici di data/ora
Usare gli operatori matematici di data/ora per eseguire l'offset o arrotondare il valore di data/ora usando semplici calcoli di data/ora.

Sintassi:

```
datetime/unit
```

```
datetime[+|-]count unit
```

| Operatore | Descrizione |
| --- | --- |
| / |La data/ora viene arrotondata all'unità specificata. Ad esempio, NOW/DAY arrotonda la data/ora corrente a mezzanotte del giorno corrente. |
| + o - |Esegue l'offset della data/ora in base al numero specificato di unità. Ad esempio, NOW+1HOUR esegue l'offset della data/ora corrente di un'ora in avanti. 2013-10-01T12:00-10DAYS esegue l'offset del valore della data indietro di 10 giorni. |

È possibile concatenare gli operatori matematici di data/ora. Ad esempio:

```
NOW+1HOUR-10MONTHS/MINUTE
```

Nella tabella seguente vengono elencate le unità di data/ora supportate.

| Unità di data/ora | Descrizione |
| --- | --- |
| YEAR, YEARS |Viene arrotondata all'anno corrente o viene eseguito l'offset in base al numero di anni specificato. |
| MONTH, MONTHS |Viene arrotondata al mese corrente o viene eseguito l'offset in base al numero di mesi specificato. |
| DAY, DAYS, DATE |Viene arrotondata al giorno corrente del mese o viene eseguito l'offset in base al numero di giorni specificato. |
| HOUR, HOURS |Viene arrotondata all'ora corrente o viene eseguito l'offset in base al numero di ore specificato. |
| MINUTE, MINUTES |Viene arrotondata al minuto corrente o viene eseguito l'offset in base al numero di minuti specificato. |
| SECOND, SECONDS |Viene arrotondata al secondo corrente o viene eseguito l'offset in base al numero di secondi specificato. |
| MILLISECOND, MILLISECONDS, MILLI, MILLIS |Viene arrotondata al millisecondo corrente o viene eseguito l'offset in base al numero di millisecondi specificato. |

### <a name="field-facets"></a>Facet di campo
Usando i facet di campo è possibile specificare la condizione di ricerca per campi specifici e i relativi valori esatti, anziché scrivere query di "testo libero" per diversi termini dell'indice. Questa sintassi è già stata usata in vari esempi nei paragrafi precedenti. Vengono ora forniti esempi più complessi.

**Sintassi**

```
field:value
```

```
field=value
```

**Descrizione**

Cerca il valore specifico nel campo. Il valore può essere un valore letterale stringa, un numero o una data/ora.

Ad esempio:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**Sintassi**

*field>value*

*field<value*

*field&gt;=value*

*field<=value*

*field!=value*

**Descrizione**

Fornisce i confronti.

ad esempio:

```
TimeGenerated>NOW+2HOURS
```

**Sintassi**

```
field:[from..to]
```

**Descrizione**

Fornisce il facet di intervallo.

Ad esempio:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="in"></a>IN
La parola chiave **IN** consente di scegliere da un elenco di valori. A seconda della sintassi usata, questo può essere un semplice elenco di valori forniti o un elenco di valori di un'aggregazione.

Sintassi 1:

```
field IN {value1,value2,value3,...}
```

Questa sintassi consente di includere tutti i valori in un elenco semplice.



Esempi:

```
EventID IN {1201,1204,1210}
```

```
Computer IN {"srv01.contoso.com","srv02.contoso.com"}
```

Sintassi 2:

```
(Outer query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

Questa sintassi consente di creare un'aggregazione. È possibile popolare l'elenco di valori da tale aggregazione in un'altra ricerca esterna (primaria) che cercherà gli eventi con tali valori. A questo scopo, racchiudere tra parentesi la ricerca interna e inserire i risultati come possibili valori per un campo nella ricerca esterna usando l'operatore IN.

Esempio di query interna: *computer attualmente privi di aggiornamenti della sicurezza* con la query di aggregazione seguente:

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

La query finale che trova *tutti gli eventi di Windows per i computer attualmente privi di aggiornamenti della sicurezza* sarà simile alla seguente:

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="contains"></a>Contiene
La parola chiave **Contains** consente di filtrare i record con un campo che contiene una stringa specificata. Viene fatta distinzione tra maiuscole e minuscole, funziona solo con campi di tipo stringa e non può includere i caratteri escape.

Sintassi:

```
field:contains("string")
```

Esempio:

```
Type:contains("Event")
```

Restituisce i record con un tipo che contiene la stringa "Event". Gli esempi includono **Event**, **SecurityEvent** e **ServiceFabricOperationEvent**.



### <a name="regular-expressions"></a>Espressioni regolari
È possibile specificare una condizione di ricerca per un campo con un'espressione regolare usando la parola chiave **Regex**. Per una descrizione completa della sintassi che è possibile usare nelle espressioni regolari, vedere [Uso di espressioni regolari per filtrare ricerche log in Log Analytics](log-analytics-log-searches-regex.md).

Sintassi:

```
field:Regex("Regular Expression")
```

Esempio:

```
Computer:Regex("^C.*")
```

### <a name="logical-operators"></a>Operatori logici
I linguaggi di query supportano gli operatori logici (*AND*, *OR* e *NOT*) e i relativi alias di tipo C, rispettivamente (*&&*, *||* e *!*). È possibile usare le parentesi per raggruppare questi operatori.

Esempi:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

È possibile omettere l'operatore logico per gli argomenti di filtro di primo livello. In questo caso, viene usato l'operatore AND.

| Espressione di filtro | Equivalente a |
| --- | --- |
| system error |system AND error |
| system "Windows Server" OR Severity:1 |system AND ("Windows Server" OR Severity:1) |

### <a name="wildcarding"></a>Utilizzo dei caratteri jolly
Il linguaggio di query supporta l'uso del carattere (\*) per rappresentare uno o più caratteri per un valore in una query.

Esempio:

 Trovare tutti i computer con "SQL" nel nome, ad esempio "Redmond-SQL".

```
Type=Event Computer=*SQL*
```

> [!NOTE]
> I caratteri jolly attualmente non possono essere usati tra virgolette, ad esempio, il messaggio `"*This text*"` considererà (\*) usato come carattere letterale (\*).


## <a name="commands"></a>Comandi:


I comandi si applicano ai risultati restituiti dalla query. Usare il carattere di barra verticale (|) per applicare un comando ai risultati recuperati. Più comandi devono essere separati dal carattere di barra verticale (|).

> [!NOTE]
> I nomi dei comandi possono essere scritti in lettere maiuscole o minuscole, a differenza dei nomi dei campi e dei dati.
>
>

### <a name="sort"></a>Ordina
Sintassi:

    sort field1 asc|desc, field2 asc|desc, …

Ordina i risultati in base a determinati campi. Il prefisso asc/desc per ordinare i risultati in ordine crescente o decrescente sono facoltativi. Se viene omesso, viene usato l'ordinamento *asc*. Per il campo **TimeGenerated** si presuppone l'ordinamento *desc* in modo che, per impostazione predefinita, vengano restituiti sempre prima i risultati più recenti.

### <a name="toplimit"></a>Top/Limit
Sintassi:

    top number


    limit number
Limita la risposta ai primi N risultati.

Esempio:

    Type:Alert errors detected | top 10

Restituisce i primi 10 risultati corrispondenti.

### <a name="skip"></a>Skip
Sintassi:

    skip number

Ignora il numero di risultati elencati.

Esempio:

    Type:Alert errors detected | top 10 | skip 200

Restituisce i primi 10 risultati corrispondenti a partire dal risultato 200.

### <a name="select"></a>Selezionare
Sintassi:

    select field1, field2, ...

Limita i risultati ai campi selezionati.

Esempio:

    Type:Alert errors detected | select Name, Severity

Limita i campi dei risultati restituiti a *Name* e *Severity*.

### <a name="measure"></a>Measure
Il comando *measure* viene usato per applicare funzioni statistiche ai risultati della ricerca non elaborati. Questo comando è molto utile per ottenere visualizzazioni di tipo *group-by* dei dati. Quando si usa il comando measure , la ricerca di Log Analytics visualizza una tabella con risultati aggregati.

**Sintassi:**

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



Aggrega i risultati per *groupField* e calcola i valori di misura aggregati tramite l'oggetto *aggregatedField*.

| Funzione statistica di measure | Descrizione |
| --- | --- |
| *aggregateFunction* |Nome della funzione di aggregazione (senza distinzione tra maiuscole e minuscole). Sono supportate le funzioni di aggregazione seguenti: COUNT MAX MIN SUM AVG STDDEV COUNTDISTINCT PERCENTILE## o PCT## (## è un numero compreso tra 1 e 99). |
| *aggregatedField* |Campo da aggregare. Questo campo è facoltativo per la funzione di aggregazione COUNT, ma deve essere un campo numerico esistente per SUM, MAX, MIN, AVG STDDEV, PERCENTILE## oppure PCT## (## è un numero compreso tra 1 e 99). Anche aggregatedField può essere una delle funzioni **Extend** supportate. |
| *fieldAlias* |Alias (facoltativo) per il valore aggregato calcolato. Se non viene specificato, il nome del campo sarà **AggregatedValue**. |
| *groupField* |Nome del campo in base al quale viene raggruppato il set di risultati. |
| *Interval* |L'intervallo di tempo nel formato:**nnnNAME**. **nnn** è il numero intero positivo. **NAME** è il nome dell'intervallo. I nomi di intervallo supportati fanno tra maiuscole e minuscole e includono: MILLISECOND[S] SECOND[S] MINUTE[S] HOUR[S] DAY[S] MONTH[S] e YEAR[S]. |

L'opzione intervallo può essere usata solo nei campi del gruppo data/ora (ad esempio *TimeGenerated* e *TimeCreated*. Questo non viene applicato dal servizio, ma attualmente un campo senza data/ora passato al back-end causa un errore di runtime. Quando viene implementata la convalida dello schema, l'API del servizio rifiuta le query che usano campi senza data/ora per l'aggregazione di intervalli. L'implementazione corrente di *Measure* supporta il raggruppamento di intervalli per qualsiasi funzione di aggregazione.

Se la clausola BY viene omessa, ma viene specificato un intervallo (come una seconda sintassi), per impostazione predefinita viene usato il campo *TimeGenerated* .

Esempi:

**Esempio 1**

    Type:Alert | measure count() as Count by ObjectId

Raggruppa gli avvisi per *ObjectID* e calcola il numero di avvisi per ogni gruppo. Il valore aggregato viene restituito come campo *Count* (alias).

**Esempio 2**

    Type:Alert | measure count() interval 1HOUR

Raggruppa gli avvisi per intervalli di 1 ora tramite il campo *TimeGenerated* e restituisce il numero di avvisi in ogni intervallo.

**Esempio 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

Uguale all'esempio precedente, ma con un alias del campo aggregato (*AlertsPerHour*).

**Esempio 4**

    * | measure count() by TimeCreated interval 5DAYS

Raggruppa i risultati per intervalli di 5 giorni tramite il campo *TimeCreated* e restituisce il numero di risultati in ogni intervallo.

**Esempio 5**

    Type:Alert | measure max(Severity) by WorkflowName

Raggruppa gli avvisi in base al nome del carico di lavoro e restituisce il valore di gravità massima degli avvisi per ogni flusso di lavoro.

**Esempio 6**

    Type:Alert | measure min(Severity) by WorkflowName

Uguale all'esempio precedente, ma con la funzione di aggregazione *min*.

**Esempio 7**

    Type:Perf | measure avg(CounterValue) by Computer

Raggruppa le prestazioni per computer e calcola la media (avg).

**Esempio 8**

    Type:Perf | measure sum(CounterValue) by Computer

Uguale all'esempio precedente, ma usa *sum*.

**Esempio 9**

    Type:Perf | measure stddev(CounterValue) by Computer

Uguale all'esempio precedente, ma usa *stddev*.

**Esempio 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

Uguale all'esempio precedente, ma usa *percentile70*.

**Esempio 11**

    Type:Perf | measure pct70(CounterValue) by Computer

Uguale all'esempio precedente, ma usa *pct70*. Notare che *PCT##* è solo un alias per la funzione *PERCENTILE##*.

**Esempio 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

Raggruppa le prestazioni prima in base al computer e poi per Nome conteggio, quindi calcola la media (avg).

**Esempio 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

Ottiene i primi cinque flussi di lavoro con il numero massimo di avvisi.

**Esempio 14**

    * | measure countdistinct(Computer) per tipo

Conta il numero di computer univoci che creano report per ogni tipo.

**Esempio 15**

    * | measure countdistinct(Computer) Interval 1HOUR

Conta il numero di computer univoci che creano report per ogni ora.

**Esempio 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

Raggruppa la % tempo processore per computer e restituisce la media per ogni ora.

**Esempio 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

Raggruppa W3CIISLog per metodo e restituisce il valore massimo ogni 5 minuti.

**Esempio 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

Raggruppa la % tempo processore per computer e restituisce il valore minimo, la media, il 75° percentile e il valore massimo per ogni ora.

**Esempio 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

Raggruppa la % tempo processore prima per computer e poi per il nome dell'istanza, quindi restituisce il valore minimo, la media, il 75° percentile e il valore massimo per ogni ora.

**Esempio 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

Calcola il numero massimo di operazioni di scrittura sul disco al minuto per ogni disco del computer in uso.

### <a name="where"></a>Where
Sintassi:

```
**where** AggregatedValue>20
```

Può essere usato solo dopo un comando *Measure* per filtrare ulteriormente i risultati aggregati prodotti dalla funzione di aggregazione *Measure*.

Esempi:

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)



### <a name="dedup"></a>Dedup
Sintassi:

    Dedup FieldName

Restituisce il primo documento trovato per ogni valore univoco del campo specificato.

Esempio:

    Type=Event | Dedup EventID | sort TimeGenerated DESC

Questo esempio restituisce un evento (l'ultimo) per ogni EventID.

### <a name="join"></a>Join
Unisce i risultati di due query in modo da formare un unico set di risultati.  Supporta più tipi di join descritti nella tabella seguente.

| Tipo di join | Descrizione |
|:--|:--|
| interno | Consente di restituire solo i record con un valore corrispondente in entrambe le query. |
| esterno | Consente di restituire tutti i record da entrambe le query.  |
| sinistro  | Consente di restituire tutti i record da query a sinistra e i record corrispondenti da query a destra. |


- I join attualmente non supportano query che includono la parola chiave **IN**, il comando **Measure** o il comando **Extend** se la destinazione è un campo della query a destra.
- Attualmente, è possibile includere solo un singolo campo in un join.
- Una singola ricerca non può includere più di un join.

**Sintassi**

```
<left-query> | JOIN <join-type> <left-query-field-name> (<right-query>) <right-query-field-name>
```

**esempi**

Per illustrare i tipi di join diversi, prendere in considerazione l'aggiunta di un tipo di dati raccolti da un log personalizzato denominato MyBackup_CL con heartbeat per ciascun computer.  Questi tipi di dati sono caratterizzati dai dati seguenti.

`Type = MyBackup_CL`

| TimeGenerated | Computer | LastBackupStatus |
|:---|:---|:---|
| 4/20/2017 01:26:32.137 AM | srv01.contoso.com | Success |
| 4/20/2017 02:13:12.381 AM | srv02.contoso.com | Success |
| 4/20/2017 02:13:12.381 AM | srv03.contoso.com | Esito negativo |

`Type = Hearbeat` (solo un sottoinsieme dei campi visualizzati)

| TimeGenerated | Computer | ComputerIP |
|:---|:---|:---|
| 4/21/2017 12:01:34.482 PM | srv01.contoso.com | 10.10.100.1 |
| 4/21/2017 12:02:21.916 PM | srv02.contoso.com | 10.10.100.2 |
| 4/21/2017 12:01:47.373 PM | srv04.contoso.com | 10.10.100.4 |

#### <a name="inner-join"></a>join interno

`Type=MyBackup_CL | join inner Computer (Type=Heartbeat) Computer`

Restituisce i record seguenti in cui il campo informatico corrisponde per entrambi i tipi di dati.

| Computer| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| srv01.contoso.com | 4/20/2017 01:26:32.137 AM | Success | 4/21/2017 12:01:34.482 PM | 10.10.100.1 | Heartbeat |
| srv02.contoso.com | 4/20/2017 02:13:12.381 AM | Success | 4/21/2017 12:02:21.916 PM | 10.10.100.2 | Heartbeat |


#### <a name="outer-join"></a>join esterno

`Type=MyBackup_CL | join outer Computer (Type=Heartbeat) Computer`

Restituisce i record seguenti per entrambi i tipi di dati.

| Computer| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| srv01.contoso.com | 4/20/2017 01:26:32.137 AM | Success  | 4/21/2017 12:01:34.482 PM | 10.10.100.1 | Heartbeat |
| srv02.contoso.com | 4/20/2017 02:14:12.381 AM | Success  | 4/21/2017 12:02:21.916 PM | 10.10.100.2 | Heartbeat |
| srv03.contoso.com | 4/20/2017 01:33:35.974 AM | Esito negativo  | 4/21/2017 12:01:47.373 PM | | |
| srv04.contoso.com |                           |          | 4/21/2017 12:01:47.373 PM | 10.10.100.2 | Heartbeat |



#### <a name="left-join"></a>join sinistro

`Type=MyBackup_CL | join left Computer (Type=Heartbeat) Computer`

Restituisce i seguenti record da MyBackup_CL con tutti i campi corrispondenti dall'Heartbeat.

| Computer| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| srv01.contoso.com | 4/20/2017 01:26:32.137 AM | Success | 4/21/2017 12:01:34.482 PM | 10.10.100.1 | Heartbeat |
| srv02.contoso.com | 4/20/2017 02:13:12.381 AM | Success | 4/21/2017 12:02:21.916 PM | 10.10.100.2 | Heartbeat |
| srv03.contoso.com | 4/20/2017 02:13:12.381 AM | Esito negativo | | | |


### <a name="extend"></a>Extend
Consente di creare campi di runtime nelle query. Si noti che i campi di runtime non possono essere usati con il comando measure per eseguire l'aggregazione.

**Esempio 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
Visualizza il punteggio delle raccomandazioni ponderato per le raccomandazioni di SQL Assessment.

**Esempio 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
Visualizza il valore del contatore in KB invece che in byte.

**Esempio 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
Ridimensiona il valore TotalBytes di WireData in modo che tutti i risultati siano compresi tra 0 e 100.

**Esempio 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
Contrassegna i valori del contatore prestazioni inferiori al 50% come LOW e gli altri come HIGH.

**Esempio 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
Calcola il numero massimo di operazioni di scrittura sul disco al minuto per ogni disco del computer in uso.

**Funzioni supportate**

| Funzione | Descrizione | Esempi di sintassi |
| --- | --- | --- |
| abs |Restituisce il valore assoluto del valore o della funzione specificata. |`abs(x)` <br> `abs(-5)` |
| acos |Restituisce l'arcocoseno di un valore o una funzione. |`acos(x)` |
| e |Restituisce un valore true se e solo se tutti gli operandi restituiscono true. |`and(not(exists(popularity)),exists(price))` |
| asin |Restituisce l'arcoseno di un valore o una funzione. |`asin(x)` |
| atan |Restituisce l'arco tangente di un valore o una funzione. |`atan(x)` |
| atan2 |Restituisce l'angolo risultante dalla conversione delle coordinate rettangolari x, y in coordinate polari. |`atan2(x,y)` |
| cbrt |Radice cubica. |`cbrt(x)` |
| ceil |Arrotonda per eccesso a un numero intero. |`ceil(x)`  <br> `ceil(5.6)`: restituisce il valore 6 |
| cos |Restituisce il coseno di un angolo. |`cos(x)` |
| cosh |Restituisce il coseno iperbolico di un angolo. |`cosh(x)` |
| def |Abbreviazione di default. Restituisce il valore del campo "field". Se il campo non esiste, restituisce il valore predefinito specificato e restituisce il primo valore in cui `exists()==true`. |`def(rating,5)`. Questa funzione def() restituisce la classificazione o, se non specificata nel documento, restituisce il valore 5. <br> `def(myfield, 1.0)` equivale a: `if(exists(myfield),myfield,1.0)`. |
| deg |Converte i radianti in gradi. |`deg(x)` |
| div |`div(x,y)` divide x per y. |`div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| dist |Restituisce la distanza tra due vettori (punti) in uno spazio a n dimensioni. Accetta la potenza e due o più istanze di ValueSource e calcola le distanze tra i due vettori. Ogni ValueSource deve essere un numero. Il numero di istanze di ValueSource passate deve essere pari e il metodo presuppone che la prima metà rappresenti il primo vettore e che la seconda metà rappresenti il secondo vettore. |`dist(2, x, y, 0, 0)` : calcola la distanza euclidea tra (0,0) e (x,y) per ogni documento. <br> `dist(1, x, y, 0, 0)` : calcola la distanza di Manhattan (geometria del taxi) tra (0,0) e (x,y) per ogni documento. <br> `dist(2,,x,y,z,0,0,0)`: distanza euclidea tra (0,0,0) e (x,y,z) per ogni documento.<br>`dist(1,x,y,z,e,f,g)`: distanza di Manhattan tra (x,y,z) e (e,f,g), dove ogni lettera è un nome di campo. |
| exists |Restituisce TRUE se esiste un membro del campo. |`exists(author)`: restituisce TRUE per i documenti con un valore nel campo "author".<br>`exists(query(price:5.00))`: restituisce TRUE se "price" corrisponde a "5.00". |
| exp |Restituisce il numero di Eulero elevato alla potenza x. |`exp(x)` |
| floor |Arrotonda per difetto a un numero intero. |`floor(x)`  <br> `floor(5.6)`: restituisce il valore 5 |
| hypo |Restituisce sqrt(sum(pow(x,2),pow(y,2))) senza overflow o underflow intermedi. |`hypo(x,y)`  <br> ` |
| if |Abilita le query funzione condizionali. In `if(test,value1,value2)`: test è o fa riferimento a un valore o a un'espressione logica che restituisce un valore logico (TRUE o FALSE). `value1` è il valore che viene restituito dalla funzione se test restituisce TRUE. `value2` è il valore che viene restituito dalla funzione se test restituisce FALSE. Un'espressione può essere una funzione che restituisce valori booleani o anche una funzione che restituisce valori numerici, nel qual caso il valore 0 verrà interpretato come false, oppure stringhe, nel qual caso una stringa vuota viene interpretata come false. |`if(termfreq(cat,'electronics'),popularity,42)`: questa funzione controlla ogni documento per verificare se contiene il termine "electronics" nel campo cat. In questo caso, viene restituito il valore del campo popularity. In caso contrario, viene restituito il valore 42. |
| linear |Implementa `m*x+c` dove m e c sono costanti e x è una funzione arbitraria. È equivalente a `sum(product(m,x),c)`, ma è un po' più efficiente perché viene implementata come funzione singola. |`linear(x,m,c) linear(x,2,4)` restituisce `2*x+4` |
| ln |Restituisce il log naturale della funzione specificata. |`ln(x)` |
| log |Restituisce il logaritmo in base 10 della funzione specificata. |`log(x)   log(sum(x,100))` |
| map |Esegue il mapping dei valori di una funzione di input x compresa tra min e max inclusi alla destinazione specificata. Gli argomenti min e max devono essere costanti. Gli argomenti target e default possono essere costanti o funzioni. Se il valore di x non è compreso tra min e max, viene restituito il valore di x oppure viene restituito un valore predefinito se viene specificato come quinto argomento. |`map(x,min,max,target) map(x,0,0,1)`: sostituisce i valori 0 con 1. Può essere utile per gestire i valori 0 predefiniti.<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)`: modifica su 1 qualsiasi valore compreso tra 0 e 100 e tutti gli altri valori diventano -1.<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))`: cambia i valori compresi tra 0 e 100 in x+599 e tutti gli altri valori nella frequenza del termine "solr" nel campo text. |
| max |Restituisce il valore numerico massimo di più funzioni o costanti annidate che vengono specificate come argomenti: `max(x,y,...)`. La funzione max può essere utile anche per ridurre il valore di un'altra funzione o campo fino a una costante specificata.  Usare la sintassi `field(myfield,max)` per selezionare il valore massimo di un singolo campo multivalore. |`max(myfield,myotherfield,0)` |
| Min |Restituisce il valore numerico minimo di più funzioni o costanti annidate che vengono specificate come argomenti: `min(x,y,...)`. La funzione min può essere utile anche per fornire un "limite superiore" per una funzione usando una costante. Usare la sintassi `field(myfield,min)` per selezionare il valore minimo di un singolo campo multivalore. |`min(myfield,myotherfield,0)` |
| mod |Calcola il modulo della funzione x per la funzione y. |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))` |
| ms |Restituisce i millisecondi di differenza tra gli argomenti. Le date sono relative al periodo temporale Unix o POSIX, mezzanotte, 1 gennaio 1970 UTC. Gli argomenti possono essere il nome di un TrieDateField indicizzato o l'operatore matematico di data basato su una data costante oppure su NOW. `ms()` equivale a `ms(NOW)`, numero di millisecondi dopo il periodo. `ms(a)` restituisce il numero di millisecondi trascorsi dal periodo rappresentato dall'argomento. `ms(a,b)` restituisce il numero di millisecondi che ci mette b per verificarsi prima di a, ovvero `a - b`. |`ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| not |Valore negativo logico della funzione di cui è stato eseguito il wrapping. |`not(exists(author))`: TRUE solo quando `exists(author)` è false. |
| oppure |Disgiunzione logica. |`or(value1,value2)`: TRUE se value1 o value2 è true. |
| pow |Genera la base specificata per la potenza specificata. `pow(x,y)` genera x per la potenza di y. |`pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)`: è lo stesso di sqrt. |
| product |Restituisce il prodotto di più valori o funzioni, che vengono specificate in un elenco con valori delimitati da virgole. `mul(...)` può essere usato anche come alias per questa funzione. |`product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip |Esegue una funzione reciproca con `recip(x,m,a,b)` che implementa `a/(m*x+b)` dove m, a, b sono costanti e x è una funzione arbitrariamente complessa. Quando a e b sono uguali e x>=0, questa funzione ha un valore massimo di 1 che diminuisce quando x aumenta. Se i valori di a e b aumentano insieme, l'intera funzione viene spostata in una parte meno piatta della curva. Queste proprietà possono rendere questa funzione ideale per il boosting dei documenti più recenti quando x è `rord(datefield)`. |`recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| rad |Converte i gradi in radianti. |`rad(x)` |
| rint |Arrotonda al numero intero più prossimo. |`rint(x)`  <br> `rint(5.6)`: restituisce il valore 6 |
| sin |Restituisce il seno di un angolo. |`sin(x)` |
| sinh |Restituisce il seno iperbolico di un angolo. |`sinh(x)` |
| scala |Ridimensiona i valori della funzione x in modo che siano compresi tra i valori minTarget e maxTarget specificati inclusi. L'implementazione corrente attraversa tutti i valori della funzione per ottenere quello minimo e quello massimo, per poter selezionare la scala corretta. L'implementazione corrente non può distinguere quando i documenti sono stati eliminati o i documenti privi di valori. In questi casi, usa i valori 0,0. Ciò significa che, se i valori di norma sono maggiori di 0,0, è possibile usare 0,0 come valore minimo da cui eseguire il mapping. In questi casi, una funzione `map()` appropriata può essere usata come soluzione alternativa per sostituire 0,0 con un valore compreso nell'intervallo reale, come illustrato qui: `scale(map(x,0,0,5),1,2)` |`scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)`: ridimensiona i valori di x in modo che tutti i valori siano compresi tra 1 e 2 inclusi. |
| sqrt |Restituisce la radice quadrata del valore o della funzione specificata. |`sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist |Calcola la distanza tra due stringhe. Usa l'interfaccia StringDistance del controllo ortografico di Lucene e supporta tutte le implementazioni disponibili in tale pacchetto, oltre a consentire alle applicazioni di collegarsi tramite le funzionalità di caricamento delle risorse di Solr. strdist accetta `(string1, string2, distance measure)`. I valori possibili per la misura della distanza sono: <ul><li>jw: Jaro-Winkler</li><li>modifcare: Levenstein oppure Modifica distanza</li><li>ngram: il valore NGramDistance, se specificato, può facoltativamente passare anche la dimensione dell'n-gramma. Il valore predefinito è 2.</li><li>FQN: nome di classe completo per un'implementazione dell'interfaccia StringDistance. Deve avere un costruttore no-arg.</li></ul> |`strdist("SOLR",id,edit)` |
| sub |Restituisce x-y da `sub(x,y)`. |`sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| Sum |Restituisce la somma di più valori o funzioni, che vengono specificate in un elenco con valori delimitati da virgole. `add(...)` può essere usato come alias per questa funzione. |`sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| termfreq |Restituisce il numero di volte in cui il termine appare nel campo del documento. |termfreq(text,'memory') |
| tan |Restituisce la tangente di un angolo. |`tan(x)` |
| tanh |Restituisce la tangente iperbolica di un angolo. |`tanh(x)` |

## <a name="search-field-and-facet-reference"></a>Riferimenti al campo Ricerca e ai facet
Quando si usa Ricerca log per trovare i dati, i risultati visualizzano vari campi e facet. Alcune informazioni potrebbero non essere molto descrittive. Usare le informazioni seguenti per comprendere i risultati.

| Campo | Tipo di ricerca | Descrizione |
| --- | --- | --- |
| TenantId |Tutti |Usato per la partizione di dati. |
| TimeGenerated |Tutti |Usato per gestire la sequenza temporale, selettori orari (in Ricerca e in altre schermate). Rappresenta quando i dati sono stati generati (in genere nell'agente). Il tempo viene espresso nel formato ISO ed è sempre UTC. Nel caso di tipi basati sulla strumentazione esistente, ad esempio gli eventi in un log, si tratta in genere del tempo reale in cui la voce, la riga o il record di log è stato registrato. Per altri tipi generati con Management Pack o nel cloud, ad esempio raccomandazioni o avvisi, rappresenta un valore diverso. Si tratta dell'ora in cui sono stati raccolti nuovi dati con uno snapshot di una configurazione di un certo tipo oppure è stata generata una raccomandazione o un avviso. |
| EventID |Evento |ID evento nel registro eventi di Windows. |
| EventLog |Evento |Registro eventi in cui l'evento è stato registrato da Windows. |
| EventLevelName |Evento |Critico/avviso/informazioni/esito positivo |
| EventLevel |Evento |Valore numerico per critico/avviso/informazioni/esito positivo; usare EventLevelName invece di query più semplici o più leggibili |
| SourceSystem |Tutti |Provenienza dei dati, in termini di modalità di "collegamento" al servizio, ad esempio Microsoft System Center Operations Manager e Archiviazione di Azure |
| ObjectName |PerfHourly |Nome oggetto delle prestazioni di Windows |
| InstanceName |PerfHourly |Nome dell'istanza del contatore delle prestazioni di Windows |
| CounteName |PerfHourly |Nome del contatore delle prestazioni di Windows |
| ObjectDisplayName |PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty |Nome visualizzato dell'oggetto di destinazione tramite una regola di raccolta delle prestazioni in Operations Manager o dell'oggetto individuato da Operational Insights o su cui è stato generato l'avviso |
| RootObjectName |PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty |Nome visualizzato del padre del padre (in una relazione di hosting doppia) dell'oggetto di destinazione di una regola di raccolta delle prestazioni in Operations Manager o dell'oggetto individuato da Operational Insights o su cui è stato generato l'avviso |
| Computer |La maggior parte dei tipi |Nome del computer a cui appartengono i dati |
| DeviceName |ProtectionStatus |Nome del computer a cui appartengono i dati (uguale a "Computer") |
| DetectionId |ProtectionStatus | |
| ThreatStatusRank |ProtectionStatus |La classificazione dello stato di minaccia è una rappresentazione numerica dello stato di minaccia e, come per i codici di risposta HTTP, sono stati lasciati spazi tra i numeri (motivo per cui nessuna minaccia è 150, 100 o 0) in modo da poter aggiungere nuovi stati. Per un rollup dello stato di minaccia e dello stato di protezione, lo scopo è visualizzare lo stato peggiore in cui il computer è rimasto durante il periodo di tempo selezionato. I numeri vengono usati per classificare i diversi stati, per cui è possibile analizzare il record con il numero più alto. |
| ThreatStatus |ProtectionStatus |Descrizione di ThreatStatus, viene eseguito il mapping 1:1 con ThreatStatusRank |
| TypeofProtection |ProtectionStatus |Prodotto anti-malware rilevato nel computer: nessuno, strumento di rimozione malware Microsoft, Forefront e così via. |
| ScanDate |ProtectionStatus | |
| SourceHealthServiceId |ProtectionStatus, RequiredUpdate |ID servizio di integrità per l'agente del computer |
| HealthServiceId |La maggior parte dei tipi |ID servizio di integrità per l'agente del computer |
| ManagementGroupName |La maggior parte dei tipi |Nome del gruppo di gestione per gli agenti di Operations Manager. In caso contrario, è null o vuoto. |
| ObjectType |ConfigurationObject |Tipo (come nel "tipo" o nella classe del Management Pack di Operations Manager) per l'oggetto individuato dalla valutazione della configurazione di Log Analytics |
| UpdateTitle |RequiredUpdate |Nome dell'aggiornamento trovato non installato |
| PublishDate |RequiredUpdate |Quando è stato pubblicato l'aggiornamento su Microsoft Update |
| Server |RequiredUpdate |Nome del computer a cui appartengono i dati (uguale a "Computer") |
| Prodotto |RequiredUpdate |Prodotto a cui si applica l'aggiornamento |
| UpdateClassification |RequiredUpdate |Tipo di aggiornamento, ad esempio rollup aggiornamento, Service Pack |
| KBID |RequiredUpdate |ID articolo della KB che descrive la procedura consigliata o l'aggiornamento |
| WorkflowName |ConfigurationAlert |Nome della regola o del monitoraggio che ha generato l'avviso |
| Severity |ConfigurationAlert |Gravità dell'avviso |
| Priorità |ConfigurationAlert |Priorità dell'avviso |
| IsMonitorAlert |ConfigurationAlert |Questo avviso viene generato da un monitoraggio (true) o da una regola (false)? |
| AlertParameters |ConfigurationAlert |XML con i parametri dell'avviso di Log Analytics |
| Context |ConfigurationAlert |XML con il contesto dell'avviso di Log Analytics |
| Carico di lavoro |ConfigurationAlert |Tecnologia o carico di lavoro a cui fa riferimento l'avviso |
| AdvisorWorkload |Raccomandazione |Tecnologia o carico di lavoro a cui fa riferimento la raccomandazione |
| Descrizione |ConfigurationAlert |Descrizione dell'avviso (breve) |
| DaysSinceLastUpdate |UpdateAgent |Quanti giorni fa (relativo a 'TimeGenerated' di questo record) questo agente ha installato un aggiornamento da Windows Server Update Service (WSUS)/Microsoft Update? |
| DaysSinceLastUpdateBucket |UpdateAgent |In base a DaysSinceLastUpdate, una categorizzazione in "bucket orari" del tempo trascorso dall'ultima installazione di aggiornamenti da WSUS/Microsoft Update da parte di un computer |
| AutomaticUpdateEnabled |UpdateAgent |Il controllo degli aggiornamenti automatici è abilitato o disabilitato su questo agente? |
| AutomaticUpdateValue |UpdateAgent |Il controllo degli aggiornamenti automatici è impostato per scaricare automaticamente e installare, solo scaricare o solo controllare? |
| WindowsUpdateAgentVersion |UpdateAgent |Numero di versione dell'agente di Microsoft Update |
| WSUSServer |UpdateAgent |A quale server WSUS è destinato questo agente di aggiornamento? |
| OSVersion |UpdateAgent |Versione del sistema operativo su cui è in esecuzione questo agente di aggiornamento |
| Nome |Recommendation, ConfigurationObjectProperty |Nome/titolo della raccomandazione o nome della proprietà di valutazione configurazione di Log Analytics |
| Valore |ConfigurationObjectProperty |Valore di una proprietà di valutazione configurazione di Log Analytics |
| KBLink |Raccomandazione |URL all'articolo della KB che descrive la procedura consigliata o l'aggiornamento |
| RecommendationStatus |Raccomandazione |Le raccomandazioni sono tra i pochi tipi i cui record vengono aggiornati, non solo aggiunti all'indice di ricerca. Questo stato cambia se la raccomandazione è attiva/aperta o se Log Analytics rileva che è stata risolta. |
| RenderedDescription |Evento |Descrizione con rendering (testo riutilizzato con parametri popolati) di un evento di Windows |
| ParameterXml |Evento |XML con i parametri della sezione relativa ai dati di un evento di Windows, come illustrato nel visualizzatore eventi |
| EventData |Evento |XML con l'intera sezione relativa ai dati di un evento di Windows, come illustrato nel visualizzatore eventi |
| Sorgente |Evento |Origine del registro eventi che ha generato l'evento |
| EventCategory |Evento |Categoria dell'evento, direttamente dal registro eventi di Windows |
| UserName |Evento |Nome utente dell'evento di Windows, in genere, NT AUTHORITY\LOCALSYSTEM |
| SampleValue |PerfHourly |Valore medio per l'aggregazione oraria di un contatore delle prestazioni |
| Min |PerfHourly |Valore minimo nell'intervallo orario di un'aggregazione oraria del contatore delle prestazioni |
| max |PerfHourly |Valore massimo nell'intervallo orario di un'aggregazione oraria del contatore delle prestazioni |
| Percentile95 |PerfHourly |Valore del 95° percentile nell'intervallo orario di un'aggregazione oraria del contatore delle prestazioni |
| SampleCount |PerfHourly |Il numero di campioni di contatori delle prestazioni non elaborati usati per produrre questo record di aggregazione oraria |
| Threat |ProtectionStatus |Nome del malware rilevato |
| StorageAccount |W3CIISLog |Account di archiviazione di Azure da cui è stato letto il log |
| AzureDeploymentID |W3CIISLog |ID distribuzione di Azure del servizio cloud a cui appartiene il log |
| Ruolo |W3CIISLog |Ruolo del servizio cloud di Azure a cui appartiene il log |
| RoleInstance |W3CIISLog |Istanza del ruolo di Azure a cui appartiene il log |
| sSiteName |W3CIISLog |Sito Web IIS a cui appartiene il log (notazione metabase); il campo s-sitename nel log originale |
| sComputerName |W3CIISLog |Il campo s-computername nel log originale |
| sIP |W3CIISLog |Indirizzo IP del server a cui è stata indirizzata la richiesta HTTP. Il campo s-ip nel log originale |
| csMethod |W3CIISLog |Metodo HTTP (ad esempio, GET/POST) usato dal client nella richiesta HTTP Il metodo cs nel log originale |
| cIP |W3CIISLog |Indirizzo IP del client da cui proviene la richiesta HTTP. Il campo c-ip nel log originale |
| csUserAgent |W3CIISLog |Agente utente HTTP dichiarato dal client (browser o altro). L'agente utente cs nel log originale |
| scStatus |W3CIISLog |Codice di stato HTTP, ad esempio 200/403/500, restituito dal server al client Lo stato cs nel log originale |
| TimeTaken |W3CIISLog |Periodo di tempo (in millisecondi) impiegato dalla richiesta per il completamento. Il campo timetaken nel log originale |
| csUriStem |W3CIISLog |URI relativo (senza indirizzo host, che è '/search') che è stato richiesto. Il campo cs-uristem nel log originale |
| csUriQuery |W3CIISLog |Query dell'URI. Le query dell'URI sono necessarie solo per le pagine dinamiche, ad esempio le pagine ASP, per cui questo campo contiene in genere un trattino per le pagine statiche. |
| sPort |W3CIISLog |Porta del server a cui è stata inviata la richiesta HTTP (e a cui IIS è in ascolto dal momento della selezione) |
| csUserName |W3CIISLog |Nome dell'utente autenticato, se la richiesta è autenticata e non anonima |
| csVersion |W3CIISLog |Versione del protocollo HTTP usata nella richiesta, ad esempio HTTP/1.1 |
| csCookie |W3CIISLog |Informazioni sui cookie |
| csReferer |W3CIISLog |Ultimo sito visitato dall'utente. Questo sito fornisce un collegamento al sito corrente. |
| csHost |W3CIISLog |Intestazione host richiesta, ad esempio www.mysite.com |
| scSubStatus |W3CIISLog |Codice errore dello stato secondario. |
| scWin32Status |W3CIISLog |Codice di stato Windows. |
| csBytes |W3CIISLog |Byte inviati nella richiesta dal client al server |
| scBytes |W3CIISLog |Byte restituiti nella risposta dal server al client |
| ConfigChangeType |ConfigurationChange |Tipo di modifica, ad esempio WindowsServices/Software |
| ChangeCategory |ConfigurationChange |Categoria di modifica (Modificato/Aggiunto/Rimosso) |
| SoftwareType |ConfigurationChange |Tipo di software (Aggiornamento/Applicazione) |
| SoftwareName |ConfigurationChange |Nome del software (applicabile solo alle modifiche del software) |
| Autore |ConfigurationChange |Fornitore che pubblica il software (applicabile solo alle modifiche software) |
| SvcChangeType |ConfigurationChange |Tipo di modifica applicata a un servizio Windows (Stato/Tipo avvio/Percorso/Account servizio) Applicabile solo alle modifiche del servizio Windows |
| SvcDisplayName |ConfigurationChange |Nome visualizzato del servizio modificato |
| SvcName |ConfigurationChange |Nome del servizio modificato |
| SvcState |ConfigurationChange |Stato nuovo (corrente) del servizio |
| SvcPreviousState |ConfigurationChange |Stato precedente noto del servizio (applicabile solo se lo stato del servizio è stato modificato) |
| SvcStartupType |ConfigurationChange |Tipo di servizio di avvio |
| SvcPreviousStartupType |ConfigurationChange |Tipo di avvio del servizio precedente (applicabile solo se il tipo di avvio del servizio è stato modificato) |
| SvcAccount |ConfigurationChange |Account del servizio |
| SvcPreviousAccount |ConfigurationChange |Account del servizio precedente (applicabile solo se l'account del servizio è stato modificato) |
| SvcPath |ConfigurationChange |Percorso dell'eseguibile per il servizio Windows |
| SvcPreviousPath |ConfigurationChange |Percorso precedente dell'eseguibile per il servizio Windows (applicabile solo se è stato modificato) |
| SvcDescription |ConfigurationChange |Descrizione del servizio |
| Precedente |ConfigurationChange |Stato precedente del software (Installato/Non installato/versione precedente) |
| Current |ConfigurationChange |Ultimo stato del software (Installato/Non installato/versione corrente) |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle ricerche nei log:

* Acquisire familiarità con le [ricerche nei log](log-analytics-log-searches.md) per visualizzare le informazioni dettagliate raccolte dalle soluzioni.
* Usare i [campi personalizzati in Log Analytics](log-analytics-custom-fields.md) per estendere le ricerche nei log.
