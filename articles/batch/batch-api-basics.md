<properties 
	pageTitle="Nozioni di base delle API per Azure Batch" 
	description="Concetti per introdurre gli sviluppatori al servizio Batch e alle API di Batch di Azure" 
	services="batch" 
	documentationCenter=".net" 
	authors="yidingzhou" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="batch" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-compute" 
	ms.date="03/19/2015" 
	ms.author="yidingz"/>

<!--The next line, with one pound sign at the beginning, is the page title-->
# Nozioni di base delle API per Azure Batch

Il servizio Azure Batch fornisce un framework di pianificazione dei processi per il calcolo scalabile e distribuito.. Il servizio Batch gestisce un set di macchine virtuali che si trovano in diversi cluster e data center in Azure. Il servizio Batch effettua il calcolo distribuito eseguendo una o più applicazioni su richiesta o pianificate per l'esecuzione in un momento specifico su una raccolta specificata di queste VM. Il servizio Batch gestisce queste macchine virtuali per eseguire le attività di calcolo in base ai requisiti delle risorse nonché alle specifiche e ai vincoli indicati dall'utente.

Tramite il servizio Batch, è possibile eliminare la necessità di scrivere codice per l'accodamento la pianificazione, l'allocazione e la gestione delle risorse di calcolo, consentendo all'utente di concentrarsi sull'applicazione specifica e di non doversi preoccuparsi della complessità della pianificazione dei processi e della gestione delle risorse nella piattaforma sottostante. Inoltre, in questo modo il servizio Batch ottimizza la posizione di questi processi, nonché l'accesso ai dati che devono elaborare.

Di seguito sono riportati alcuni scenari che è possibile abilitare tramite il servizio Batch:

- Elaborazione parallela con attività di calcolo complesse.

- Pulizia giornaliera dei file

- Elaborazione batch

## <a name="resource"></a> Risorse del servizio Batch

Quando si usa il servizio Batch, è possibile sfruttare le risorse seguenti:

