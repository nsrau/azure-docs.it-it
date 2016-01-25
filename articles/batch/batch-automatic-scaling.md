
<properties
	pageTitle="Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch | Microsoft Azure"
	description="Abilitare il ridimensionamento automatico in un pool cloud per adeguare dinamicamente il numero di nodi di calcolo nel pool."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="01/08/2015"
	ms.author="marsma"/>

# Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch

Il ridimensionamento automatico in Azure Batch è l'adeguamento automatico della potenza di elaborazione usata dall'applicazione, aggiungendo o rimuovendo dinamicamente i nodi di calcolo durante l'esecuzione del processo. Questo adeguamento automatico può far risparmiare tempo e denaro.

Il ridimensionamento automatico viene abilitato in un pool di nodi di calcolo associando una *formula di ridimensionamento automatico* al pool, ad esempio con il metodo [PoolOperations.EnableAutoScale][net_enableautoscale] nella libreria [Batch .NET](batch-dotnet-get-started.md). Il servizio Batch usa quindi questa formula per determinare il numero di nodi di calcolo necessari per eseguire il carico di lavoro. Agisce su campioni di dati di metrica del servizio raccolti periodicamente, adeguando il numero dei nodi di calcolo nel pool a intervalli configurabili, in base alla formula associata.

Il ridimensionamento automatico può essere abilitato quando si crea un pool o in un pool esistente. È anche possibile modificare una formula esistente in un pool già abilitato per il ridimensionamento automatico. Batch consente di valutare le formule prima di assegnarle ai pool e di monitorare lo stato delle esecuzioni del ridimensionamento automatico.

## Formule di ridimensionamento automatico

Una formula di ridimensionamento automatico è un valore stringa contenente una o più istruzioni assegnato all'elemento [autoScaleFormula][rest_autoscaleformula] di un pool \(API Batch REST\) o alla proprietà [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] \(API Batch .NET\). Queste formule vengono definite dall'utente e quando sono assegnate a un pool determinano il numero di nodi di calcolo disponibili nel pool per l'intervallo di elaborazione successivo \(altre informazioni sugli intervalli più avanti\). La stringa della formula, le cui dimensioni non possono superare 8 KB, può includere fino a 100 istruzioni separate da punti e virgola, nonché interruzioni di riga e commenti.

È possibile paragonare le formule di ridimensionamento automatico all'uso di un "linguaggio" di ridimensionamento automatico di Batch. Le istruzioni nella formula sono espressioni in formato libero, possono includere variabili di sistema e definite dall'utente, oltre a costanti, e possono eseguire diverse operazioni su questi valori usando funzioni, operatori e tipi predefiniti. Ad esempio, un'istruzione può avere il formato seguente:

`VAR = Expression(system-defined variables, user-defined variables);`

In genere le formule contengono più istruzioni che eseguono operazioni su valori ottenuti nelle istruzioni precedenti:

```
VAR₀ = Expression₀(system-defined variables);
VAR₁ = Expression₁(system-defined variables, VAR₀);
```

Se si usano le istruzioni nella formula, l'obiettivo è arrivare a un numero di nodi di calcolo a cui deve essere ridimensionato il pool, il numero di **destinazione** dei **nodi dedicati**. Questo numero di "destinazione di nodi dedicati" può essere maggiore, minore o uguale al numero attuale di nodi nel pool. Batch valuta la formula di ridimensionamento automatico del pool a un intervallo specifico \([intervalli di ridimensionamento automatico](#interval) illustrati di seguito\) e regola il numero di destinazione di nodi nel pool in base al numero specificato dalla formula al momento della valutazione.

Come semplice esempio, questa formula di ridimensionamento automatico di due righe specifica che il numero di nodi deve essere adeguato in base al numero di attività attive, con un massimo di 10 nodi di calcolo:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

