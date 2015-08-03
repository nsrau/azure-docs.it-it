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

- [Macchina virtuale delle attività](#taskvm)

- [Pool](#pool)

- [Elemento di lavoro](#workitem)

- [Processo](#job)

- [Attività](#task)

	- [Attività di avvio](#starttask)
	
	- [Attività di gestione dei processi](#jobmanagertask)

### <a name="account"></a>Account

Un account di Batch è un'entità identificata in modo univoco all'interno del servizio Batch. Tutte le operazioni di elaborazione avvengono tramite un account di Batch. Quando si eseguono operazioni con il servizio Batch, è necessario il nome dell'account e la chiave per l'account. Per creare un account Batch, fare riferimento alla sezione account Batch di [Panoramica di Azure Batch][].


### <a name="taskvm"></a>Macchina virtuale delle attività

Una macchina virtuale delle attività (TVM) è una macchina virtuale di Azure dedicata a un carico di lavoro specifico per l'applicazione. La dimensione di una TVM determina il numero di core CPU, la capacità di memoria e la dimensione del file system locale allocati alla TVM. Una TVM può essere una macchina virtuale di piccole dimensioni, grandi o grandissime dimensioni, come descritto in [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/dn197896.aspx).

I tipi di programmi che una TVM può eseguire includono file eseguibili (.exe), file di comando (.cmd), file batch (.bat) e file script. Una TVM presenta inoltre gli attributi seguenti:

- Cartelle del file system che sono allo stesso tempo specifiche delle attività e condivise. In ogni VM del pool vengono create una struttura di cartelle e variabili di ambiente. La struttura di cartelle seguente viene creata con una cartella "shared" per applicazioni e dati condivisi tra attività e una cartella per ogni attività.

![][1]

- File stdout.txt e stderr.txt che vengono scritti in una cartella di attività specifiche

- Variabili di ambiente per l'elaborazione

- Impostazioni del firewall sono configurate per controllare l'accesso

>Accesso alla VM
>
>Se l'accesso a una VM è obbligatorio, ad esempio per il debug, è possibile ottenere il file RDP, che può essere quindi usato per accedere alla VM tramite Desktop remoto.


### <a name="pool"></a>Pool

Un pool è una raccolta di TVMs in cui viene eseguita l'applicazione. Il pool può essere creato dall'utente o viene creato automaticamente dal servizio Batch quando si specifica il lavoro da eseguire. È possibile creare e gestire un pool che soddisfi le esigenze dell'applicazione. Un pool può essere usato solo dall'account Batch in cui è stato creato. Un account Batch può avere più pool.

I pool di Azure Batch sono basati sulla piattaforma di calcolo di Azure e offrono allocazione su larga scala, installazione di applicazioni e dati, spostamento di dati, monitoraggio dell'integrità e scalabilità flessibile delle VM.

A ogni TVM aggiunta a un pool viene assegnato un nome univoco e un indirizzo IP associato. Quando una TVM viene rimossa da un pool, perde le modifiche apportate al sistema operativo, tutti i relativi file locali, il nome e il relativo indirizzo IP. Quando una TVM esce da un pool, la sua durata è terminata.

È possibile configurare un pool per consentire la comunicazione tra TVM al suo interno. Se è richiesta la comunicazione all'interno dello stesso pool per un pool, il servizio Batch abilita un numero di porte superiore a 1100 su ogni TVM nel pool. Ogni TVM nel pool è configurata per consentire e limitare le connessioni in ingresso per questo intervallo di porte e solo da altre TVM nel pool. Se l'applicazione non richiede la comunicazione tra TVM, il servizio Batch è in grado di allocare al pool un numero elevato di TVM fra diversi cluster o data center per consentire una maggiore elaborazione parallela.

Quando si crea un pool, è possibile specificare gli attributi seguenti:

- Le **dimensioni delle VM** nel pool.
	- È necessario scegliere dimensioni appropriate per le VM, in base alle caratteristiche e ai requisiti delle applicazioni da usare sulle VM. In genere, le dimensioni delle VM vengono selezionate presupponendo che sulla VM verrà eseguita un'attività alla volta. Ad esempio, la scelta delle dimensioni più adatte e convenienti per le VM dipende dal fatto che l'applicazione sia multi-thread e dalla quantità di memoria richiesta. È possibile che vengano assegnate più attività e che più istanze di applicazione vengano eseguite in parallelo. In questo caso, verrà in genere scelta una VM di dimensioni maggiori. Vedere più avanti nella sezione relativa al numero massimo di attività per ogni VM. 
	- Tutte le VM di un pool devono avere le stesse dimensioni. Se è necessario eseguire applicazioni diverse con requisiti di sistema diversi e/o con carichi diversi, è consigliabile creare pool separati.
	- È possibile configurare per un pool tutte le dimensioni di VM del servizio cloud, ad eccezione di A0.

- La famiglia e la versione del sistema operativo in esecuzione nelle VM.
	- Analogamente ai ruoli di lavoro, è possibile configurare la famiglia e la versione del sistema operativo.
	- La famiglia del sistema operativo determina anche le versioni di .NET installate con il sistema operativo.
	- Analogamente ai ruoli di lavoro, per la versione del sistema operativo è consigliabile usare "*", in modo che le VM vengano aggiornate automaticamente e non sia necessaria alcuna modifica per le nuove versioni. Il motivo principale per la selezione di una versione specifica del sistema operativo consiste nell'assicurare il mantenimento della compatibilità delle applicazioni, permettendo l'esecuzione del test di compatibilità con le versioni precedenti prima di consentire l'aggiornamento della versione. Dopo la convalida, la versione del sistema operativo per il pool può essere aggiornata ed è possibile installare la nuova immagine del sistema operativo. Eventuali attività in esecuzione verranno interrotte e accodate di nuovo.

- Il numero di macchine virtuali di destinazione che devono essere disponibili per il pool.

- I criteri di scalabilità per il pool. Oltre al numero di VM, è possibile specificare una formula di scalabilità automatica per ogni pool. Il servizio Batch eseguirà la formula per adattare il numero di VM in base al pool e alle statistiche relative agli elementi di lavoro.

- Configurazione della pianificazione
	- La configurazione predefinita prevede l'esecuzione di un'attività alla volta in una VM del pool, ma in alcuni scenari risulta utile eseguire più di un'attività contemporaneamente in una VM, ad esempio per incrementare l'utilizzo della VM se un'applicazione deve rimanere in attesa di I/O. L'esecuzione di più applicazioni incrementerà l'utilizzo della CPU. È anche possibile, ad esempio, ridurre il numero di VM nel pool, in modo da ridurre la quantità di copie di dati necessarie per set di dati di riferimento di grandi dimensioni. Se una dimensione A1 può risultare idonea per l'applicazione, sarà possibile scegliere A4 e impostare la configurazione per l'esecuzione di 8 attività contemporaneamente, ognuna delle quali utilizza un core.
	- La configurazione di tipo "numero massimo di attività per ogni VM" determina il numero massimo di attività che è possibile eseguire in parallelo.
	- È anche possibile specificare un "criterio di riempimento", che determina se Batch riempie ogni VM a partire dalla prima o se le attività vengono distribuite in tutte le VM.
 
- Lo stato delle comunicazioni delle VM nel pool.
 	- In gran parte degli scenari le attività funzionano in modo indipendente e non devono comunicare con altre attività, ma in alcune applicazioni le attività comunicano, ad esempio nelle applicazioni che usano MPI.
	- È possibile usare una configurazione che controlla se le VM saranno in grado di comunicare e che permette di definire l'infrastruttura della rete sottostante e influisce sul posizionamento delle VM.

- L'attività di avvio per le TVM nel pool.

Quando si crea un pool, è possibile specificare l'account di archiviazione con cui deve essere associato. Il servizio Batch alloca le TVM dal data center con migliori capacità di connettività di rete e larghezza di banda per l'account di archiviazione specificato. Ciò consente ai carichi di lavoro accedere ai dati in modo più efficace.

### <a name="workitem"></a>Elemento di lavoro

Un elemento di lavoro specifica la modalità di esecuzione di calcoli sulle TVM in un pool.

- Un elemento di lavoro può avere uno o più processi associati. È possibile specificare una pianificazione facoltativa per un elemento di lavoro. In questo caso viene creato un processo per ogni occorrenza nella pianificazione. Se non viene specificata alcuna pianificazione, per un lavoro su richiesta viene creato immediatamente un processo.
- L'elemento di lavoro specifica il pool in cui verrà eseguito il lavoro. Il pool può essere un pool esistente, già creato, usato da molti elementi di lavoro, ma è anche possibile creare un pool per ogni processo associato all'elemento di lavoro o per tutti i processi associati all'elemento di lavoro.
- È possibile specificare una priorità facoltativa. Quando viene inviato un elemento di lavoro con priorità superiore rispetto agli altri elementi di lavoro ancora in corso, l'elemento di lavoro con priorità più elevata verrà inserito nella coda davanti alle attività degli elementi di lavoro con priorità minore. Le attività con priorità minore già in esecuzione non verranno messe in attesa.
- È possibile specificare vincoli da applicare al processo o ai processi associati.
	- È possibile impostare un tempo reale massimo per i processi. Se l'esecuzione dei processi supera il tempo reale specificato, il processo e tutte le attività associate verranno terminati.
	- Azure Batch può rilevare attività con esito negativo e provare a eseguirle di nuovo. Il numero massimo predefinito di nuovi tentativi per l'attività può essere specificato sotto forma di vincolo ed è anche possibile specificare che è necessario effettuare sempre nuovi tentativi per un'attività o non effettuarli mai. Per nuovo tentativo si intende che l'attività viene riaccodata e verrà eseguita di nuovo.
- Le attività da eseguire per il processo dell'elemento di lavoro possono essere specificate dal client in modo analogo alla creazione dell'elemento di lavoro, ma è anche possibile specificare un'attività di tipo Gestore di processi. Un'attività di tipo Gestore di processi usa l'API Batch e contiene la coda per la creazione delle attività necessarie per un processo con l'attività in esecuzione in una delle VM del pool. L'attività di tipo Gestore di processi viene gestita in modo specifico da Batch, ovvero viene accodata non appena viene creato il processo e viene riavviata in caso di esito negativo per qualsiasi motivo. Un Gestore di processi è necessario per gli elementi di lavoro con pianificazione associata, poiché costituisce l'unico modo per definire le attività prima di creare le istanze di un processo.

### <a name="job"></a>Processo

Un processo è un'istanza in esecuzione di un elemento di lavoro ed è costituito da una raccolta di attività. Il servizio Batch crea un'istanza di un processo in base alla configurazione dell'elemento di lavoro. Il processo usa TVM del pool associato all'elemento di lavoro.

### <a name="task"></a>Attività

Un'attività è un'unità di calcolo che viene associata a un processo e viene eseguito su una TVM. Le attività vengono assegnate a una VM per l'esecuzione o vengono accodate fino a quando non diventa disponibile una VM. Un'attività usa le risorse seguenti:

- Il programma specificato nell'elemento di lavoro.

- I file di risorse che contengono i dati da elaborare. Questi file vengono copiati automaticamente nella TVM dall'archiviazione BLOB. Per altre informazioni, vedere il file e directory.

- Le impostazioni di ambiente che sono necessarie per il programma. Per altre informazioni, vedere Impostazioni di ambiente per le attività.

- I vincoli relativi all'esecuzione del calcolo. Ad esempio, il tempo massimo in cui l'attività può essere eseguita, il numero massimo di volte in cui un'attività deve essere ritentata se non viene eseguita e il tempo massimo in cui vengono mantenuti i file nella directory di lavoro.

Oltre alle attività che è possibile definire per eseguire il calcolo su una TVM, è possibile usare le attività speciali seguenti fornite dal servizio Batch:

- [Attività di avvio](#starttask)

- [Attività di gestione dei processi](#jobmanagertask)

#### <a name="starttask"></a>Attività di avvio

È possibile configurare il sistema operativo delle VM in un pool associando un'attività di avvio con il pool. L'installazione del software e l'avvio dei processi in background sono alcune delle azioni che un'attività di avvio può eseguire. L'attività di avvio viene eseguita a ogni avvio di una VM per tutto il tempo in cui quest'ultima rimane nel pool.

Analogamente a qualsiasi attività Batch, è possibile specificare un elenco di file in Archiviazione di Azure oltre a una riga di comando eseguita da Batch. Azure Batch copierà prima di tutto i file da Archiviazione di Azure e quindi eseguirà la riga di comando. Per un'attività di avvio del pool, l'elenco di file include in genere i file delle applicazioni o un pacchetto, ma può anche includere dati di riferimento, che verranno usati da tutte le attività in esecuzione nelle VM del pool. La riga di comando può eseguire qualsiasi script di PowerShell o eseguire robocopy, ad esempio, per copiare i file dell'applicazione nella cartella "condivisa". Può anche eseguire un file MSI.

In genere è consigliabile che Batch attenda il completamento dell'attività di avvio e quindi consideri la VM pronta per l'assegnazione di attività, ma è possibile configurare questo comportamento.

Se un'attività di avvio ha esito negativo per una VM del pool, lo stato della VM verrà aggiornato per riflettere l'errore e la VM non sarà disponibile per l'assegnazione di attività. Un'attività di avvio può avere esito negativo in caso di problemi durante la copia dei file specificati per l'attività di avvio o se il processo dell'attività di avvio restituisce un valore diverso da zero.

Il fatto che tutte le informazioni necessarie per configurare le VM e installare le applicazioni siano dichiarate significa che l'aumento del numero di VM in un pool è semplice quanto specificare il nuovo numero necessario. Batch ha tutte le informazioni necessarie per configurare le VM e prepararle all'accettazione di attività.

Un'attività di avvio viene definita mediante l'aggiunta di una sezione JSON al corpo della richiesta per l'operazione Aggiungi pool. L'esempio seguente illustra una definizione di base di un'attività di avvio:

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

Un'interfaccia C# ha un aspetto analogo al seguente:

	ICloudPool pool = pm.CreatePool(poolName, targetDedicated: 3, vmSize: "small", osFamily: "3");
	pool.StartTask = new StartTask();
	pool.StartTask.CommandLine = "mypoolsetup.exe";
	pool.StartTask.ResourceFiles = new List<IResourceFile>();
	pool.StartTask.ResourceFiles.Add(new ResourceFile("http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d", "mypoolsetup.exe"));
	pool.Commit();


#### <a name="jobmanagertask"></a>Attività di gestione dei processi

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

1. Caricare i file da usare nello scenario di calcolo distribuito in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. Il servizio Batch li carica in una TVM quando viene eseguita l'attività.

2. Caricare i file binari dipendenti nell'account di archiviazione. I file binari includono il programma che viene eseguito dall'attività e gli assembly dipendenti. Questi file devono inoltre essere accessibili dalla risorsa di archiviazione ed essere caricati nella TVM.

3. Creare un pool di TVM. È possibile assegnare la dimensione della macchina virtuale delle attività da usare durante la creazione del pool. Quando viene eseguita un'attività, le viene assegnata una TVM da questo pool.

4. Creare un elemento di lavoro. Un processo viene automaticamente creato quando si crea un elemento di lavoro. Un elemento di lavoro consente di gestire un processo di attività.

5. Aggiungere le attività all'elemento di lavoro. Ogni attività usa il programma caricato per elaborare le informazioni da un file caricato.

6. Monitorare i risultati dell'output.

## <a name="files"></a>File e directory

Ogni attività dispone di una directory di lavoro in cui può creare directory e file per archiviare il programma eseguito da un'attività, i dati elaborati da un'attività e l'output dell'elaborazione eseguita da un'attività. Tali directory e file sono quindi disponibili per l'uso da altre attività durante l'esecuzione di un processo. Tutte le attività e le directory nonché tutti i file di una TVM sono di proprietà di un singolo account utente.

Il servizio Batch espone una parte del file system a una TVM come directory radice. La directory radice della TVM è disponibile per un'attività tramite la variabile di ambiente WATASK_TVM_ROOT_DIR. Per altre informazioni sull'uso delle variabili di ambiente, vedere Impostazioni di ambiente per le attività.

La directory radice contiene le sottodirectory seguenti:

- **Attività**: il percorso in cui vengono archiviati tutti i file che appartengono alle attività eseguite sulla TVM. Per ogni attività, il servizio Batch crea una directory di lavoro con il percorso univoco nel formato %WATASK_TVM_ROOT_DIR%/tasks/workitemName/jobName/taskName/. Questa directory fornisce accesso in lettura/scrittura all'attività. L'attività può creare, leggere, aggiornare ed eliminare i file in tale directory e questa directory viene mantenuta in base al vincolo RetentionTime specificato per l'attività.

- **Condiviso**: questo percorso è una directory condivisa per tutte le attività dell'account. Nella TVM la directory condivisa è % %WATASK_TVM_ROOT_DIR%/shared. Questa directory fornisce accesso in lettura/scrittura all'attività. L'attività può creare, leggere, aggiornare ed eliminare file in tale directory.

- **Start**: questo percorso viene usato da un'attività di avvio come directory di lavoro. Anche tutti i file scaricati dal servizio Batch per avviare l'attività di avvio vengono archiviati in tale directory. Nella TVM la directory di avvio è %WATASK_TVM_ROOT_DIR%/start. L'attività può creare, leggere, aggiornare ed eliminare i file in tale directory e questa directory può essere usata dalle attività di avvio per configurare il sistema operativo.

Quando una TVM viene rimossa dal pool, vengono rimossi tutti i file archiviati nella TVM.

## <a name="lifetime"></a>Durata del pool e della macchina virtuale

Una decisione di progettazione fondamentale è rappresentata dal momento di creazione dei pool e dal tempo per cui le VM vengono mantenute disponibili.

È ad esempio possibile creare un pool per ogni processo quando il processo viene inviato e rimuovere le VM al termine dell'esecuzione delle attività. Ciò consente di massimizzare l'utilizzo, poiché le VM vengono allocate solo quando assolutamente necessario e vengono arrestate non appena risultano inattive. Ciò significa che il processo deve attendere l'allocazione delle VM, anche se è importante notare che le attività verranno pianificate e allocate nelle VM non appena risultano individualmente disponibili e dopo il completamento dell'attività di avvio. Ad esempio, Batch NON attende che tutte le VM di un pool siano disponibili, poiché ciò comporterebbe un utilizzo non ottimale.

Se l'avvio immediato dell'esecuzione dei processi è una priorità, è consigliabile creare un pool e rendere disponibili le VM prima dell'invio di un processo. Le attività possono essere avviate immediatamente, ma è possibile che le VM siano inattive in attesa di attività dei processi, a seconda del carico.

Un modello comune per situazioni di quantità variabili di carico in corso consiste nel creare un pool a cui vengono inviati più processi, ma aumentare o ridurre il numero di VM in base al carico in modo reattivo o proattivo, se è possibile prevedere il carico.

## <a name="scaling"></a>Scalabilità delle applicazioni

Il numero di istanze di un'applicazione può essere facilmente aumentato o ridotto in modo automatico per consentire il calcolo necessario. È possibile regolare automaticamente il numero di TVM in un pool in base alle statistiche correnti del carico di lavoro e di utilizzo delle risorse. È inoltre possibile ottimizzare il costo complessivo dell'esecuzione dell'applicazione configurandone la scalabilità automatica. Quando si crea un pool è possibile specificarne le impostazioni di scalabilità e aggiornare la configurazione in qualsiasi momento.

Per una riduzione nel numero di VM, è possibile che nelle VM siano in esecuzione attività che devono essere prese in considerazione. Viene specificato un criterio di deallocazione, che determina se le attività in esecuzione vengono arrestate per rimuovere immediatamente la VM o se si consentirà il completamento delle attività prima della rimozione delle VM. Per massimizzare l'utilizzo, impostare il numero di VM su zero alla fine di un processo, ma consentire il completamento delle attività in esecuzione.

È possibile specificare la scalabilità automatica di un'applicazione mediante un set di formule di scalabilità, che possono essere usate per determinare il numero di TVM presenti nel pool per il successivo intervallo di scalabilità. Ad esempio, per inviare un numero elevato di attività da eseguire in un pool, è possibile assegnare al pool una formula di scalabilità che specifichi la dimensione del pool in base al numero corrente di attività in sospeso e alla percentuale di completamento delle attività. Il servizio Batch valuta periodicamente la formula e ridimensiona il pool in base al carico di lavoro.

Una formula può essere basata sulle seguenti metriche:

- **Metriche temporali**: basate sulle statistiche raccolte ogni cinque minuti nel numero di ore specificato.

- **Metriche delle risorse**: basate su utilizzo di CPU, larghezza di banda, memoria e numero di TVM.

- **Metriche delle attività**: basate sullo stato delle attività, ad esempio Attiva, In sospeso e Completata.

Per altre informazioni sulla scalabilità di un'applicazione, vedere la sezione relativa alla configurazione della scalabilità automatica delle macchine virtuali delle attività.

>Eliminare le macchine virtuali
>
>Non è sempre necessario, ma è possibile specificare singole VM da rimuovere da un pool. Se si sospetta che una VM sia meno affidabile, è possibile rimuoverla, ad esempio.

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

###Gestione degli errori delle attività
Gli errori delle attività rientrano nelle categorie seguenti:

- Errori di pianificazione:
	- Se vengono specificati file per l'attività, è possibile che la copia di uno o più file non riesca, ad esempio perché i file sono stati spostati, l'account di archiviazione non è più disponibile e così via.
	- In questo caso viene impostato un "errore di pianificazione" per l'attività.
- Errori delle applicazioni:
	- Anche il processo dell'attività specificato dalla riga di comando può avere esito negativo. Il processo viene considerato non riuscito quando viene restituito un codice di uscita diverso da zero.
	- Per gli errori delle applicazioni è possibile configurare Batch in modo che riprovi automaticamente a eseguire l'attività per un numero di volte specificato. 
- Errori relativi ai vincoli:
	- È possibile specificare un vincolo per la quantità massima di tempo di esecuzione per un processo o un'attività. Ciò può risultare utile per terminare un'attività bloccata.
	- Quando viene superata la quantità massima di tempo, l'attività viene contrassegnata come completata ma il codice di uscita sarà contrassegnato come `0xC000013A` e il campo schedulingError sarà contrassegnato come `{ category:“ServerError”, code=“TaskEnded”}`.

###Debug degli errori delle applicazioni

Un'applicazione può produrre dati di diagnostica che possono essere usati per la risoluzione dei problemi. Le applicazioni scrivono spesso informazioni nei file stdout e stderr o forniscono output a fine personalizzati. In questi casi viene fornita un'API per ottenere i file, specificando l'attività o la VM.

È anche possibile accedere alle VM del pool. Un'API restituisce il file RDP per una VM, che può essere quindi usato per l'accesso alla VM.

###Gestione di errori e problemi delle attività

Le attività possono avere esito negativo o possono essere interrotte per alcuni motivi. È possibile che si verifichi un errore dell'applicazione dell'attività stessa, che la VM in cui è in esecuzione l'attività venga riavviata o che la VM venga rimossa da un ridimensionamento del pool con criterio di deallocazione impostato sulla rimozione immediata della VM senza attendere il completamento dell'attività. In tutti questi casi, Batch può riaccodare automaticamente l'attività ed eseguirla in un'altra VM.

È anche possibile che un problema intermittente provochi il blocco di un'attività o ne renda troppo lunga l'esecuzione. È possibile impostare un tempo di esecuzione massimo per un'attività. In caso di superamento di questo valore, Batch interromperà l'applicazione dell'attività. Il riaccodamento automatico non è attualmente possibile per questa situazione, ma il client può rilevare questa situazione e inviare una nuova attività.

###Gestione di VM "non valide"

A ogni VM di un pool viene assegnato un nome univoco e la VM in cui viene eseguita un'attività è inclusa nei metadati dell'attività. Se è presente una VM che per qualche motivo provoca un errore delle attività, il client potrà rilevare il problema ed eliminare la VM sospetta dal pool. Se un'attività era in esecuzione nella VM eliminata, verrà riaccodata automaticamente ed eseguita in un'altra VM.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[Panoramica di Azure Batch]: batch-technical-overview.md

<!---HONumber=July15_HO4-->