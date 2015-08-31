
<properties
	pageTitle="Scalabilità automatica dei nodi di calcolo in un pool di Azure Batch"
	description="La scalabilità automatica viene eseguita abilitandola in un pool e associando al pool una formula che viene usata per calcolare il numero di nodi di calcolo necessari per elaborare l'applicazione."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="08/05/2015"
	ms.author="davidmu"/>

# Scalabilità automatica dei nodi di calcolo in un pool di Azure Batch

La scalabilità automatica dei nodi di calcolo in un pool di Azure Batch è una regolazione dinamica della potenza di elaborazione usata dall'applicazione. Tale semplicità di regolazione consente di risparmiare tempo e denaro. Per altre informazioni sui nodi di calcolo e i pool, vedere [Panoramica tecnica di Azure Batch](batch-technical-overview.md).

La scalabilità automatica viene implementata quando è abilitata per un pool e al pool è associata una formula. La formula viene usata per determinare il numero di nodi di calcolo necessari per elaborare l'applicazione. La scalabilità automatica può essere impostata al momento della creazione di un pool oppure in seguito per un pool esistente. È inoltre possibile aggiornare la formula in un pool in cui è stata abilitata la scalabilità automatica.

Quando la scalabilità automatica è abilitata, il numero di nodi di calcolo disponibili viene adeguato ogni 15 minuti in base alla formula. La formula agisce su campioni raccolti periodicamente, ma esiste un ritardo tra il momento in cui viene raccolto un campione e il momento in cui è disponibile per la formula. Quando si usa il metodo GetSample descritto di seguito, è necessario tenere conto di ciò.

È sempre consigliabile valutare la formula prima di assegnarla a un pool ed è importante monitorare lo stato delle esecuzioni della scalabilità automatica.

> [AZURE.NOTE]Ogni account Azure Batch è limitato a un numero massimo di nodi di calcolo che può essere usato per l'elaborazione. Il sistema crea i nodi fino a tale limite e potrebbe non raggiungere i numeri previsti specificati da una formula.

## Definire la formula

La formula definita dall'utente viene usata per stabilire il numero di nodi di calcolo disponibili nel pool per il successivo intervallo di elaborazione. La formula è una stringa che non può superare gli 8 KB di dimensioni e può includere fino a 100 istruzioni separate da punti e virgola.

Le istruzioni in una formula sono espressioni in formato libero. Possono includere qualsiasi variabile definita dal sistema, variabili definite dall'utente, valori costanti e operazioni supportate su queste variabili o costanti:

	VAR = Function(System defined variables, user-defined variables);

È possibile combinare le variabili per creare formule complesse:

	VAR₀ = Function₀(system-defined variables);
	VAR₁ = Function₁(system-defined variables, VAR₀);

### Variabili

In una formula possono essere usate variabili definite dal sistema e variabili definite dall'utente. È possibile impostare il valore di queste variabili definite dal sistema per gestire i nodi di calcolo in un pool.

<table>
  <tr>
    <th>Variabile</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>Il numero di destinazione di nodi di calcolo dedicati per il pool. Il valore può essere modificato in base all'utilizzo effettivo per le attività.</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>L'azione che si verifica quando i nodi di calcolo vengono rimossi da un pool. I valori possibili sono:
      <br/>
      <ul>
        <li><p><b>requeue</b>: terminare immediatamente le attività e reinserirle nella coda dei processi in modo che vengano ripianificate.</p></li>
        <li><p><b>terminate</b>: terminare immediatamente le attività e rimuoverle dalla coda dei processi.</p></li>
        <li><p><b>taskcompletion</b>: attendere il completamento delle attività in esecuzione e quindi rimuovere il nodo dal pool.</p></li>
        <li><p><b>retaineddata</b>: attendere che tutti i dati mantenuti per le attività locali nel nodo vengano ripuliti prima di rimuovere il nodo dal pool.</p></li>
      </ul></td>
   </tr>
</table>

È possibile leggere solo i valori delle variabili definite dal sistema per apportare modifiche in base alle metriche dai nodi di calcolo nell'esempio.

