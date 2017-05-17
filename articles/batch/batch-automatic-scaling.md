---
title: Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch | Microsoft Docs
description: Abilitare il ridimensionamento automatico in un pool cloud per adeguare dinamicamente il numero di nodi di calcolo nel pool.
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 05/05/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 85a2fac77cf5beae6debad044df169301d43f4ca
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Creare una formula di scalabilità automatica per la scalabilità dei nodi di calcolo in un pool Batch

Con il ridimensionamento automatico, il servizio Azure Batch può aggiungere o rimuovere in modo dinamico i nodi di calcolo in un pool in base ai parametri definiti dall'utente. È possibile risparmiare tempo e denaro regolando automaticamente il numero dei nodi di calcolo usati dall'applicazione. La scalabilità automatica consente di aggiungere nodi man mano che le richieste di attività del processo aumentano e di rimuoverle quando si riducono.

Per abilitare la scalabilità automatica in un pool di nodi di calcolo, definire e associare al pool una *formula di scalabilità automatica*. In Batch .NET, ad esempio, è possibile usare il metodo [PoolOperations.EnableAutoScale][net_enableautoscale]. Il servizio Batch usa la formula di scalabilità automatica per determinare il numero di nodi di calcolo necessari per eseguire il carico di lavoro. Batch risponde ai dati delle metriche del servizio raccolti periodicamente. Usando i dati delle metriche, Batch regola il numero di nodi di calcolo nel pool in base alla formula e a un intervallo configurabile.

È possibile abilitare il ridimensionamento automatico al momento della creazione di un pool o in un pool esistente. Si può anche modificare una formula esistente in un pool abilitato per la "scalabilità automatica". Batch consente di valutare le formule prima di assegnarle ai pool e di monitorare lo stato delle esecuzioni della scalabilità automatica.

Questo articolo illustra le diverse entità che costituiranno le formule di scalabilità automatica, tra cui variabili, operatori, operazioni e funzioni. Si scoprirà come ottenere varie metriche relative ad attività e a risorse di calcolo all'interno di Batch. È possibile usare queste metriche adeguare in modo intelligente il numero di nodi del pool in base all'utilizzo delle risorse e allo stato delle attività. Verrà quindi illustrato come costruire una formula e abilitare il ridimensionamento automatico in un pool con le API Batch REST e Batch .NET, concludendo con alcune formule di esempio.

> [!IMPORTANT]
> Ogni account Azure Batch è limitato a un numero massimo di core, e quindi di nodi di calcolo, che può essere usato per l'elaborazione. Il servizio Batch crea nuovi nodi solo fino al raggiungimento del limite di core e può non raggiungere il numero di nodi di calcolo specificato da una formula di scalabilità automatica. Per informazioni su come visualizzare e aumentare le quote dell'account, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md) .
>
>

## <a name="automatic-scaling-formulas"></a>Formule di ridimensionamento automatico
Una formula di scalabilità automatica è un valore stringa definito che contiene una o più istruzioni. La formula di scalabilità automatica viene assegnata all'elemento [autoScaleFormula][rest_autoscaleformula] di un pool (Batch REST) o alla proprietà [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (Batch .NET). Il servizio Batch usa la formula per determinare il numero di nodi di calcolo di destinazione del pool per l'intervallo di elaborazione successivo. La stringa della formula, le cui dimensioni non possono superare 8 KB, può includere fino a 100 istruzioni separate da punti e virgola, nonché interruzioni di riga e commenti.

