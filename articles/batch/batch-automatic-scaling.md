---
title: Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch | Microsoft Docs
description: Abilitare il ridimensionamento automatico in un pool cloud per adeguare dinamicamente il numero di nodi di calcolo nel pool.
services: batch
documentationcenter: ''
author: mmacy
manager: timlt
editor: tysonn

ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 09/27/2016
ms.author: marsma

---
# Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch
Con il ridimensionamento automatico, il servizio Azure Batch può aggiungere o rimuovere in modo dinamico i nodi di calcolo in un pool in base ai parametri definiti dall'utente. È possibile risparmiare tempo e denaro ottimizzando automaticamente la potenza di calcolo usata dall'applicazione, aggiungendo nodi quando l'attività del processo richiede un incremento della potenza e rimuovendo nodi quando questa esigenza viene meno.

È possibile abilitare il ridimensionamento automatico in un pool di nodi di calcolo associandolo a una *formula di ridimensionamento automatico* definita dall'utente, ad esempio con il metodo [PoolOperations.EnableAutoScale][net_enableautoscale] nella libreria [Batch .NET](batch-dotnet-get-started.md). Il servizio Batch usa quindi questa formula per determinare il numero di nodi di calcolo necessari per eseguire il carico di lavoro. Batch tiene conto dei campioni di dati di metrica del servizio raccolti periodicamente e modifica il numero di nodi di calcolo nel pool a intervalli configurabili, in base alla formula associata.

È possibile abilitare il ridimensionamento automatico al momento della creazione di un pool o in un pool esistente. Si può anche modificare una formula esistente in un pool abilitato per la "scalabilità automatica". Batch consente di valutare le formule prima di assegnarle ai pool e di monitorare lo stato delle esecuzioni del ridimensionamento automatico.

## Formule di ridimensionamento automatico
Una formula di ridimensionamento automatico è un valore stringa definito dall'utente che contiene una o più istruzioni ed è assegnato all'elemento [autoScaleFormula][rest_autoscaleformula] di un pool (Batch REST) o alla proprietà [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] \(Batch .NET). Quando le formule sono assegnate a un pool, vengono usate dal servizio Batch per determinare il numero di nodi di calcolo disponibili nel pool per l'intervallo di elaborazione successivo. Altre informazioni sugli intervalli sono disponibili più avanti. La stringa della formula, le cui dimensioni non possono superare 8 KB, può includere fino a 100 istruzioni separate da punti e virgola, nonché interruzioni di riga e commenti.

È possibile paragonare le formule di ridimensionamento automatico all'uso di un "linguaggio" di ridimensionamento automatico di Batch. Le istruzioni nella formula sono espressioni in formato libero che possono includere variabili definite dal servizio Batch e variabili definite dall'utente. Possono eseguire diverse operazioni su questi valori usando funzioni, operatori e tipi predefiniti. Ad esempio, un'istruzione può avere il formato seguente:

`$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);`

