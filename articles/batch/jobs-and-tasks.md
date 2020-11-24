---
title: Processi e attività in Azure Batch
description: Informazioni sui processi e sulle attività e su come vengono usati in un flusso di lavoro Azure Batch dal punto di vista dello sviluppo.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1ca721ec7527d9d042c129c22cf0266e57c32e9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808583"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Processi e attività in Azure Batch

In Azure Batch, un'*attività* rappresenta un'unità di calcolo. Un *processo* è una raccolta di attività. Qui sotto sono fornite altre informazioni sui processi e sulle attività e su come vengono usati in un flusso di lavoro Azure Batch.

## <a name="jobs"></a>Processi

Un processo è una raccolta di attività. Gestisce la modalità di esecuzione dei calcoli da parte delle attività nei nodi di calcolo di un pool.

Un processo specifica il [pool](nodes-and-pools.md#pools) in cui eseguire il lavoro. È possibile creare un nuovo pool per ogni processo o usare un pool per più processi. È possibile creare un pool per ogni processo associato a una pianificazione di [processo](#scheduled-jobs)o un pool per tutti i processi associati a una pianificazione del processo.

### <a name="job-priority"></a>Priorità del processo

È possibile assegnare a un processo la priorità facoltativa rispetto ai processi creati. Il servizio batch usa il valore di priorità del processo per determinare l'ordine di pianificazione (per tutte le attività all'interno del processo) wtihin ogni pool.

Per aggiornare la priorità di un processo, chiamare l' [aggiornamento delle proprietà di un'operazione del processo](/rest/api/batchservice/job/update) (batch Rest) o modificare [CloudJob. Priority](/dotnet/api/microsoft.azure.batch.cloudjob) (batch .NET). I valori di priorità sono compresi tra-1000 (priorità più bassa) e 1000 (priorità più alta).

All'interno dello stesso pool, i processi con priorità più alta hanno priorità di pianificazione rispetto ai processi con priorità più bassa. Le attività in processi con priorità più bassa già in esecuzione non verranno interrotte dalle attività in un processo con priorità più alta. I processi con lo stesso livello di priorità hanno la stessa probabilità di essere pianificati e l'ordine di esecuzione dell'attività non è definito.