È possibile paragonare le formule di ridimensionamento automatico all'uso di un "linguaggio" di ridimensionamento automatico di Batch. Le istruzioni nella formula sono espressioni in formato libero che possono includere variabili definite dal servizio Batch e variabili definite dall'utente. Possono eseguire diverse operazioni su questi valori usando funzioni, operatori e tipi predefiniti. Ad esempio, un'istruzione può avere il formato seguente:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Le formule contengono in genere più istruzioni che eseguono operazioni su valori ottenuti nelle istruzioni precedenti. Ad esempio, si ottiene prima di tutto un valore per `variable1`, quindi il valore viene passato a una funzione per popolare `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Includere queste istruzioni nella formula di scalabilità automatica per arrivare a un numero di nodi di calcolo a cui deve essere ridimensionato il pool, ovvero il numero di **destinazione** dei **nodi dedicati**. Questo numero può essere maggiore, minore o uguale al numero attuale di nodi nel pool. Batch valuta la formula di scalabilità automatica del pool a intervalli specifici. Gli [intervalli di scalabilità automatica](#automatic-scaling-interval) sono illustrati di seguito. Batch regola quindi il numero di nodi di destinazione nel pool in base al numero specificato dalla formula di scalabilità automatica al momento della valutazione.

### <a name="sample-autoscale-formula"></a>Esempio di formula di scalabilità automatica