<table>
  <tr>
    <th>Variabile</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>La percentuale media di utilizzo della CPU</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>Il numero di secondi utilizzato</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>Il numero medio di megabyte usati</td>
  <tr>
    <td>$DiskBytes</td>
    <td>Il numero medio di gigabyte usati nei dischi locali</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>Il numero di byte letti</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>Il numero di byte scritti</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>Il numero di operazioni di lettura del disco eseguite</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>Il numero di operazioni di scrittura su disco eseguite</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>Il numero di byte in ingresso</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>Il numero di byte in uscita</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>Il numero di nodi di calcolo</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>Il numero di attività che si trovano in uno stato attivo</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>Il numero di attività in esecuzione</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>Il numero di attività completate correttamente</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>Il numero di attività non riuscite</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>Il numero corrente di nodi di calcolo dedicati</td>
  </tr>
</table>

### Tipi

Questi tipi sono supportati in una formula:

- double
- doubleVec
- stringa
- timestamp. timestamp è una struttura composta che contiene i membri seguenti.
	- year
	- month (1-12)
	- day (1-31)
	- weekday (in formato numero. Ad esempio, 1 per lunedì)
	- hour (in formato 24 ore. Ad esempio, 13 significa 1 del pomeriggio)
	- minute (00-59)
	- second (00-59)
- timeInterval
	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

### Operazioni

Queste operazioni sono consentite sui tipi sopra elencati.

<table>
  <tr>
    <th>Operazione</th>
    <th>Operatori consentiti</th>
  </tr>
  <tr>
    <td>double &lt;operatore> double => double</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>double &lt;operatore> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operatore> double => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operatore> doubleVec => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operatore> double => timeinterval</td>
    <td>*, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operatore> timeinterval => timeinterval</td>
    <td>+, -</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operatore> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operatore> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operatore> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;operatore>double => double</td>
    <td>-, !</td>
  </tr>
  <tr>
    <td>&lt;operatore>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;operatore> double => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>string &lt;operatore> string => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timestamp &lt;operatore> timestamp => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operatore> timeinterval => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>double &lt;operatore> double => double</td>
    <td>&amp;&amp;, ||</td>
  </tr>
  <tr>
    <td>testare solo double (diverso da zero è true, zero è false)</td>
    <td>? :</td>
  </tr>
</table>

### Funzioni

Queste funzioni predefinite sono disponibili per definire una formula di scalabilità automatica.

<table>
  <tr>
    <th>Funzione</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td>double avg(doubleVecList)</td>
    <td>Il valore medio per tutti i valori in doubleVecList.</td>
  </tr>
  <tr>
    <td>double len(doubleVecList)</td>
    <td>La lunghezza del vettore creato da doubleVecList.</td>
  <tr>
    <td>double lg(double)</td>
    <td>Logaritmo in base 2.</td>
  </tr>
  <tr>
    <td>doubleVec lg(doubleVecList)</td>
    <td>Logaritmo in base 2 component-wise. È necessario passare in modo esplicito un vec(double) per singolo parametro double, in caso contrario verrà presupposta la versione double lg(double).</td>
  </tr>
  <tr>
    <td>double ln(double)</td>
    <td>Logaritmo naturale.</td>
  </tr>
  <tr>
    <td>doubleVec ln(doubleVecList)</td>
    <td>Logaritmo in base 2 component-wise. È necessario passare in modo esplicito un vec(double) per singolo parametro double, in caso contrario verrà presupposta la versione double lg(double).</td>
  </tr>
  <tr>
    <td>double log(double)</td>
    <td>Logaritmo in base 10.</td>
  </tr>
  <tr>
    <td>doubleVec log(doubleVecList)</td>
    <td>Logaritmo in base 10 component-wise. È necessario passare in modo esplicito un vec(double) per singolo parametro double, in caso contrario verrà presupposta la versione double log(double).</td>
  </tr>
  <tr>
    <td>double max(doubleVecList)</td>
    <td>Il valore massimo in doubleVecList.</td>
  </tr>
  <tr>
    <td>double min(doubleVecList)</td>
    <td>Il valore minimo in doubleVecList.</td>
  </tr>
  <tr>
    <td>double norm(doubleVecList)</td>
    <td>Norma 2 del vettore creato da doubleVecList.
  </tr>
  <tr>
    <td>double percentile(doubleVec v, double p)</td>
    <td>Elemento percentile del vettore v.</td>
  </tr>
  <tr>
    <td>double rand()</td>
    <td>Un valore casuale compreso tra 0,0 e 1,0.</td>
  </tr>
  <tr>
    <td>double range(doubleVecList)</td>
    <td>La differenza tra i valori minimo e massimo in doubleVecList.</td>
  </tr>
  <tr>
    <td>double std(doubleVecList)</td>
    <td>La deviazione standard del campione dei valori in doubleVecList.</td>
  </tr>
  <tr>
    <td>stop()</td>
    <td>Interrompere la valutazione delle espressioni per la scalabilità automatica.</td>
  </tr>
  <tr>
    <td>double sum(doubleVecList)</td>
    <td>La somma di tutti i componenti di doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp time(string dateTime="")</td>
    <td>Il timestamp dell'ora corrente se non vengono passati parametri o il timestamp della stringa dateTime passata. I formati dateTime supportati sono W3CDTF e RFC1123.</td>
  </tr>
  <tr>
    <td>double val(doubleVec v, double i)</td>
    <td>Il valore dell'elemento nella posizione i nel vettore v con un indice iniziale pari a zero.</td>
  </tr>