Un processo con un valore di priorità alta in esecuzione in un pool non influirà sulla pianificazione dei processi in esecuzione in un pool separato o in un account batch diverso. La priorità del processo non si applica ai [pool](nodes-and-pools.md#autopools), che vengono creati durante l'invio del processo.

### <a name="job-constraints"></a>Vincoli del processo

È possibile usare i vincoli del processo per specificare determinati limiti per i processi:

- È possibile impostare un **tempo massimo** in modo che, se un processo viene eseguito per un periodo più lungo del tempo specificato, il processo e tutte le attività vengano terminati.
- È possibile specificare il **numero massimo di tentativi per l'attività** sotto forma di vincolo, indicando anche se un'attività viene sempre ripetuta oppure mai. Se un'attività viene ripetuta, se l'attività ha esito negativo, verrà riaccodata per essere eseguita nuovamente.

### <a name="job-manager-tasks-and-automatic-termination"></a>Attività del gestore di processi e chiusura automatica

L'applicazione client può aggiungere attività a un processo oppure è possibile specificare un'[attività del gestore di processi](#job-manager-task). Un'attività del gestore di processi contiene le informazioni necessarie per creare le attività obbligatorie per un processo. L'attività del gestore di processi viene eseguita in uno dei nodi di calcolo del pool. L'attività del gestore di processi viene gestita in modo specifico da Batch, ovvero viene accodata non appena si crea il processo e viene riavviata se l'operazione non riesce. Un'attività del gestore di processi è obbligatoria per i processi creati da una [pianificazione di processi](#scheduled-jobs), perché è l'unico modo per definire le attività prima della creazione di istanze del processo.

Per impostazione predefinita, i processi rimangono nello stato attivo dopo il completamento di tutte le attività del processo. È possibile modificare questo comportamento in modo che il processo venga terminato automaticamente una volta completate tutte le relative attività. Impostare la proprietà **onAllTasksComplete** del processo ([onAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob) in batch .NET) su `terminatejob` *' per terminare automaticamente il processo quando tutte le relative attività sono nello stato completato.

Il servizio Batch considera un processo *senza* attività quando ha tutte le relative attività completate. Di conseguenza, questa opzione viene usata più comunemente con un' [attività del gestore di processi](#job-manager-task). Se si vuole usare la terminazione automatica dei processi senza un gestore di processi, è necessario impostare inizialmente la proprietà **onAllTasksComplete** di un nuovo processo su `noaction` , quindi impostarla su `terminatejob` *' solo dopo aver completato l'aggiunta di attività al processo.

### <a name="scheduled-jobs"></a>Processi pianificati

Le [pianificazioni dei processi](/rest/api/batchservice/jobschedule) consentono di creare processi ricorrenti nel servizio Batch. Una pianificazione del processo specifica quando eseguire i processi e include le specifiche per i processi da eseguire. È possibile specificare la durata della pianificazione (per quanto tempo e quando è effettiva la pianificazione) e con quale frequenza vengono creati i processi durante quell'intervallo di tempo.

## <a name="tasks"></a>Attività

Un'attività è un'unità di calcolo che viene associata a un processo e viene eseguita su un nodo. Le attività vengono assegnate a un nodo per l'esecuzione o vengono accodate fino a quando non diventa disponibile un nodo. In parole semplici, un'attività esegue uno o più programmi o script in un nodo di calcolo per eseguire le operazioni necessarie.

Quando si crea un'attività, è possibile specificare:

- La **riga di comando** dell'attività. È la riga di comando che esegue l'applicazione o lo script nel nodo di calcolo.

    È importante notare che la riga di comando non viene eseguita in una shell. Non può quindi usare in modo nativo i vantaggi delle funzionalità della shell, ad esempio l'espansione delle [variabili di ambiente](#environment-settings-for-tasks), incluso `PATH`. Per usare queste funzionalità, è necessario richiamare la shell nella riga di comando, ad esempio avviando `cmd.exe` nei nodi Windows o `/bin/sh` in Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Se le attività devono eseguire un'applicazione o uno script non presente nell'elemento `PATH` del nodo o fare riferimento alle variabili di ambiente, richiamare la shell in modo esplicito nella riga di comando dell'attività.
- **File di risorse** contenenti i dati da elaborare. Questi file vengono copiati automaticamente nel nodo dall'archivio BLOB in un account di archiviazione di Azure prima che venga eseguita la riga di comando dell'attività. Per altre informazioni, vedere [Attività di avvio](#start-task) e [File e directory](files-and-directories.md).
- **Variabili di ambiente** richieste dall'applicazione. Per altre informazioni, vedere [Impostazioni di ambiente per le attività](#environment-settings-for-tasks).
- **Vincoli** in base ai quali viene eseguita l'attività. Ad esempio, i vincoli includono l'intervallo di tempo massimo in cui l'attività può essere eseguita, il numero massimo di ripetizioni dei tentativi di un'attività non riuscita e l'intervallo di tempo massimo in cui i file vengono mantenuti nella directory di lavoro dell'attività.
- **Pacchetti dell'applicazione** per distribuire il nodo di calcolo in cui è pianificata l'esecuzione dell'attività. I [pacchetti dell'applicazione](batch-application-packages.md) consentono una distribuzione e un controllo delle versioni più semplici delle applicazioni eseguite dalle attività. I pacchetti dell'applicazione a livello di attività sono particolarmente utili in ambienti di pool condivisi, in cui diversi processi vengono eseguiti in un pool e il pool non viene eliminato quando viene completato un processo. Se il processo ha meno attività che nodi nel pool, i pacchetti dell'applicazione di attività possono ridurre il trasferimento dei dati, perché l'applicazione viene distribuita solo nei nodi che eseguono le attività.
- Un riferimento all'**immagine del contenitore** nell'hub Docker oppure in un registro privato e altre impostazioni per creare un contenitore Docker in cui l'attività viene eseguita nel nodo. Queste informazioni vengono specificate solo se il pool è definito con una configurazione del contenitore.

> [!NOTE]
> La durata massima di un'attività, da quando viene aggiunta al processo a quando viene completata, è di 180 giorni. Le attività completate vengono conservate per 7 giorni. I dati per le attività non completate entro la durata massima non sono accessibili.

Oltre alle attività definite dall'utente per eseguire il calcolo in un nodo, il servizio Batch fornisce anche numerose attività speciali:

- [Attività di avvio](#start-task)
- [Attività di gestione dei processi](#job-manager-task)
- [Attività di preparazione e rilascio dei processi](#job-preparation-and-release-tasks)
- [Attività a istanze multiple](#multi-instance-task)
- [Dipendenze dell'attività](#task-dependencies)

### <a name="start-task"></a>Attività di avvio

Associando un'attività di avvio a un pool, è possibile preparare l'ambiente operativo dei rispettivi nodi. È ad esempio possibile eseguire azioni quali l'installazione delle applicazioni che vengono eseguite dalle attività o l'avvio dei processi in background. L'attività di avvio viene eseguita ogni volta che viene avviato un nodo, purché rimanga nel pool. Ciò include il momento in cui il nodo viene aggiunto al pool e quando viene riavviato o ne viene ricreata l'immagine.

Il vantaggio principale dell'attività di avvio consiste nel fatto che può contenere tutte le informazioni necessarie per configurare un nodo di calcolo e installare le applicazioni richieste per l'esecuzione delle attività. Pertanto, l'aumento del numero di nodi in un pool è semplice quanto specificare il nuovo numero di nodi di destinazione. L'attività di avvio fornisce al servizio Batch le informazioni necessarie per configurare i nuovi nodi e prepararli ad accettare le attività.

Come per qualsiasi attività di Azure Batch, è possibile specificare un elenco di file di risorse in [Archiviazione di Azure](../storage/index.yml), oltre a una riga di comando da eseguire. Il servizio Batch copia prima di tutto i file di risorse nel nodo da Archiviazione di Azure, quindi esegue la riga di comando. Per un'attività di avvio del pool l'elenco di file include in genere l'applicazione dell'attività e le rispettive dipendenze.

L'attività di avvio può tuttavia includere anche dati di riferimento che devono essere usati da tutte le attività in esecuzione nel nodo di calcolo. La riga di comando di un'attività di avvio può ad esempio eseguire un'operazione `robocopy` per copiare i file dell'applicazione, che sono stati specificati come file di risorse e scaricati nel nodo, dalla [directory di lavoro](files-and-directories.md) dell'attività di avvio alla cartella **condivisa** ed eseguire quindi un file MSI o `setup.exe`.

È in genere consigliabile che il servizio Batch attenda il completamento dell'attività di avvio prima di considerare il nodo pronto per l'assegnazione di attività, ma questo comportamento è configurabile.

Se un'attività di avvio non riesce in un nodo di calcolo, lo stato del nodo viene aggiornato per riflettere l'errore e al nodo non viene assegnata alcuna attività. Un'attività di avvio può non riuscire se si verifica un problema durante la copia dei file di risorse dall'archiviazione o se il processo eseguito dalla riga di comando restituisce un codice di uscita diverso da zero.

Se si aggiunge o si aggiorna l'attività di avvio per un pool esistente, è necessario riavviare i relativi nodi di calcolo per applicare l'attività di avvio ai nodi.

>[!NOTE]
> Batch limita le dimensioni totali di un'attività di avvio, che include i file di risorse e le variabili di ambiente. Se è necessario ridurre le dimensioni di un'attività di avvio, è possibile usare uno dei due approcci:
>
> 1. È possibile usare i pacchetti dell'applicazione per distribuire le applicazioni o i dati in ogni nodo del pool di Batch. Per altre informazioni sui pacchetti dell'applicazione, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).
> 2. È possibile creare manualmente un archivio compresso contenente i file delle applicazioni. Caricare l'archivio compresso in Archiviazione di Azure come BLOB. Specificare l'archivio compresso come file di risorse per l'attività di avvio. Prima di eseguire la riga di comando per l'attività di avvio, decomprimere l'archivio dalla riga di comando. 
>
>    Per decomprimere l'archivio, è possibile usare lo strumento di archiviazione preferito. Sarà necessario includere lo strumento usato per decomprimere l'archivio come file di risorse per l'attività di avvio.

### <a name="job-manager-task"></a>Attività di gestione dei processi

In genere si usa un'attività del gestore di processi per controllare e/o monitorare l'esecuzione dei processi, ad esempio per creare e inviare le attività per un processo, determinare le attività aggiuntive da eseguire e stabilire quando il lavoro è stato completato.

Un'attività del gestore di processi, tuttavia, non è limitata a queste attività. Si tratta di un'attività completa, che consente di eseguire qualsiasi azione necessaria per il processo. Un'attività del gestore di processi può ad esempio scaricare un file specificato come parametro, analizzare il contenuto del file e inviare attività aggiuntive in base a quel contenuto.

Un'attività di gestione dei processi viene avviata prima di tutte le altre attività. Offre le funzionalità seguenti:

- Viene inviata automaticamente come attività dal servizio Batch quando si crea il processo.
- Viene pianificata per l'esecuzione prima di altre attività di un processo.
- Il nodo associato è l'ultimo a essere rimosso da un pool quando il pool viene ridimensionato.
- La sua terminazione può essere associata alla terminazione di tutte le attività del processo.
- A questa attività viene assegnata la priorità più alta quando deve essere riavviata. Se un nodo inattivo non è disponibile, il servizio Batch può terminare una delle altre attività in esecuzione nel pool per consentire l'esecuzione dell'attività del gestore di processi.
- Un'attività del gestore di processi in un processo non ha la priorità sulle attività di altri processi. Tra i processi vengono rispettate solo le priorità a livello di processo.

### <a name="job-preparation-and-release-tasks"></a>Attività di preparazione e rilascio dei processi

Batch fornisce le attività di preparazione dei processi per l'installazione dell'esecuzione pre-processo e le attività di rilascio del processo per la manutenzione o la pulizia post-processo.

un'attività di preparazione del processo viene eseguita in tutti i nodi di calcolo pianificati per l'esecuzione di attività, prima dell'esecuzione di qualsiasi altra attività di un processo. Ad esempio, per copiare i dati condivisi da tutte le attività, ma univoci per il processo, è possibile usare l'attività di preparazione del processo.

dopo il completamento di un processo, viene eseguita un'attività di rilascio del processo in ogni nodo del pool che ha eseguito almeno un'attività. Un'attività di rilascio del processo, ad esempio, può eliminare i dati copiati dall'attività di preparazione del processo o comprimere e caricare i dati del log di diagnostica.

Sia le attività di preparazione del processo sia quelle di rilascio del processo consentono di specificare una riga di comando da eseguire quando viene richiamata l'attività. Le attività offrono funzionalità quali download di file, esecuzione con privilegi elevati, variabili di ambiente personalizzate, durata massima di esecuzione, numero di tentativi e periodo di conservazione dei file.

Per altre informazioni sulle attività di preparazione e rilascio dei processi, vedere [Eseguire attività di preparazione e completamento dei processi nei nodi di calcolo di Azure Batch](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Attività a istanze multiple

Un'[attività a istanze multiple](batch-mpi.md) è un'attività configurata per l'esecuzione contemporanea in più nodi di calcolo. Con le attività a istanze multiple è possibile abilitare scenari di calcolo ad alte prestazioni che richiedono un gruppo di nodi di calcolo allocati insieme per elaborare un singolo carico di lavoro, ad esempio MPI (Message Passing Interface).

Per una discussione dettagliata sull'esecuzione di processi MPI in Batch usando la libreria Batch .NET, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](batch-mpi.md).

### <a name="task-dependencies"></a>Dipendenze dell'attività

Le [relazioni tra attività](batch-task-dependencies.md), come suggerito dal nome, consentono di specificare che un'attività dipende dal completamento di altre attività prima della rispettiva esecuzione. Questa funzionalità fornisce il supporto nelle situazioni in cui un'attività di tipo "downstream" usa l'output di un'attività di tipo "upstream" oppure quando un'attività upstream esegue un'inizializzazione richiesta da un'attività downstream.

Per usare questa funzionalità, è innanzitutto necessario [abilitare le relazioni tra attività](batch-task-dependencies.md#enable-task-dependencies
) nel processo batch. Per ogni attività che dipende da un'altra (o da più altre), specificare quindi le attività da cui dipende tale attività.

Con le relazioni tra attività, è possibile configurare scenari come i seguenti:

- L'*attivitàB* dipende dall'*attivitàA*, ovvero l'esecuzione dell'*attivitàB* inizierà solo dopo il completamento dell'*attivitàA*.
- L'*attivitàC* dipende sia dall'*attivitàA* che dall'*attivitàB*.
- L'*attivitàD* dipende da un intervallo di attività, ad esempio le attività da *1* a *10*, prima che venga eseguita.

Per altri dettagli, vedere [Relazioni tra attività in Azure Batch](batch-task-dependencies.md) e l'esempio di codice [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) nel repository [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples) in GitHub.

### <a name="environment-settings-for-tasks"></a>Impostazioni di ambiente per le attività

Ogni attività eseguita dal servizio Batch ha accesso a variabili di ambiente che imposta sui nodi di calcolo. Sono incluse variabili di ambiente definite dal servizio Batch ([service-defined](./batch-compute-node-environment-variables.md)) e variabili di ambiente personalizzate che è possibile definire per le attività. Le applicazioni e gli script eseguiti dalle attività hanno accesso a queste variabili di ambiente durante l'esecuzione.

È possibile impostare variabili di ambiente personalizzate a livello di attività o di processo popolando le proprietà delle *impostazioni di ambiente* per queste entità. Vedere ad esempio l'operazione [Aggiungere un'attività a un processo](/rest/api/batchservice/task/add?) (API Batch REST) o le proprietà [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) e [CloudJob.CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) in Batch .NET.

L'applicazione o il servizio client può ottenere le variabili di ambiente di un'attività, sia quelle definite dal servizio che quelle personalizzate, usando l'operazione [Ottenere informazioni su un'attività](/rest/api/batchservice/task/get) (Batch REST) o accedendo alla proprietà [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) (Batch .NET). I processi eseguiti in un nodo di calcolo possono accedere a queste e ad altre variabili di ambiente nel nodo, ad esempio usando la sintassi familiare `%VARIABLE_NAME%` (Windows) o `$VARIABLE_NAME` (Linux).

È possibile trovare un elenco completo di tutte le variabili di ambiente definite dal servizio in [Variabili di ambiente per i nodi di calcolo](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [File e directory](files-and-directories.md).