Di seguito è riportato un esempio di formula di scalabilità automatica che può essere adattato alla maggior parte degli scenari. Le variabili `startingNumberOfVMs` e `maxNumberofVMs` nell'esempio di formula possono essere modificate in base alle proprie esigenze.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs, pendingTaskSamples);
```

Con questa formula di scalabilità automatica il pool viene inizialmente creato con una singola macchina virtuale. La metrica $PendingTasks definisce il numero di attività in esecuzione o in coda. La formula trova il numero medio di attività in sospeso negli ultimi 180 secondi e imposta TargetDedicated di conseguenza. La formula garantisce che TargetDedicated non superi mai 25 macchine virtuali. Man mano che vengono inviate nuove attività, il pool si espande automaticamente. Al termine delle attività le macchine virtuali diventano libere una ad una e la formula di scalabilità automatica riduce il pool.

## <a name="variables"></a>variables
Nelle formule di scalabilità automatica è possibile usare sia **variabili definite dal servizio** sia **variabili definite dall'utente**. Le variabili definite dal servizio sono incorporate nel servizio Batch, alcune sono in lettura/scrittura e altre di sola lettura. Le variabili definite dall'utente vengono configurate dall' *utente* . Nella formula di esempio della sezione precedente `$TargetDedicated` e `$PendingTasks` sono variabili definite dal servizio. Le variabili `startingNumberOfVMs` e `maxNumberofVMs` sono definite dall'utente.

> [!NOTE]
> Le variabili definite dal servizio sono sempre precedute da un segno di dollaro ($). Per le variabili definite dall'utente il segno di dollaro è facoltativo.
>
>

Le tabelle seguenti includono variabili di lettura/scrittura e di sola lettura definite dal servizio Batch.

È possibile **ottenere** e **impostare** i valori di queste variabili definite dal servizio per gestire il numero di nodi di calcolo in un pool:

| Variabili in lettura/scrittura definite dal servizio | Descrizione |
| --- | --- |
| $TargetDedicated |Il numero di **destinazione** dei **nodi di calcolo dedicati** per il pool è il numero di nodi di calcolo a cui deve essere ridimensionato il pool. È un numero di "destinazione", perché è possibile che un pool non raggiunga il numero di nodi di destinazione. Ad esempio, se il numero di nodi di destinazione viene modificato di nuovo da una valutazione di scalabilità automatica successiva prima che il pool raggiunga la destinazione iniziale, è possibile che il pool non raggiunga il numero di nodi di destinazione. Può verificarsi anche se è stata raggiunta la quota di nodi o di core di un account Batch prima che venga raggiunto il numero di nodi di destinazione. |
| $NodeDeallocationOption |L'azione che si verifica quando i nodi di calcolo vengono rimossi da un pool. I valori possibili sono:<ul><li>**requeue**: termina immediatamente le attività e le reinserisce nella coda dei processi in modo che vengano ripianificate.<li>**terminate**: termina immediatamente le attività e le rimuove dalla coda dei processi.<li>**taskcompletion**: attende il completamento delle attività in esecuzione e quindi rimuove il nodo dal pool.<li>**retaineddata**: attende che tutti i dati mantenuti per le attività locali nel nodo vengano ripuliti prima di rimuovere il nodo dal pool.</ul> |

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
| $PreemptedNodeCount | Il numero di nodi nel pool che si trovano nello stato Annullato. |

> [!TIP]
> Le variabili di sola lettura definite dal servizio illustrate sopra sono *oggetti* che offrono vari metodi per accedere ai dati associati a ognuno. Per altre informazioni, vedere [Ottenere dati di esempio](#getsampledata) di seguito.
>
>

## <a name="types"></a>Types
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

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Operazioni
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
| timestamp *operatore* timestamp |- |timeInterval |
| *operatoree*double |-, ! |double |
| *operatoree*timeInterval |- |timeInterval |
| double *operatore* double |<, <=, ==, >=, >, != |double |
| string *operatore* string |<, <=, ==, >=, >, != |double |
| timestamp *operatore* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operatore* timeinterval |<, <=, ==, >=, >, != |double |
| double *operatore* double |&&, &#124;&#124; |double |

Durante il test di un valore double con un operatore ternario (`double ? statement1 : statement2`), diverso da zero è **true** e zero è **false**.

## <a name="functions"></a>Funzioni
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

Alcune delle funzioni descritte nella tabella precedente possono accettare un elenco come argomento. L'elenco con valori delimitati da virgole è una combinazione qualsiasi di *double* e *doubleVec*. Ad esempio:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Il valore *doubleVecList* viene convertito in un singolo *doubleVec* prima della valutazione. Ad esempio, se `v = [1,2,3]`, la chiamata di `avg(v)` equivale alla chiamata di `avg(1,2,3)`. La chiamata di `avg(v, 7)` equivale alla chiamata di `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Ottenere dati di esempio
Le formule di ridimensionamento automatico agiscono sui dati di metrica (campioni) forniti dal servizio Batch. Una formula aumenta o riduce le dimensioni del pool in base ai valori che ottiene dal servizio. Le variabili definite dal servizio descritte sopra sono oggetti che offrono vari metodi per accedere ai dati associati a un dato oggetto. Ad esempio, l'espressione seguente mostra una richiesta per recuperare gli ultimi 5 minuti di utilizzo della CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metodo | Descrizione |
| --- | --- |
| GetSample() |Il metodo `GetSample()` restituisce un vettore relativo ai campioni di dati.<br/><br/>Un campione rappresenta 30 secondi di dati di metrica. In altre parole i campioni vengono raccolti ogni 30 secondi, ma come indicato di seguito si verifica un ritardo tra il momento in cui un campione viene raccolto e il momento in cui è disponibile per una formula. Di conseguenza, i campioni per un determinato periodo di tempo potrebbero non essere tutti disponibili per la valutazione da parte di una formula.<ul><li>`doubleVec GetSample(double count)`<br/>Specifica il numero di campioni da ottenere tra quelli raccolti più di recente.<br/><br/>`GetSample(1)` restituisce l'ultimo campione disponibile. Per le metriche come `$CPUPercent` non deve tuttavia essere usato perché non è possibile sapere *quando* è stato raccolto il campione. Potrebbe essere recente o risultare molto meno recente a causa di problemi di sistema. In questi casi è preferibile usare un intervallo di tempo, come illustrato di seguito.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Specifica un intervallo di tempo per la raccolta di dati di esempio. Facoltativamente specifica anche la percentuale di campioni che deve essere disponibile nell'intervallo di tempo richiesto.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` restituisce 20 campioni se nella cronologia CPUPercent sono presenti tutti i campioni per gli ultimi dieci minuti. Se l'ultimo minuto della cronologia non è disponibile, vengono tuttavia restituiti solo 18 campioni. In questo caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` avrà esito negativo poiché è disponibile solo il 90% dei campioni.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` avrà esito positivo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Specifica un intervallo di tempo per la raccolta dei dati, con un'ora di inizio e un'ora di fine.<br/><br/>Come indicato in precedenza, si verifica un ritardo tra il momento in cui un campione viene raccolto e il momento in cui è disponibile per una formula. È necessario tenere presente questo particolare quando si usa il metodo `GetSample`. Vedere `GetSamplePercent` di seguito. |
| GetSamplePeriod() |Restituisce il periodo dei campioni raccolti in un set di dati campione cronologici. |
| Count() |Restituisce il numero totale di campioni nella cronologia dei dati di metrica. |
| HistoryBeginTime() |Restituisce il timestamp del campione di dati disponibile meno recente per la metrica. |
| GetSamplePercent() |Restituisce la percentuale di campioni disponibili per un determinato intervallo di tempo. Ad esempio:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Poiché il metodo `GetSample` non riesce se la percentuale di campioni restituiti è minore del valore `samplePercent` specificato, è possibile eseguire prima il controllo con il metodo `GetSamplePercent`. È quindi possibile eseguire un'azione alternativa se non sono presenti campioni sufficienti, senza interrompere la valutazione del ridimensionamento automatico. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Campioni, percentuale di campioni e metodo *GetSample()*
L'operazione di base per il funzionamento di una formula di ridimensionamento automatico consiste nell'ottenere i dati di metrica relativi a risorse ed attività e quindi l'adeguamento delle dimensioni del pool in base a tali dati. Di conseguenza, è importante conoscere a fondo la modalità di interazione delle formule di ridimensionamento automatico con i dati di metrica o "campioni".

**Esempi**

Il servizio Batch acquisisce periodicamente *campioni* di metriche relative a risorse ed attività e li rende disponibili per le formule di scalabilità automatica. Questi campioni vengono registrati ogni 30 secondi dal servizio Batch. In genere si verifica una certa latenza che causa un ritardo tra il momento in cui i campioni sono stati registrati e il momento in cui vengono resi disponibili e possono essere letti dalle formule di ridimensionamento automatico. Inoltre, a causa di vari fattori, ad esempio problemi di rete o dell'infrastruttura, è possibile che i campioni non siano stati registrati per un particolare intervallo, determinando una condizione di campioni "mancanti".

**Percentuale di campioni**

Quando si passa un oggetto `samplePercent` al metodo `GetSample()` o si chiama il metodo `GetSamplePercent()`, il termine "percentuale" si riferisce a un confronto tra il *possibile* numero totale di campioni registrati dal servizio Batch e il numero di campioni effettivamente *disponibili* per la formula di scalabilità automatica.

Come esempio, si esaminerà un intervallo di tempo di 10 minuti. Poiché i campioni vengono registrati ogni 30 secondi, in un intervallo di tempo di 10 minuti il numero massimo totale di campioni registrati da Batch sarà di 20 campioni (2 al minuto). Tuttavia, a causa della latenza intrinseca del meccanismo di creazione di report o di un altro problema all'interno dell'infrastruttura di Azure, potrebbero essere disponibili solo 15 campioni per la lettura da parte della formula di ridimensionamento automatico. Ciò significa che per il periodo di 10 minuti solo il **75%** del numero totale di campioni registrati è effettivamente disponibile per la formula.

**GetSample() e intervalli di campioni**

Le formule di ridimensionamento automatico aumentano e riducono i pool, aggiungendovi nodi o rimuovendoli. Poiché i nodi hanno un costo, si vuole garantire che le formule applichino decisioni intelligenti sulla base di dati sufficienti. È quindi consigliabile usare un tipo di analisi delle tendenze nelle formule, aumentando e riducendo i pool in base a un *intervallo* di campioni raccolti.

A tale scopo, usare `GetSample(interval look-back start, interval look-back end)` per restituire un **vettore** di campioni:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando la riga precedente viene valutata da Batch, restituisce un intervallo di campioni come vettore di valori. Ad esempio:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Dopo aver raccolto il vettore di campioni, è possibile usare funzioni come `min()`, `max()` e `avg()` per derivare valori significativi dall'intervallo raccolto.

Per maggiore sicurezza, è possibile fare in modo che la valutazione di una formula *non riesca* se per un determinato periodo di tempo è disponibile una quantità di campioni inferiore a una certa percentuale. L'impostazione di un esito negativo della valutazione della formula indica a Batch di interromperne l'ulteriore valutazione se la percentuale di campioni specificata non è disponibile. In questo caso non viene apportata alcuna modifica alla dimensione del pool. Per specificare una percentuale di campioni obbligatoria perché la valutazione riesca, specificarla come terzo parametro in `GetSample()`. Qui è specificato un requisito pari al 75% dei campioni:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Poiché si può verificare un ritardo nella disponibilità dei campioni, è importante specificare sempre un intervallo di tempo con un'ora di inizio antecedente di almeno un minuto. La propagazione dei campioni attraverso il sistema richiede circa un minuto, quindi i campioni nell'intervallo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` possono non essere disponibili. Anche in questo caso, è possibile usare il parametro percentuale di `GetSample()` per imporre uno specifico requisito di percentuale dei campioni.