Le formule contengono in genere più istruzioni che eseguono operazioni su valori ottenuti nelle istruzioni precedenti. Ad esempio, si ottiene prima di tutto un valore per `variable1`, quindi il valore viene passato a una funzione per popolare `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Con queste istruzioni nella formula, l'obiettivo è arrivare a un numero di nodi di calcolo in cui deve essere ridimensionato il pool, ovvero il numero di **destinazione** dei **nodi dedicati**. Questo numero può essere maggiore, minore o uguale al numero attuale di nodi nel pool. Batch valuta la formula di ridimensionamento automatico del pool secondo un intervallo specifico. Gli [intervalli di ridimensionamento automatico](#automatic-scaling-interval) sono illustrati di seguito. Regolerà quindi il numero di destinazione dei nodi nel pool in base al numero specificato dalla formula al momento della valutazione.

Come semplice esempio, questa formula di ridimensionamento automatico di due righe specifica che il numero di nodi deve essere adeguato in base al numero di attività attive, con un massimo di 10 nodi di calcolo:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

Le sezioni successive dell'articolo illustrano le diverse entità che costituiranno le formule di ridimensionamento automatico, ad esempio variabili, operatori, operazioni e funzioni. Si scoprirà come ottenere varie metriche relative ad attività e a risorse di calcolo all'interno di Batch. È possibile usare queste metriche adeguare in modo intelligente il numero di nodi del pool in base all'utilizzo delle risorse e allo stato delle attività. Verrà quindi illustrato come costruire una formula e abilitare il ridimensionamento automatico in un pool con le API Batch REST e Batch .NET, concludendo con alcune formule di esempio.

> [!IMPORTANT]
> Ogni account Azure Batch è limitato a un numero massimo di core, e quindi di nodi di calcolo, che può essere usato per l'elaborazione. Il servizio Batch crea nodi solo fino al raggiungimento del limite di core. Potrebbe quindi non raggiungere il numero di nodi di calcolo specificato da una formula. Per informazioni su come visualizzare e aumentare le quote dell'account, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).
> 
> 

## Variabili
Nelle formule di ridimensionamento automatico si possono usare **variabili definite dal servizio** e **variabili definite dall'utente**. Le variabili definite dal servizio sono incorporate nel servizio Batch, alcune sono in lettura/scrittura e altre di sola lettura. Le variabili definite dall'utente vengono configurate dall'*utente*. Nella precedente formula di esempio di due righe, `$TargetDedicated` è una variabile definita dal servizio, mentre `$averageActiveTaskCount` è una variabile definita dall'utente.

Le tabelle seguenti includono variabili di lettura/scrittura e di sola lettura definite dal servizio Batch.

È possibile **ottenere** e **impostare** i valori di queste variabili definite dal servizio per gestire il numero di nodi di calcolo in un pool:

| Variabili in lettura/scrittura definite dal servizio | Descrizione |
| --- | --- |
| $TargetDedicated |Numero di **destinazione** dei **nodi di calcolo dedicati** per il pool. È il numero di nodi di calcolo in cui deve essere ridimensionato il pool. È un numero di "destinazione", perché è possibile che un pool non raggiunga il numero di nodi di destinazione. Questa condizione può verificarsi se il numero di nodi di destinazione viene modificato di nuovo da una valutazione di ridimensionamento automatico successiva, prima che il pool abbia raggiunto la destinazione iniziale Può verificarsi anche se è stata raggiunta la quota di nodi o di core di un account Batch prima che venga raggiunto il numero di nodi di destinazione. |
| $NodeDeallocationOption |L'azione che si verifica quando i nodi di calcolo vengono rimossi da un pool. I valori possibili sono:<ul><li>**requeue** - termina immediatamente le attività e le inserisce nuovamente nella coda dei processi affinché vengano ripianificate;<li>**terminate** - termina immediatamente le attività e le rimuove dalla coda dei processi;<li>**taskcompletion** - attende il completamento delle attività attualmente in esecuzione e successivamente rimuove il nodo dal pool;<li>**retaineddata** - attende che tutti i dati conservati dalle attività locali sul nodo vengano cancellati prima di rimuovere il nodo dal pool.</ul> |

È possibile **ottenere** il valore di queste variabili definite dal servizio per eseguire adeguamenti basati sulla metrica del servizio Batch:

| Variabili di sola lettura definite dal servizio | Descrizione |
| --- | --- |
| $CPUPercent |Percentuale media di utilizzo della CPU. |
| $WallClockSeconds |Numero di secondi utilizzati. |
| $MemoryBytes |Numero medio di megabyte usati. |
| $DiskBytes |Numero medio di gigabyte usati sui dischi locali. |
| $DiskReadBytes |Numero di byte letti. |
| $DiskWriteBytes |Numero di byte scritti. |
| $DiskReadOps |Numero di operazioni di lettura del disco eseguite. |
| $DiskWriteOps |Numero di operazioni di scrittura sul disco eseguite. |
| $NetworkInBytes |Numero di byte in ingresso. |
| $NetworkOutBytes |Numero di byte in uscita. |
| $SampleNodeCount |Conteggio dei nodi di calcolo. |
| $ActiveTasks |Numero di attività in uno stato attivo. |
| $RunningTasks |Numero di attività nello stato in corso di esecuzione. |
| $PendingTasks |La somma di $ActiveTasks e $RunningTasks. |
| $SucceededTasks |Numero di attività completate correttamente. |
| $FailedTasks |Numero di attività non riuscite. |
| $CurrentDedicated |Numero corrente di nodi di calcolo dedicati. |

> [!TIP]
> Le variabili di sola lettura definite dal servizio illustrate sopra sono *oggetti* che offrono vari metodi per accedere ai dati associati a ognuno. Per altre informazioni, vedere [Ottenere dati di esempio](#getsampledata) di seguito.
> 
> 

## Types
Questi sono i **tipi** supportati in una formula:

* double
* doubleVec
* doubleVecList
* string
* timestamp, è una struttura composta che contiene i membri seguenti:
  
  * year
  * month (1-12)
  * day (1-31)
  * weekday (in formato numero, ad esempio 1 per lunedì)
  * hour (in formato 24 ore, ad esempio 13 per indicare le ore 13.00)
  * minute (00-59)
  * second (00-59)
* timeInterval
  
  * TimeInterval\_Zero
  * TimeInterval\_100ns
  * TimeInterval\_Microsecond
  * TimeInterval\_Millisecond
  * TimeInterval\_Second
  * TimeInterval\_Minute
  * TimeInterval\_Hour
  * TimeInterval\_Day
  * TimeInterval\_Week
  * TimeInterval\_Year

## Operazioni
Queste **operazioni** sono consentite sui tipi elencati sopra.

| Operazione | Operatori supportati | Tipo di risultato |
| --- | --- | --- |
| double *operatore* double |+, -, *, / |double |
| double *operatore* timeinterval |* |timeInterval |
| doubleVec *operatore* double |+, -, *, / |doubleVec |
| doubleVec *operatore* doubleVec |+, -, *, / |doubleVec |
| timeinterval *operatore* double |*, / |timeInterval |
| timeinterval *operatore* timeinterval |+, - |timeInterval |
| timeinterval *operatore* timestamp |+ |timestamp |
| timestamp *operatore* timeinterval |+ |timestamp |
| timestamp *operatore* timestamp |- |timeinterval |
| *operatore*double |-, ! |double |
| *operatore*timeinterval |- |timeinterval |
| double *operatore* double |<, <=, ==, >=, >, != |double |
| string *operatore* string |<, <=, ==, >=, >, != |double |
| timestamp *operatore* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operatore* timeinterval |<, <=, ==, >=, >, != |double |
| double *operatore* double |&&, &#124;&#124; |double |

Durante il test di un valore double con un operatore ternario (`double ? statement1 : statement2`), diverso da zero è **true** e zero è **false**.

## Funzioni
Queste **funzioni** predefinite sono disponibili per consentire la definizione di una formula di ridimensionamento automatico.

| Funzione | Tipo restituito | Descrizione |
| --- | --- | --- |
| avg(doubleVecList) |double |Restituisce il valore medio per tutti i valori in doubleVecList. |
| len(doubleVecList) |double |Restituisce la lunghezza del vettore creato da doubleVecList. |
| lg(double) |double |Restituisce il logaritmo in base 2 di double. |
| lg(doubleVecList) |doubleVec |Restituisce il logaritmo in base 2 a livello di componente di doubleVecList. vec(double) deve essere passato in modo esplicito per il parametro. In caso contrario viene usata la versione double lg(double). |
| ln(double) |double |Restituisce il logaritmo naturale di double. |
| ln(doubleVecList) |doubleVec |Restituisce il logaritmo in base 2 a livello di componente di doubleVecList. vec(double) deve essere passato in modo esplicito per il parametro. In caso contrario viene usata la versione double lg(double). |
| log(double) |double |Restituisce il logaritmo in base 10 di double. |
| log(doubleVecList) |doubleVec |Restituisce il logaritmo in base 10 a livello di componente di doubleVecList. vec(double) deve essere passato in modo esplicito per il singolo parametro double. In caso contrario, viene usata la versione double log(double). |
| max(doubleVecList) |double |Restituisce il valore massimo in doubleVecList. |
| min(doubleVecList) |double |Restituisce il valore minimo in doubleVecList. |
| norm(doubleVecList) |double |Restituisce la norma 2 del vettore creato da doubleVecList. |
| percentile(doubleVec v, double p) |double |Restituisce l'elemento percentile del vettore v. |
| rand() |double |Restituisce un valore casuale compreso tra 0,0 e 1,0. |
| range(doubleVecList) |double |Restituisce la differenza tra i valori minimo e massimo in doubleVecList. |
| std(doubleVecList) |double |Restituisce la deviazione standard del campione dei valori in doubleVecList. |
| stop() | |Arresta la valutazione dell'espressione per il ridimensionamento automatico. |
| sum(doubleVecList) |double |Restituisce la somma di tutti i componenti di doubleVecList. |
| time(string dateTime="") |timestamp |Restituisce il timestamp dell'ora corrente se non vengono passati parametri oppure il timestamp della stringa dateTime, se è stata passata. I formati dateTime supportati sono W3C-DTF e RFC 1123. |
| val(doubleVec v, double i) |double |Restituisce il valore dell'elemento nella posizione i nel vettore v con un indice iniziale pari a zero. |

Alcune delle funzioni descritte nella tabella precedente possono accettare un elenco come argomento. L'elenco con valori delimitati da virgole è una combinazione qualsiasi di *double* e *doubleVec*. ad esempio:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Il valore *doubleVecList* viene convertito in un singolo valore *doubleVec* prima della valutazione. Ad esempio, se `v = [1,2,3]`, la chiamata di `avg(v)` equivale alla chiamata di `avg(1,2,3)`. La chiamata di `avg(v, 7)` equivale alla chiamata di `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Ottenere dati di esempio
Le formule di ridimensionamento automatico agiscono sui dati di metrica (campioni) forniti dal servizio Batch. Una formula aumenta o riduce le dimensioni del pool in base ai valori che ottiene dal servizio. Le variabili definite dal servizio descritte sopra sono oggetti che offrono vari metodi per accedere ai dati associati a un dato oggetto. Ad esempio, l'espressione seguente mostra una richiesta per recuperare gli ultimi 5 minuti di utilizzo della CPU:

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