</table>

Alcune delle funzioni descritte nella tabella possono accettare un elenco come argomento. L'elenco delimitato da virgole è qualsiasi combinazione di double e doubleVec. Ad esempio:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

Il valore doubleVecList viene convertito in un singolo doubleVec prima della valutazione. Ad esempio, se v = [1,2,3], la chiamata di avg(v) equivale alla chiamata di avg(1,2,3) e la chiamata di avg(v, 7) equivale alla chiamata di avg(1,2,3,7).

### Dati di esempio

Le variabili definite dal sistema sono oggetti che forniscono metodi per accedere ai dati associati. Ad esempio, l'espressione seguente mostra una richiesta per recuperare gli ultimi 5 minuti di utilizzo della CPU:

	$CPUPercent.GetSample(TimeInterval_Minute*5)

Questi metodi possono essere usati per recuperare dati di esempio.

<table>
  <tr>
    <th>Metodo</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td>Count()</td>
    <td>Restituisce il numero totale di campioni nella cronologia dei dati di metrica.</td>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>Restituisce un vettore dei campioni di dati. Ad esempio:</p>
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>: specifica il numero di campioni richiesti dai campioni più recenti.</p>
				  <p>Un campione rappresenta 5 secondi di dati di metrica. GetSample(1) restituisce l'ultimo campione disponibile, ma per le metriche come $CPUPercent non è consigliabile usarlo perché non è possibile sapere quando è stato raccolto il campione. Potrebbe essere recente o a causa di problemi di sistema, potrebbe essere molto meno recente. È preferibile usare un intervallo di tempo, come illustrato di seguito.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>: specifica un intervallo di tempo per la raccolta di dati campione e facoltativamente specifica la percentuale di campioni che deve essere compresa nell'intervallo richiesto.</p>
          <p>$CPUPercent.GetSample(TimeInterval\_Minute*10) dovrebbe restituire 200 campioni se nella cronologia CPUPercent sono presenti tutti i campioni per gli ultimi dieci minuti. Se l'ultimo minuto di cronologia non è ancora presente, vengono restituiti solo 180 campioni.</p>
					<p>$CPUPercent.GetSample(TimeInterval\_Minute*10, 80) ha esisto positivo e $CPUPercent.GetSample(TimeInterval_Minute*10,95) ha esito negativo.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>: specifica un intervallo di tempo per la raccolta di dati con un'ora di inizio e un'ora di fine.</p></li></ul></td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Restituisce il periodo dei campioni prelevati in un set di dati campione cronologici.</td>
  </tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Restituisce il timestamp del campione di dati disponibile meno recente per la metrica.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Restituisce la percentuale di campioni attualmente disponibili in una cronologia per un determinato intervallo di tempo. Ad esempio:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>: poiché il metodo GetSample non riesce se la percentuale di campioni restituita è minore del valore samplePercent specificato, è possibile usare i metodi GetSamplePercent prima di tutto per controllare e poi eseguire un'azione alternativa se non sono presenti campioni sufficienti senza interrompere la valutazione per la scalabilità automatica.</p></td>
  </tr>
</table>

### Metrics

Queste metriche possono essere definite in una formula.

