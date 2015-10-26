
<properties
	pageTitle="Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch | Microsoft Azure"
	description="Abilitare il ridimensionamento automatico in un pool cloud per regolare dinamicamente il numero di nodi di calcolo nel pool."
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
	ms.date="08/26/2015"
	ms.author="davidmu"/>

# Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch

Il ridimensionamento automatico dei nodi di calcolo in un pool di Azure Batch è una regolazione dinamica della potenza di elaborazione usata dall'applicazione. Tale semplicità di regolazione consente di risparmiare tempo e denaro. Per altre informazioni sui nodi di calcolo e i pool, vedere [Panoramica tecnica di Azure Batch](batch-technical-overview.md).

Il ridimensionamento automatico viene applicato quando è abilitato per un pool e al pool è associata una formula. La formula viene usata per determinare il numero di nodi di calcolo necessari per elaborare l'applicazione. Agisce sui campioni raccolti periodicamente e il numero dei nodi di calcolo disponibili nel pool viene regolato ogni 15 minuti in base alla formula associata.

È possibile impostare il ridimensionamento automatico al momento della creazione di un pool oppure in seguito per un pool esistente. È anche possibile aggiornare la formula in un pool in cui il ridimensionamento automatico è stato abilitato in precedenza. È sempre consigliabile valutare la formula prima di assegnarla a un pool ed è importante monitorare lo stato delle esecuzioni del ridimensionamento automatico. Questi argomenti verranno illustrati più avanti nel documento.

> [AZURE.NOTE]Ogni account Azure Batch è limitato a un numero massimo di nodi di calcolo che può essere usato per l'elaborazione. Il sistema creerà i nodi solo fino a tale limite e potrebbe non raggiungere i numeri previsti specificati da una formula.

## Ridimensionare automaticamente le risorse di calcolo