| Metodo | Descrizione |
| --- | --- |
| GetSample() |Il metodo `GetSample()` restituisce un vettore relativo al campione di dati.<br/><br/>Un campione vale 30 secondi di dati metrici. In altre parole i campioni vengono raccolti ogni 30 secondi, ma come indicato di seguito si verifica un ritardo tra il momento in cui un campione viene raccolto e il momento in cui è disponibile per una formula. Di conseguenza, non tutti i campioni di un determinato periodo di tempo possono essere disponibili per la valutazione mediante una formula.<ul><li>`doubleVec GetSample(double count)`<br/> specifica il numero di campioni da ottenere dai campioni raccolti più di recente.<br/><br/>`GetSample(1)` restituisce l'ultimo campione disponibile. Per le metriche come `$CPUPercent` non deve tuttavia essere usato perché non è possibile sapere *quando* è stato raccolto il campione. Potrebbe essere recente o risultare molto meno recente a causa di problemi di sistema. In questi casi è preferibile usare un intervallo di tempo come illustrato di seguito.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/> specifica una finestra temporale per la raccolta dei dati campione. Opzionalmente, specifica anche la percentuale di campioni che deve essere disponibile nell'intervallo di tempo richiesto.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` restituisce 20 campioni se nella cronologia CPUPercent sono presenti tutti i campioni per gli ultimi dieci minuti. Se l'ultimo minuto della cronologia non è disponibile, vengono tuttavia restituiti solo 18 campioni. In questo caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` avrebbe esito negativo poiché è disponibile solo il 90% dei campioni.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` potrebbe avere successo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/> specifica un intervallo di tempo per la raccolta dei dati, con un'ora di inizio e un'ora di fine.<br/><br/>Come indicato in precedenza, si verifica un ritardo tra il momento in cui un campione viene raccolto e il momento in cui è disponibile per una formula. È necessario tenere presente questo particolare quando si usa il metodo `GetSample`. Vedere `GetSamplePercent` di seguito. |
| GetSamplePeriod() |Restituisce il periodo dei campioni raccolti in un set di dati campione cronologici. |
| Count() |Restituisce il numero totale di campioni nella cronologia dei dati di metrica. |
| HistoryBeginTime() |Restituisce il timestamp del campione di dati disponibile meno recente per la metrica. |
| GetSamplePercent() |Restituisce la percentuale di campioni disponibili per un determinato intervallo di tempo. Ad esempio:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>poiché il metodo `GetSample` non riesce se la percentuale di campioni restituiti è minore del valore `samplePercent` specificato, è possibile eseguire prima il controllo con il metodo `GetSamplePercent`. È quindi possibile eseguire un'azione alternativa se non sono presenti campioni sufficienti, senza interrompere la valutazione del ridimensionamento automatico. |

### Campioni, percentuale di campioni e metodo *GetSample()*
L'operazione di base per il funzionamento di una formula di ridimensionamento automatico consiste nell'ottenere i dati di metrica relativi a risorse ed attività e quindi l'adeguamento delle dimensioni del pool in base a tali dati. Di conseguenza, è importante conoscere a fondo la modalità di interazione delle formule di ridimensionamento automatico con i dati di metrica o "campioni".

**Esempi**

Il servizio Batch acquisisce periodicamente *campioni* di metriche relative a risorse ed attività e li rende disponibili per le formule di ridimensionamento automatico. Questi campioni vengono registrati ogni 30 secondi dal servizio Batch. In genere si verifica una certa latenza che causa un ritardo tra il momento in cui i campioni sono stati registrati e il momento in cui vengono resi disponibili e possono essere letti dalle formule di ridimensionamento automatico. Inoltre, a causa di vari fattori, ad esempio problemi di rete o dell'infrastruttura, è possibile che i campioni non siano stati registrati per un particolare intervallo, determinando una condizione di campioni "mancanti".

**Percentuale di campioni**

Quando si passa un oggetto `samplePercent` al metodo `GetSample()` o si chiama il metodo `GetSamplePercent()`, il termine "percentuale" si riferisce a un confronto tra il *possibile* numero totale di campioni registrati dal servizio Batch e il numero di campioni effettivamente *disponibili* per la formula di ridimensionamento automatico.

Come esempio, si esaminerà un intervallo di tempo di 10 minuti. Poiché i campioni vengono registrati ogni 30 secondi, in un intervallo di tempo di 10 minuti il numero massimo totale di campioni registrati da Batch sarà di 20 campioni (2 al minuto). Tuttavia, a causa della latenza intrinseca del meccanismo di creazione di report o di un altro problema all'interno dell'infrastruttura di Azure, potrebbero essere disponibili solo 15 campioni per la lettura da parte della formula di ridimensionamento automatico. Ciò significa che per il periodo di 10 minuti solo il **75%** del numero totale di campioni registrati è effettivamente disponibile per la formula.

**GetSample() e intervalli di campioni**

Le formule di ridimensionamento automatico aumentano e riducono i pool, aggiungendovi nodi o rimuovendoli. Poiché i nodi hanno un costo, si vuole garantire che le formule applichino decisioni intelligenti sulla base di dati sufficienti. È quindi consigliabile usare un tipo di analisi delle tendenze nelle formule, aumentando e riducendo i pool in base a un *intervallo* di campioni raccolti.

A tale scopo, usare `GetSample(interval look-back start, interval look-back end)` per restituire un **vettore** di campioni:

`runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);`

Quando la riga precedente viene valutata da Batch, restituisce un intervallo di campioni come vettore di valori. Ad esempio:

`runningTasksSample=[1,1,1,1,1,1,1,1,1,1];`

Dopo aver raccolto il vettore di campioni, è possibile usare funzioni come `min()`, `max()` e `avg()` per derivare valori significativi dall'intervallo raccolto.

Per maggiore sicurezza, è possibile fare in modo che la valutazione di una formula *non riesca* se per un determinato periodo di tempo è disponibile una quantità di campioni inferiore a una certa percentuale. L'impostazione di un esito negativo della valutazione della formula indica a Batch di interromperne l'ulteriore valutazione se la percentuale di campioni specificata non è disponibile, evitando quindi di modificare le dimensioni del pool. Per specificare una percentuale di campioni obbligatoria perché la valutazione riesca, specificarla come terzo parametro in `GetSample()`. Qui è specificato un requisito pari al 75% dei campioni:

`runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);`

A causa del ritardo nella disponibilità dei campioni citato in precedenza, è anche importante specificare sempre un intervallo di tempo con un'ora di inizio antecedente di almeno un minuto. La propagazione dei campioni attraverso il sistema richiede infatti un minuto circa, quindi i campioni nell'intervallo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` spesso non saranno disponibili. Anche in questo caso, è possibile usare il parametro percentuale di `GetSample()` per imporre uno specifico requisito di percentuale dei campioni.

