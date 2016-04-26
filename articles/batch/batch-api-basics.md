<properties
	pageTitle="Cenni preliminari sulla funzionalità Azure Batch | Microsoft Azure"
	description="Informazioni sulle funzionalità del servizio Batch e le relative API dal punto di vista dello sviluppatore."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="03/11/2016"
	ms.author="yidingz;marsma"/>

# Cenni preliminari sulle funzionalità di Azure Batch

Questo articolo fornisce una panoramica di base delle principali funzionalità delle API del servizio Azure Batch. Per lo sviluppo di una soluzione di calcolo distribuita con l'API [Batch REST][batch_rest_api] o [.NET Batch][batch_net_api], si useranno molte delle entità e delle funzionalità descritte di seguito.

> [AZURE.TIP] Per una panoramica tecnica di Batch di livello superiore, vedere [Nozioni di base di Azure Batch](batch-technical-overview.md).

## <a name="workflow"></a>Flusso di lavoro del servizio Batch

Il flusso di lavoro di alto livello seguente è quello tipico usato in quasi tutti gli scenari di calcolo distribuiti sviluppati nel servizio Batch:

1. Caricare i *file di dati* da usare nello scenario di calcolo distribuito in un account di [Archiviazione di Azure][azure_storage]. I file devono trovarsi nell'account di archiviazione per consentire al servizio Batch di accedervi. Le attività scaricheranno i file nei [nodi di calcolo](#computenode) al momento dell'esecuzione.

2. Caricare i *file binari* dipendenti nell'account di archiviazione. Questi file binari includono il programma da eseguire tramite le attività e i relativi assembly dipendenti. I file devono essere accessibili anche dall'account di archiviazione per consentire alle attività di scaricarli nei nodi di calcolo.