> [!IMPORTANT]
> È **consigliabile** **evitare di basarsi *solo* su `GetSample(1)` nelle formule di scalabilità automatica**. `GetSample(1)` indica infatti essenzialmente al servizio Batch di restituire l'ultimo campione disponibile, indipendentemente da quanto tempo prima è stato recuperato. Essendo solo un singolo campione, che potrebbe anche non essere recente, potrebbe non essere rappresentativo dell'immagine più ampia dello stato recente di attività o risorse. Se si usa `GetSample(1)`, accertarsi che faccia parte di un'istruzione di dimensioni maggiori e non sia il solo punto dati su cui si basa la formula.
>
>

## <a name="metrics"></a>Metrica
Quando si definisce una formula, è possibile usare metriche di **risorse** e di **attività**. Adeguare il numero di destinazione di nodi dedicati nel pool in base ai dati di metrica ottenuti e valutati. Per altre informazioni su ogni metrica, vedere la sezione [Variabili](#variables) precedente.

<table>
  <tr>
    <th>Metrica</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td><b>Risorsa</b></td>
    <td><p>La <b>metrica delle risorse</b> si basa sull'uso della memoria, della CPU e della larghezza di banda dei nodi di calcolo, nonché sul numero di nodi.</p>
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

## <a name="write-an-autoscale-formula"></a>Scrivere una formula di scalabilità automatica
La compilazione di una formula di scalabilità automatica prevede la composizione di istruzioni che usano i componenti precedenti e quindi la combinazione di tali istruzioni in una formula completa. In questa sezione si creerà una formula di scalabilità automatica di esempio che può assumere alcune decisioni di scalabilità in scenari del mondo reale.

In primo luogo, definire i requisiti per la nuova formula di scalabilità automatica. La formula deve:

1. **Aumentare** il numero di destinazione dei nodi di calcolo in un pool se l'uso della CPU è elevato.
2. **Ridurre** il numero di destinazione dei nodi di calcolo in un pool se l'uso della CPU è basso.
3. Limitare sempre il numero **massimo** di nodi a 400.

Per l'*aumento* del numero di nodi durante l'uso elevato della CPU, viene definita un'istruzione che popola una variabile definita dall'utente (`$totalNodes`) con un valore pari al 110% dell'attuale numero di destinazione dei nodi, ma solo se l'uso minimo medio della CPU negli ultimi 10 minuti è superiore al 70%. In caso contrario, viene usato il valore dedicato corrente.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Per la *riduzione* del numero di nodi durante l'uso non elevato della CPU, l'istruzione successiva nella formula imposta la stessa variabile `$totalNodes` sul 90% dell'attuale numero di destinazione dei nodi, se l'uso medio della CPU negli ultimi 60 minuti è stato inferiore al 20%. In caso contrario, usare il valore corrente di `$totalNodes`, popolato nell'istruzione precedente.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Ora viene limitato il numero di destinazione dei nodi di calcolo dedicati a un **massimo** di 400:

```
$TargetDedicated = min(400, $totalNodes)
```

Ecco la formula completa:

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Creare un pool abilitato per la scalabilità automatica
Per creare un nuovo pool abilitato per la scalabilità automatica, è possibile usare una delle tecniche seguenti:

**Batch .NET**

1. Creare il pool con [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
2. Impostare la proprietà [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) su `true`.
3. Impostare la proprietà [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) con la formula di scalabilità automatica.
4. (Facoltativo) Impostare la proprietà [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (valore predefinito è 15 minuti).
5. Eseguire il commit del pool con [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) o [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**API Batch REST**

* [Aggiungere un pool a un account](https://msdn.microsoft.com/library/azure/dn820174.aspx): specificare gli elementi `enableAutoScale` e `autoScaleFormula` nella richiesta di API REST per configurare la scalabilità automatica alla creazione di un pool.

Il frammento di codice seguente crea un pool abilitato per la scalabilità automatica usando la libreria [Batch .NET][net_api]. La formula di scalabilità automatica del pool imposta il numero di destinazione dei nodi su 5 il lunedì e su 1 per tutti gli altri giorni della settimana. L'[intervallo di scalabilità automatica](#automatic-scaling-interval) è impostato su 30 minuti. In questo e in altri frammenti di codice C# in questo articolo, "myBatchClient" è un'istanza correttamente inizializzata di [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Oltre alle API REST di Batch e l'SDK .NET, è possibile usare qualsiasi altro [SDK Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [cmdlet PowerShell di Batch](batch-powershell-cmdlets-get-started.md) e [interfaccia della riga di comando di Batch](batch-cli-get-started.md) da usare per la scalabilità automatica.

> [!IMPORTANT]
> Quando si crea un pool abilitato per la scalabilità automatica, **non** specificare il parametro `targetDedicated`. Si noti anche che per ridimensionare manualmente un pool abilitato per la scalabilità automatica, ad esempio con [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool], è necessario **disabilitare** prima la scalabilità automatica nel pool e quindi ridimensionarlo.
>
>

### <a name="automatic-scaling-interval"></a>Intervallo di ridimensionamento automatico
Per impostazione predefinita, il servizio Batch adegua le dimensioni di un pool in base alla relativa formula di ridimensionamento automatico ogni **15 minuti**. Questo intervallo è tuttavia configurabile usando le proprietà del pool seguenti:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (API REST)

L'intervallo minimo è di 5 minuti e il massimo di 168 ore. Se viene specificato un intervallo che non rientra in questi valori, il servizio Batch restituisce un errore di richiesta non valida (400).

> [!NOTE]
> La funzionalità di ridimensionamento automatico non è attualmente concepita come risposta alle modifiche in meno di un minuto, ma piuttosto per l'adeguamento graduale delle dimensioni del pool durante l'esecuzione di un carico di lavoro.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Abilitare la scalabilità automatica in un pool esistente
Se è già stato creato un pool con un numero di set di nodi di calcolo usando il parametro *targetDedicated*, è comunque possibile attivare la scalabilità del pool. Ogni SDK di Batch fornisce un'operazione di abilitazione della scalabilità automatica, ad esempio:

* [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale] (Batch .NET)
* [Abilitare la scalabilità automatica in un pool][rest_enableautoscale] (API REST)

Quando si abilita la scalabilità automatica in un pool esistente, si applica quanto segue:

* Se la scalabilità automatica è attualmente **disabilitata** nel pool quando si esegue la richiesta per l'abilitazione, è *necessario* specificare una formula di scalabilità automatica valida quando si esegue la richiesta. *Facoltativamente*, è possibile specificare un intervallo di valutazione della scalabilità automatica. Se non si specifica un intervallo, viene applicato il valore predefinito, pari a 15 minuti.
* Se la scalabilità automatica è attualmente **abilitata** nel pool, è possibile specificare una formula di scalabilità automatica, un intervallo di valutazione o entrambi. Non è possibile omettere entrambe le proprietà.

  * Se si specifica un nuovo intervallo per la valutazione della scalabilità automatica, la pianificazione esistente per la valutazione viene arrestata e viene avviata una nuova pianificazione. L'ora di inizio della nuova pianificazione corrisponde al momento in cui è stata inviata la richiesta di abilitazione della scalabilità automatica.
  * Se si omette la formula di scalabilità automatica o l'intervallo di valutazione, il servizio Batch continuerà a usare il valore corrente.

> [!NOTE]
> Se è stato specificato un valore per il parametro *targetDedicated* durante la creazione del pool, questo verrà ignorato quando viene valutata la formula di ridimensionamento automatico.
>
>

Questo frammento di codice C# usa la libreria [Batch .NET][net_api] per abilitare la scalabilità automatica in un pool esistente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aggiornare una formula di scalabilità automatica
Usare la stessa richiesta di abilitazione della scalabilità automatica per *aggiornare* la formula in un pool esistente abilitato per la scalabilità automatica, ad esempio con [EnableAutoScale][net_enableautoscale] in Batch .NET. Non è prevista alcuna operazione speciale per l'aggiornamento della scalabilità automatica. Ad esempio, se la scalabilità automatica è già abilitata su "myexistingpool" quando viene eseguito il codice seguente, la relativa formula di scalabilità automatica viene sostituita con il contenuto di `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aggiornare l'intervallo di scalabilità automatica
Come per l'aggiornamento di una formula di scalabilità automatica, usare il metodo [EnableAutoScale][net_enableautoscale] per modificare l'intervallo di valutazione della scalabilità automatica di un pool esistente abilitato per la scalabilità automatica. Ad esempio, per impostare l'intervallo di valutazione della scalabilità automatica su 60 minuti per un pool già abilitato per la scalabilità automatica:

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Valutare la formula di scalabilità automatica
È possibile valutare la formula prima di applicarla a un pool. In questo modo, è possibile eseguire un "test" della formula per vedere in che modo le relative istruzioni vengono valutate prima di inserire la formula nell'ambiente di produzione.

Per valutare una formula di scalabilità automatica, è necessario avere abilitato prima la **scalabilità automatica** del pool con una **formula valida**. Se si desidera testare una formula in un pool che non dispone ancora di scalabilità automatica abilitata, è possibile usare la formula `$TargetDedicated = 0` a una riga quando si abilita per la prima volta la scalabilità automatica. Usare quindi uno dei metodi seguenti per valutare la formula da testare:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) o [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    Questi metodi .NET Batch richiedono l'ID di un pool esistente e una stringa contenente la formula di scalabilità automatica da valutare. I risultati della valutazione sono contenuti nell'istanza [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) restituita.
* [Valutare la formula di scalabilità automatica](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    In questa richiesta di API REST specificare l'ID del pool nell'URI e la formula di scalabilità automatica nell'elemento *autoScaleFormula* del corpo della richiesta. La risposta dell'operazione contiene eventuali informazioni sugli errori che potrebbero essere correlate alla formula.

Questo frammento di codice [Batch .NET][net_api] valuta una formula prima che sia applicata a [CloudPool][net_cloudpool]. Se il pool non dispone di scalabilità automatica abilitata, è necessario abilitarla prima.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
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
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

La valutazione corretta della formula in questo frammento di codice genererà un output simile al seguente:

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Visualizzare informazioni sulle esecuzioni della scalabilità automatica
Per garantire che la formula funzioni come previsto, è consigliabile controllare periodicamente i risultati di "runs" della scalabilità automatica che Batch esegue sul pool. A tale scopo, ottenere o aggiornare un riferimento al pool ed esaminare le proprietà dell'ultima esecuzione di scalabilità automatica.

In .NET di Batch la proprietà [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) presenta varie proprietà che forniscono informazioni sull'ultima esecuzione di scalabilità automatica sul pool da parte del servizio Batch.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

Nell'API REST la richiesta [Ottenere informazioni su un pool](https://msdn.microsoft.com/library/dn820165.aspx) restituisce informazioni relative al pool, che includono l'ultima esecuzione della scalabilità automatica in [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun).

Il frammento di codice C# seguente usa la libreria .NET di Batch per stampare informazioni sull'ultima esecuzione della scalabilità automatica nel pool "myPool":

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Esempio di output del frammento precedente:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Formule di scalabilità automatica di esempio
Esaminiamo alcune formule che mostrano diverse modalità per regolare la quantità di risorse di calcolo in un pool.

### <a name="example-1-time-based-adjustment"></a>Esempio 1: Adeguamento basato sul tempo
Si vogliono regolare le dimensioni del pool in base al giorno della settimana e all'ora del giorno, aumentando o riducendo di conseguenza il numero di nodi nel pool.

Questa formula ottiene l'ora corrente. Durante i giorni della settimana, da 1 a 5, e durante l'orario lavorativo, dalle 8.00 alle 18.00, le dimensioni del pool di destinazione sono impostate su 20 nodi. In caso contrario, vengono impostate su 10 nodi.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Esempio 2: Adeguamento basato sulle attività
In questo esempio le dimensioni del pool vengono regolate in base al numero di attività nella coda. Si noti che sia i commenti che le interruzioni di riga sono accettabili nelle stringhe della formula.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Esempio 3: Considerazioni sulle attività parallele
Questo è un altro esempio che adegua le dimensioni del pool in base al numero di attività. Questa formula considera anche il valore [MaxTasksPerComputeNode][net_maxtasks] impostato per il pool. Questa operazione è particolarmente utile nelle situazioni in cui l' [esecuzione di attività parallele](batch-parallel-node-tasks.md) è abilitata nel pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Esempio 4: Impostazione di dimensioni iniziali del pool
Questo esempio mostra un segmento di codice C# con una formula di ridimensionamento automatico che imposta le dimensioni del pool su un certo numero di nodi per un periodo di tempo iniziale. Adegua quindi le dimensioni del pool in base al numero di attività in esecuzione e attive dopo la scadenza del periodo di tempo iniziale.

La formula nel frammento di codice seguente:

* Imposta le dimensioni iniziali del pool su 4 nodi.
* Non modifica le dimensioni del pool nei primi 10 minuti del relativo ciclo di vita.
* Dopo 10 minuti ottiene il valore massimo del numero di attività in esecuzione e attive negli ultimi 60 minuti.
  * Se entrambi i valori corrispondono a 0, ovvero nessuna attività era in esecuzione o attiva negli ultimi 60 minuti, le dimensioni del pool vengono impostate su 0.
  * Se uno dei valori è maggiore di zero, non viene apportata alcuna modifica.

```csharp
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

## <a name="next-steps"></a>Passaggi successivi
* [Ottimizzare l'uso delle risorse di calcolo di Azure Batch con attività dei nodi simultanee](batch-parallel-node-tasks.md) contiene informazioni dettagliate su come è possibile eseguire più attività contemporaneamente sui nodi di calcolo nel pool. Oltre al ridimensionamento automatico, questa funzionalità può contribuire a ridurre la durata del processo per alcuni carichi di lavoro, riducendo i costi.
* Per ottimizzare ulteriormente l'efficienza, assicurarsi che l'applicazione Batch esegua query sul servizio Batch in modo ottimale. In [Eseguire query sul servizio Azure Batch in modo efficiente](batch-efficient-list-queries.md)si apprenderà come limitare la quantità dei dati trasmessi in rete quando si esegue una query sullo stato di migliaia di nodi di calcolo o attività.

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
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

