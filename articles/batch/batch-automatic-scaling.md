---
title: Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch
description: Abilitare il ridimensionamento automatico in un pool cloud per adeguare dinamicamente il numero di nodi di calcolo nel pool.
ms.topic: how-to
ms.date: 07/27/2020
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: 0309a5665cf9338340a21f4c8d0eb5bc3c848a04
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387473"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Creare una formula di scalabilità automatica per i nodi di calcolo in un pool Batch

Azure Batch possibile ridimensionare automaticamente i pool in base ai parametri definiti, risparmiando tempo e denaro. Con il ridimensionamento automatico, batch aggiunge dinamicamente nodi a un pool come richieste di attività aumenta e rimuove i nodi di calcolo quando le richieste di attività diminuiscono.

Per abilitare la scalabilità automatica in un pool di nodi di calcolo, è necessario associare il pool a una *formula di scalabilità* automatica definita dall'utente. Il servizio batch usa la formula di scalabilità automatica per determinare il numero di nodi necessari per l'esecuzione del carico di lavoro. Questi nodi possono essere nodi dedicati o [nodi con priorità bassa](batch-low-pri-vms.md). Batch esamina periodicamente i dati delle metriche del servizio e li usa per modificare il numero di nodi nel pool in base alla formula e a un intervallo definito dall'utente.

È possibile abilitare il ridimensionamento automatico durante la creazione di un pool o applicarlo a un pool esistente. Batch consente di valutare le formule prima di assegnarle ai pool e di monitorare lo stato delle esecuzioni della scalabilità automatica. Dopo aver configurato un pool con scalabilità automatica, è possibile apportare modifiche alla formula in un secondo momento.