Le sezioni successive dell'articolo illustrano le diverse entità che costituiranno le formule di ridimensionamento automatico, ad esempio variabili, operatori, operazioni e funzioni. Si scoprirà come ottenere varie metriche relative ad attività e a risorse di calcolo all'interno di Batch, per adeguare in modo intelligente il numero di nodi del pool in base all'utilizzo delle risorse e allo stato delle attività. Verrà quindi illustrato come costruire una formula e abilitare il ridimensionamento automatico in un pool con le API Batch REST e Batch .NET, concludendo con alcune formule di esempio.

> [AZURE.NOTE]Ogni account Azure Batch è limitato a un numero massimo di nodi di calcolo che può essere usato per l'elaborazione. Il servizio Batch crea nodi solo fino al raggiungimento di questo limite e quindi potrebbe non raggiungere il numero di destinazione specificato da una formula. Per istruzioni su come visualizzare e aumentare le quote dell'account, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).

## <a name="variables"></a>Variabili

In una formula di ridimensionamento automatico possono essere usate variabili definite dal sistema e dall'utente. Nella precedente formula di esempio di due righe `$TargetDedicated` è una variabile definita dal sistema, mentre `$averageActiveTaskCount` è definita dall'utente. Le tabelle seguenti includono variabili di lettura/scrittura e di sola lettura definite dal servizio Batch.

È possibile *ottenere* e *impostare* il valore di queste **variabili definite dal sistema** per gestire i nodi di calcolo in un pool:

<table>
  <tr>
    <th>Variabili (lettura e scrittura)</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>Numero di <b>destinazione</b> dei <b>nodi di calcolo dedicati</b> per il pool. È il numero di nodi di calcolo in cui deve essere ridimensionato il pool. È un numero di "destinazione", perché è possibile che un pool non raggiunga il numero di nodi di destinazione. Questa condizione può verificarsi se il numero di nodi di destinazione viene modificato di nuovo da una valutazione di ridimensionamento automatico successiva, prima che il pool abbia raggiunto la destinazione iniziale o perché è stata raggiunta la quota di nodi o core di un account Batch prima che venga raggiunto il numero di nodi di destinazione.</td>
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

*Ottenere* il valore di queste **variabili definite dal sistema** per eseguire adeguamenti in base alla metrica del servizio Batch.

<table>
  <tr>
    <th>Variabili (sola lettura)</th>
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