<table>
  <tr>
    <th>Metrica</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td>Risorsa</td>
    <td><p>Basata sull'utilizzo della CPU, l'utilizzo della larghezza di banda, l'utilizzo della memoria e il numero di nodi di calcolo. Queste variabili di sistema descritte in precedenza vengono usate nelle formule per gestire i nodi di calcolo in un pool:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>Queste variabili di sistema vengono usate per gli adattamenti in base alle metriche dei nodi:</p>
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
    <p>In questo esempio viene mostrata una formula che consente di impostare il numero di nodi di calcolo nel pool sul 110% del numero di nodi di destinazione corrente se l'utilizzo della CPU medio minimo negli ultimi 10 minuti è superiore al 70%:</p>
    <p><b>totalTVMs = (min($CPUPercent.GetSample(TimeInterval\_Minute*10)) > 0,7) ? ($CurrentDedicated * 1,1) : $CurrentDedicated;</b></p>
    <p>In questo esempio viene mostrata una formula che consente di impostare il numero di nodi di calcolo nel pool sul 90% del numero di destinazione corrente di nodi, se l'utilizzo medio della CPU negli ultimi 60 minuti è inferiore al 20%:</p>
    <p><b>totalTVMs = (avg($CPUPercent.GetSample(TimeInterval\_Minute*60)) &lt; 0,2) ? ($CurrentDedicated * 0,9) : totalTVMs;</b></p>
    <p>Questo esempio imposta il numero di nodi di calcolo dedicati di destinazione su un massimo di 400:</p>
    <p><b>$TargetDedicated = min(400, totalTVMs);</b></p></td>
  </tr>
  <tr>
    <td>Attività</td>
    <td><p>Basata sullo stato delle attività, ad esempio Attiva, In sospeso e Completata.</p>
    <p>Queste variabili di sistema vengono usate per gli adattamenti in base alle metriche delle attività:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p>
    <p>In questo esempio viene illustrata una formula che rileva se è stato registrato il 70% dei campioni negli ultimi 15 minuti. In caso contrario, viene usato l'ultimo campione. Tenta di aumentare il numero di nodi di calcolo in modo che corrisponda al numero di attività attive, con un massimo di 3. Imposta il numero di nodi su 1/4 del numero di attività attive perché la proprietà MaxTasksPerVM del pool è impostata su 4. Imposta inoltre l'opzione DeallocationOption su "taskcompletion" per mantenere la macchina fino alla fine dell'attività.</p>
    <p><b>$Samples = $ActiveTasks.GetSamplePercent(TimeInterval\_Minute * 15); $Tasks = $Samples &lt; 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval\_Minute * 15))); $Cores = $TargetDedicated * 4; $ExtraVMs = ($Tasks - $Cores) / 4; $TargetVMs = ($TargetDedicated+$ExtraVMs);$TargetDedicated = max(0,min($TargetVMs,3)); $TVMDeallocationOption = taskcompletion;</b></p></td>
  </tr>
</table>

## Valutare la formula di scalabilità automatica

È sempre consigliabile valutare una formula prima di usarla nell'applicazione. La formula viene valutata eseguendo un test su un pool esistente. A tale scopo usare uno dei metodi seguenti:

- [Metodo IPoolManager.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/dn931617.aspx) o [Metodo IPoolManager.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/dn931545.aspx): questi metodi .NET richiedono il nome di un pool esistente e la stringa che contiene la formula di scalabilità automatica. I risultati della chiamata sono contenuti in un'istanza della [classe AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Valutare una formula di scalabilità automatica](https://msdn.microsoft.com/library/azure/dn820183.aspx): in questa operazione REST, il nome del pool è specificato nell'URI e la formula di scalabilità automatica è specificata nell'elemento autoScaleFormula del corpo della richiesta. La risposta dell'operazione contiene eventuali informazioni sugli errori che potrebbero essere correlate alla formula.

## Creare un pool con la scalabilità automatica abilitata