3. Creare un [pool](#pool) di nodi di calcolo. Specificare le [dimensioni dei nodi di calcolo][cloud_service_sizes] da usare quando si crea il pool. Quando viene eseguita un'attività, viene assegnato un nodo in questo pool.

4. Creare un [processo](#job). Un processo consente di gestire le attività di una raccolta.

5. Aggiungere [attività](#task) al processo. Ogni attività usa il programma caricato per elaborare le informazioni nei file di dati caricati nell'account di archiviazione.

6. Monitorare lo stato del processo e recuperare i risultati.

> [AZURE.NOTE] È necessario un [account Batch](batch-account-create-portal.md) per usare il servizio Batch e quasi tutte le soluzioni useranno un account di[Archiviazione di Azure][azure_storage] per l'archiviazione e il recupero dei file.

Le sezioni seguenti illustrano ognuna delle risorse citate nel flusso di lavoro precedente e molte altre funzionalità di Batch che abiliteranno lo scenario di calcolo distribuito.

## <a name="resource"></a> Risorse del servizio Batch

Quando si usa il servizio Batch, si usano molte delle risorse seguenti. Alcune di queste risorse, ad esempio gli account, i nodi di calcolo, i pool, i processi e le attività, vengono usate in tutte le soluzioni del servizio Batch. Altre risorse, ad esempio le pianificazioni di processi e i pacchetti dell'applicazione, sono funzionalità utili ma facoltative.

- [Account](#account)
- [Nodo di calcolo](#computenode)
- [Pool](#pool)
- [Processo](#job)
- [Attività](#task)
	- [Attività di avvio](#starttask)
	- [Attività di gestione dei processi](#jobmanagertask)
	- [Attività di preparazione e rilascio dei processi](#jobpreprelease)
	- [Attività a istanze multiple](#multiinstance)
    - [Dipendenze dell'attività](#taskdep)
- [Pianificazioni dei processi](#jobschedule)
- [Pacchetti dell'applicazione](#appkg)

### <a name="account"></a>Account

Un account Batch è un'entità identificata in modo univoco all'interno del servizio Batch. Tutte le operazioni di elaborazione sono associata a un account Batch. Quando si eseguono operazioni con il servizio Batch, sono necessari il nome dell'account e la chiave dell'account. Per creare un account Batch, vedere [Creare e gestire un account Azure Batch nel portale di Azure](batch-account-create-portal.md).

### <a name="computenode"></a>Nodo di calcolo

Un nodo di calcolo è una macchina virtuale di Azure dedicata a un carico di lavoro specifico per l'applicazione. Le dimensioni di un nodo determinano il numero di core CPU, la capacità di memoria e le dimensioni del file system locale allocati al nodo. Un nodo può avere una qualsiasi delle [dimensioni dei nodi del servizio cloud][cloud_service_sizes], ad eccezione di A0.

I nodi possono eseguire file eseguibili e script, inclusi eseguibili (.exe), file di comando (.cmd), file batch (.bat) e script di PowerShell. Un nodo ha anche gli attributi seguenti:

- In ogni nodo di calcolo vengono create una **struttura di cartelle** standard e le **variabili di ambiente** associate con il dettaglio dei percorsi. Per altre informazioni, vedere [File e directory](#files) di seguito.
- **Variabili di ambiente** disponibili per riferimento da parte delle attività.
- Impostazioni del **firewall** configurate per controllare l'accesso.
- Se l'**accesso remoto** a un nodo di calcolo è obbligatorio, ad esempio per il debug, è possibile ottenere un file RDP, che può essere quindi usato per accedere al nodo tramite *Desktop remoto*.

### <a name="pool"></a>Pool

Un pool è una raccolta di nodi in cui viene eseguita l'applicazione. Il pool può essere creato manualmente dall'utente o automaticamente dal servizio Batch quando si specifica il lavoro da eseguire. È possibile creare e gestire un pool che soddisfi le esigenze dell'applicazione. I pool possono essere usati solo dall'account Batch in cui sono stati creati. Un account Batch può avere più pool.

I pool di Azure Batch sono basati sulla piattaforma di calcolo Azure e offrono allocazione su larga scala, installazione di applicazioni, distribuzione dei dati e monitoraggio dell'integrità, oltre alla regolazione flessibile del numero di nodi di calcolo in un pool (scalabilità).

A ogni nodo aggiunto a un pool viene assegnato un nome univoco e un indirizzo IP. Quando un nodo viene rimosso da un pool, vengono perse tutte le modifiche apportate al sistema operativo o ai file e il relativo nome e indirizzo IP vengono rilasciati per uso futuro. Quando un nodo esce da un pool, la sua durata è terminata.

È possibile configurare un pool per consentire la comunicazione tra nodi al suo interno. Se per un pool è richiesta la comunicazione all'interno dello stesso pool, il servizio Batch abilita un numero di porte maggiore di 1100 in ogni nodo del pool. Ogni nodo del pool è configurato per consentire connessioni in ingresso per questo intervallo di porte e solo da altri nodi all'interno del pool. Se l'applicazione non richiede la comunicazione tra nodi, il servizio Batch può allocare al pool un numero potenzialmente elevato di nodi da diversi cluster e data center per consentire una maggiore capacità di elaborazione parallela.

Quando si crea un pool, è possibile specificare gli attributi seguenti:

- **Dimensioni dei nodi** nel pool
	- È necessario scegliere dimensioni appropriate per i nodi, tenendo in considerazione le caratteristiche e i requisiti dell'applicazione o delle applicazioni che saranno eseguite nei nodi. Le dimensioni del nodo vengono in genere scelte presupponendo che nel nodo venga eseguita un'attività alla volta. Per determinare la dimensioni del nodo più appropriate e convenienti, considerare vari aspetti, ad esempio se si tratta di un'applicazione multithreading e la quantità di memoria che utilizza. È possibile che vengano assegnate più attività e che più istanze dell'applicazione vengano eseguite in parallelo. In questo caso, si sceglierà di solito un nodo di dimensioni maggiori. Per altre informazioni, vedere di seguito "Criteri di pianificazione delle attività".
	- Tutti i nodi in un pool devono avere le stesse dimensioni. Se è necessario eseguire applicazioni diverse con requisiti di sistema diversi e/o carichi diversi, è consigliabile creare pool separati.
	- È possibile configurare tutte le [dimensioni dei nodi del servizio cloud][cloud_service_sizes] per un pool, ad eccezione di A0.

- **Famiglia del sistema operativo** e **versione ** in esecuzione nei nodi.
	- Come con i ruoli di lavoro all'interno di Servizi cloud, si possono specificare la *famiglia del sistema operativo* e la *versione del sistema operativo*. Per altre informazioni sui ruoli di lavoro, vedere la sezione [Informazioni sui servizi cloud][about_cloud_services] in *Opzioni di hosting di calcolo fornite da Azure*.
	- La famiglia del sistema operativo determina anche le versioni di .NET installate con il sistema operativo.
	- Analogamente ai ruoli di lavoro, è consigliabile specificare `*` per la versione del sistema operativo, così che i nodi vengano aggiornati automaticamente senza doversi occupare delle nuove versioni rilasciate. Il caso d'uso principale per la scelta di una versione specifica del sistema operativo consiste nell'assicurare il mantenimento della compatibilità delle applicazioni, abilitando l'esecuzione del test di compatibilità con le versioni precedenti prima di consentire l'aggiornamento della versione. Dopo la convalida, la versione del sistema operativo per il pool può essere aggiornata ed è possibile installare la nuova immagine del sistema operativo. Eventuali attività in esecuzione saranno interrotte e accodate di nuovo.

- **Numero di nodi di destinazione** che devono essere disponibili per il pool

- **Criteri di scalabilità** per il pool
	- Oltre al numero di nodi, per un pool è possibile specificare anche una [formula di scalabilità automatica](batch-automatic-scaling.md). Il servizio Batch eseguirà la formula e rettificherà il numero di nodi all'interno del pool in base a vari parametri relativi a pool, processi e attività specificati.

- Criteri di **pianificazione di attività**
	- L'opzione di configurazione [Numero massimo attività per nodo](batch-parallel-node-tasks.md) determina il numero massimo di attività che è possibile eseguire in parallelo nel nodo.
	- La configurazione predefinita prevede l'esecuzione di una sola attività alla volta in un nodo di calcolo, ma in alcuni scenari risulta utile eseguire più di un'attività contemporaneamente in un nodo, ad esempio per aumentare l'utilizzo del nodo se un'applicazione deve rimanere in attesa di I/O. L'esecuzione contemporanea di più di un'applicazione aumenta l'utilizzo della CPU. Un altro esempio consiste nel ridurre il numero di nodi nel pool. Questo approccio può ridurre la quantità di operazioni di trasferimento dei dati richieste per i set di dati di riferimento di grandi dimensioni. Se le dimensioni di un nodo A1 sono sufficienti per un'applicazione, si potrebbe invece scegliere di impostare le dimensioni del nodo A4 e configurare il pool per 8 attività in parallelo, dove ognuna usa un core.
	- È anche possibile specificare un "tipo di riempimento" che determina se Batch distribuisce le attività in modo uniforme a tutti i nodi o se satura ogni nodo con il numero massimo di attività prima di assegnarle ad altri nodi nel pool.

- **Stato delle comunicazioni** dei nodi nel pool
	- Un pool può essere configurato per consentire la comunicazione tra i nodi del pool stesso, determinando l'infrastruttura di rete sottostante. Si noti che questa configurazione ha effetto anche sul posizionamento dei nodi del cluster.
	- Nella maggior parte degli scenari, il funzionamento delle attività è indipendente e non è necessario che comunichino reciprocamente, mentre alcune applicazioni potrebbero richiedere la comunicazione tra le attività.

- **Attività di avvio** per i nodi del pool
	- È possibile specificare che un'*attività di avvio* venga eseguita ogni volta che si aggiunge un nodo di calcolo al pool e quando si riavvia un nodo. Questa impostazione viene spesso usata per installare un'applicazione usata dalle attività in esecuzione nel nodo.

### <a name="job"></a>Processo

Un processo è una raccolta di attività e specifica la modalità di esecuzione dei calcoli nei nodi di calcolo di un pool.

- Il processo specifica il **pool** in cui verrà eseguito il lavoro. Il pool può essere un pool esistente creato in precedenza per l'uso da parte di molti processi oppure creato su richiesta per ogni processo associato a una pianificazione del processo o per tutti i processi associati a una pianificazione del processo.
- È possibile specificare una **priorità del processo** facoltativa. Quando si invia un processo con priorità più alta rispetto ad altri processi in corso, le attività del processo con la priorità più alta saranno inserite nella coda prima delle attività del processo con priorità più bassa. Le attività con priorità più bassa già in esecuzione non saranno messe in attesa.
- I **vincoli** del processo specificano determinati limiti per i processi.
	- È possibile impostare un **tempo reale massimo** per i processi. Se l'esecuzione dei processi supera il tempo reale specificato, il processo e tutte le attività associate verranno terminati.
	- Azure Batch può rilevare attività con esito negativo e provare a eseguirle di nuovo. Il **numero massimo di tentativi per l'attività** può essere specificato sotto forma di vincolo, indicando anche se un'attività viene sempre ripetuta o mai. Per nuovo tentativo si intende che l'attività viene riaccodata per essere eseguita di nuovo.
- Le attività possono essere aggiunte al processo dall'applicazione client oppure si può specificare un'[attività del gestore di processi](#jobmanagertask). Un'attività del gestore di processi usa l'API Batch e contiene le informazioni necessarie per creare le attività necessarie per un processo. L'attività viene eseguita in uno dei nodi di calcolo del pool. L'attività del gestore di processi viene gestita in modo specifico da Batch, ovvero viene accodata non appena si crea il processo e viene riavviata se l'operazione non riesce. Per i processi creati in base a una pianificazione del processo è obbligatorio usare un'attività del gestore di processi, perché è l'unico modo per definire le attività prima di creare istanze del processo. Di seguito sono riportate altre informazioni sulle attività del gestore di processi.

### <a name="task"></a>Attività

Un'attività è un'unità di calcolo associata a un processo ed eseguita in un nodo. Le attività vengono assegnate a un nodo per l'esecuzione o vengono accodate fino a quando non diventa disponibile un nodo. Un'attività usa le risorse seguenti:

- Applicazione specificata nella **riga di comando** dell'attività.

- **File di risorse** contenenti i dati da elaborare. Questi file vengono copiati automaticamente nel nodo dall'archivio BLOB in un account di archiviazione di Azure. Per altre informazioni, vedere [File e directory](#files) di seguito.

- **Variabili di ambiente** richieste dall'applicazione. Per altre informazioni, vedere [Impostazioni di ambiente per le attività](#environment) di seguito.

- **Vincoli** in base ai quali viene eseguito il calcolo. Ad esempio, l'intervallo di tempo massimo in cui l'attività può essere eseguita, il numero massimo di ripetizioni dei tentativi di un'attività se non riesce e l'intervallo di tempo massimo in cui i file vengono mantenuti nella directory di lavoro.

Oltre alle attività definite dall'utente per eseguire il calcolo in un nodo, il servizio Batch fornisce le attività speciali seguenti:

- [Attività di avvio](#starttask)
- [Attività di gestione dei processi](#jobmanagertask)
- [Attività di preparazione e rilascio dei processi](#jobmanagertask)
- [Attività a istanze multiple](#multiinstance)
- [Dipendenze dell'attività](#taskdep)

#### <a name="starttask"></a>Attività di avvio

Associando un'**attività di avvio ** a un pool, è possibile configurare l'ambiente operativo dei nodi, eseguendo azioni come l'installazione di software o l'avvio di processi in background. L'attività di avvio viene eseguita a ogni avvio di un nodo per tutto il tempo in cui questa rimane nel pool, incluso il momento in cui il nodo viene aggiunto al pool. Il vantaggio principale dell'attività di avvio consiste nel fatto che contiene tutte le informazioni necessarie per configurare i nodi di calcolo e installare le applicazioni necessarie per l'esecuzione dell'attività di processo. In questo modo, l'aumento del numero di nodi in un pool è semplice come quando si specifica il nuovo conteggio dei nodi di destinazione. Batch ha già tutte le informazioni necessarie per configurare i nuovi nodi e prepararli perché accettino le attività.

Come per qualsiasi attività Batch, è possibile specificare un elenco di **file di risorse** in [Archiviazione di Azure][azure_storage], oltre a una **riga di comando** da eseguire. Azure Batch copierà prima di tutto i file da Archiviazione di Azure, quindi eseguirà la riga di comando. Per un'attività di avvio del pool, l'elenco di file include in genere il pacchetto o i file dell'applicazione, ma può anche includere dati di riferimento da usare in tutte le attività in esecuzione nei nodi di calcolo. La riga di comando dell'attività di avvio potrebbe eseguire uno script di PowerShell o eseguire un'operazione `robocopy`, ad esempio, copiare i file dell'applicazione nella cartella "condivisa", quindi eseguire successivamente un file MSI o `setup.exe`.

In genere è consigliabile che il servizio Batch attenda il completamento dell'attività di avvio prima di considerare il nodo pronto per l'assegnazione di attività, ma questo comportamento è configurabile.

Se un'attività di avvio non riesce in un nodo di calcolo, lo stato del nodo viene aggiornato per riflettere l'errore e il nodo non sarà disponibile per l'assegnazione di attività. Un'attività di avvio può non riuscire se si verifica un problema durante la copia dei file di risorse dall'archiviazione o se il processo eseguito dalla riga di comando restituisce un codice di uscita diverso da zero.

#### <a name="jobmanagertask"></a>Attività di gestione dei processi

L'**attività del gestore di processi** viene in genere usata per il controllo e/o il monitoraggio dell'esecuzione del processo. Ad esempio, creare e inviare attività per un processo, determinare attività aggiuntive da eseguire e determinare quando il lavoro è completato. Un'attività del gestore di processi non è tuttavia limitata a queste operazioni, è un'attività completa che può eseguire qualsiasi operazione richiesta per il processo. Ad esempio, un'attività del gestore di processi può scaricare un file specificato come parametro, analizzare il contenuto del file e inviare attività aggiuntive in base a quel contenuto.

Un'attività del gestore di processi viene avviata prima di tutte le altre attività e offre le funzionalità seguenti:

- Viene inviata automaticamente come attività dal servizio Batch quando si crea il processo.

- Viene pianificata per l'esecuzione prima di altre attività di un processo.

- Il nodo associato è l'ultimo a essere rimosso da un pool quando il pool viene ridimensionato.

- La sua terminazione può essere associata alla terminazione di tutte le attività del processo.

- A questa attività viene assegnata la priorità più alta quando deve essere riavviata. Se un nodo inattivo non è disponibile, il servizio Batch può terminare una delle altre attività in esecuzione nel pool per consentire l'esecuzione dell'attività del gestore di processi.

- Un'attività del gestore di processi in un processo non ha la priorità sulle attività di altri processi. Tra i processi vengono rispettate solo le priorità a livello di processo.

#### <a name="jobpreprelease"></a>Attività di preparazione e rilascio dei processi

Batch fornisce l'attività di preparazione del processo per la configurazione dell'esecuzione pre-processo e l'attività di rilascio del processo per la manutenzione o la pulizia post-processo.

- **Attività di preparazione del processo**: viene eseguita su tutti i nodi di calcolo pianificati per l'esecuzione di attività, prima dell'esecuzione di qualsiasi altra attività di un processo. Ad esempio, per copiare i dati condivisi da tutte le attività, ma univoci per il processo, usare l'attività di preparazione del processo.
- **Attività di rilascio del processo**: dopo aver completato il processo, viene eseguita l'attività di rilascio del processo in ogni nodo del pool che ha eseguito almeno un'attività. Ad esempio, usare l'attività di rilascio del processo per eliminare i dati copiati dall'attività di preparazione del processo o comprimere e caricare i dati del log di diagnostica.

Le attività di preparazione e rilascio dei processi consentono di specificare una riga di comando da eseguire quando si richiama l'attività e offrono funzionalità quali download dei file, esecuzione con privilegi elevati, variabili di ambiente personalizzate, durata di esecuzione massima, numero di nuovi tentativi e periodo di conservazione dei file.

Per altre informazioni sulle attività di preparazione e rilascio dei processi, vedere [Eseguire attività di preparazione e completamento dei processi nei nodi di calcolo di Azure Batch](batch-job-prep-release.md).

#### <a name="multiinstance"></a>Attività a istanze multiple

Un'[attività a istanze multiple](batch-mpi.md) è un'attività configurata per l'esecuzione contemporanea in più nodi di calcolo. Con le attività a istanze multiple è possibile abilitare scenari high performance computing, ad esempio MPI (Message Passing), che richiedono l'allocazione di un gruppo di nodi di calcolo per l'elaborazione di un singolo carico di lavoro.

Per una discussione dettagliata sull'esecuzione di processi MPI in Batch usando la libreria Batch .NET, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](batch-mpi.md).

#### <a name="taskdep"></a>Relazioni tra attività

Le relazioni tra attività consentono di specificare che un'attività dipende dal completamento di altre attività prima della rispettiva esecuzione. Questa funzionalità fornisce il supporto nelle situazioni in cui un'attività di "downstream" utilizza l'output di un'attività di "upstream" oppure quando un'attività di upstream esegue un'inizializzazione richiesta da un'attività di downstream. Per usare questa funzionalità, prima è necessario abilitare le relazioni tra attività nel processo batch. Per ogni attività che dipende da un'altra (o da più altre), specificare quindi le attività da cui dipende tale attività.

Con le relazioni tra attività, è possibile configurare scenari come i seguenti:

* L'*attivitàB* dipende dall'*attivitàA* (l'esecuzione dell'*attivitàB* inizierà solo dopo il completamento dell'*attivitàA*)
* L'*attivitàC* dipende sia dall'*attivitàA* che dall'*attivitàB*
* L'*attivitàD* dipende da un intervallo di attività, ad esempio le attività da *1* a *10*, prima che venga eseguita

Vedere l'esempio di codice [TaskDependencies][github_sample_taskdeps] nel repository di GitHub [azure-batch-samples][github_samples], in cui viene illustrato come configurare le attività che dipendono da altre attività usando la libreria [Batch .NET][batch_net_api].

### <a name="jobschedule"></a>Processi pianificati

Le pianificazioni dei processi consentono di creare processi ricorrenti nel servizio Batch. Una pianificazione del processo specifica quando eseguire i processi e include le specifiche per i processi da eseguire. Una pianificazione del processo consente di specificare la durata della pianificazione, per quanto tempo e quando è effettiva la pianificazione, e con quale frequenza devono essere creati i processi durante quell'intervallo di tempo.

### <a name="appkg"></a>Pacchetti dell'applicazione

La funzionalità relativa ai [pacchetti dell'applicazione](batch-application-packages.md) consente di gestire e distribuire con facilità le applicazioni ai nodi di calcolo nei pool. I pacchetti dell'applicazione consentono di caricare e gestire con facilità più versioni delle applicazioni eseguite dalle attività, inclusi i file binari e i file di supporto, quindi di distribuire automaticamente una o più applicazioni nei nodi di calcolo del pool.

Il servizio Batch gestisce i dettagli dell'uso in background dell'Archiviazione di Azure, per archiviare e distribuire in modo sicuro i pacchetti dell'applicazione ai nodi di calcolo, in modo che sia possibile semplificare il codice e l'overhead di gestione.

Per altre informazioni sulla funzionalità relativa ai pacchetti dell'applicazione, vedere [Distribuzione di applicazioni con i pacchetti dell'applicazione di Azure Batch](batch-application-packages.md).

## <a name="files"></a>File e directory

Ogni attività ha una directory di lavoro in cui può creare zero o più file e directory per archiviare il programma eseguito da un'attività, i dati elaborati e l'output dell'elaborazione eseguita dall'attività. Questi file e directory sono quindi disponibili per l'uso da parte di altre attività durante l'esecuzione di un processo. Tutte le attività, i file e le directory in un nodo sono di proprietà di un singolo account utente.

Il servizio Batch espone una parte del file system in un nodo come "directory radice". La directory radice è disponibile per un'attività mediante l'accesso alla variabile di ambiente `%AZ_BATCH_NODE_ROOT_DIR%`. Per altre informazioni sull'uso delle variabili di ambiente, vedere [Impostazioni di ambiente per le attività](#environment).

![Struttura di directory dei nodi di calcolo][1]

La directory radice contiene la struttura di directory seguente:

- **Condiviso**: questo percorso è una directory condivisa per tutte le attività eseguite in un nodo, indipendentemente dal processo. Nel nodo è possibile accedere alla directory condivisa tramite `%AZ_BATCH_NODE_SHARED_DIR%`. Questa directory fornisce l'accesso in lettura/scrittura a tutte le attività eseguite nel nodo. L'attività possono creare, leggere, aggiornare ed eliminare file in questa directory.

- **Start**: questo percorso viene usato da un'attività di avvio come directory di lavoro. Anche tutti i file scaricati dal servizio Batch per avviare l'attività di avvio vengono archiviati in tale directory. Nel nodo la directory di avvio è disponibile tramite la variabile di ambiente `%AZ_BATCH_NODE_STARTUP_DIR%`. L'attività di avvio può creare, leggere, aggiornare ed eliminare i file in questa directory, che può essere usata dalle attività di avvio per configurare il sistema operativo.

- **Attività**: viene creata una directory per ogni attività in esecuzione sul nodo, accessibile tramite `%AZ_BATCH_TASK_DIR%`. All'interno di ogni directory di attività, il servizio Batch crea una directory di lavoro (`wd`), il cui percorso univoco viene specificato dalla variabile di ambiente `%AZ_BATCH_TASK_WORKING_DIR%`. Questa directory fornisce l'accesso in lettura/scrittura all'attività. L'attività può creare, leggere, aggiornare ed eliminare i file nella directory che viene mantenuta in base al vincolo *RetentionTime* specificato per l'attività.
  - `stdout.txt` e `stderr.txt`: questi file vengono scritti nella cartella dell'attività durante l'esecuzione di quest'ultima.

Quando un nodo viene rimosso dal pool, vengono rimossi tutti i file archiviati nel nodo.

## <a name="lifetime"></a>Durata del pool e dei nodi di calcolo

Quando si progetta una soluzione Azure Batch, è necessario prendere una decisione a livello di progettazione per stabilire come e quando vengono creati i pool e per quanto tempo vengono mantenuti disponibili i nodi di calcolo all'interno dei pool.

Da un lato, si potrebbe creare un pool per ogni processo quando viene inviato e rimuovere i relativi nodi al termine dell'esecuzione delle attività. In questo modo è possibile massimizzare l'utilizzo, perché i nodi vengono allocati solo quando è assolutamente necessario e vengono arrestati non appena risultano inattivi. Mentre ciò significa che il processo deve attendere l'allocazione dei nodi, è importante notare che le attività verranno pianificate nei nodi non appena risultano disponibili e allocate individualmente e dopo il completamento dell'attività di avvio. Batch *non* attende che tutti i nodi di un pool siano disponibili prima di assegnare le attività, assicurando quindi il massimo utilizzo di tutti i nodi disponibili.

Dall'altro lato, se l'avvio immediato dei processi ha la priorità più alta, si può creare subito un pool e rendere disponibili i relativi nodi prima dell'invio dei processi. In questo scenario, le attività di processo possono essere avviate immediatamente, ma i nodi possono rimanere inattivi in attesa dell'assegnazione delle attività.

Un approccio combinato, usato in genere per gestire un carico variabile ma continuativo, consiste nell'avere un pool al quale vengono inviati più processi, ma aumentando o riducendo il numero di nodi in base al carico di processi. Vedere *Scalabilità delle applicazioni* di seguito. Questa operazione può essere eseguita in modo reattivo in base al carico corrente o in modo proattivo se è possibile prevedere il carico.

## <a name="scaling"></a>Scalabilità delle applicazioni

Con il [ridimensionamento automatico](batch-automatic-scaling.md) è possibile fare in modo che il servizio Batch modifichi dinamicamente il numero di nodi di calcolo in un pool in base al carico di lavoro corrente e all'utilizzo delle risorse dello scenario di calcolo. In questo modo è possibile ridurre il costo complessivo dell'esecuzione dell'applicazione usando solo le risorse necessarie e rilasciando quelle non necessarie. È possibile specificare le impostazioni di ridimensionamento automatico per un pool quando viene creato oppure abilitare il ridimensionamento in seguito ed è possibile aggiornare le impostazioni di ridimensionamento in un pool abilitato per il ridimensionamento automatico.

Il ridimensionamento automatico viene eseguito specificando una **formula di ridimensionamento automatico** per un pool. Il servizio Batch usa questa formula per determinare il numero di nodi di destinazione nel pool per l'intervallo di ridimensionamento successivo (un intervallo che è possibile specificare).

Ad esempio, un processo richiede l'invio di un numero elevato di attività per cui pianificare l'esecuzione. È possibile assegnare al pool una formula di ridimensionamento che il numero di nodi nel pool in base al numero corrente di attività in sospeso e alla percentuale di completamento delle attività. Il servizio Batch valuta periodicamente la formula e ridimensiona il pool in base al carico di lavoro e alle impostazioni della formula.

Una formula può essere basata sulle metriche seguenti:

- **Metriche temporali**: basate sulle statistiche raccolte ogni cinque minuti nel numero di ore specificato.

- **Metriche delle risorse**: basate su utilizzo di CPU, larghezza di banda, memoria e numero di nodi.

- **Metriche delle attività**: basate sullo stato delle attività, ad esempio Attiva, In sospeso e Completata.

Quando il ridimensionamento automatico riduce il numero di nodi di calcolo in un pool, devono essere tenute in considerazione le attività attualmente in esecuzione. A questo scopo, la formula può includere un'impostazione di criteri di deallocazione dei nodi che specifica se le attività in esecuzione devono essere arrestate immediatamente o possono terminare prima che il nodo venga rimosso dal pool.

> [AZURE.TIP] Per massimizzare l'utilizzo delle risorse di calcolo, impostare il numero di nodi su zero alla fine di un processo, ma consentire il completamento delle attività in esecuzione.

Per altre informazioni sulla scalabilità automatica di un'applicazione, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](batch-automatic-scaling.md).

## <a name="cert"></a>Sicurezza con certificati

In genere è necessario usare certificati per crittografare o decrittografare informazioni riservate per le attività, ad esempio la chiave per un [account di archiviazione Azure][azure_storage]. A questo scopo, i certificati possono essere installati nei nodi. I segreti crittografati vengono passati alle attività nei parametri della riga di comando o incorporati in una delle risorse dell'attività e i certificati installati possono essere usati per decrittografarli.

Per aggiungere un certificato a un account Batch, usare l'operazione [Aggiungere un certificato a un account][rest_add_cert] (API Batch REST) o il metodo[CertificateOperations.CreateCertificate][net_create_cert] (API Batch .NET). È quindi possibile associare il certificato a un pool nuovo o esistente. Quando un certificato è associato a un pool, il servizio Batch installa il certificato in ogni nodo del pool. Il servizio Batch installa i certificati appropriati all'avvio del nodo, prima di avviare le attività, incluse quelle di avvio e del gestore di processi.

## <a name="scheduling"></a>Priorità di pianificazione

È possibile assegnare una priorità ai processi creati in Batch. Il servizio Batch usa il valore di priorità del processo per determinare l'ordine di pianificazione dei processi in un account. I valori di priorità sono compresi in un intervallo da -1000 a 1000, dove -1000 è la priorità più bassa e 1000 la più alta. È possibile aggiornare la priorità di un processo tramite l'operazione [Aggiornare un processo][rest_update_job] (API Batch REST) o modificando la proprietà [CloudJob.Priority][net_cloudjob_priority] (API Batch .NET).

All'interno dello stesso account i processi con priorità più alta hanno precedenza di pianificazione rispetto ai processi con priorità inferiori. Un processo con un valore di priorità più elevato in un account non dispone di tale precedenza di pianificazione rispetto a un altro processo con un valore di priorità inferiore in un account diverso.

La pianificazione di attività dei pool è indipendente. In pool diversi non è garantito che un processo con priorità più alta venga pianificato per primo se nel pool associato non è disponibile un numero sufficiente di nodi inattivi. Nello stesso pool i processi con lo stesso livello di priorità hanno la stessa probabilità di essere pianificati.

## <a name="environment"></a>Impostazioni di ambiente per le attività

Ogni attività eseguita all'interno di un processo Batch può accedere alle variabili di ambiente, sia quelle impostate dal servizio Batch (definite dal sistema, vedere la tabella seguente) sia quelle definite dall'utente. Le applicazioni e gli script eseguiti dalle attività nei nodi di calcolo hanno accesso a queste variabili di ambiente durante l'esecuzione nel nodo.

Impostare le variabili di ambiente definite dall'utente quando si usa l'operazione [Aggiungere un'attività a un processo][rest_add_task] (API Batch REST) o si modifica la proprietà [CloudTask.EnvironmentSettings][net_cloudtask_env] (API Batch .NET) durante l'aggiunta di attività a un processo.

Ottenere le variabili di ambiente di un'attività, sia quelle definite dal sistema che dall'utente, usando l'operazione [Ottenere informazioni su un'attività][rest_get_task_info] (API Batch REST) o accedendo alla proprietà [CloudTask.EnvironmentSettings][net_cloudtask_env] (API Batch .NET). Come accennato, i processi eseguiti in un nodo di calcolo possono accedere anche a tutte le variabili di ambiente, ad esempio usando la familiare sintassi `%VARIABLE_NAME%`.

Per qualsiasi attività pianificata in un processo, il servizio Batch imposta il set di variabili di ambiente definite dal sistema seguente:

| Nome di variabile di ambiente | Descrizione |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME` | Nome dell'account a cui appartiene l'attività. |
| `AZ_BATCH_JOB_ID` | ID del processo a cui appartiene l'attività. |
| `AZ_BATCH_JOB_PREP_DIR` | Percorso completo della directory di attività di preparazione sul nodo. |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | Percorso completo della directory di lavoro di attività di preparazione sul nodo. |
| `AZ_BATCH_NODE_ID` | ID del nodo in cui è in esecuzione l'attività. |
| `AZ_BATCH_NODE_ROOT_DIR` | Percorso completo della directory radice nel nodo. |
| `AZ_BATCH_NODE_SHARED_DIR` | Percorso completo della directory condivisa nel nodo. |
| `AZ_BATCH_NODE_STARTUP_DIR` | Percorso completo della directory delle attività di avvio del nodo di calcolo nel nodo. |
| `AZ_BATCH_POOL_ID` | ID del pool in cui è in esecuzione l'attività. |
| `AZ_BATCH_TASK_DIR` | Percorso completo della directory di attività nel nodo. |
| `AZ_BATCH_TASK_ID` | ID dell'attività corrente. |
| `AZ_BATCH_TASK_WORKING_DIR` | Percorso completo della directory di lavoro nel nodo. |

>[AZURE.NOTE] Non è possibile sovrascrivere le variabili definite dal sistema precedenti, perché sono di sola lettura.

## <a name="errorhandling"></a>Gestione degli errori

Potrebbe essere necessario gestire sia gli errori delle attività che quelli delle applicazioni nella soluzione Batch.

### Gestione degli errori delle attività
Gli errori delle attività rientrano nelle categorie seguenti:

- **Errori di pianificazione**
	- Se il trasferimento di file specificato per un'attività non riesce per qualsiasi motivo, per l'attività viene impostato un "errore di pianificazione".
	- Le cause degli errori di pianificazione possono essere dovute a file spostati, un account di archiviazione non più disponibile o un altro problema che ha impedito la copia corretta dei file nel nodo.
- **Errori delle applicazioni**
	- Anche il processo specificato dalla riga di comando dell'attività può non riuscire. Il processo viene considerato non riuscito quando il processo eseguito dall'attività restituisce un codice di uscita diverso da zero.
	- Per gli errori delle applicazioni è possibile configurare Batch in modo che riprovi automaticamente a eseguire l'attività per un numero di volte specificato.
- **Errori relativi ai vincoli**
	- È possibile impostare un vincolo che specifichi la durata massima di esecuzione per un processo o un'attività, *maxWallClockTime*. Questa impostazione può essere utile per terminare le attività "bloccate".
	- Quando viene superata la quantità massima di tempo impostata, l'attività viene contrassegnata come *completata*, ma il codice di uscita viene impostato su `0xC000013A` e il campo *schedulingError* sarà contrassegnato come `{ category:"ServerError", code="TaskEnded"}`.

### Debug degli errori delle applicazioni

Durante l'esecuzione un'applicazione può generare un output di diagnostica che può essere usato per la risoluzione dei problemi. Come indicato sopra in [File e directory](#files), il servizio di Batch invia l'output di stdout e stderr ai file `stdout.txt` e `stderr.txt` che si trovano nella directory dell'attività nel nodo di calcolo. Con [ComputeNode.GetNodeFile][net_getfile_node] e [CloudTask.GetNodeFile][net_getfile_task] nell'API .NET Batch è possibile recuperare questi e altri file per la risoluzione dei problemi.

È possibile eseguire un'operazione di debug ancora più approfondita accedendo a un nodo di calcolo tramite *Desktop remoto*. È possibile [Ottenere un file RDP da un nodo][rest_rdp] (API Batch REST) o usare il metodo [ComputeNode.GetRDPFile][net_rdp] (API Batch .NET) per l'accesso remoto.

>[AZURE.NOTE] Per connettersi a un nodo tramite RDP, è necessario creare prima di tutto un utente nel nodo. [Aggiungere un account utente a un nodo][rest_create_user] nell'API Batch REST o usare il metodo [ComputeNode.CreateComputeNodeUser][net_create_user] in Batch .NET.

### Considerazioni sugli errori o sulle interruzioni delle attività

In alcuni casi, le attività non riescono o vengono interrotte. È possibile che si verifichi un errore dell'applicazione dell'attività stessa, che il nodo in cui è in esecuzione l'attività venga riavviato o che il nodo venga rimosso dal pool durante un'operazione di ridimensionamento se nei criteri di deallocazione del pool è impostata la rimozione immediata dei nodi senza attendere il completamento delle attività. In ogni caso, Batch può riaccodare automaticamente l'attività per eseguirla in un altro nodo.

È anche possibile che un problema intermittente provochi il blocco di un'attività o ne renda troppo lunga l'esecuzione. È possibile impostare il tempo di esecuzione massimo per un'attività. In caso di superamento di questo valore, Batch interromperà l'applicazione dell'attività.

### Risoluzione dei problemi relativi ai nodi di calcolo non validi

Nei casi in cui alcune attività non riescono, il servizio o l'applicazione client Batch può esaminare i metadati delle attività non riuscite per identificare un nodo non correttamente funzionante. A ogni nodo di un pool viene assegnato un ID univoco e il nodo in cui viene eseguita un'attività viene incluso nei metadati dell'attività. Una volta identificato, è possibile eseguire diverse azioni:

- **Riavviare il nodo** ([REST][rest_reboot] | [.NET][net_reboot])

	Il riavvio del nodo a volte consente di eliminare problemi latenti, ad esempio processi bloccati o arrestati in modo anomalo. Si noti che, se il pool usa un'attività di avvio o il processo usa un'attività di preparazione del processo, verranno eseguite quando il nodo viene riavviato.

- **Ricreare l'immagine del nodo** ([REST][rest_reimage] | [.NET][net_reimage])

	Il sistema operativo viene reinstallato nel nodo. Come durante il riavvio di un nodo, le attività di avvio e le attività di preparazione del processo vengono rieseguite dopo che è stata ricreata l'immagine del nodo.

- **Rimuovere il nodo dal pool** ([REST][rest_remove] | [.NET][net_remove])

	A volte è necessario rimuovere completamente il nodo dal pool.

- **Disabilitare la pianificazione delle attività nel nodo** ([REST][rest_offline] | [.NET][net_offline])

	In questo modo il nodo in realtà passa "offline" e non è possibile assegnargli altre attività, ma può rimanere in esecuzione e nel pool. È quindi possibile eseguire altre indagini sulla causa degli errori senza perdere i dati delle attività non riuscite e senza che il nodo generi altri errori delle attività. È possibile, ad esempio, disabilitare la pianificazione delle attività nel nodo, quindi accedere in remoto per esaminare i registri eventi del nodo o eseguire altre operazioni di risoluzione dei problemi. Al termine dell'indagine, è possibile riportare il nodo online abilitando la pianificazione delle attività ([REST][rest_online], [.NET][net_online]) o eseguire una delle altre azioni illustrate in precedenza.

> [AZURE.IMPORTANT] Con ogni azione precedente (riavvio, ricreazione dell'immagine, rimozione, disabilitazione della pianificazione delle attività) è possibile specificare come gestire le attività attualmente in esecuzione nel nodo quando si esegue l'azione. Ad esempio, quando si disabilita la pianificazione delle attività in un nodo con la libreria client Batch .NET, è possibile specificare un valore enum [DisableComputeNodeSchedulingOption][net_offline_option] per specificare se eseguire l'operazione **Terminate** per terminare le attività in esecuzione o **Requeue** per riaccodarle per la pianificazione in altri nodi oppure consentire il completamento delle attività in esecuzione prima di eseguire l'azione (**TaskCompletion**).

## Passaggi successivi

- Creare della prima applicazione Batch seguendo i passaggi in [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md)
- Scaricare e compilare il progetto di esempio [Batch Explorer][batch_explorer_project] da usare durante lo sviluppo di soluzioni Batch. Con Batch Explorer è possibile eseguire le operazioni seguenti e altre ancora:
  - Monitorare e gestire pool, processi e attività nell'account Batch
  - Scaricare `stdout.txt`, `stderr.txt` e altri file dai nodi
  - Creare utenti nei nodi e scaricare i file RDP per l'accesso remoto

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: ../cloud-services/cloud-services-choose-me.md
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

<!---HONumber=AcomDC_0420_2016-->