> [!IMPORTANT]
> È **consigliabile** **evitare di basarsi *solo* su `GetSample(1)` nelle formule di ridimensionamento automatico**. `GetSample(1)` indica infatti essenzialmente al servizio Batch di restituire l'ultimo campione disponibile, indipendentemente da quanto tempo prima è stato ottenuto. Essendo solo un singolo campione, che potrebbe anche non essere recente, potrebbe non essere rappresentativo dell'immagine più ampia dello stato recente di attività o risorse. Se si usa `GetSample(1)`, accertarsi che faccia parte di un'istruzione di dimensioni maggiori e non sia il solo punto dati su cui si basa la formula.
> 
> 

## Metrica
Quando si definisce una formula, è possibile usare metriche di **risorse** e di **attività**. Adeguare il numero di destinazione di nodi dedicati nel pool in base ai dati di metrica ottenuti e valutati. Per altre informazioni su ogni metrica, vedere la sezione [Variabili](#variables) precedente.

<table>
  <tr>
    <th>Metrica</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td><b>Risorsa</b></td>
    <td><p><b>La metrica delle risorse</b> si basa sull'utilizzo della memoria, della CPU e della larghezza di banda dei nodi di calcolo, nonché sul numero di nodi.</p>
        <p> Queste variabili definite dal servizio sono utili per eseguire adeguamenti in base al conteggio dei nodi:</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Queste variabili definite dal servizio sono utili per eseguire adeguamenti in base all'utilizzo delle risorse dei nodi:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Attività</b></td>
    <td><p>La <b>metrica delle attività</b> si basa sullo stato delle attività, ad esempio Attiva, In sospeso e Completata. Le variabili definite dal servizio seguenti sono utili per gli adeguamenti delle dimensioni del pool basati sulla metrica delle attività:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## Creare una formula di ridimensionamento automatico
La creazione di una formula di ridimensionamento automatico prevede la composizione di istruzioni che usano i componenti precedenti e quindi la combinazione di tali istruzioni in una formula completa. Ad esempio, per creare una formula è prima necessario definire i requisiti per una formula che:

1. Aumenta il numero di destinazione dei nodi di calcolo in un pool se l'utilizzo della CPU è elevato.
2. Riduce il numero di destinazione dei nodi di calcolo in un pool se l'utilizzo della CPU è basso.
3. Limita sempre il numero massimo di nodi a 400.

Per l'*aumento* dei nodi durante l'uso elevato della CPU, viene definita un'istruzione che popola una variabile definita dall'utente ($TotalNodes) con un valore pari al 110% dell'attuale numero di destinazione dei nodi, se l'uso minimo medio della CPU negli ultimi 10 minuti è superiore al 70%:

`$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;`

L'istruzione successiva imposta la stessa variabile sul 90% dell'attuale numero di destinazione dei nodi, se l'uso medio della CPU negli ultimi 60 minuti è stato *inferiore* al 20%. Questo riduce il numero di destinazione durante l'utilizzo ridotto della CPU. Si noti che questa istruzione fa anche riferimento alla variabile definita dall'utente *$TotalNodes* dell'istruzione precedente.

`$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;`

Ora viene limitato il numero di destinazione dei nodi di calcolo dedicati a un **massimo** di 400:

`$TargetDedicated = min(400, $TotalNodes)`

Ecco la formula completa:

```
$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
$TargetDedicated = min(400, $TotalNodes)
```

> [!NOTE]
> Una formula di ridimensionamento automatico è composta da variabili, tipi, operazioni e funzioni dell'API [Batch REST][rest_api]. Questi elementi vengono usati nelle stringhe della formula anche quando si usa la libreria [Batch .NET][net_api].
> 
> 

## Creare un pool con il ridimensionamento automatico abilitato
Per abilitare il ridimensionamento automatico durante la creazione di un pool, usare una delle tecniche seguenti:

* [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx): questo cmdlet di Azure PowerShell usa il parametro AutoScaleFormula per specificare la formula di ridimensionamento automatico.
* [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx): dopo la chiamata di questo metodo .NET per creare un pool, vengono impostate le proprietà [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) e [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) del pool per abilitare il ridimensionamento automatico.
* [Aggiungere un pool a un account](https://msdn.microsoft.com/library/azure/dn820174.aspx): gli elementi enableAutoScale e autoScaleFormula vengono usati in questa richiesta dell'API REST per impostare il ridimensionamento automatico del pool quando viene creato.

> [!IMPORTANT]
> Se si crea un pool abilitato per il ridimensionamento automatico usando una delle tecniche descritte sopra, il parametro *targetDedicated* per il pool **non** deve essere specificato. Si noti anche che per ridimensionare manualmente un pool abilitato per il ridimensionamento automatico, ad esempio con [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool], è necessario **disabilitare** prima di tutto il ridimensionamento automatico nel pool e quindi ridimensionarlo.
> 
> 

Il frammento di codice seguente illustra la creazione di un pool abilitato per il ridimensionamento automatico ([CloudPool][net_cloudpool]) usando la libreria [Batch .NET][net_api]. La formula di ridimensionamento automatico del pool imposta il numero di destinazione dei nodi su 5 il lunedì e su 1 per tutti gli altri giorni della settimana. L'intervallo per il ridimensionamento automatico è impostato su 30 minuti. Vedere [Intervallo di ridimensionamento automatico](#automatic-scaling-interval) di seguito. In questo e in altri frammenti di codice C# in questo articolo, "myBatchClient" è un'istanza correttamente inizializzata di [BatchClient][net_batchclient].

```
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

### Intervallo di ridimensionamento automatico
Per impostazione predefinita, il servizio Batch adegua le dimensioni di un pool in base alla relativa formula di ridimensionamento automatico ogni **15 minuti**. Questo intervallo è tuttavia configurabile usando le proprietà del pool seguenti:

* API REST: [autoScaleEvaluationInterval][rest_autoscaleinterval]
* API .NET: [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval]

L'intervallo minimo è di 5 minuti e il massimo di 168 ore. Se viene specificato un intervallo che non rientra in questi valori, il servizio Batch restituisce un errore di richiesta non valida (400).

> [!NOTE]
> La funzionalità di ridimensionamento automatico non è attualmente concepita come risposta alle modifiche in meno di un minuto, ma piuttosto per l'adeguamento graduale delle dimensioni del pool durante l'esecuzione di un carico di lavoro.
> 
> 

## Abilitare il ridimensionamento automatico dopo la creazione di un pool
Se è già stato configurato un pool con un numero specificato di nodi di calcolo usando il parametro *targetDedicated*, è possibile aggiornare il pool esistente in un secondo momento per la scalabilità automatica. Questa operazione può essere eseguita in uno dei modi seguenti:

* [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale]\: questo metodo .NET richiede l'ID di un pool esistente e la formula di ridimensionamento automatico da applicare al pool.
* [Abilitare il ridimensionamento automatico in un pool][rest_enableautoscale]\: questa richiesta dell'API REST richiede la presenza dell'ID del pool esistente nell'URI e della formula di ridimensionamento automatico nel corpo della richiesta.

> [!NOTE]
> Se è stato specificato un valore per il parametro *targetDedicated* durante la creazione del pool, questo verrà ignorato quando viene valutata la formula di ridimensionamento automatico.
> 
> 

Questo frammento di codice illustra come abilitare il ridimensionamento automatico in un pool esistente con la libreria [Batch .NET][net_api]. Si noti che per l'abilitazione e l'aggiornamento della formula in un pool esistente viene usato lo stesso metodo. Di conseguenza, se il ridimensionamento automatico è già stato abilitato, questa tecnica *aggiornerà* la formula nel pool specificato. Nel frammento di codice si presuppone che "mypool" sia l'ID di un pool [CloudPool][net_cloudpool] esistente.

         // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
         // Mondays, and 1 on every other day of the week
         string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

         // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
         // method, passing in both the pool's ID and the new formula.
         myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## Valutare la formula di ridimensionamento automatico
È sempre consigliabile valutare una formula prima di usarla nell'applicazione. La formula viene valutata eseguendo un test della formula su un pool esistente. A questo scopo, usare:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) o [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx): questi metodi .NET richiedono l'ID di un pool esistente e la stringa che contiene la formula di ridimensionamento automatico. I risultati della chiamata sono contenuti in un'istanza della classe [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
* [Valutare una formula di ridimensionamento automatico](https://msdn.microsoft.com/library/azure/dn820183.aspx): in questa richiesta dell'API REST, l'ID del pool è specificato nell'URI. La formula di ridimensionamento automatico è specificata nell'elemento *autoScaleFormula* del corpo della richiesta. La risposta dell'operazione contiene eventuali informazioni sugli errori che potrebbero essere correlate alla formula.

> [!NOTE]
> Per valutare una formula di ridimensionamento automatico, è necessario avere abilitato prima il ridimensionamento automatico del pool usando una formula valida.
> 
> 

Questo frammento di codice che usa la libreria [Batch .NET][net_api] consente di valutare una formula prima di applicarla al pool [CloudPool][net_cloudpool].

```
// First obtain a reference to the existing pool
CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
{
    // The formula to evaluate - adjusts target number of nodes based on day of week and time of day
    string myFormula = @"
        $CurTime=time();
        $WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
        $IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
        $IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
        $TargetDedicated=$IsWorkingWeekdayHour?20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
    // the pool.
    AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.AutoScaleRun.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun. This will display the values of each
        // variable as evaluated by the autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

        // Apply the formula to the pool since it evaluated successfully
        client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
    }
}
```

La valutazione corretta della formula in questo frammento di codice genererà un output simile al seguente:

`AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0`

## Ottenere informazioni sulle esecuzioni del ridimensionamento automatico
È opportuno verificare periodicamente i risultati delle esecuzioni del ridimensionamento automatico per assicurarsi che il comportamento della formula sia quello previsto.

* [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx): quando si usa la libreria .NET, questa proprietà di un pool fornisce un'istanza della classe [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx). Questa classe fornisce le proprietà seguenti dell'esecuzione più recente del ridimensionamento automatico:
  * [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  * [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  * [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [Ottenere informazioni su un pool](https://msdn.microsoft.com/library/dn820165.aspx): questa richiesta dell'API REST restituisce informazioni relative al pool, che includono l'esecuzione più recente del ridimensionamento automatico.

## <a name="examples"></a>Formule di esempio
Si esamineranno ora alcuni esempi che mostrano come si possono usare le formule per ridimensionare automaticamente le risorse di calcolo in un pool.

### Esempio 1: Adeguamento basato sul tempo
Si vogliono regolare le dimensioni del pool in base al giorno della settimana e all'ora del giorno, aumentando o riducendo di conseguenza il numero di nodi nel pool:

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

Questa formula ottiene l'ora corrente. Durante i giorni della settimana, da 1 a 5, e durante l'orario lavorativo, dalle 8.00 alle 18.00, le dimensioni del pool di destinazione sono impostate su 20 nodi. In caso contrario, le dimensioni del pool sono impostate su 10 nodi.

### Esempio 2: Adeguamento basato sulle attività
In questo esempio le dimensioni del pool vengono regolate in base al numero di attività nella coda. Si noti che sia i commenti che le interruzioni di riga sono accettabili nelle stringhe della formula.

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point, otherwise we use the maximum of
// last sample point and the history average.
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
$TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
// should be adjusted according to your use case.
$TargetDedicated = max(0,min($TargetVMs,20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Esempio 3: Considerazioni sulle attività parallele
Questo è un altro esempio che adegua le dimensioni del pool in base al numero di attività. Questa formula considera anche il valore [MaxTasksPerComputeNode][net_maxtasks] impostato per il pool. Questa operazione è particolarmente utile nelle situazioni in cui l'[esecuzione di attività parallele](batch-parallel-node-tasks.md) è abilitata nel pool.

```
// Determine whether 70 percent of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Esempio 4: Impostazione di dimensioni iniziali del pool
Questo esempio mostra un segmento di codice C# con una formula di ridimensionamento automatico che imposta le dimensioni del pool su un certo numero di nodi per un periodo di tempo iniziale. Adegua quindi le dimensioni del pool in base al numero di attività in esecuzione e attive dopo la scadenza del periodo di tempo iniziale.

```
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"

    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

La formula nel frammento di codice precedente:

* Imposta le dimensioni iniziali del pool su 4 nodi.
* Non modifica le dimensioni del pool nei primi 10 minuti del relativo ciclo di vita.
* Dopo 10 minuti ottiene il valore massimo del numero di attività in esecuzione e attive negli ultimi 60 minuti.
  * Se entrambi i valori corrispondono a 0, ovvero nessuna attività era in esecuzione o attiva negli ultimi 60 minuti, le dimensioni del pool vengono impostate su 0.
  * Se uno dei valori è maggiore di zero, non viene apportata alcuna modifica.

## Passaggi successivi
* [Ottimizzare l'uso delle risorse di calcolo di Azure Batch con attività dei nodi simultanee](batch-parallel-node-tasks.md) contiene informazioni dettagliate su come è possibile eseguire più attività contemporaneamente sui nodi di calcolo nel pool. Oltre al ridimensionamento automatico, questa funzionalità può contribuire a ridurre la durata del processo per alcuni carichi di lavoro, riducendo i costi.
* Per ottimizzare ulteriormente l'efficienza, assicurarsi che l'applicazione Batch esegua query sul servizio Batch in modo ottimale. In [Eseguire query sul servizio Azure Batch in modo efficiente](batch-efficient-list-queries.md) si apprenderà come limitare la quantità dei dati trasmessi in rete quando si esegue una query sullo stato di migliaia di nodi di calcolo o attività.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/it-IT/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

<!---HONumber=AcomDC_0928_2016-->