- [Account](#account)

- [Macchina virtuale](#taskvm)

- [Pool](#pool)

- [Elemento di lavoro](#workitem)

- [Processo](#job)

- [Task](#task)

	- [Attività di avvio](#starttask)
	
	- [Processo ManagerTask](#jobmanagertask)

### <a name="account"></a>Account

Un account di Batch è un'entità identificata in modo univoco all'interno del servizio Batch. Tutte le operazioni di elaborazione avvengono tramite un account di Batch. Quando si eseguono operazioni con il servizio Batch, è necessario il nome dell'account e la chiave per l'account. Per creare un account di batch, fare riferimento alla sezione account Batch di [Panoramica Azure Batch][].


### <a name="taskvm"></a>Macchina virtuale

Una macchina virtuale delle attività (TVM) è una macchina virtuale di Azure dedicata a un carico di lavoro specifico per l'applicazione. La dimensione di una TVM determina il numero di core CPU, la capacità di memoria e la dimensione del file system locale allocati alla TVM. Una TVM può essere una macchina virtuale di piccole dimensioni, grande o molto grande, come descritto in [macchine virtuali e Cloud Service Sizes for Azure](http://msdn.microsoft.com/library/dn197896.aspx).

I tipi di programmi che una TVM può eseguire includono file eseguibili (.exe), file di comando (.cmd), file batch (.bat) e file script. Una TVM presenta inoltre gli attributi seguenti:

- Cartelle del file system che sono specifici dell'attività e condivise. In ciascun pool di macchine Virtuali vengono create le variabili di ambiente e la struttura di cartelle. La struttura di cartelle seguente viene creata con una cartella "condivisa" per le applicazioni e dati condivisi tra attività, oltre a una cartella per ogni attività.

![][1]

- File stdout.txt e stderr.txt che vengono scritti in una cartella di attività specifiche

- Variabili di ambiente per l'elaborazione

- Impostazioni del firewall sono configurate per controllare l'accesso

>Accesso alla VM
>
>Se l'accesso a una macchina virtuale è obbligatorio per il debug, ad esempio, è possibile ottenere il file RDP che può essere utilizzato per accedere alla macchina virtuale tramite desktop remoto.


### <a name="pool"></a>Pool

Un pool è una raccolta di TVMs in cui viene eseguita l'applicazione. Il pool può essere creato dall'utente o viene creato automaticamente dal servizio Batch quando si specifica il lavoro da eseguire. È possibile creare e gestire un pool che soddisfi le esigenze dell'applicazione. Un pool può essere usato solo dall'account Batch in cui è stato creato. Un account Batch può avere più pool.

Azure pool Batch basate sulla piattaforma di calcolo di Azure di base; Pool di batch forniscono allocazione su larga scala, applicazione & installazione dei dati, lo spostamento dei dati, il monitoraggio dello stato e scalabilità flessibile della macchina virtuale.

A ogni TVM aggiunta a un pool viene assegnato un nome univoco e un indirizzo IP associato. Quando una TVM viene rimossa da un pool, perde le modifiche apportate al sistema operativo, tutti i relativi file locali, il nome e il relativo indirizzo IP. Quando una TVM esce da un pool, la sua durata è terminata.

È possibile configurare un pool per consentire la comunicazione tra TVM al suo interno. Se è richiesta la comunicazione all'interno dello stesso pool per un pool, il servizio Batch abilita un numero di porte superiore a 1100 su ogni TVM nel pool. Ogni TVM nel pool è configurata per consentire e limitare le connessioni in ingresso per questo intervallo di porte e solo da altre TVM nel pool. Se l'applicazione non richiede la comunicazione tra TVM, il servizio Batch è in grado di allocare al pool un numero elevato di TVM fra diversi cluster o data center per consentire una maggiore elaborazione parallela.

Quando si crea un pool, è possibile specificare gli attributi seguenti:

- Il **dimensioni delle macchine virtuali** nel pool.
	- La dimensione di macchina virtuale appropriata deve essere scelto, a seconda delle caratteristiche e i requisiti dell'applicazione o le applicazioni che sta per essere utilizzato nella macchina virtuale. In genere le dimensioni della macchina virtuale verranno prelevata presupponendo un'attività deve essere eseguita contemporaneamente su macchina virtuale; ad esempio, se l'applicazione è multi-thread e quanto richiede memoria determina la dimensione di macchina virtuale più adatta ed economica. È possibile disporre di più attività assegnate e più istanze dell'applicazione in esecuzione in parallelo, nel qual caso una VM più grande in genere verrà scelto, vedere di seguito in "numero massimo di attività per ogni macchina virtuale". 
	- Tutte le della macchina virtuale in un pool debba essere le stesse dimensioni. Se diverse applicazioni devono essere eseguiti con requisiti di sistema diversi e/o con carico diversi, è necessario creare il pool diversi.
	- Tutte le dimensioni di macchina virtuale del servizio cloud possono essere configurate per un pool, ad eccezione di A0.

- La famiglia di sistemi operativi e la versione che viene eseguito nelle macchine virtuali.
	- Come con ruoli di lavoro, la famiglia di sistemi operativi e la versione del sistema operativo possono essere configurati.
	- Famiglia del sistema operativo determina inoltre quali versioni di .NET vengono installate con il sistema operativo.
	- Come con i ruoli di lavoro per la versione del sistema operativo è consigliabile che "*" utilizzato in modo sono della macchina virtuale aggiornato automaticamente e non vi è alcun lavoro necessario per rispondere alle nuove versioni. Il caso di utilizzo principali per scegliere una versione specifica del sistema operativo consiste nell'assicurare che venga mantenuta la compatibilità delle applicazioni, consentendo la compatibilità di test da eseguire prima di consentire la versione da aggiornare. Una volta convalidato, la versione del sistema operativo per il pool può essere aggiornata e la nuova immagine del sistema operativo installato: qualsiasi attività in esecuzione verrà interrotta e nuovamente in coda.

- Numero di macchine virtuali che devono essere disponibili per il pool di destinazione.

- I criteri di scalabilità per il pool. Oltre a numero di macchine virtuali, è inoltre possibile specificare una formula per ogni pool di scalabilità automatica. Servizio batch eseguirà la formula per regolare il numero di macchine Virtuali in base alle statistiche del pool ed elemento di lavoro.

- Configurazione di pianificazione
	- La configurazione predefinita è per un'attività da eseguire in qualsiasi momento in un pool di macchine Virtuali, ma esistono scenari in cui è opportuno avere più di un'attività di essere in grado di eseguire contemporaneamente in una macchina virtuale. Esempio, è possibile aumentare l'utilizzo di macchina virtuale se dispone di un'applicazione per l'attesa dei / o; con più di un'applicazione in esecuzione aumenta l'utilizzo della CPU. Un altro esempio è quello di ridurre il numero di macchine Virtuali in pool. Questo potrebbe ridurre la quantità di copie di dati necessari per i set di dati di grandi dimensioni di riferimento. Se un A1 sarebbe la dimensione corretta per l'applicazione, un A4 può essere scelta e la configurazione impostata per l'esecuzione fino a 8 attività alla volta, ogni utilizzo di un core.
	- La configurazione "max attività per ogni macchina virtuale" determina il numero massimo di attività che possono essere eseguiti in parallelo.
	- Un "criterio"riempimento inoltre è possibile specificare che determina se il Batch riempie prima della macchina virtuale o se attività sono distribuite su tutte le VM.
 
- Lo stato delle comunicazioni delle macchine virtuali nel pool.
 	- In gran parte degli scenari attività funzionano in modo indipendente e non è necessario comunicare con altre attività, ma esistono alcune applicazioni in cui attività comunicherà (ad esempio, le applicazioni utilizzando MPI).
	- Esiste una configurazione che controlla se la macchina virtuale sarà in grado di comunicare, che consente di configurare la posizione di infrastruttura e gli impatti rete sottostante della macchina virtuale.

- L'attività di avvio per le TVM nel pool.

Quando si crea un pool, è possibile specificare l'account di archiviazione con cui deve essere associato. Il servizio Batch alloca le TVM dal data center con migliori capacità di connettività di rete e larghezza di banda per l'account di archiviazione specificato. Ciò consente ai carichi di lavoro accedere ai dati in modo più efficace.

### <a name="workitem"></a>Elemento di lavoro

Un elemento di lavoro specifica la modalità di esecuzione di calcoli sulle TVM in un pool.

- Un elemento di lavoro può avere uno o più processi associati. Una pianificazione facoltativa può essere specificata per un elemento di lavoro in questo caso viene creato un processo per ogni occorrenza della pianificazione. Se viene specificata alcuna pianificazione, per il lavoro su richiesta, viene creato un processo immediatamente.
- L'elemento di lavoro specifica il pool in cui verrà eseguito il lavoro. Il pool può essere un oggetto esistente già creati pool viene utilizzato da molti elementi di lavoro, ma in alternativa possibile creare un pool per ogni processo associato all'elemento di lavoro o per tutti i processi associati all'elemento di lavoro.
- È possibile specificare facoltativamente una priorità. Quando un elemento di lavoro viene inviato con una priorità più elevata rispetto agli altri elementi di lavoro in corso, l'attività di elemento di lavoro di priorità più elevata ottenere inserita nella coda prima l'attività di elemento di lavoro di priorità inferiore. Attività di priorità inferiore che sono già in esecuzione non è rimovibile.
- È possibile specificare i vincoli che verranno applicate al processo associato o processi.
	- Per i processi, è possibile impostare un tempo massimo wallclock. Se i processi in esecuzione per più di tempo massimo wallclock specificato, verrà terminata processo e delle attività associato.
	- Batch di Azure è in grado di rilevare attività che ha esito negativo e ripetere le attività. Numero massimo di tentativi di attività può essere specificato come un vincolo predefinito specificando che è sempre di un'attività ripetuta o mai ripetuta. Nuovo tentativo in corso un'attività significa che l'attività è nuovamente in coda e verrà eseguita di nuovo.
- È possibile specificare le attività da eseguire per il processo di elemento di lavoro dal client nello stesso modo che l'elemento di lavoro è stato creato, ma in alternativa è possibile specificare un'attività di Gestione processi. Un'attività di gestione del processo utilizza l'API di Batch e contiene il codice per creare le attività richieste per un processo con l'attività in esecuzione su una macchina virtuale pool. Le attività di gestione del processo viene gestito in modo specifico dal Batch, viene messa in coda, non appena il processo viene creato e viene riavviato in caso di esito negativo per qualsiasi motivo. Un gestore di processi è necessario per gli elementi di lavoro con una pianificazione associata come è l'unico modo per definire le attività prima che viene creata un'istanza del processo.

### <a name="job"></a>Processo

Un processo è un'istanza in esecuzione di un elemento di lavoro ed è costituito da una raccolta di attività. Il servizio Batch crea un'istanza di un processo in base alla configurazione dell'elemento di lavoro. Il processo usa TVM del pool associato all'elemento di lavoro.

### <a name="task"></a>Attività

Un'attività è un'unità di calcolo che viene associata a un processo e viene eseguito su una TVM. Attività assegnate a una macchina virtuale per l'esecuzione o in coda fino a quando non diventa disponibile una macchina virtuale. Un'attività usa le risorse seguenti:

- Il programma specificato nell'elemento di lavoro.

- I file di risorse che contengono i dati da elaborare. Questi file vengono copiati automaticamente nella TVM dall'archiviazione BLOB. Per altre informazioni, vedere il file e directory.

- Le impostazioni di ambiente che sono necessarie per il programma. Per altre informazioni, vedere Impostazioni di ambiente per le attività.

- I vincoli relativi all'esecuzione del calcolo. Ad esempio, il tempo massimo in cui l'attività può essere eseguita, il numero massimo di volte in cui un'attività deve essere ritentata se non viene eseguita e il tempo massimo in cui vengono mantenuti i file nella directory di lavoro.

Oltre alle attività che è possibile definire per eseguire il calcolo su una TVM, è possibile usare le attività speciali seguenti fornite dal servizio Batch:

- [Attività di avvio](#starttask)

- [Attività di gestione del processo](#jobmanagertask)

#### <a name="starttask"></a>Attività di avvio

È possibile configurare il sistema operativo delle macchine virtuali in un pool di associare un'attività di avvio con il pool. L'installazione del software e l'avvio dei processi in background sono alcune delle azioni che un'attività di avvio può eseguire. L'inizio dell'operazione viene eseguita ogni volta che una macchina virtuale viene avviato per finché rimane nel pool.

Come con qualsiasi attività Batch un elenco di file nell'archiviazione di Azure può essere specificato oltre a una riga di comando che viene eseguita in Batch. Azure Batch verrà innanzitutto copiare i file dall'archiviazione di Azure e quindi eseguire la riga di comando. Per un'attività di avvio del pool, l'elenco dei file contiene in genere il file delle applicazioni o il pacchetto, ma può inoltre includere dati di riferimento che verranno utilizzati da tutte le attività in esecuzione nella macchina virtuale pool. La riga di comando è possibile eseguire qualsiasi script di PowerShell o robocopy, ad esempio, per copiare i file dell'applicazione nella cartella "condiviso". inoltre possibile eseguire un file MSI.

In genere è utile per Batch di attesa per l'attività di avvio completare e quindi prendere in considerazione la macchina virtuale pronta per l'assegnazione attività, ma questo è configurabile.

Se un'attività di avvio non riesce per un pool di macchine Virtuali, lo stato della macchina virtuale viene aggiornato per riflettere l'errore e la macchina virtuale non sarà disponibile per le attività da assegnare. Un'attività di avvio potrebbe non riuscire se si verifica un problema di copia di file specificati per l'attività di avvio o il processo di avvio attività restituisce diverso da zero.

Il fatto che tutte le informazioni necessarie per configurare la macchina virtuale e installare le applicazioni viene dichiarate significa che l'aumento del numero di macchine Virtuali in un pool è semplice come specificare il nuovo numero richiesto; Batch contiene tutte le informazioni necessarie per configurare la macchina virtuale e renderle pronto ad accettare l'attività.

Un'attività di avvio viene definita mediante l'aggiunta di una sezione JSON per il corpo della richiesta per l'operazione Aggiungi Pool. Nell'esempio seguente viene illustrata una definizione di base di un'attività di avvio:

	{
		“commandLine”:”mypoolsetup.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”mypoolsetup.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“maxTaskRetryCount”:0
	}

Un'interfaccia c# è simile al seguente:

	ICloudPool pool = pm.CreatePool(poolName, targetDedicated: 3, vmSize: "small", osFamily: "3");
	pool.StartTask = new StartTask();
	pool.StartTask.CommandLine = "mypoolsetup.exe";
	pool.StartTask.ResourceFiles = new List<IResourceFile>();
	pool.StartTask.ResourceFiles.Add(new ResourceFile("http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d", "mypoolsetup.exe"));
	pool.Commit();


#### <a name="jobmanagertask"></a>Attività di gestione del processo

Un'attività di gestione dei processi viene avviata prima di tutte le altre attività e offre i vantaggi seguenti:

- Viene creata automaticamente dal servizio Batch quando viene creato il processo.

- Viene pianificata prima di altre attività del processo.

- La TVM a essa associata è l'ultima da rimuovere da un pool quando il pool viene ridimensionato.

- A essa viene assegnata la priorità più alta quando deve essere riavviata. Se una TVM inattiva non è disponibile, il servizio Batch può terminare una delle attività in esecuzione nel pool per consentire l'esecuzione della TVM.

- La sua terminazione può essere associata alla terminazione di tutte le attività del processo.

Un'attività di gestione dei processi in un processo non ha priorità sulle attività di altri processi. Tra i processi, è possibile osservare solo priorità a livello di processo. Un'attività di gestione dei processi viene definita mediante l'aggiunta di una sezione XML al corpo della richiesta per l'operazione Aggiungi elementi di lavoro. Nell'esempio seguente viene illustrata una definizione di base di un'attività di gestione dei processi:

	{
		“name”:”jmTask”,
		“commandLine”:”myapp1.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp1.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“taskConstraints”:
		{
			“maxWallClockTime”:”PT1H”,
			“maxTaskRetryCount”:0,
			“retentionTime”:”PT1H”
		},
		“killJobOnCompletion”:true,
		“runElevated”:false,
		“runExclusive”:true
	}




## <a name="workflow"></a>Flusso di lavoro del servizio Batch

Per usare il servizio Batch è necessario un account di Batch e per pianificare il calcolo si usano più risorse del servizio. Quando si crea un scenario di calcolo distribuito con il servizio Batch, usare il seguente flusso di lavoro di base:

1. caricare i file che si desidera utilizzare nello scenario di calcolo distribuito a un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Il servizio Batch li carica in una TVM quando viene eseguita l'attività.

2. caricare i file binari dipendenti nell'account di archiviazione. I file binari includono il programma che viene eseguito dall'attività e gli assembly dipendenti. Questi file devono inoltre essere accessibili dalla risorsa di archiviazione ed essere caricati nella TVM.

3. creare un pool di TVMs. È possibile assegnare la dimensione della macchina virtuale delle attività da usare durante la creazione del pool. Quando viene eseguita un'attività, le viene assegnata una TVM da questo pool.

4. creare un elemento di lavoro. Un processo viene automaticamente creato quando si crea un elemento di lavoro. Un elemento di lavoro consente di gestire un processo di attività.

5. aggiungere attività all'elemento di lavoro. Ogni attività usa il programma caricato per elaborare le informazioni da un file caricato.

6. monitorare i risultati dell'output.

## <a name="files"></a>File e directory

Ogni attività dispone di una directory di lavoro in cui può creare directory e file per archiviare il programma eseguito da un'attività, i dati elaborati da un'attività e l'output dell'elaborazione eseguita da un'attività. Tali directory e file sono quindi disponibili per l'uso da altre attività durante l'esecuzione di un processo. Tutte le attività e le directory nonché tutti i file di una TVM sono di proprietà di un singolo account utente.

Il servizio Batch espone una parte del file system a una TVM come directory radice. La directory radice del TVM è disponibile per un'attività tramite la variabile di ambiente WATASK_TVM_ROOT_DIR. Per altre informazioni sull'uso delle variabili di ambiente, vedere Impostazioni di ambiente per le attività.

La directory radice contiene le sottodirectory seguenti:

- **Attività** – questo percorso è tutti i file di archiviazione che appartengono alle attività eseguite nel TVM. Per ogni attività, il servizio di Batch crea una directory di lavoro con il percorso nel formato % WATASK_TVM_ROOT_DIR%/tasks/workitemName/jobName/taskName/ univoco. Questa directory fornisce accesso in lettura/scrittura all'attività. L'attività può creare, leggere, aggiornare ed eliminare i file in tale directory e questa directory viene mantenuta in base al vincolo RetentionTime specificato per l'attività.

- **Shared** – questo percorso è una directory condivisa per tutte le attività con l'account. In TVM, la directory condivisa è % WATASK_TVM_ROOT_DIR%/shared. Questa directory fornisce accesso in lettura/scrittura all'attività. L'attività può creare, leggere, aggiornare ed eliminare file in tale directory.

- **Avviare** – questo percorso viene utilizzato da un'attività di avvio come cartella di lavoro. Anche tutti i file scaricati dal servizio Batch per avviare l'attività di avvio vengono archiviati in tale directory. Su TVM, la directory di avvio è % WATASK_TVM_ROOT_DIR%/start. L'attività può creare, leggere, aggiornare ed eliminare i file in tale directory e questa directory può essere usata dalle attività di avvio per configurare il sistema operativo.

Quando una TVM viene rimossa dal pool, vengono rimossi tutti i file archiviati nella TVM.

## <a name="lifetime"></a>Pool e la durata di macchina virtuale

Una decisione di progettazione fondamentale è quando vengono creati pool e la durata della macchina virtuale viene mantenuta disponibile.

A un'estremità è possibile creare un pool per ogni processo durante il processo viene inviato e la macchina virtuale rimosso come attività di fine dell'esecuzione. Ciò consentirà di ottimizzare l'utilizzo come la macchina virtuale viene allocata solo quando strettamente necessario e arresto del sistema, non appena diventano inattivi. Questo significa che il processo necessario attendere della macchina virtuale da allocare, anche se è importante notare che attività verrà programmato per della macchina virtuale, non appena sono disponibili singolarmente, allocato e l'inizio dell'operazione è stata completata; ad esempio Batch non attende finché tutte le macchine Virtuali in un pool sono disponibili come che potrebbe causare l'utilizzo non appropriato.

Se con i processi di avvio immediato dell'esecuzione è la priorità, è necessario creare un pool e VM disponibile della prima che venga inviato il processo. Le attività possono avviare immediatamente, ma potrebbe essere inattiva in attesa per le attività di processo, a seconda del carico della macchina virtuale.

In genere di schema quando esiste una quantità di carico in corso variabile consiste nel disporre di un pool in cui vengono inviati più processi, ma la scalabilità verso l'alto o verso il basso il numero di macchine Virtuali in base a carico; è possibile utilizzare obbligatoriamente o Pro-attivamente se carico può essere previsto.

## <a name="scaling"></a>Scalabilità delle applicazioni

Il numero di istanze di un'applicazione può essere facilmente aumentato o ridotto in modo automatico per consentire il calcolo necessario. È possibile regolare automaticamente il numero di TVM in un pool in base alle statistiche correnti del carico di lavoro e di utilizzo delle risorse. È inoltre possibile ottimizzare il costo complessivo dell'esecuzione dell'applicazione configurandone la scalabilità automatica. Quando si crea un pool è possibile specificarne le impostazioni di scalabilità e aggiornare la configurazione in qualsiasi momento.

Per una riduzione del numero di macchine Virtuali, potrebbe essere in esecuzione della macchina virtuale che devono essere considerati attività. Un criterio di deallocazione viene specificato che determina se l'attività in esecuzione vengono arrestate per rimuovere la macchina virtuale immediatamente o se sono consentite operazioni per il completamento prima che la macchina virtuale viene rimosse. Impostazione del numero di destinazione della macchina virtuale a zero alla fine di un processo, ma consentendo l'esecuzione di attività da completare, consentirà di ottimizzare l'utilizzo.

È possibile specificare la scalabilità automatica di un'applicazione mediante un set di formule di scalabilità, che possono essere usate per determinare il numero di TVM presenti nel pool per il successivo intervallo di scalabilità. Ad esempio, per inviare un numero elevato di attività da eseguire in un pool, è possibile assegnare al pool una formula di scalabilità che specifichi la dimensione del pool in base al numero corrente di attività in sospeso e alla percentuale di completamento delle attività. Il servizio Batch valuta periodicamente la formula e ridimensiona il pool in base al carico di lavoro.

Una formula può essere basata sulle seguenti metriche:

- **Metriche temporali** – in base alle statistiche raccolte ogni cinque minuti nel numero specificato di ore.

- **Metriche delle risorse** – in base all'utilizzo della CPU, l'utilizzo della larghezza di banda, l'utilizzo della memoria e numero di TVMs.

- **Attività metriche** : in base allo stato delle attività, ad esempio attivo, in sospeso e completato.

Per altre informazioni sulla scalabilità di un'applicazione, vedere la sezione relativa alla configurazione della scalabilità automatica delle macchine virtuali delle attività.

>Eliminazione della macchina virtuale
>
>Non è spesso necessaria, ma è possibile specificare singoli della macchina virtuale da rimuovere da un pool. Se è presente una macchina virtuale che si sospetta sia minore affidabili potrebbe essere rimosso, ad esempio.

## <a name="cert"></a>Certificati per le applicazioni

In genere, è necessario usare certificati per crittografare informazioni segrete. I certificati possono essere installati nelle TVM. I segreti crittografati vengono passati alle attività nei parametri della riga di comando o incorporati in una delle risorse e i certificati installati possono essere usati per decrittografarli. Un esempio di informazione segreta è la chiave per un account di archiviazione.

Usare l'operazione Aggiungi certificato per aggiungere un certificato a un account di Batch. È quindi possibile associare il certificato a un pool nuovo o esistente. Quando un certificato è associato a un pool, il servizio Batch installa il certificato in ogni TVM nel pool. Il servizio Batch consente di installare i certificati appropriati all'avvio della TVM, prima che vengano avviate le attività, che include le attività di avvio e le attività di gestione dei processi.

## <a name="scheduling"></a>Priorità di pianificazione

Quando si crea un elemento di lavoro, è possibile assegnare a esso una priorità. Ogni processo dell'elemento di lavoro viene creato con questa priorità. Il servizio Batch usa i valori di priorità del processo per determinare l'ordine di pianificazione dei processi all'interno di un account. I valori di priorità possono variare da -1000 a 1000, dove -1000 è la priorità più bassa e 1000 la priorità più alta. È possibile aggiornare la priorità di un processo usando l'operazione di aggiornamento del processo.

All'interno dello stesso account i processi con priorità più alta hanno precedenza di pianificazione rispetto ai processi con priorità inferiori. Un processo con un valore di priorità più elevato in un account non dispone di tale precedenza di pianificazione rispetto a un altro processo con un valore di priorità inferiore in un account diverso.

Le pianificazioni dei processi eseguite su pool diversi sono indipendenti. In pool diversi non è garantito che un processo con priorità più elevato venga pianificato per primo se il relativo pool associato non dispone di un numero sufficiente di TVM inattive. Nello stesso pool i processi con lo stesso livello di priorità hanno la stessa probabilità di pianificazione.

## <a name="environment"></a>Impostazioni di ambiente per attività

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
  WATASK_PORTRANGE_LOW WATASK_PORTRANGE_HIGH
</td>
<td>
  L'intervallo di porte, inclusi i valori iniziale e finale, che l'attività può usare per la comunicazione quando è abilitata la comunicazione all'interno dello stesso pool.
</td>
</table>

**Nota**

Non è possibile sovrascrivere queste variabili definite dal sistema.

È possibile recuperare il valore delle impostazioni di ambiente mediante l'operazione di ottenimento dell'attività.

## <a name="errorhandling"></a>Gestione degli errori

###Gestione degli errori di attività
Gli errori di attività rientrano nelle categorie seguenti:

- Gli errori di programmazione:
	- Se i file vengono specificati per l'attività, la copia di uno o più file potrebbe non riuscire. È possibile che i file sono stati spostati, l'account di archiviazione non è più disponibile e così via.
	- "Errore pianificazione" è impostato per l'attività in questo caso.
- Errori di applicazione:
	- Il processo di attività specificato da riga di comando può non riuscire. Il processo viene considerato riuscito quando viene restituito un codice di uscita diverso da zero.
	- Per gli errori delle applicazioni è possibile configurare Batch per ripetere automaticamente l'attività di un numero di volte specificato. 
- Errori relativi ai vincoli:
	- Un vincolo può essere specificato per la quantità massima di tempo per che possono essere eseguite da un processo o un'attività. Il può essere utile per terminare un'attività che si è bloccato.
	- Quando viene superata la quantità massima di tempo, quindi l'attività viene contrassegnata come completata ma uscita codice verrà contrassegnato come `0xC000013A` e schedulingError campo sarà contrassegnato come `{ category:“ServerError”, code=“TaskEnded”}`.

###Debug degli errori delle applicazioni

Un'applicazione può produrre diagnostica che può essere utilizzato per risolvere i problemi. Spesso le applicazioni scrive informazioni di stdout e stderr file o un output di file personalizzati. In questi casi viene fornita un'API per ottenere i file, specificando l'attività o una macchina virtuale.

È inoltre possibile eseguire l'accesso al pool macchina virtuale. Un'API restituisce il file RDP per una macchina virtuale, che può quindi essere utilizzata per l'accesso alla macchina virtuale.

###Per gli errori di attività e problemi di cucina

Attività può avere esito negativo o essere interrotta per diversi motivi. L'applicazione dell'attività stessa potrebbe non riuscire, ottiene riavviare la macchina virtuale in cui è in esecuzione l'attività o la macchina virtuale viene rimosso da un ridimensionamento pool con i criteri di deallocazione impostati per rimuovere immediatamente la macchina virtuale senza attendere il completamento dell'attività. In tutti i casi l'attività può essere automaticamente nuovamente in coda dal Batch ed eseguire in un'altra macchina virtuale.

È inoltre possibile per un problema intermittente per fare in modo di blocco o richiedono troppo tempo per eseguire un'attività. Tempo massimo di esecuzione può essere impostato per un'attività e se superata la soglia Batch verrà interrotto l'applicazione di attività. Attualmente, l'accodamento nuovamente automatica non è possibile in questo caso, ma il caso può essere rilevato dal client che possono inviare una nuova attività.

###Cucina per "Bad" VM

Ogni macchina virtuale in un pool viene assegnato un nome univoco e la macchina virtuale in cui viene eseguita un'attività inclusa nei metadati di attività. Nel caso in cui è presente una macchina virtuale che per qualche motivo causa attività esito negativo, quindi può essere stabilita dal client e sospetto che macchina virtuale eliminata dal pool. Se un'attività è in esecuzione nella macchina virtuale che è stata eliminata, quindi verrà automaticamente nuovamente in coda ed eseguito in un'altra macchina virtuale.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[Panoramica Azure Batch]: batch-technical-overview.md

<!---HONumber=GIT-SubDir-->