> [AZURE.TIP]Le variabili di sola lettura definite dal sistema illustrate sopra sono *oggetti* che forniscono vari metodi per accedere ai dati associati a ognuno. Per altre informazioni, vedere [Ottenere dati di esempio](#getsampledata) di seguito.

## Types

Questi sono i **tipi** supportati in una formula:

- double
- doubleVec
- doubleVecList
- string
- timestamp. timestamp è una struttura composta che contiene i membri seguenti:
	- year
	- month \(1-12\)
	- day \(1-31\)
	- weekday \(in formato numero, ad esempio 1 per lunedì\)
	- hour \(in formato 24 ore, ad esempio 13 per indicare le ore 13.00\)
	- minute \(00-59\)
	- second \(00-59\)
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

## Operazioni

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

## Functions

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

Alcune delle funzioni descritte nella tabella precedente possono accettare un elenco come argomento. L'elenco delimitato da virgole è qualsiasi combinazione di *double* e *doubleVec*. ad esempio:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Il valore *doubleVecList* viene convertito in un singolo valore *doubleVec* prima della valutazione. Ad esempio, se `v = [1,2,3]`, la chiamata di `avg(v)` equivale alla chiamata di `avg(1,2,3)` e la chiamata di `avg(v, 7)` equivale alla chiamata di `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Ottenere dati di esempio

Le formule di ridimensionamento automatico agiscono sui dati di metrica \(campioni\) forniti dal servizio Batch, aumentando o riducendo le dimensioni del pool in base ai valori che la formula ottiene dal servizio. Le variabili definite dal sistema illustrate sopra sono oggetti che forniscono vari metodi per accedere ai dati associati all'oggetto. Ad esempio, l'espressione seguente mostra una richiesta per recuperare gli ultimi 5 minuti di utilizzo della CPU:

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

<table>
  <tr>
    <th>Metodo</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>Il metodo <b>GetSample()</b> restituisce un vettore di campioni di dati.
	<p>Un campione rappresenta 30 secondi di dati di metrica. In altre parole i campioni vengono raccolti ogni 30 secondi ma, come indicato di seguito, si verifica un ritardo tra il momento in cui un campione viene raccolto e il momento in cui è disponibile per una formula. Di conseguenza, i campioni per un determinato periodo di tempo potrebbero non essere tutti disponibili per la valutazione da parte di una formula.
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>: specifica il numero di campioni da ottenere tra quelli più recenti.</p>
				  <p>GetSample (1) restituisce l'ultimo campione disponibile. Per le metriche come $CPUPercent, tuttavia, non deve essere usato perché non è possibile sapere <em>quando</em> è stato raccolto il campione: potrebbe essere recente o risultare molto meno recente a causa di problemi di sistema. In questi casi è preferibile usare un intervallo di tempo, come illustrato di seguito.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>: specifica un intervallo di tempo per la raccolta di dati campione e facoltativamente specifica la percentuale di campioni che deve essere disponibile nell'intervallo di tempo richiesto.</p>
          <p><em>$CPUPercent.GetSample(TimeInterval_Minute * 10)</em> restituisce 20 campioni se nella cronologia CPUPercent sono presenti tutti i campioni per gli ultimi dieci minuti. Se l'ultimo minuto di cronologia non è disponibile, tuttavia, vengono restituiti solo 18 esempi. In tal caso:<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)</em> non riesce perché è disponibile solo il 90% dei campioni;<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)</em> ha esito positivo.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>: specifica un intervallo di tempo per la raccolta di dati con un'ora di inizio e un'ora di fine.</p></li></ul>
		  <p>Come indicato in precedenza, si verifica un ritardo tra il momento in cui un campione viene raccolto e il momento in cui è disponibile per una formula. Quando si usa il metodo <em>GetSample</em> descritto di seguito, è necessario tenere conto di ciò. Vedere <em>GetSamplePercent</em> di seguito.</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Restituisce il periodo dei campioni prelevati in un set di dati campione cronologici.</td>
  </tr>
	<tr>
		<td>Count()</td>
		<td>Restituisce il numero totale di campioni nella cronologia dei dati di metrica.</td>
	</tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Restituisce il timestamp del campione di dati disponibile meno recente per la metrica.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Restituisce la percentuale di campioni disponibili per un determinato intervallo di tempo. Ad esempio:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>Poiché il metodo GetSample non riesce se la percentuale di campioni restituita è minore del valore samplePercent specificato, è possibile usare il metodo GetSamplePercent prima di tutto per controllare e poi per eseguire un'azione alternativa se non sono presenti campioni sufficienti, senza interrompere la valutazione del ridimensionamento automatico.</p></td>
  </tr>
</table>

### Campioni, percentuale di campioni e metodo *GetSample\(\)*

Il recupero dei dati di metrica relativi a risorse ed attività e l'adeguamento delle dimensioni del pool in base a tali dati costituiscono la base per il funzionamento di una formula di ridimensionamento automatico. Di conseguenza, è importante conoscere a fondo la modalità di interazione delle formule di ridimensionamento automatico con i dati di metrica o "campioni".

**Esempi**

Il servizio Batch acquisisce periodicamente *campioni * di metriche relative a risorse ed attività e li rende disponibili per le formule di ridimensionamento automatico. Questi campioni vengono registrati ogni 30 secondi dal servizio Batch. In genere si verifica una certa latenza che causa un ritardo tra il momento in cui i campioni sono stati registrati e il momento in cui vengono resi disponibili e possono essere letti dalle formule di ridimensionamento automatico. Inoltre, a causa di vari fattori, ad esempio problemi di rete o dell'infrastruttura, è possibile che i campioni non siano stati registrati per un particolare intervallo, determinando una condizione di campioni "mancanti".

**Percentuale di campioni**

Quando si passa un oggetto `samplePercent` al metodo `GetSample()` o si chiama il metodo `GetSamplePercent()`, il termine "percentuale" si riferisce a un confronto tra il *possibile* numero totale di campioni registrati dal servizio Batch e il numero di campioni effettivamente *disponibili* per la formula di ridimensionamento automatico.

Come esempio, si esaminerà un intervallo di tempo di 10 minuti. Poiché i campioni vengono registrati ogni 30 secondi, in un intervallo di tempo di 10 minuti il numero massimo totale di campioni registrati da Batch avrebbe dovuto essere 20 campioni \(2 al minuto\). Tuttavia, a causa della latenza intrinseca del meccanismo di creazione di report o di un altro problema all'interno dell'infrastruttura di Azure, potrebbero essere disponibili solo 15 campioni per la lettura da parte della formula di ridimensionamento automatico. Ciò significa che, per il periodo di 10 minuti, solo il **75%** del numero totale di campioni registrati è effettivamente disponibile per la formula.

**GetSample\(\) e intervalli di campioni**

Le formule di ridimensionamento automatico aumentano e riducono i pool, aggiungendovi nodi o rimuovendoli, e poiché i nodi hanno un costo, si vuole garantire che le formule applichino decisioni intelligenti sulla base di dati sufficienti. È quindi consigliabile usare un tipo di analisi delle tendenze nelle formule, aumentando e riducendo i pool in base a un *intervallo* di campioni raccolti.

A tale scopo, usare `GetSample(interval look-back start, interval look-back end)` per restituire un **vettore** di campioni:

`runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);`

Quando la riga precedente viene valutata da Batch, restituisce un intervallo di campioni come vettore di valori, ad esempio:

`runningTasksSample=[1,1,1,1,1,1,1,1,1,1];`

Dopo aver raccolto il vettore di campioni, è quindi possibile usare funzioni come `min()`, `max()` e `avg()` per derivare valori significativi dall'intervallo raccolto.

Per maggiore sicurezza, è possibile fare in modo che la valutazione di una formula *non riesca* se per un determinato periodo di tempo è disponibile una quantità di campioni inferiore a una certa percentuale. L'impostazione di un esito negativo della valutazione della formula indica a Batch di interromperne l'ulteriore valutazione se la percentuale di campioni specificata non è disponibile, evitando quindi di modificare le dimensioni del pool. Per specificare una percentuale di campioni obbligatoria perché la valutazione riesca, specificarla come terzo parametro in `GetSample()`. Qui viene specificato un requisito pari al 75% dei campioni:

`runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);`

A causa del ritardo nella disponibilità dei campioni citato in precedenza, è anche importante specificare sempre un intervallo di tempo con un'ora di inizio antecedente di almeno un minuto. La propagazione dei campioni attraverso il sistema richiede infatti un minuto circa, quindi i campioni nell'intervallo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` spesso non saranno disponibili. Anche in questo caso, è possibile usare il parametro percentuale di `GetSample()` per imporre uno specifico requisito di percentuale dei campioni.

> [AZURE.IMPORTANT]È **consigliabile** **evitare di basarsi *solo* su `GetSample(1)` nelle formule di ridimensionamento automatico**. Infatti `GetSample(1)` indica essenzialmente al servizio Batch, di rendere disponibile l'ultimo campione disponibile, indipendentemente da quanto tempo fa è stato ottenuto. Essendo solo un singolo campione, che potrebbe anche non essere recente, potrebbe non essere rappresentativo dell'immagine più ampia dello stato recente di attività o risorse. Se si usa `GetSample(1)`, accertarsi che faccia parte di un'istruzione di dimensioni maggiori e non sia il solo punto dati su cui si basa la formula.

## Metrics

Quando si definisce una formula, è possibile usare sia la **metrica delle risorse** che la **metrica delle attività**, adeguando il numero di destinazione di nodi dedicati nel pool in base ai dati di metrica ottenuti e valutati. Per altre informazioni su ogni metrica, vedere la sezione [Variabili](#variables) precedente.

<table>
  <tr>
    <th>Metrica</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td><b>Risorsa</b></td>
    <td><p><b>La metrica delle risorse</b> si basa sull'utilizzo della memoria, della CPU e della larghezza di banda dei nodi di calcolo, nonché sul numero di nodi.</p>
		<p> Queste variabili definite dal sistema sono utili per eseguire adeguamenti in base al conteggio dei nodi:</p>
    <p><ul>
      <li>$TargetDedicated</li>
			<li>$CurrentDedicated</li>
			<li>$SampleNodeCount</li>
    </ul></p>
    <p>Queste variabili definite dal sistema sono utili per eseguire adeguamenti in base all'utilizzo delle risorse dei nodi:</p>
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
    <td><p><b>La metrica delle attività</b> si basa sullo stato delle attività, ad esempio Attiva, In sospeso e Completata. Le variabili definite dal sistema seguenti sono utili per gli adeguamenti delle dimensioni del pool in base alla metrica delle attività:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
			<li>$FailedTasks</li></ul></p>
		</td>
  </tr>
</table>

## Creare una formula di ridimensionamento automatico

La creazione di una formula di ridimensionamento automatico prevede la composizione delle istruzioni usando i componenti indicati in precedenza e la combinazione di tali istruzioni in una formula completa. Ad esempio, per creare una formula è prima necessario definire i requisiti per una formula che:

1. Aumenterà il numero di destinazione dei nodi di calcolo in un pool se l'utilizzo della CPU è elevato
2. Ridurrà il numero di destinazione dei nodi di calcolo in un pool se l'utilizzo della CPU è basso
3. Limiterà sempre il numero massimo di nodi a 400

Per l'*aumento* dei nodi durante l'utilizzo elevato della CPU, viene definita un'istruzione che popola una variabile definita dall'utente \($TotalNodes\) con un valore pari al 110% del numero di destinazione corrente dei nodi se l'utilizzo medio minimo della CPU negli ultimi 10 minuti è superiore al 70%:

`$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;`

L'istruzione successiva imposta la stessa variabile sul 90% del numero di destinazione corrente dei nodi se l'utilizzo medio della CPU negli ultimi 60 minuti è *inferiore* al 20%, abbassando il numero di destinazione durante l'utilizzo ridotto della CPU. Si noti che questa istruzione fa anche riferimento alla variabile definita dall'utente *$TotalNodes* dell'istruzione precedente.

`$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;`

Ora viene limitato il numero di destinazione dei nodi di calcolo dedicati a un **massimo** di 400:

`$TargetDedicated = min(400, $TotalNodes)`

Ecco la formula completa:

```
$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
$TargetDedicated = min(400, $TotalNodes)
```

> [AZURE.NOTE]Una formula di ridimensionamento automatico è composta da variabili, tipi, operazioni e funzioni dell'API [Batch REST][rest_api]. Questi elementi sono usati nelle stringhe della formula anche quando si usa la libreria [Batch .NET][net_api].

## Creare un pool con il ridimensionamento automatico abilitato

Per abilitare il ridimensionamento automatico durante la creazione di un pool, usare una delle tecniche seguenti:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx): questo cmdlet di Azure PowerShell usa il parametro AutoScaleFormula per specificare la formula di ridimensionamento automatico.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx): dopo la chiamata di questo metodo .NET per creare un pool, vengono impostate le proprietà [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) e [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) del pool per abilitare il ridimensionamento automatico.
- [Aggiungere un pool a un account](https://msdn.microsoft.com/library/azure/dn820174.aspx): gli elementi enableAutoScale e autoScaleFormula vengono usati in questa richiesta dell'API REST per impostare il ridimensionamento automatico per il pool quando viene creato.

> [AZURE.IMPORTANT]Se si crea un pool abilitato per il ridimensionamento automatico usando una delle tecniche descritte sopra, il parametro *targetDedicated* per il pool **non** deve essere specificato. Si noti anche che per ridimensionare manualmente un pool abilitato per il ridimensionamento automatico, ad esempio con [BatchClient.PoolOperations.ResizePool,][net_poolops_resizepool] è necessario **disabilitare** prima di tutto il ridimensionamento automatico nel pool e quindi ridimensionarlo.

Il frammento di codice seguente illustra la creazione di un oggetto [CloudPool][net_cloudpool] abilitato per il ridimensionamento automatico usando la libreria [Batch .NET][net_api] la cui formula imposta il numero di destinazione dei nodi su 5 il lunedì e su 1 per tutti gli altri giorni della settimana. Inoltre, l'intervallo per il ridimensionamento automatico è impostato su 30 minuti. Vedere [Intervallo di ridimensionamento automatico](#interval) di seguito. In questo e in altri frammenti di codice C\# in questo articolo "myBatchClient" è un'istanza correttamente inizializzata di [BatchClient][net_batchclient].

```
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

### <a name="interval"></a>Intervallo di ridimensionamento automatico

Per impostazione predefinita, il servizio Batch adegua le dimensioni di un pool in base alla relativa formula di ridimensionamento automatico ogni **15 minuti**. Questo intervallo è tuttavia configurabile usando le proprietà del pool seguenti:

- API REST - [autoScaleEvaluationInterval][rest_autoscaleinterval]
- API .NET - [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval]

L'intervallo minimo è di 5 minuti e il massimo di 168 ore. Se viene specificato un intervallo che non rientra in questi valori, il servizio Batch restituisce un errore di richiesta non valida \(400\).

> [AZURE.NOTE]La funzionalità di ridimensionamento automatico non è attualmente concepita come risposta alle modifiche inferiore a un minuto, ma piuttosto per adeguare gradualmente le dimensioni del pool durante l'esecuzione di un carico di lavoro.

## Abilitare il ridimensionamento automatico dopo la creazione di un pool

Se è già stato configurato un pool con un numero specificato di nodi di calcolo usando il parametro *targetDedicated*, è possibile aggiornare il pool esistente in un secondo momento per il ridimensionamento automatico. Questa operazione può essere eseguita in uno dei modi seguenti:

- [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale]\: questo metodo .NET richiede l'ID di un pool esistente e la formula di ridimensionamento automatico da applicare al pool.
- [Abilitare il ridimensionamento automatico in un pool][rest_enableautoscale]\: questa richiesta dell'API REST richiede l'ID del pool esistente nell'URI e la formula di ridimensionamento automatico nel corpo della richiesta.

> [AZURE.NOTE]Se è stato specificato un valore per il parametro *targetDedicated* durante la creazione del pool, questo verrà ignorato quando viene valutata la formula di ridimensionamento automatico.

Questo frammento di codice illustra come abilitare il ridimensionamento automatico in un pool esistente con la libreria [Batch .NET][net_api]. Si noti che per l'abilitazione e l'aggiornamento della formula in un pool esistente viene usato lo stesso metodo. Di conseguenza, se il ridimensionamento automatico è già stato abilitato, questa tecnica *aggiornerà* la formula nel pool specificato. Nel frammento di codice si presuppone che "mypool" sia l'ID di un oggetto [CloudPool][net_cloudpool] esistente.

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

In questo frammento di codice viene usata la libreria [Batch .NET][net_api] per valutare una formula prima di applicarla all'oggetto [CloudPool][net_cloudpool].

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
```

La valutazione corretta della formula in questo frammento di codice genererà un output simile al seguente:

`AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0`

## Ottenere informazioni sulle esecuzioni del ridimensionamento automatico

È opportuno verificare periodicamente i risultati delle esecuzioni di ridimensionamento automatico per assicurarsi che il comportamento della formula sia quello previsto.

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx): quando si usa la libreria .NET, questa proprietà del pool fornisce un'istanza della classe [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) che a sua volta fornisce le proprietà seguenti dell'ultima esecuzione di ridimensionamento automatico:
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [Ottenere informazioni su un pool](https://msdn.microsoft.com/library/dn820165.aspx): questa richiesta dell'API REST restituisce informazioni relative al pool, che includono l'esecuzione più recente del ridimensionamento automatico.

## <a name="examples"></a>Formule di esempio

Ora verranno esaminati alcuni esempi che mostrano come si possono usare le formule per ridimensionare automaticamente le risorse di calcolo in un pool.

### Esempio 1: Adeguamento basato sul tempo

Si vuole regolare la dimensione del pool in base al giorno della settimana e l'ora del giorno, aumentando o riducendo il numero di nodi nel pool di conseguenza:

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

Questa formula ottiene l'ora corrente. Durante i giorni della settimana \(da 1 a 5\) e durante l'orario lavorativo \(dalle 8.00 alle 18.00\), le dimensioni del pool di destinazione sono impostate su 20 nodi. In caso contrario, le dimensioni del pool sono impostate su 10 nodi.

### Esempio 2: Adeguamento basato sulle attività

In questo esempio le dimensioni del pool vengono regolate in base al numero di attività nella coda. Si noti che sia i commenti che le interruzioni di riga sono accettabili nelle stringhe della formula.

```
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
```

### Esempio 3: Considerazioni sulle attività parallele

In questo esempio le dimensioni del pool vengono regolate in base al numero di attività, tenendo anche conto del valore di [MaxTasksPerComputeNode][net_maxtasks] impostato per il pool. Questa operazione è particolarmente utile nelle situazioni in cui l'[esecuzione di attività parallele](batch-parallel-node-tasks.md) è abilitata nel pool.

```
// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
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

Questo esempio mostra un segmento di codice C\# con una formula di ridimensionamento automatico che imposta le dimensioni del pool su un certo numero di nodi per un periodo di tempo iniziale, quindi adegua le dimensioni del pool in base al numero di attività attive e in esecuzione dopo la scadenza del periodo di tempo iniziale.

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

La formula nel frammento di codice precedente ha le caratteristiche seguenti:

- Imposta la dimensione iniziale del pool su 4 nodi
- Non modifica la dimensione del pool nei primi 10 minuti del ciclo di vita del pool
- Dopo 10 minuti, ottiene il valore massimo del numero di attività attive e in esecuzione negli ultimi 60 minuti
  - Se entrambi i valori sono 0 \(nessuna attività attiva o in esecuzione negli ultimi 60 minuti\), la dimensione del pool viene impostata su 0
  - Se uno dei valori è maggiore di zero, non viene apportata alcuna modifica

## Passaggi successivi

1. Potrebbe essere necessario accedere al nodo di calcolo per poter valutare completamente l'efficienza dell'applicazione. Per sfruttare i vantaggi dell'accesso remoto, è necessario aggiungere un account utente al nodo di calcolo a cui si vuole accedere e recuperare un file RDP per tale nodo.
    - Aggiungere l'account utente in uno dei modi seguenti:
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx): questo cmdlet di PowerShell accetta il nome del pool, il nome del nodo di calcolo, il nome dell'account e la password come parametri.
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx): questo metodo .NET crea un'istanza della classe [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx) in cui è possibile impostare il nome dell'account e la password per il nodo di calcolo, quindi viene chiamato il metodo [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx) sull'istanza per creare l'utente in quel nodo.
        * [Aggiungere un account utente a un nodo](https://msdn.microsoft.com/library/dn820137.aspx): il nome del pool del nodo di calcolo vengono specificati nell'URI e il nome dell'account e la password vengono inviati al nodo nel corpo di questa richiesta dell'API REST.
    - Recuperare il file RDP:
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx): questo metodo .NET richiede l'ID del pool, l'ID del nodo e il nome del file RDP da creare.
        * [Recuperare un file RDP \(Remote Desktop Protocol\) da un nodo](https://msdn.microsoft.com/library/dn820120.aspx): questa richiesta dell'API REST richiede il nome del pool e il nome del nodo di calcolo. La risposta include il contenuto del file RDP.
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx): questo cmdlet di PowerShell recupera il file RDP dal nodo di calcolo specificato e lo salva nel percorso di file specificato oppure in un flusso.
2.	Alcune applicazioni producono grandi quantità di dati che possono essere difficili da elaborare. L'esecuzione di [query di elenco efficienti](batch-efficient-list-queries.md) è uno dei modi per risolvere questa difficoltà.

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
[rest_autoscaleinterval]: https://msdn.microsoft.com/en-us/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

<!---HONumber=AcomDC_0114_2016-->