La formula di ridimensionamento definita determina il numero di nodi di calcolo disponibili nel pool per l'intervallo di elaborazione successivo. Una formula di ridimensionamento automatico è semplicemente un valore stringa assegnato all'elemento [autoScaleFormula](https://msdn.microsoft.com/library/azure/dn820173.aspx) di un pool nel corpo della richiesta (API REST) o nella proprietà [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) (API .NET). La stringa della formula non può superare gli 8 KB di dimensioni e può includere fino a 100 istruzioni separate da punti e virgola, nonché interruzioni di riga e commenti.

Le istruzioni in una formula sono espressioni in formato libero. Possono includere qualsiasi variabile definita dal sistema, variabili definite dall'utente, valori costanti e operazioni supportate su queste variabili o costanti.

	VAR = Expression(system-defined variables, user-defined variables);

È possibile creare formule complesse usando più istruzioni e variabili:

	VAR₀ = Expression₀(system-defined variables);
	VAR₁ = Expression₁(system-defined variables, VAR₀);

> [AZURE.NOTE]Una formula di ridimensionamento automatico è composta da variabili, tipi, operazioni e funzioni dell'API [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx). Questi elementi sono usati nelle stringhe della formula anche quando si usa la libreria [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx).

### Variabili

In una formula possono essere usate variabili definite dal sistema e variabili definite dall'utente.

È possibile *ottenere* e *impostare* il valore di queste **variabili definite dal sistema** per gestire i nodi di calcolo in un pool.

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

È possibile *ottenere* i valori delle **variabili definite dal sistema** per apportare modifiche in base alle metriche dai nodi di calcolo nei campioni. Queste variabili sono di sola lettura.

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

Questi sono i **tipi** supportati in una formula:

- double
- doubleVec
- string
- timestamp. timestamp è una struttura composta che contiene i membri seguenti:
	- year
	- month (1-12)
	- day (1-31)
	- weekday (in formato numero, ad esempio 1 per lunedì)
	- hour (in formato 24 ore, ad esempio 13 per indicare le ore 13.00)
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

Queste sono le **operazioni** consentite sui tipi sopra elencati.

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

### Functions

Queste **funzioni** predefinite sono disponibili per definire una formula di ridimensionamento automatico.

<table>
  <tr>
    <th>Funzione</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>Il valore medio per tutti i valori in doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>La lunghezza del vettore creato da doubleVecList.</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>Logaritmo in base 2.</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>Logaritmo in base 2 component-wise. È necessario passare in modo esplicito un vec(double) per singolo parametro double, in caso contrario verrà presupposta la versione double lg(double).</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>Logaritmo naturale.</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>Logaritmo in base 2 component-wise. È necessario passare in modo esplicito un vec(double) per singolo parametro double, in caso contrario verrà presupposta la versione double lg(double).</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>Logaritmo in base 10.</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>Logaritmo in base 10 component-wise. È necessario passare in modo esplicito un vec(double) per singolo parametro double, in caso contrario verrà presupposta la versione double log(double).</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>Il valore massimo in doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>Il valore minimo in doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>Norma 2 del vettore creato da doubleVecList.
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>Elemento percentile del vettore v.</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>Un valore casuale compreso tra 0,0 e 1,0.</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>La differenza tra i valori minimo e massimo in doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>La deviazione standard del campione dei valori in doubleVecList.</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>Arrestare la valutazione delle espressioni per il ridimensionamento automatico.</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>La somma di tutti i componenti di doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>Il timestamp dell'ora corrente se non vengono passati parametri o il timestamp della stringa dateTime passata. I formati dateTime supportati sono W3CDTF e RFC1123.</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>Il valore dell'elemento nella posizione i nel vettore v con un indice iniziale pari a zero.</td>
  </tr>
</table>

Alcune delle funzioni descritte nella tabella precedente possono accettare un elenco come argomento. L'elenco delimitato da virgole è qualsiasi combinazione di *double* e *doubleVec*. Ad esempio:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

Il valore *doubleVecList* viene convertito in un singolo valore *doubleVec* prima della valutazione. Ad esempio, se v = [1,2,3], la chiamata di avg(v) equivale alla chiamata di avg(1,2,3) e la chiamata di avg(v, 7) equivale alla chiamata di avg(1,2,3,7).

### Ottenere i dati dei campioni

Le variabili definite dal sistema descritte in precedenza sono oggetti che forniscono metodi per accedere ai dati associati. Ad esempio, l'espressione seguente mostra una richiesta per recuperare gli ultimi 5 minuti di utilizzo della CPU:

	$CPUPercent.GetSample(TimeInterval_Minute*5)

Questi metodi possono essere usati per ottenere i dati campione.

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
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>: specifica un intervallo di tempo per la raccolta di dati con un'ora di inizio e un'ora di fine.</p></li></ul>
		  <p>Si noti che si verifica un ritardo tra il momento in cui un campione viene raccolto e il momento in cui questo è disponibile per una formula. È necessario tenere conto di questo aspetto quando si usa il metodo GetSample (vedere GetSamplePercent di seguito).</td>
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
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>Poiché il metodo GetSample non riesce se la percentuale di campioni restituita è minore del valore samplePercent specificato, è possibile usare i metodi GetSamplePercent prima di tutto per controllare e poi eseguire un'azione alternativa se non sono presenti campioni sufficienti senza interrompere la valutazione per il ridimensionamento automatico.</p></td>
  </tr>
</table>

### Metrics

Quando si definisce una formula, è possibile usare sia le **metriche** dell'attività che delle risorse. Queste metriche possono quindi essere usate per gestire i nodi di calcolo in un pool.

<table>
  <tr>
    <th>Metrica</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td>Risorsa</td>
    <td><p>Le metriche delle risorse sono basate su utilizzo di CPU, larghezza di banda, utilizzo della memoria e numero di nodi calcolo. Queste variabili definite dal sistema (descritte nella sezione **Variabili**) vengono usate nelle formule per gestire i nodi di calcolo in un pool:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>Queste variabili definite dal sistema vengono usate per le regolazioni in base alle metriche delle risorse:</p>
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
    <td>Attività</td>
    <td><p>Basata sullo stato delle attività, ad esempio Attiva, In sospeso e Completata.</p>
    <p>Queste variabili definite dal sistema vengono usate per le regolazioni in base alle metriche delle attività:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p></td>
  </tr>
</table>

## Creare una formula di ridimensionamento automatico

La creazione di una formula di ridimensionamento automatico prevede la composizione delle istruzioni usando i componenti indicati in precedenza e la combinazione di tali istruzioni in una formula completa. Ad esempio, per creare una formula è prima necessario definire i requisiti per una formula che:

1. Aumenterà il numero di destinazione dei nodi di calcolo in un pool se l'utilizzo della CPU è elevato
2. Ridurrà il numero di destinazione dei nodi di calcolo in un pool se l'utilizzo della CPU è basso
3. Limiterà sempre il numero massimo di nodi a 400

Per l'*aumento* dei nodi durante l'utilizzo elevato della CPU, viene definita un'istruzione che popola una variabile definita dall'utente ($TotalNodes) con un valore pari al 110% del numero di destinazione corrente dei nodi se l'utilizzo medio minimo della CPU negli ultimi 10 minuti è superiore al 70%:

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;

L'istruzione successiva imposta la stessa variabile sul 90% del numero di destinazione corrente dei nodi se l'utilizzo medio della CPU negli ultimi 60 minuti è *inferiore* al 20%, abbassando il numero di destinazione durante l'utilizzo ridotto della CPU. Si noti che questa istruzione fa anche riferimento alla variabile definita dall'utente *$TotalNodes* dell'istruzione precedente.

	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;

Ora viene limitato il numero di destinazione dei nodi di calcolo dedicati a un **massimo** di 400:

	$TargetDedicated = min(400, $TotalNodes)

Ecco la formula completa:

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
	$TargetDedicated = min(400, $TotalNodes)

## Creare un pool con il ridimensionamento automatico abilitato

Per abilitare il ridimensionamento automatico durante la creazione di un pool, usare una delle tecniche seguenti:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx): questo cmdlet di Azure PowerShell usa il parametro AutoScaleFormula per specificare la formula di ridimensionamento automatico.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx): dopo la chiamata di questo metodo .NET per creare un pool, vengono impostate le proprietà [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) e [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) del pool per abilitare il ridimensionamento automatico.
- [Aggiungere un pool a un account](https://msdn.microsoft.com/library/azure/dn820174.aspx): gli elementi enableAutoScale e autoScaleFormula vengono usati in questa richiesta dell'API REST per impostare il ridimensionamento automatico per il pool quando viene creato.

> [AZURE.NOTE]Se si imposta il ridimensionamento automatico quando il pool viene creato con le risorse sopra indicate, il parametro *targetDedicated* per il pool non viene usato (e non deve essere usato). Si noti anche che per ridimensionare manualmente un pool abilitato per il ridimensionamento automatico, ad esempio con [BatchClient.PoolOperations.ResizePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx), è prima necessario disabilitare il ridimensionamento automatico nel pool e quindi ridimensionarlo.

Il frammento di codice seguente illustra la creazione di un oggetto [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) abilitato per il ridimensionamento automatico usando la libreria [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) la cui formula imposta il numero di destinazione dei nodi su 5 il lunedì e su 1 per tutti gli altri giorni della settimana. Nel frammento di codice "myBatchClient" è un'istanza correttamente inizializzata di [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx):

		CloudPool pool myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
		pool.AutoScaleEnabled = true;
		pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
		pool.Commit();

## Abilitare il ridimensionamento automatico dopo la creazione di un pool

Se è già stato configurato un pool con un numero specificato di nodi di calcolo usando il parametro *targetDedicated*, è possibile aggiornare il pool esistente in un secondo momento per il ridimensionamento automatico. Questa operazione può essere eseguita in uno dei modi seguenti:

- [BatchClient.PoolOperations.EnableAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx): questo metodo .NET richiede l'ID di un pool esistente e la formula di ridimensionamento automatico da applicare al pool.
- [Abilitare il ridimensionamento automatico in un pool](https://msdn.microsoft.com/library/azure/dn820173.aspx): questa richiesta dell'API REST richiede il nome del pool esistente nell'URI e la formula di ridimensionamento automatico nel corpo della richiesta.

> [AZURE.NOTE]Se è stato specificato un valore per il parametro *targetDedicated* durante la creazione del pool, questo verrà ignorato quando viene valutata la formula di ridimensionamento automatico.

Questo frammento di codice illustra come abilitare il ridimensionamento automatico in un pool esistente tramite la libreria [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx). Si noti che per l'abilitazione e l'aggiornamento della formula in un pool esistente viene usato lo stesso metodo. Di conseguenza, se il ridimensionamento automatico è già stato abilitato, questa tecnica *aggiornerà* la formula nel pool specificato. Questo frammento di codice presume che "myBatchClient" sia un'istanza inizializzata correttamente di [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) e che "mypool" sia l'ID di un oggetto [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) esistente.

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## Valutare la formula di ridimensionamento automatico

È sempre consigliabile valutare una formula prima di usarla nell'applicazione. La formula viene valutata eseguendo un test della formula su un pool esistente. A questo scopo, usare:

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) o [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx): questi metodi .NET richiedono l'ID di un pool esistente e la stringa che contiene la formula di ridimensionamento automatico. I risultati della chiamata sono contenuti in un'istanza della classe [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Valutare una formula di ridimensionamento automatico](https://msdn.microsoft.com/library/azure/dn820183.aspx): in questa richiesta dell'API REST, l'ID del pool è specificato nell'URI e la formula di ridimensionamento automatico è specificata nell'elemento *autoScaleFormula* del corpo della richiesta. La risposta dell'operazione contiene eventuali informazioni sugli errori che potrebbero essere correlate alla formula.

> [AZURE.NOTE]Per valutare una formula di ridimensionamento automatico, è prima necessario aver abilitato il ridimensionamento automatico nel pool usando una formula valida.

In questo frammento di codice viene usata la libreria [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) per valutare una formula prima di applicarla all'oggetto [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx).

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
				// variable as evaluated by the the autoscaling formula.
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

La valutazione corretta della formula in questo frammento di codice genererà un output simile al seguente:

		AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0

## Ottenere informazioni sulle esecuzioni del ridimensionamento automatico

È opportuno verificare periodicamente i risultati delle operazioni di ridimensionamento automatico per assicurarsi che il comportamento della formula sia quello previsto. Questa operazione può essere eseguita in uno dei modi seguenti:

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx): quando si usa la libreria .NET, questa proprietà del pool fornisce un'istanza della classe [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) che a sua volta fornisce le proprietà seguenti dell'ultima esecuzione di ridimensionamento automatico:
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [Ottenere informazioni su un pool](https://msdn.microsoft.com/library/dn820165.aspx): questa richiesta dell'API REST restituisce informazioni relative al pool, che includono l'esecuzione più recente del ridimensionamento automatico.

## Formule di esempio

Ora verranno esaminati alcuni esempi che mostrano come si possono usare le formule per ridimensionare automaticamente le risorse di calcolo in un pool.

### Esempio 1

Si vuole regolare la dimensione del pool in base al giorno della settimana e l'ora del giorno, aumentando o riducendo il numero di nodi nel pool di conseguenza:

		$CurTime=time();
		$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
		$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
		$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
		$TargetDedicated=$IsWorkingWeekdayHour?20:10;

Questa formula ottiene l'ora corrente. Durante i giorni della settimana (da 1 a 5) e durante l'orario lavorativo (dalle 8.00 alle 18.00), le dimensioni del pool di destinazione sono impostate su 20 nodi. In caso contrario, le dimensioni del pool sono impostate su 10 nodi.

### Esempio 2

In questo esempio le dimensioni del pool vengono regolate in base al numero di attività nella coda. Si noti che sia i commenti che le interruzioni di riga sono accettabili nelle stringhe della formula.

	    // Get pending tasks for the past 15 minutes.
	    $Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
	    // If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of
		// last sample point and the history average.
	    $Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
	    // If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
	    $TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
	    // The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
		// should be adjusted according to your use case.
	    $TargetDedicated = max(0,min($TargetVMs,20));
	    // Set node deallocation mode - keep nodes active only until tasks finish
	    $NodeDeallocationOption = taskcompletion;

### Esempio 3

In questo esempio le dimensioni del pool vengono regolate in base al numero di attività, tenendo anche conto del valore di [MaxTasksPerComputeNode](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx) impostato per il pool. Questa operazione è utile nei casi in cui nei nodi di calcolo viene eseguita l'esecuzione di attività parallele.

		// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
		$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
		$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
		// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
		// property on this pool is set to 4, adjust this number for your use case)
		$Cores = $TargetDedicated * 4;
		$ExtraVMs = ($Tasks - $Cores) / 4;
		$TargetVMs = ($TargetDedicated+$ExtraVMs);
		// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
		$TargetDedicated = max(0,min($TargetVMs,3));
		// Keep the nodes active until the tasks finish
		$NodeDeallocationOption = taskcompletion;

## Passaggi successivi

1. Potrebbe essere necessario accedere al nodo di calcolo per poter valutare completamente l'efficienza dell'applicazione. Per sfruttare i vantaggi dell'accesso remoto, è necessario aggiungere un account utente al nodo di calcolo a cui si vuole accedere e recuperare un file RDP per tale nodo.
    - Aggiungere l'account utente in uno dei modi seguenti:
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx): questo cmdlet di PowerShell accetta il nome del pool, il nome del nodo di calcolo, il nome dell'account e la password come parametri.
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx): questo metodo .NET crea un'istanza della classe [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx) in cui è possibile impostare il nome dell'account e la password per il nodo di calcolo, quindi viene chiamato il metodo [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx) sull'istanza per creare l'utente in quel nodo.
        * [Aggiungere un account utente a un nodo](https://msdn.microsoft.com/library/dn820137.aspx): il nome del pool del nodo di calcolo vengono specificati nell'URI e il nome dell'account e la password vengono inviati al nodo nel corpo della richiesta di questa API REST.
    - Recuperare il file RDP:
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx): questo metodo .NET richiede l'ID del pool, l'ID del nodo e il nome del file RDP da creare.
        * [Recuperare un file RDP (Remote Desktop Protocol) da un nodo](https://msdn.microsoft.com/library/dn820120.aspx): questa richiesta dell'API REST richiede il nome del pool e il nome del nodo di calcolo. La risposta include il contenuto del file RDP.
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx): questo cmdlet di PowerShell recupera il file RDP dal nodo di calcolo specificato e lo salva nel percorso di file specificato oppure in un flusso.
2.	Alcune applicazioni producono grandi quantità di dati che possono essere difficili da elaborare. L'esecuzione di [query di tipo elenco efficienti](batch-efficient-list-queries.md) è uno dei modi per risolvere il problema.

<!---HONumber=Oct15_HO3-->