> [!IMPORTANT]
> Quando si crea un account batch, è possibile specificare la [modalità di allocazione pool](accounts.md), che determina se i pool vengono allocati in una sottoscrizione del servizio batch (impostazione predefinita) o nella sottoscrizione utente. Se è stato creato l'account batch con la configurazione predefinita del servizio batch, l'account è limitato a un numero massimo di core che possono essere usati per l'elaborazione. Il servizio Batch gestisce la scalabilità dei nodi di calcolo solo fino al raggiungimento del limite di core. Per questo motivo, il servizio Batch può non raggiungere il numero di nodi di calcolo specificato da una formula di scalabilità automatica. Per informazioni su come visualizzare e aumentare le quote dell'account, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md) .
>
>Se l'account è stato creato con la modalità di sottoscrizione utente, l'account condivide la quota di core per la sottoscrizione. Per altre informazioni, vedere [Limiti relativi a Macchine virtuali](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) in [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="autoscale-formulas"></a>Formule di scalabilità automatica

Una formula di scalabilità automatica è un valore stringa definito che contiene una o più istruzioni. La formula di scalabilità automatica viene assegnata all'elemento [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) di un pool (Batch REST) o alla proprietà [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (Batch .NET). Il servizio Batch usa la formula per determinare il numero di nodi di calcolo di destinazione del pool per l'intervallo di elaborazione successivo. La stringa della formula non può superare 8 KB, può includere fino a 100 istruzioni separate da punti e virgola e può includere interruzioni di riga e commenti.

È possibile paragonare le formule di ridimensionamento automatico a un "linguaggio" di ridimensionamento automatico di Batch. Le istruzioni formula sono espressioni in formato libero che possono includere sia variabili definite dal servizio (definite dal servizio batch) che variabili definite dall'utente. Le formule possono eseguire varie operazioni su questi valori usando i tipi, gli operatori e le funzioni predefiniti. Ad esempio, un'istruzione può avere il formato seguente:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Le formule contengono in genere più istruzioni che eseguono operazioni su valori ottenuti nelle istruzioni precedenti. Ad esempio, si ottiene prima di tutto un valore per `variable1`, quindi il valore viene passato a una funzione per popolare `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Includere queste istruzioni nella formula di scalabilità automatica per arrivare al numero di nodi di calcolo di destinazione. I nodi dedicati e i nodi con priorità bassa hanno le proprie impostazioni di destinazione. Una formula di scalabilità automatica può includere un valore di destinazione per i nodi dedicati, un valore di destinazione per i nodi con priorità bassa o entrambi.

Il numero di nodi di destinazione può essere maggiore, minore o uguale al numero attuale di nodi di tale tipo nel pool. Batch valuta la formula di scalabilità automatica di un pool a [intervalli di scalabilità automatica](#automatic-scaling-interval)specifici. Batch regola quindi il numero di nodi di destinazione di ogni tipo nel pool in base al numero specificato dalla formula di scalabilità automatica al momento della valutazione.

### <a name="sample-autoscale-formulas"></a>Formule di scalabilità automatica di esempio

Di seguito sono riportati due esempi di formule di scalabilità automatica, che possono essere adattate per la maggior parte degli scenari. Le variabili `startingNumberOfVMs` e `maxNumberofVMs` nelle formule di esempio possono essere modificate in base alle esigenze.

#### <a name="pending-tasks"></a>Attività in sospeso

Con questa formula di scalabilità automatica il pool viene inizialmente creato con una singola macchina virtuale. La metrica `$PendingTasks` definisce il numero di attività in esecuzione o in coda. La formula trova il numero medio di attività in sospeso negli ultimi 180 secondi e imposta la variabile `$TargetDedicatedNodes` di conseguenza. La formula garantisce che il numero di nodi dedicati di destinazione non superi mai 25 macchine virtuali. Man mano che vengono inviate nuove attività, il pool si espande automaticamente. Quando le attività vengono completate, le VM diventano gratuite e la formula di scalabilità automatica compatta il pool.

Questa formula ridimensiona i nodi dedicati, ma può essere modificata per applicarla anche ai nodi con priorità bassa.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Nodi interrotti

Questo esempio crea un pool che inizia con 25 nodi con priorità bassa. Ogni volta che un nodo con priorità bassa viene interrotto, viene sostituito con un nodo dedicato. Come nel primo esempio, la variabile `maxNumberofVMs` impedisce al pool di superare 25 VM. Questo esempio è utile per sfruttare le macchine virtuali con priorità bassa, garantendo al tempo stesso che per la durata del pool si verificherà solo un numero fisso di interruzioni.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Verranno fornite altre informazioni su [come creare formule di scalabilità](#write-an-autoscale-formula) automatica e vedere ulteriori [formule di scalabilità](#example-autoscale-formulas) automatica di esempio più avanti in questo argomento.

## <a name="variables"></a>variables

Nelle formule di scalabilità automatica è possibile usare sia **variabili definite dal servizio** sia **variabili definite dall'utente**.

Le variabili definite dal servizio sono incorporate nel servizio Batch. Alcune sono in lettura/scrittura e altre di sola lettura.

Le variabili definite dall'utente vengono configurate dall'utente. Nella formula di esempio illustrata in precedenza `$TargetDedicatedNodes` e `$PendingTasks` sono variabili definite dal servizio, mentre `startingNumberOfVMs` e `maxNumberofVMs` sono variabili definite dall'utente.

> [!NOTE]
> Le variabili definite dal servizio sono sempre precedute da un segno di dollaro ($). Per le variabili definite dall'utente il segno di dollaro è facoltativo.

Le tabelle seguenti illustrano le variabili di lettura/scrittura e di sola lettura definite dal servizio batch.

### <a name="read-write-service-defined-variables"></a>Variabili in lettura/scrittura definite dal servizio

È possibile ottenere e impostare i valori di queste variabili definite dal servizio per gestire il numero di nodi di calcolo in un pool.

| Variabile | Descrizione |
| --- | --- |
| $TargetDedicatedNodes |Numero di destinazione dei nodi di calcolo dedicati per il pool. Viene specificato come destinazione perché un pool potrebbe non ottenere sempre il numero desiderato di nodi. Se, ad esempio, il numero di destinazione dei nodi dedicati viene modificato da una valutazione di scalabilità automatica prima che il pool raggiunga la destinazione iniziale, il pool potrebbe non raggiungere la destinazione. <br /><br /> Un pool in un account creato in modalità servizio batch potrebbe non raggiungere la destinazione se la destinazione supera un nodo dell'account batch o una quota di core. Un pool in un account creato in modalità di sottoscrizione utente potrebbe non raggiungere la destinazione se la destinazione supera la quota Core condivisa per la sottoscrizione.|
| $TargetLowPriorityNodes |Numero di destinazione dei nodi di calcolo con priorità bassa per il pool. Viene specificato come destinazione perché un pool potrebbe non sempre raggiungere il numero desiderato di nodi. Se, ad esempio, il numero di destinazione di nodi con priorità bassa viene modificato da una valutazione di scalabilità automatica prima che il pool raggiunga la destinazione iniziale, è possibile che il pool non raggiunga la destinazione. Un pool potrebbe anche non raggiungere il valore di destinazione se supera la quota di nodi o core di un account Batch. <br /><br /> Per altre informazioni sui nodi calcolo con priorità bassa, vedere [Usare le macchine virtuali con priorità bassa in Batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |L'azione che si verifica quando i nodi di calcolo vengono rimossi da un pool. I valori possibili sono:<ul><li>**requeue**: valore predefinito. Termina immediatamente le attività e le inserisce nuovamente nella coda di processi in modo che vengano ripianificate. Questa azione garantisce che il numero di nodi di destinazione sia raggiunto il più rapidamente possibile. Tuttavia, può essere meno efficiente, in quanto tutte le attività in esecuzione verranno interrotte e dovranno essere riavviate completamente. <li>**Terminate**: termina immediatamente le attività e le rimuove dalla coda dei processi.<li>**taskcompletion**: attende il completamento delle attività attualmente in esecuzione e quindi rimuove il nodo dal pool. Usare questa opzione per evitare che le attività vengano interrotte e riaccodate, sprecando qualsiasi lavoro eseguito dall'attività.<li>**retaineddata**: attende la pulizia di tutti i dati memorizzati nell'attività locale nel nodo prima di rimuovere il nodo dal pool.</ul> |

> [!NOTE]
> La variabile `$TargetDedicatedNodes` può anche essere specificata con l'alias `$TargetDedicated`. Analogamente, la variabile `$TargetLowPriorityNodes` può essere specificata con l'alias `$TargetLowPriority`. Se la formula imposta sia la variabile con il nome completo sia il relativo alias, il valore assegnato alla variabile con il nome completo avrà la precedenza.

### <a name="read-only-service-defined-variables"></a>Variabili di sola lettura definite dal servizio

È possibile ottenere il valore di queste variabili definite dal servizio per apportare modifiche basate sulle metriche del servizio batch.

> [!IMPORTANT]
> Le attività di rilascio del processo non sono attualmente incluse nelle variabili che forniscono i conteggi delle attività, ad esempio $ActiveTasks e $PendingTasks. A seconda della formula di scalabilità automatica, questo può comportare la rimozione dei nodi senza nodi disponibili per l'esecuzione delle attività di rilascio del processo.

| Variabile | Descrizione |
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
| $ActiveTasks |Numero di attività che sono pronte per l'esecuzione, ma non sono ancora in esecuzione. Sono incluse tutte le attività in stato attivo e le cui dipendenze sono state soddisfatte. Tutte le attività che sono nello stato attivo, ma per le quali le dipendenze non sono state soddisfatte vengono escluse dal conteggio per $ActiveTasks. Per un'attività con più istanze, $ActiveTasks includerà il numero di istanze impostate nell'attività.|
| $RunningTasks |Numero di attività nello stato in corso di esecuzione. |
| $PendingTasks |La somma di $ActiveTasks e $RunningTasks. |
| $SucceededTasks |Numero di attività completate correttamente. |
| $FailedTasks |Numero di attività non riuscite. |
| $CurrentDedicatedNodes |Numero corrente di nodi di calcolo dedicati. |
| $CurrentLowPriorityNodes |Numero corrente di nodi di calcolo con priorità bassa, inclusi eventuali nodi superati. |
| $PreemptedNodeCount | Il numero di nodi nel pool che si trovano nello stato Annullato. |

> [!TIP]
> Queste variabili definite dal servizio di sola lettura sono *oggetti* che forniscono diversi metodi per accedere ai dati associati a ognuno di essi. Per altre informazioni, vedere [Ottenere dati di esempio](#obtain-sample-data) più avanti in questo articolo.

## <a name="types"></a>Tipi

Le formule di scalabilità automatica supportano i tipi seguenti:

- double
- doubleVec
- doubleVecList
- string
- timestamp: una struttura composta che contiene i membri seguenti:
  - year
  - month (1-12)
  - day (1-31)
  - weekday (in formato numero, ad esempio 1 per lunedì)
  - hour (in formato 24 ore, ad esempio 13 per indicare le ore 13.00)
  - minute (00-59)
  - second (00-59)
- timeInterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

## <a name="operations"></a>Operazioni

Queste operazioni sono consentite sui tipi elencati nella sezione precedente.

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
| timestamp *operatore* timestamp |- |timeInterval |
| *operatore* Double |-, ! |double |
| *operatore* timeInterval |- |timeInterval |
| double *operatore* double |<, <=, ==, >=, >, != |double |
| string *operatore* string |<, <=, ==, >=, >, != |double |
| timestamp *operatore* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operatore* timeinterval |<, <=, ==, >=, >, != |double |
| double *operatore* double |&&, &#124;&#124; |double |

Durante il test di un valore double con un operatore ternario (`double ? statement1 : statement2`), diverso da zero è **true** e zero è **false**.

## <a name="functions"></a>Funzioni

È possibile usare queste **funzioni** predefinite per la definizione di una formula di scalabilità automatica.

| Funzione | Tipo restituito | Descrizione |
| --- | --- | --- |
| avg(doubleVecList) |double |Restituisce il valore medio per tutti i valori in doubleVecList. |
| len(doubleVecList) |double |Restituisce la lunghezza del vettore creato da doubleVecList. |
| lg(double) |double |Restituisce il logaritmo in base 2 di double. |
| lg(doubleVecList) |doubleVec |Restituisce il logaritmo in base 2 a livello di componente di doubleVecList. vec(double) deve essere passato in modo esplicito per il parametro. In caso contrario viene usata la versione double lg(double). |
| ln(double) |double |Restituisce il logaritmo naturale di double. |
| ln(doubleVecList) |doubleVec |Restituisce il logaritmo naturale di double. |
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
| time(string dateTime="") |timestamp |Restituisce il timestamp dell'ora corrente se non viene passato alcun parametro o il timestamp della stringa dateTime se viene passato. I formati dateTime supportati sono W3C-DTF e RFC 1123. |
| val(doubleVec v, double i) |double |Restituisce il valore dell'elemento nella posizione i nel vettore v con un indice iniziale pari a zero. |

Alcune delle funzioni descritte nella tabella precedente possono accettare un elenco come argomento. L'elenco con valori delimitati da virgole è una combinazione qualsiasi di *double* e *doubleVec*. Ad esempio:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Il valore *doubleVecList* viene convertito in un singolo *doubleVec* prima della valutazione. Ad esempio, se `v = [1,2,3]`, la chiamata di `avg(v)` equivale alla chiamata di `avg(1,2,3)`. La chiamata di `avg(v, 7)` equivale alla chiamata di `avg(1,2,3,7)`.

## <a name="metrics"></a>Metriche

Quando si definisce una formula, è possibile usare metriche di risorse e di attività. Adeguare il numero di destinazione di nodi dedicati nel pool in base ai dati di metrica ottenuti e valutati. Per ulteriori informazioni su ogni metrica, vedere la sezione delle [variabili](#variables) precedente.

<table>
  <tr>
    <th>Metrica</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td><b>Risorsa</b></td>
    <td><p>La metrica delle risorse si basa sull'uso della memoria, della CPU e della larghezza di banda dei nodi di calcolo, nonché sul numero di nodi.</p>
        <p> Queste variabili definite dal servizio sono utili per eseguire adeguamenti in base al conteggio dei nodi:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
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
    <td><p>La metrica delle attività si basa sullo stato delle attività, ad esempio Attiva, In sospeso e Completata. Le variabili definite dal servizio seguenti sono utili per gli adeguamenti delle dimensioni del pool basati sulla metrica delle attività:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>Ottenere dati di esempio

Il funzionamento di base di una formula di scalabilità automatica consiste nell'ottenere i dati delle metriche relative a attività e risorse (esempi) e quindi modificare le dimensioni del pool in base a tali dati. Di conseguenza, è importante comprendere chiaramente il modo in cui le formule di scalabilità automatica interagiscono con gli esempi.

### <a name="methods"></a>Metodi

Le formule di scalabilità automatica agiscono su campioni di dati di metrica forniti dal servizio batch. Una formula consente di aumentare o ridurre le dimensioni del pool in base ai valori ottenuti. Le variabili definite dal servizio sono oggetti che forniscono metodi per accedere ai dati associati a tale oggetto. Ad esempio, l'espressione seguente mostra una richiesta per recuperare gli ultimi 5 minuti di utilizzo della CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

È possibile utilizzare i metodi seguenti per ottenere dati di esempio sulle variabili definite dal servizio.

| Metodo | Descrizione |
| --- | --- |
| GetSample() |Il metodo `GetSample()` restituisce un vettore relativo ai campioni di dati.<br/><br/>Un campione rappresenta 30 secondi di dati di metrica. In altre parole i campioni vengono raccolti ogni 30 secondi, ma come indicato di seguito si verifica un ritardo tra il momento in cui un campione viene raccolto e il momento in cui è disponibile per una formula. Di conseguenza, i campioni per un determinato periodo di tempo potrebbero non essere tutti disponibili per la valutazione da parte di una formula.<ul><li>`doubleVec GetSample(double count)`: Specifica il numero di campioni da ottenere dagli esempi più recenti raccolti. `GetSample(1)` restituisce l'ultimo campione disponibile. Per le metriche come `$CPUPercent` , tuttavia, `GetSample(1)` non deve essere usato, perché è impossibile capire *quando* è stato raccolto il campione. Potrebbe essere recente o, a causa di problemi di sistema, potrebbe essere molto più vecchio. In questi casi, è preferibile usare un intervallo di tempo, come illustrato di seguito.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Specifica un intervallo di tempo per la raccolta dei dati di esempio. Facoltativamente specifica anche la percentuale di campioni che deve essere disponibile nell'intervallo di tempo richiesto. Ad esempio, `$CPUPercent.GetSample(TimeInterval_Minute * 10)` restituisce 20 campioni se nella cronologia sono presenti tutti i campioni per gli ultimi 10 minuti `CPUPercent` . Se l'ultimo minuto della cronologia non era disponibile, verrebbero restituiti solo 18 esempi. In questo caso `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` , avrà esito negativo perché solo il 90% degli esempi è disponibile, ma avrà `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` esito positivo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Specifica un intervallo di tempo per la raccolta dei dati, con un'ora di inizio e un'ora di fine. Come indicato in precedenza, si verifica un ritardo tra il momento in cui un campione viene raccolto e il momento in cui diventa disponibile per una formula. È necessario tenere presente questo ritardo quando si usa il metodo `GetSample`. Vedere `GetSamplePercent` di seguito. |
| GetSamplePeriod() |Restituisce il periodo dei campioni raccolti in un set di dati campione cronologici. |
| Count() |Restituisce il numero totale di campioni nella cronologia dei dati di metrica. |
| HistoryBeginTime() |Restituisce il timestamp del campione di dati disponibile meno recente per la metrica. |
| GetSamplePercent() |Restituisce la percentuale di campioni disponibili per un determinato intervallo di tempo. Ad esempio: `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Poiché il metodo `GetSample` non riesce se la percentuale di campioni restituiti è minore del valore `samplePercent` specificato, è possibile eseguire prima il controllo con il metodo `GetSamplePercent`. È quindi possibile eseguire un'azione alternativa se non sono presenti campioni sufficienti, senza interrompere la valutazione del ridimensionamento automatico. |

### <a name="samples"></a>Esempi

Il servizio Batch acquisisce periodicamente campioni di metriche relative a risorse e attività e li rende disponibili per le formule di scalabilità automatica. Questi campioni vengono registrati ogni 30 secondi dal servizio Batch. In genere si verifica un ritardo tra il momento in cui i campioni sono stati registrati e il momento in cui vengono resi disponibili e possono essere letti dalle formule di scalabilità automatica. Inoltre, gli esempi potrebbero non essere registrati per un determinato intervallo a causa di fattori quali la rete o altri problemi di infrastruttura.

### <a name="sample-percentage"></a>Percentuale di campioni

Quando si passa un oggetto `samplePercent` al metodo `GetSample()` o si chiama il metodo `GetSamplePercent()`, il termine _percentuale_ si riferisce a un confronto tra il possibile numero totale di campioni registrati dal servizio Batch e il numero di campioni disponibili per la formula di scalabilità automatica.

Come esempio, si esaminerà un intervallo di tempo di 10 minuti. Poiché i campioni vengono registrati ogni 30 secondi in un intervallo di tempo di 10 minuti, il numero massimo totale di campioni registrati dal batch sarà di 20 campioni (2 al minuto). Tuttavia, a causa della latenza intrinseca del meccanismo di creazione di report e di altri problemi a all'interno di Azure, potrebbero essere disponibili solo 15 campioni per la lettura da parte della formula di scalabilità automatica. Per il periodo di 10 minuti, ad esempio, potrebbe essere quindi disponibile per la formula solo il 75% del numero totale di campioni registrati.

### <a name="getsample-and-sample-ranges"></a>GetSample() e intervalli di campioni

Le formule di scalabilità automatica aumentano e riducono i pool mediante l'aggiunta o la rimozione di nodi. Poiché i nodi costano il denaro, assicurarsi che le formule usino un metodo di analisi intelligente basato su dati sufficienti. Si consiglia di usare un'analisi del tipo di tendenza nelle formule. aumentando e riducendo i pool in base a un intervallo di campioni raccolti.

A tale scopo, usare `GetSample(interval look-back start, interval look-back end)` per restituire un vettore di campioni:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando la riga precedente viene valutata da Batch, restituisce un intervallo di campioni come vettore di valori. Ad esempio:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Dopo aver raccolto il vettore di campioni, è possibile usare funzioni come `min()`, `max()` e `avg()` per derivare valori significativi dall'intervallo raccolto.

Per maggiore sicurezza, è possibile fare in modo che la valutazione di una formula non riesca se per un determinato periodo di tempo è disponibile una quantità di campioni inferiore a una certa percentuale. L'impostazione di un esito negativo della valutazione della formula indica a Batch di interromperne l'ulteriore valutazione se la percentuale di campioni specificata non è disponibile. In questo caso non viene apportata alcuna modifica alla dimensione del pool. Per specificare una percentuale di campioni obbligatoria perché la valutazione riesca, specificarla come terzo parametro in `GetSample()`. Qui è specificato un requisito pari al 75% dei campioni:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Poiché è possibile che si verifichi un ritardo nella disponibilità di esempio, è sempre necessario specificare un intervallo di tempo con un'ora di inizio di ricerca precedente a un minuto. La propagazione dei campioni attraverso il sistema richiede circa un minuto, quindi i campioni nell'intervallo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` possono non essere disponibili. Anche in questo caso, è possibile usare il parametro percentuale di `GetSample()` per imporre uno specifico requisito di percentuale dei campioni.

> [!IMPORTANT]
> È consigliabile **evitare di basarsi *solo* su `GetSample(1)` nelle formule di scalabilità automatica**. `GetSample(1)` indica infatti essenzialmente al servizio Batch di restituire l'ultimo campione disponibile, indipendentemente da quanto tempo prima è stato recuperato. Essendo solo un singolo campione, che potrebbe anche non essere recente, potrebbe non essere rappresentativo dell'immagine più ampia dello stato recente di attività o risorse. Se si usa `GetSample(1)`, accertarsi che faccia parte di un'istruzione di dimensioni maggiori e non sia il solo punto dati su cui si basa la formula.

## <a name="write-an-autoscale-formula"></a>Scrivere una formula di scalabilità automatica

La compilazione di una formula di scalabilità automatica prevede la composizione di istruzioni che usano i componenti precedenti e quindi la combinazione di tali istruzioni in una formula completa. In questa sezione viene creata una formula di scalabilità automatica di esempio in grado di eseguire decisioni di scalabilità reali e apportare modifiche.

In primo luogo, definire i requisiti per la nuova formula di scalabilità automatica. La formula deve:

- Aumentare il numero di destinazione dei nodi di calcolo dedicati in un pool se l'uso della CPU è elevato.
- Ridurre il numero di destinazione dei nodi di calcolo dedicati in un pool se l'uso della CPU è basso.
- Limitare sempre il numero massimo di nodi dedicati a 400.
- Quando si riduce il numero di nodi, non rimuovere i nodi che eseguono attività; Se necessario, attendere il completamento delle attività prima di rimuovere i nodi.

La prima istruzione della formula aumenterà il numero di nodi durante l'uso elevato della CPU. Verrà definita un'istruzione che popola una variabile definita dall'utente ( `$totalDedicatedNodes` ) con un valore pari al 110% del numero di destinazione corrente dei nodi dedicati, ma solo se l'utilizzo minimo medio della CPU negli ultimi 10 minuti è superiore al 70%. In caso contrario, viene utilizzato il valore per il numero corrente di nodi dedicati.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Per ridurre il numero di nodi dedicati durante un utilizzo ridotto della CPU, l'istruzione successiva nella formula imposta la stessa `$totalDedicatedNodes` variabile sul 90% del numero di destinazione corrente dei nodi dedicati, se l'utilizzo medio della CPU negli ultimi 60 minuti è inferiore al 20%. In caso contrario, viene utilizzato il valore corrente di `$totalDedicatedNodes` compilato nell'istruzione precedente.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

A questo punto, il numero di destinazione dei nodi di calcolo dedicati verrà limitato a un massimo di 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Infine, si verificherà che i nodi non verranno rimossi fino al termine delle attività.

```
$NodeDeallocationOption = taskcompletion;
```

Ecco la formula completa:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> Se si sceglie di, è possibile includere sia i commenti che le interruzioni di riga nelle stringhe della formula.

## <a name="automatic-scaling-interval"></a>Intervallo di ridimensionamento automatico

Per impostazione predefinita, il servizio Batch adegua le dimensioni di un pool in base alla relativa formula di ridimensionamento automatico ogni 15 minuti. Questo intervallo è configurabile usando le proprietà del pool seguenti:

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

L'intervallo minimo è di 5 minuti e il massimo di 168 ore. Se viene specificato un intervallo che non rientra in questi valori, il servizio Batch restituisce un errore di richiesta non valida (400).

> [!NOTE]
> La funzionalità di ridimensionamento automatico non è attualmente concepita come risposta alle modifiche in meno di un minuto, ma piuttosto per l'adeguamento graduale delle dimensioni del pool durante l'esecuzione di un carico di lavoro.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Creare un pool abilitato per la scalabilità automatica con gli SDK Batch

La scalabilità automatica del pool può essere configurata usando uno degli [SDK di Batch](batch-apis-tools.md#azure-accounts-for-batch-development), l'[API REST Batch](/rest/api/batchservice/), i [cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md) e l'[interfaccia della riga di comando di Batch](batch-cli-get-started.md). In questa sezione è possibile vedere esempi per .NET e Python.

### <a name="net"></a>.NET

Per creare un pool con la scalabilità automatica abilitata in .NET, seguire questi passaggi:

1. Creare il pool con [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Impostare la proprietà [CloudPool.AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) su `true`.
1. Impostare la proprietà [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) con la formula di scalabilità automatica.
1. (Facoltativo) Impostare la proprietà [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (valore predefinito è 15 minuti).
1. Eseguire il commit del pool con [CloudPool.Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) o [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

L'esempio seguente crea un pool abilitato per la scalabilità automatica in .NET. La formula di scalabilità automatica del pool imposta il numero di destinazione dei nodi dedicati su 5 il lunedì e su 1 per ogni altro giorno della settimana. L'[intervallo di scalabilità automatica](#automatic-scaling-interval) è impostato su 30 minuti. In questo e in altri frammenti di codice C# di questo articolo, `myBatchClient` è un'istanza correttamente inizializzata della classe [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Quando si crea un pool abilitato per la scalabilità automatica, non specificare il parametro _targetDedicatedNodes_ o il parametro _targetLowPriorityNodes_ nella chiamata a **CreatePool**. Specificare invece le proprietà **AutoScaleEnabled** e **AutoScaleFormula** nel pool. I valori per queste proprietà determinano il numero di destinazione di ogni tipo di nodo.
>
> Per ridimensionare manualmente un pool abilitato per la scalabilità automatica, ad esempio con [BatchClient. PoolOperations. ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync), è prima necessario disabilitare la scalabilità automatica nel pool, quindi ridimensionarla.

### <a name="python"></a>Python

Per creare un pool abilitato per la scalabilità automatica con Python SDK:

1. Creare un pool e specificarne la configurazione.
1. Aggiungere il pool al client del servizio.
1. Abilitare la scalabilità automatica nel pool con una formula scritta.

Nell'esempio seguente vengono illustrati questi passaggi.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
             $isWorkingWeekdayHour = $workHours && $isWeekday;
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10),
                                            pool_enable_auto_scale_options=None,
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Altri esempi dell'uso di Python SDK sono disponibili nel [repository di argomenti di avvio rapido su Batch e Python](https://github.com/Azure-Samples/batch-python-quickstart) in GitHub.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Abilitare la scalabilità automatica in un pool esistente

Ogni SDK di batch fornisce un modo per abilitare il ridimensionamento automatico. Ad esempio:

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [Abilitare la scalabilità automatica in un pool](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (API REST)

Quando si Abilita la scalabilità automatica in un pool esistente, tenere presente quanto segue:

- Se la scalabilità automatica è attualmente disabilitata nel pool, è necessario specificare una formula di scalabilità automatica valida quando si emette la richiesta. Facoltativamente, è possibile specificare un intervallo di ridimensionamento automatico. Se non si specifica un intervallo, viene usato il valore predefinito di 15 minuti.
- Se la scalabilità automatica è attualmente abilitata nel pool, è possibile specificare una nuova formula, un nuovo intervallo o entrambi. È necessario specificare almeno una di queste proprietà.
  - Se si specifica un nuovo intervallo di ridimensionamento automatico, la pianificazione esistente viene arrestata e viene avviata una nuova pianificazione. L'ora di inizio della nuova pianificazione corrisponde al momento in cui è stata inviata la richiesta di abilitazione della scalabilità automatica.
  - Se si omette la formula o l'intervallo di scalabilità automatica, il servizio batch continuerà a usare il valore corrente dell'impostazione.

> [!NOTE]
> Se sono stati specificati i valori per i parametri *targetDedicatedNodes* o *TargetLowPriorityNodes* del metodo **CreatePool** quando è stato creato il pool in .NET o per i parametri confrontabili in un'altra lingua, questi valori vengono ignorati quando viene valutata la formula di scalabilità automatica.

Questo esempio C# usa la libreria [batch .NET](/dotnet/api/microsoft.azure.batch) per abilitare la scalabilità automatica in un pool esistente.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aggiornare una formula di scalabilità automatica

Per aggiornare la formula in un pool esistente abilitato per la scalabilità automatica, chiamare l'operazione per abilitare di nuovo la scalabilità automatica con la nuova formula. Ad esempio, se la scalabilità automatica è già abilitata su `myexistingpool` quando viene eseguito il codice .NET seguente, la relativa formula di scalabilità automatica viene sostituita con il contenuto di `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aggiornare l'intervallo di scalabilità automatica

Per aggiornare l'intervallo di valutazione della scalabilità automatica in un pool esistente abilitato per la scalabilità automatica, chiamare l'operazione per abilitare di nuovo la scalabilità automatica con il nuovo intervallo. Ad esempio, per impostare l'intervallo di valutazione della scalabilità automatica su 60 minuti per un pool già abilitato per la scalabilità automatica in .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Valutare la formula di scalabilità automatica

È possibile valutare la formula prima di applicarla a un pool. In questo modo è possibile testare i risultati della formula prima di inserirli nell'ambiente di produzione.

Prima di poter valutare una formula di scalabilità automatica, è necessario abilitare prima la scalabilità automatica nel pool con una formula valida, ad esempio la formula a una riga `$TargetDedicatedNodes = 0` . Usare quindi uno dei metodi seguenti per valutare la formula da testare:

- [BatchClient.PoolOperations.EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) o [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Questi metodi .NET Batch richiedono l'ID di un pool esistente e una stringa contenente la formula di scalabilità automatica da valutare.

- [Valutare la formula di scalabilità automatica](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    In questa richiesta di API REST specificare l'ID del pool nell'URI e la formula di scalabilità automatica nell'elemento *autoScaleFormula* del corpo della richiesta. La risposta dell'operazione contiene eventuali informazioni sugli errori che potrebbero essere correlate alla formula.

Questo esempio di [batch .NET](/dotnet/api/microsoft.azure.batch) valuta una formula di scalabilità automatica. Se il pool non usa ancora la scalabilità automatica, viene abilitata per prima.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

La valutazione corretta della formula in questo frammento di codice produce risultati simili ai seguenti:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Visualizzare informazioni sulle esecuzioni della scalabilità automatica

Per garantire che la formula funzioni come previsto, è consigliabile controllare periodicamente i risultati delle operazioni di scalabilità automatica eseguite da Batch sul pool. A tale scopo, ottenere o aggiornare un riferimento al pool, quindi esaminare le proprietà dell'ultima esecuzione della scalabilità automatica.

In .NET di Batch la proprietà [CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) presenta varie proprietà che forniscono informazioni sull'ultima esecuzione di scalabilità automatica sul pool:

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun.Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun.Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

Nell'API REST la richiesta [Ottenere informazioni su un pool](/rest/api/batchservice/get-information-about-a-pool) restituisce informazioni relative al pool, che includono l'ultima esecuzione della scalabilità automatica nella proprietà [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool).

Nell'esempio C# seguente viene usata la libreria batch .NET per stampare informazioni sull'ultima esecuzione della scalabilità automatica nel _pool_di pool.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Esempio di output dell'esempio precedente:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Formule di scalabilità automatica di esempio

Di seguito verranno esaminate alcune formule che mostrano diverse modalità per regolare la quantità di risorse di calcolo in un pool.

### <a name="example-1-time-based-adjustment"></a>Esempio 1: Adeguamento basato sul tempo

Si supponga di volte regolare le dimensioni del pool in base al giorno della settimana e all'ora del giorno. Questo esempio mostra come aumentare o ridurre il numero di nodi nel pool di conseguenza.

La formula ottiene prima di tutto l'ora corrente. Durante i giorni della settimana, da 1 a 5, e durante l'orario lavorativo, dalle 8.00 alle 18.00, le dimensioni del pool di destinazione sono impostate su 20 nodi. In caso contrario, vengono impostate su 10 nodi.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` può essere modificato in base al fuso orario locale con l'aggiunta di `time()` al prodotto di `TimeZoneInterval_Hour` e dell'offset UTC. Ad esempio, usare `$curTime = time() + (-6 * TimeInterval_Hour);` per l'ora legale del fuso occidentale (MDT). Tenere presente che l'offset deve essere regolato all'inizio e alla fine dell'ora legale, se applicabile.

### <a name="example-2-task-based-adjustment"></a>Esempio 2: Adeguamento basato sull'attività

In questo esempio C#, le dimensioni del pool vengono regolate in base al numero di attività nella coda. Sono stati inclusi sia i commenti che le interruzioni di riga nelle stringhe della formula.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Esempio 3: Considerazioni sulle attività parallele

Questo esempio C# regola le dimensioni del pool in base al numero di attività. Questa formula considera anche il valore [MaxTasksPerComputeNode](/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode) impostato per il pool. Questo approccio è utile nelle situazioni in cui l'[esecuzione di attività parallele](batch-parallel-node-tasks.md) è abilitata nel pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (theMaxTasksPerComputeNode property on this pool is set to 4, adjust
// this number for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Esempio 4: Impostazione delle dimensioni iniziali del pool

Questo esempio illustra un esempio C# con una formula di scalabilità automatica che imposta le dimensioni del pool su un numero specificato di nodi per un periodo di tempo iniziale. In seguito, le dimensioni del pool vengono regolate in base al numero di attività in esecuzione e attive.

In particolare, questa formula esegue le operazioni seguenti:

- Imposta le dimensioni iniziali del pool su 4 nodi.
- Non modifica le dimensioni del pool nei primi 10 minuti del relativo ciclo di vita.
- Dopo 10 minuti ottiene il valore massimo del numero di attività in esecuzione e attive negli ultimi 60 minuti.
  - Se entrambi i valori corrispondono a 0, ovvero nessuna attività era in esecuzione o attiva negli ultimi 60 minuti, le dimensioni del pool vengono impostate su 0.
  - Se uno dei valori è maggiore di zero, non viene apportata alcuna modifica.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [eseguire contemporaneamente più attività nei nodi di calcolo del pool](batch-parallel-node-tasks.md). Insieme alla scalabilità automatica, questo può aiutare a ridurre la durata dei processi per alcuni carichi di lavoro, risparmiando denaro.
- Informazioni su come [eseguire query sul servizio Azure batch in modo efficiente](batch-efficient-list-queries.md) per un'ulteriore efficienza.
