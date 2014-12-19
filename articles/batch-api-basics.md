
<properties title="API basics for Azure Batch" pageTitle="Nozioni di base delle API per Azure Batch" description="Concepts to introduce developers to the Azure Batch APIs and Batch service" metaKeywords="" services="batch" solutions="" documentationCenter=".NET" authors="yidingz, karran.batta" videoId="" scriptId="" manager="timlt" />

<tags ms.service="batch" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="big-compute" ms.date="10/27/2014" ms.author="yidingz, karran.batta" />


<!--The next line, with one pound sign at the beginning, is the page title--> 
# Nozioni di base delle API per Azure Batch

Il servizio Azure Batch fornisce un framework di pianificazione dei processi per il calcolo scalabile e distribuito.. Il servizio Batch gestisce un set di macchine virtuali che si trovano in diversi cluster e data center in Azure. Il servizio Batch effettua il calcolo distribuito eseguendo una o più applicazioni su richiesta o pianificate per l'esecuzione in un momento specifico su una raccolta specificata di queste VM. Il servizio Batch gestisce queste macchine virtuali per eseguire le attività di calcolo in base ai requisiti delle risorse nonché alle specifiche e ai vincoli indicati dall'utente.

Tramite il servizio Batch, è possibile eliminare la necessità di scrivere codice per l'accodamento la pianificazione, l'allocazione e la gestione delle risorse di calcolo, consentendo all'utente di concentrarsi sull'applicazione specifica e di non doversi preoccuparsi della complessità della pianificazione dei processi e della gestione delle risorse nella piattaforma sottostante. Inoltre, in questo modo il servizio Batch ottimizza la posizione di questi processi, nonché l'accesso ai dati che devono elaborare.

Di seguito sono riportati alcuni scenari che è possibile abilitare tramite il servizio Batch:

- Elaborazione parallela con attività di calcolo complesse.

- Pulizia giornaliera dei file

- Elaborazione batch

Per altre informazioni sul servizio Batch leggere le sezioni seguenti:

- [Risorse del servizio Batch](#resource)

- [Flusso di lavoro del servizio Batch](#workflow)

- [File e directory](#file)

- [Scalabilità delle applicazioni](#scaling)

- [Certificati per le applicazioni](#cert)

- [Priorità di pianificazione](#scheduling)

- [Impostazioni di ambiente per le attività](#environment)

## <a name="resource"></a> Risorse del servizio Batch

Quando si usa il servizio Batch, è possibile sfruttare le risorse seguenti:

- [Account](#account)

- [Macchina virtuale delle attività](#taskvm)

- [Pool](#pool)

- [Elemento di lavoro](#workitem)

- [Processo](#job)

- [Attività](#task)

### <a name="account"></a>Account

Un account di Batch è un'entità identificata in modo univoco all'interno del servizio Batch. Tutte le operazioni di elaborazione avvengono tramite un account di Batch. Quando si eseguono operazioni con il servizio Batch, è necessario il nome dell'account e la chiave per l'account. Attualmente, è necessario contattare il team di Azure Batch per creare un nuovo account. Dopo la creazione dell'account, all'utente viene fornita una chiave.

### <a name="taskvm"></a>Macchina virtuale delle attività

Una macchina virtuale delle attività (TVM) è una macchina virtuale di Azure dedicata a un carico di lavoro specifico per l'applicazione. La dimensione di una TVM determina il numero di core CPU, la capacità di memoria e la dimensione del file system locale allocati alla TVM. Una TVM può essere una macchina virtuale di piccole dimensioni, grandi o grandissime dimensioni, come descritto in [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/it-it/library/dn197896.aspx).

I tipi di programmi che una TVM può eseguire includono file eseguibili (.exe), file di comando (.cmd), file batch (.bat) e file script. Una TVM presenta inoltre gli attributi seguenti:

- Cartelle del file system che sono allo stesso tempo specifiche delle attività e condivise

- File stdout.txt e stderr.txt che vengono scritti in una cartella di attività specifiche

- Variabili di ambiente per l'elaborazione

- Impostazioni del firewall sono configurate per controllare l'accesso

### <a name="pool"></a>Pool

Un pool è una raccolta di TVMs in cui viene eseguita l'applicazione. Il pool può essere creato dall'utente o viene creato automaticamente dal servizio Batch quando si specifica il lavoro da eseguire. È possibile creare e gestire un pool che soddisfi le esigenze dell'applicazione. Un pool può essere usato solo dall'account Batch in cui è stato creato. Un account Batch può avere più pool.

A ogni TVM aggiunta a un pool viene assegnato un nome univoco e un indirizzo IP associato. Quando una TVM viene rimossa da un pool, perde le modifiche apportate al sistema operativo, tutti i relativi file locali, il nome e il relativo indirizzo IP. Quando una TVM esce da un pool, la sua durata è terminata.

È possibile configurare un pool per consentire la comunicazione tra TVM al suo interno. Se è richiesta la comunicazione all'interno dello stesso pool per un pool, il servizio Batch abilita un numero di porte superiore a 1100 su ogni TVM nel pool. Ogni TVM nel pool è configurata per consentire e limitare le connessioni in ingresso per questo intervallo di porte e solo da altre TVM nel pool. Se l'applicazione non richiede la comunicazione tra TVM, il servizio Batch è in grado di allocare al pool un numero elevato di TVM fra diversi cluster o data center per consentire una maggiore elaborazione parallela.

Quando si crea un pool, è possibile specificare gli attributi seguenti:

- La dimensione delle TVMs nel pool.

- La famiglia di sistemi operativi e la versione che esegue le TVM.

- Il numero di destinazione di TVM che devono essere disponibili per il pool.

- I criteri di scalabilità per il pool.

- Lo stato di comunicazione delle TVM nel pool.

- L'attività di avvio per le TVM nel pool.

Quando si crea un pool, è possibile specificare l'account di archiviazione con cui deve essere associato. Il servizio Batch alloca le TVM dal data center con migliori capacità di connettività di rete e larghezza di banda per l'account di archiviazione specificato. Ciò consente ai carichi di lavoro accedere ai dati in modo più efficace.

### <a name="workitem"></a>Elemento di lavoro

Un elemento di lavoro specifica la modalità di esecuzione di calcoli sulle TVM in un pool. Un elemento di lavoro include gli elementi di configurazione seguenti:

1.Il programma che esegue l'elaborazione sulla TVM.

2.I parametri della riga di comando per il programma.

3.La priorità del calcolo.

4.La pianificazione del calcolo, che include la definizione nel caso in cui il calcolo debba essere eseguito nuovamente.

Un elemento di lavoro viene sempre assegnato a un pool e il pool può esistere già o essere creato automaticamente quando viene creato l'elemento di lavoro.

### <a name="job"></a>Processo

Un processo è un'istanza in esecuzione di un elemento di lavoro ed è costituito da una raccolta di attività. Il servizio Batch crea un'istanza di un processo in base alla configurazione dell'elemento di lavoro. Il processo usa TVM del pool associato all'elemento di lavoro.

### <a name="task"></a>Attività

Un'attività è un'unità di calcolo che viene associata a un processo e viene eseguito su una TVM. Un'attività usa le risorse seguenti:

- Il programma specificato nell'elemento di lavoro.

- I file di risorse che contengono i dati da elaborare. Questi file vengono copiati automaticamente nella TVM dall'archiviazione BLOB. Per altre informazioni, vedere il file e directory.

- Le impostazioni di ambiente che sono necessarie per il programma. Per altre informazioni, vedere Impostazioni di ambiente per le attività.

- I vincoli relativi all'esecuzione del calcolo. Ad esempio, il tempo massimo in cui l'attività può essere eseguita, il numero massimo di volte in cui un'attività deve essere ritentata se non viene eseguita e il tempo massimo in cui vengono mantenuti i file nella directory di lavoro.

Oltre alle attività che è possibile definire per eseguire il calcolo su una TVM, è possibile usare le attività speciali seguenti fornite dal servizio Batch:

- [Attività di avvio](#starttask)

- [Attività di gestione dei processi](#jobmanagertask)

#### <a name="starttask"></a>Attività di avvio

È possibile configurare il sistema operativo delle TVM in un pool associando un'attività di avvio con il pool. L'installazione del software e l'avvio dei processi in background sono alcune delle azioni che un'attività di avvio può eseguire. L'attività di avvio viene eseguita a ogni avvio di una TVM per tutto il tempo in cui quest'ultima rimane nel pool.

Un'attività di avvio viene definita mediante l'aggiunta di una sezione XML al corpo della richiesta per l'operazione Aggiungi pool. Nell'esempio seguente viene illustrata una definizione di base di un'attività di avvio:

	{
		"commandLine":"mypoolsetup.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"mypoolsetup.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"maxTaskRetryCount":0
	}


#### <a name="jobmanagertask"></a>Attività di gestione dei processi

Un'attività di gestione dei processi viene avviata prima di tutte le altre attività e offre i vantaggi seguenti:

- Viene creata automaticamente dal servizio Batch quando viene creato il processo.

- Viene pianificata prima di altre attività del processo.

- La TVM a essa associata è l'ultima da rimuovere da un pool quando il pool viene ridimensionato.

- A essa viene assegnata la priorità più alta quando deve essere riavviata. Se una TVM inattiva non è disponibile, il servizio Batch può terminare una delle attività in esecuzione nel pool per consentire l'esecuzione della TVM.

- La sua terminazione può essere associata alla terminazione di tutte le attività del processo.

Un'attività di gestione dei processi in un processo non ha priorità sulle attività di altri processi. Tra i processi, è possibile osservare solo priorità a livello di processo. Un'attività di gestione dei processi viene definita mediante l'aggiunta di una sezione XML al corpo della richiesta per l'operazione Aggiungi elementi di lavoro. Nell'esempio seguente viene illustrata una definizione di base di un'attività di gestione dei processi:

	{
		"name":"jmTask",
		"commandLine":"myapp1.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp1.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"taskConstraints":
		{
			"maxWallClockTime":"PT1H",
			"maxTaskRetryCount":0,
			"retentionTime":"PT1H"
		},
		"killJobOnCompletion":true,
		"runElevated":false,
		"runExclusive":true
	}




## <a name="workflow"></a>Flusso di lavoro del servizio Batch

Per usare il servizio Batch è necessario un account di Batch e per pianificare il calcolo si usano più risorse del servizio. Quando si crea un scenario di calcolo distribuito con il servizio Batch, usare il seguente flusso di lavoro di base:

1.Caricare i file da usare nello scenario di calcolo distribuito in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Il servizio Batch li carica in una TVM quando l'attività viene eseguita.

2.Caricare i file binari dipendenti nell'account di archiviazione. I file binari includono il programma che viene eseguito dall'attività e gli assembly dipendenti. Questi file devono inoltre essere accessibili dalla risorsa di archiviazione ed essere caricati nella TVM.

3.Creare un pool di TVM. È possibile assegnare la dimensione della macchina virtuale delle attività da usare durante la creazione del pool. Quando un'attività viene eseguita, a essa viene assegnata una TVM da questo pool.

4.Creare un elemento di lavoro. Un processo viene automaticamente creato quando si crea un elemento di lavoro. Un elemento di lavoro consente di gestire un processo di attività.

5.Aggiungere le attività all'elemento di lavoro. Ogni attività usa il programma caricato per elaborare le informazioni da un file caricato.

6.Monitorare i risultati dell'output.

## <a name="files"></a>File e directory

Ogni attività dispone di una directory di lavoro in cui può creare directory e file per archiviare il programma eseguito da un'attività, i dati elaborati da un'attività e l'output dell'elaborazione eseguita da un'attività. Tali directory e file sono quindi disponibili per l'uso da altre attività durante l'esecuzione di un processo. Tutte le attività e le directory nonché tutti i file di una TVM sono di proprietà di un singolo account utente.

Il servizio Batch espone una parte del file system a una TVM come directory radice. La directory radice della TVM è disponibile per un'attività tramite la variabile di ambiente WATASK\_TVM\_ROOT\_DIR. Per altre informazioni sull'uso delle variabili di ambiente, vedere Impostazioni di ambiente per le attività.

La directory radice contiene le sottodirectory seguenti:

- **Attività**: il percorso in cui vengono archiviati tutti i file che appartengono alle attività eseguite sulla TVM. Per ogni attività, il servizio Batch crea una directory di lavoro con il percorso univoco nel formato %WATASK\_TVM\_ROOT\_DIR%/tasks/workitemName/jobName/taskName/. Questa directory fornisce accesso in lettura/scrittura all'attività. L'attività può creare, leggere, aggiornare ed eliminare i file in tale directory e questa directory viene mantenuta in base al vincolo RetentionTime specificato per l'attività.

- **Condiviso**: tale percorso è una directory condivisa per tutte le attività dell'account. Nella TVM la directory condivisa è % WATASK\_TVM\_ROOT\_DIR%/shared. Questa directory fornisce accesso in lettura/scrittura all'attività. L'attività può creare, leggere, aggiornare ed eliminare file in tale directory.

- **Start**: questo percorso viene usato da un'attività di avvio come directory di lavoro. Anche tutti i file scaricati dal servizio Batch per avviare l'attività di avvio vengono archiviati in tale directory. Nella TVM la directory di avvio è % WATASK\_TVM\_ROOT\_DIR%/start. L'attività può creare, leggere, aggiornare ed eliminare i file in tale directory e questa directory può essere usata dalle attività di avvio per configurare il sistema operativo.

Quando una TVM viene rimossa dal pool, vengono rimossi tutti i file archiviati nella TVM.

## <a name="scaling"></a>Scalabilità delle applicazioni

Il numero di istanze di un'applicazione può essere facilmente aumentato o ridotto in modo automatico per consentire il calcolo necessario. È possibile regolare automaticamente il numero di TVM in un pool in base alle statistiche correnti del carico di lavoro e di utilizzo delle risorse. È inoltre possibile ottimizzare il costo complessivo dell'esecuzione dell'applicazione configurandone la scalabilità automatica. Quando si crea un pool è possibile specificarne le impostazioni di scalabilità e aggiornare la configurazione in qualsiasi momento.

È possibile specificare la scalabilità automatica di un'applicazione mediante un set di formule di scalabilità, che possono essere usate per determinare il numero di TVM presenti nel pool per il successivo intervallo di scalabilità. Ad esempio, per inviare un numero elevato di attività da eseguire in un pool, è possibile assegnare al pool una formula di scalabilità che specifichi la dimensione del pool in base al numero corrente di attività in sospeso e alla percentuale di completamento delle attività. Il servizio Batch valuta periodicamente la formula e ridimensiona il pool in base al carico di lavoro.

Una formula può essere basata sulle seguenti metriche:

- **Metriche temporali**: in base alle statistiche raccolte ogni cinque minuti nel numero di ore specificato.

- **Metriche delle risorse**: basate sull'utilizzo di CPU, larghezza di banda, memoria e al numero di TVM.

- **Metriche delle attività**: basate sullo stato delle attività, ad esempio Attiva, In sospeso e Completata.

Per altre informazioni sulla scalabilità di un'applicazione, vedere la sezione relativa alla configurazione della scalabilità automatica delle macchine virtuali delle attività.

## <a name="cert"></a>Certificati per le applicazioni

In genere, è necessario usare certificati per crittografare informazioni segrete. I certificati possono essere installati nelle TVM. I segreti crittografati vengono passati alle attività nei parametri della riga di comando o incorporati in una delle risorse e i certificati installati possono essere usati per decrittografarli. Un esempio di informazione segreta è la chiave per un account di archiviazione.

Usare l'operazione Aggiungi certificato per aggiungere un certificato a un account di Batch. È quindi possibile associare il certificato a un pool nuovo o esistente. Quando un certificato è associato a un pool, il servizio Batch installa il certificato in ogni TVM nel pool. Il servizio Batch consente di installare i certificati appropriati all'avvio della TVM, prima che vengano avviate le attività, che include le attività di avvio e le attività di gestione dei processi.

## <a name="scheduling"></a>Priorità di pianificazione

Quando si crea un elemento di lavoro, è possibile assegnare a esso una priorità. Ogni processo dell'elemento di lavoro viene creato con questa priorità. Il servizio Batch usa i valori di priorità del processo per determinare l'ordine di pianificazione dei processi all'interno di un account. I valori di priorità possono variare da -1000 a 1000, dove -1000 è la priorità più bassa e 1000 la priorità più alta. È possibile aggiornare la priorità di un processo usando l'operazione di aggiornamento del processo.

All'interno dello stesso account i processi con priorità più alta hanno precedenza di pianificazione rispetto ai processi con priorità inferiori. Un processo con un valore di priorità più elevato in un account non dispone di tale precedenza di pianificazione rispetto a un altro processo con un valore di priorità inferiore in un account diverso.

Le pianificazioni dei processi eseguite su pool diversi sono indipendenti. In pool diversi non è garantito che un processo con priorità più elevato venga pianificato per primo se il relativo pool associato non dispone di un numero sufficiente di TVM inattive. Nello stesso pool i processi con lo stesso livello di priorità hanno la stessa probabilità di pianificazione.

## <a name="environment"></a>Impostazioni di ambiente per le attività

È possibile specificare le impostazioni di ambiente che possono essere usate nel contesto di un'attività. Le impostazioni di ambiente per un'attività di avvio e le attività in esecuzione in un processo vengono definite mediante l'aggiunta di una sezione XML al corpo della richiesta delle operazioni di aggiunta di attività o di aggiornamento di attività.

Nell'esempio seguente viene illustrata la definizione di un'impostazione dell'ambiente:

Per ogni attività pianificata in un processo, un set specifico di variabili di ambiente vengono impostate tramite il servizio Batch. Nella tabella seguente sono elencate le variabili di ambiente impostate dal servizio Batch per tutte le attività.

<table>
  <tr>
   <th>Nome di variabile di ambiente
   </th>
   <th>
   Descrizione
   </th>
  </tr>
 <tr>
  <td>
  WATASK_ ACCOUNT_NAME
  </td>
  <td>
  Nome dell'account a cui appartiene l'attività.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_WORKITEM_NAME
  </td>
  <td >Il nome dell'elemento di lavoro a cui appartiene l'attività.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_JOB_NAME
  </td>
  <td >Il nome del processo a cui appartiene l'attività.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TASK_NAME
  </td>
  <td >Il nome dell'attività corrente.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_POOL_NAME
  </td>
  <td >Nome del pool in cui è in esecuzione l'attività.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_NAME
  </td>
  <td >Nome della TVM in cui è in esecuzione l'attività.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_ROOT_DIR
  </td>
  <td >Il percorso completo della directory radice nella TVM.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_PORTRANGE_LOW
  WATASK_PORTRANGE_HIGH
</td>
<td>
  L'intervallo di porte, inclusi i valori iniziale e finale, che l'attività può usare per la comunicazione quando è abilitata la comunicazione all'interno dello stesso pool.
</td>
</table>


**Nota** 

Non è possibile sovrascrivere queste variabili definite dal sistema.

È possibile recuperare il valore delle impostazioni di ambiente mediante l'operazione di ottenimento dell'attività.