Creare un pool in uno dei modi seguenti:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx): questo cmdlet usa il parametro AutoScaleFormula per specificare la formula di scalabilità automatica.
- [Metodo IPoolManager.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.createpool.aspx): dopo la chiamata di questo metodo .NET per creare un pool, la [proprietà ICloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleenabled.aspx) e la [proprietà ICloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleformula.aspx) vengono impostate nel pool per abilitare la scalabilità automatica.
- [Aggiungere un pool a un account](https://msdn.microsoft.com/library/azure/dn820174.aspx): gli elementi enableAutoScale e autoScaleFormula vengono usati in questa API REST per impostare la scalabilità automatica per il pool quando viene creato.

> [AZURE.NOTE]Se si imposta la scalabilità automatica quando il pool viene creato con le risorse sopra indicate, il parametro targetDedicated per il pool non viene usato.

## Abilitare la scalabilità automatica dopo la creazione di un pool

Se è già stato configurato un pool con un numero specificato di nodi di calcolo utilizzando il parametro targetDedicated, è possibile aggiornare il pool esistente in un secondo momento per la scalabilità automatica. Questa operazione può essere eseguita in uno dei modi seguenti:

- [Metodo IPoolManager.EnableAutoScale](https://msdn.microsoft.com/library/azure/dn931709.aspx): questo metodo .NET richiede il nome del pool esistente e la formula di scalabilità automatica.
- [Abilitare/disabilitare la scalabilità automatica](https://msdn.microsoft.com/library/azure/dn820173.aspx): questa API REST richiede il nome del pool esistente nell'URI e la formula di scalabilità automatica nel corpo della richiesta.

> [AZURE.NOTE]Il valore specificato per il parametro targetDedicated durante la creazione del pool viene ignorato quando viene valutata la formula di scalabilità automatica.

## Ottenere informazioni sulle esecuzioni della scalabilità automatica

È consigliabile controllare periodicamente i risultati delle esecuzioni della scalabilità automatica. Questa operazione può essere eseguita in uno dei modi seguenti:

- [Proprietà ICloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscalerun.aspx): quando si usa la libreria .NET, questa proprietà di un pool fornisce un'istanza della [classe AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx), che fornisce una [proprietà AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx), una [proprietà AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx) e una [proprietà AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx).
- [Recuperare informazioni su un pool](https://msdn.microsoft.com/library/dn820165.aspx): questa API REST restituisce informazioni relative al pool, che includono l'esecuzione più recente della scalabilità automatica.

## esempi

### Esempio 1.

Si desidera modificare le dimensioni del pool in base al tempo della settimana.

    curTime=time();
    workhours=curTime.hour>=8 && curTime.hour <18;
    isweekday=curTime.weekday>=1 && curTime.weekday<=5;
    isworkingweekdayhour = workhours && isweekday;
    $TargetDedicated=workhours?20:10;
    
Questa formula rileverà l'ora corrente. Se si tratta del giorno della settimana (1..5) e ore di lavoro (8 del mattino... 6 del mattino), la dimensione del pool di destinazione verrà impostata su 20. In caso contrario, le dimensioni del pool sono indirizzate al 10.

### Esempio 2.

Un altro esempio per la regolazione delle dimensioni del pool in base alle attività nella coda.

    // Get pending tasks for the past 15 minutes
    $Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15); 
    // If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of last sample point and the history average
    $Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
    // If number of pending task is not 0, set targetVM to pending tasks, otherwise half of current dedicated
    $TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
    // The pool size is capped at 20, if target vm value is more than that, set it to 20. This value should be adjusted according to your case.
    $TargetDedicated = max(0,min($TargetVMs,20));
    // optionally, set vm Deallocation mode - shrink VM after task is done.
    $TVMDeallocationOption = taskcompletion;
    

## Passaggi successivi

1.	Potrebbe essere necessario accedere al nodo di calcolo per poter valutare completamente l'efficienza dell'applicazione. Per sfruttare i vantaggi dell'accesso remoto, è necessario aggiungere un account utente al nodo di calcolo a cui si desidera accedere e recuperare un file RDP da tale nodo. Aggiungere l'account utente in uno dei modi seguenti:

	- [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx): questo cmdlet accetta il nome del pool, il nome del nodo di calcolo, il nome dell'account e la password come parametri.
	- [Metodo IVM.CreateUser](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.createuser.aspx): il metodo .NET crea un'istanza dell'[interfaccia IUser](https://msdn.microsoft.com/library/microsoft.azure.batch.iuser.aspx) in cui è possibile impostare il nome dell'account e la password per il nodo di calcolo.
	- [Aggiungere un account utente a un nodo](https://msdn.microsoft.com/library/dn820137.aspx): il nome del pool del nodo di calcolo vengono specificati nell'URI e il nome dell'account e la password vengono inviati al nodo nel corpo della richiesta di questa API REST.

		Recuperare il file RDP:

	- [Metodo IVM.GetRDPFile](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.getrdpfile.aspx): questo metodo .NET richiede il nome del file RDP da creare.
	- [Recuperare un file RDP (Remote Desktop Protocol) da un nodo](https://msdn.microsoft.com/library/dn820120.aspx): questa API REST richiede il nome del pool e il nome del nodo di calcolo. La risposta include il contenuto del file RDP.
	- [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx): questo cmdlet recupera il file RDP dal nodo di calcolo specificato e lo salva nel percorso di file specificato oppure in un flusso.
2.	Alcune applicazioni producono grandi quantità di dati che possono essere difficili da elaborare. L'esecuzione di [query di elenco efficienti](batch-efficient-list-queries.md) è uno dei modi per risolvere questa difficoltà.

<!---HONumber=August15_HO8-->