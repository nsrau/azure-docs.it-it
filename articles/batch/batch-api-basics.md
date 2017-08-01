---
title: Panoramica di Azure Batch per sviluppatori | Microsoft Docs
description: "Informazioni sulle funzionalità del servizio Batch e le relative API dal punto di vista dello sviluppatore."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 93f80018d71368c800abd3dceb42b2ab51e60659
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017


---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Sviluppare soluzioni di calcolo parallele su larga scala con Batch

Questa panoramica dei componenti di base del servizio Azure Batch illustra le funzionalità e le risorse primarie del servizio che gli sviluppatori di Batch possono usare per compilare soluzioni di calcolo parallele su larga scala.

Si usano molte delle risorse e delle funzionalità illustrate in questo articolo, sia per sviluppare un'applicazione o un servizio di calcolo distribuito che rilascia chiamate [API REST][batch_rest_api] dirette che quando si usa uno degli [SDK di Batch](batch-apis-tools.md#azure-accounts-for-batch-development).

> [!TIP]
> Per un'introduzione più generale al servizio Batch, vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-technical-overview.md).
>
>

## <a name="batch-service-workflow"></a>Flusso di lavoro servizio Batch
Il flusso di lavoro generale seguente è proprio di quasi tutte le applicazioni e i servizi che usano il servizio Batch per l'elaborazione di carichi di lavoro paralleli:

1. Caricare i **file di dati** da elaborare in un account di [archiviazione di Azure][azure_storage]. Batch include il supporto predefinito per accedere all'archivio BLOB di Azure e le attività possono scaricare questi file nei [nodi di calcolo](#compute-node) quando vengono eseguite.
2. Caricare i **file dell'applicazione** che verranno eseguiti dalle attività. Questi file possono essere binari o script con le relative dipendenze e vengono eseguiti dalle attività nei processi. Le attività possono scaricare questi file dall'account di archiviazione oppure è possibile usare la funzionalità [Pacchetti dell'applicazione](#application-packages) di Batch per la gestione e la distribuzione di applicazioni.
3. Creare un [pool](#pool) di nodi di calcolo. Quando si crea un pool, si specifica il numero di nodi di calcolo per il pool, le dimensioni e il sistema operativo. Quando ogni attività del processo viene eseguita, viene assegnata per l'esecuzione a uno dei nodi del pool.
4. Creare un [processo](#job). Un processo gestisce una raccolta di attività. Ogni processo viene associato a un pool specifico in cui verranno eseguite le attività del processo.
5. Aggiungere [attività](#task) al processo. Ogni attività esegue l'applicazione o lo script caricato per elaborare i file di dati scaricati dall'account di archiviazione. Ogni attività, una volta completata, può caricare l'output in Archiviazione di Azure.
6. Monitorare lo stato del processo e recuperare l'output dell'attività da Archiviazione di Azure.

Le sezioni seguenti illustrano queste e altre risorse di Batch che abilitano lo scenario di calcolo distribuito.

> [!NOTE]
> Per usare il servizio Batch, è necessario un [account Batch](#account). Quasi tutte le soluzioni usano anche un account di [archiviazione di Azure][azure_storage] per il recupero e l'archiviazione dei file. Batch supporta attualmente solo il tipo di account di archiviazione **Utilizzo generico**, come descritto nel passaggio 5 di [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) in [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).
>
>

## <a name="batch-service-resources"></a>Risorse del servizio Batch
Alcune delle risorse seguenti, ovvero account, nodi di calcolo, pool, processi e attività, sono richieste da tutte le soluzioni che usano il servizio Batch. Altre risorse, come le pianificazioni di processi e i pacchetti dell'applicazione, sono funzionalità utili ma facoltative.

* [Account](#account)
* [Nodo di calcolo](#compute-node)
* [Pool](#pool)
* [Processo](#job)

  * [Pianificazioni dei processi](#scheduled-jobs)
* [Attività](#task)

  * [Attività di avvio](#start-task)
  * [Attività di gestione dei processi](#job-manager-task)
  * [Attività di preparazione e rilascio dei processi](#job-preparation-and-release-tasks)
  * [Attività a istanze multiple (MPI)](#multi-instance-tasks)
  * [Dipendenze dell'attività](#task-dependencies)
* [Pacchetti dell'applicazione](#application-packages)

## <a name="account"></a>Account
Un account Batch è un'entità identificata in modo univoco all'interno del servizio Batch. Tutte le operazioni di elaborazione sono associata a un account Batch.

È possibile creare un account Batch di Azure usando il [portale di Azure](batch-account-create-portal.md) o a livello di codice, ad esempio con la [libreria di gestione .NET per Batch](batch-management-dotnet.md). Quando si crea l'account, è possibile associarvi un account di archiviazione di Azure.

Batch supporta due configurazioni dell'account e quando si crea l'account Batch è necessario selezionare la configurazione appropriata. La differenza tra le due configurazioni dell'account riguarda la modalità di allocazione dei [pool](#pool) di Batch per l'account. È possibile allocare pool di nodi di calcolo in una sottoscrizione gestita da Azure Batch oppure è possibile allocarli nella propria sottoscrizione. La proprietà *Modalità di allocazione pool* per l'account determina la configurazione usata. 

Per decidere quale configurazione dell'account usare, considerare ciò che è più adatto al proprio scenario:

* **Servizio Batch**: Servizio Batch rappresenta la configurazione dell'account predefinita. Per un account creato con questa configurazione, i pool di Batch vengono allocati in background in sottoscrizioni gestite da Azure. Tenere presenti questi punti importanti relativi alla configurazione dell'account di tipo Servizio Batch:

    - La configurazione dell'account di tipo Servizio Batch supporta sia pool del servizio cloud che pool di macchine virtuali.
    - La configurazione dell'account di tipo Servizio Batch supporta l'accesso alle API Batch usando l'autenticazione con chiave condivisa o l'[autenticazione di Azure Active Directory](batch-aad-auth.md). 
    - È possibile usare nodi di calcolo dedicati o con priorità bassa in pool nella configurazione dell'account di tipo Servizio Batch.
    - Non usare la configurazione dell'account di tipo Servizio Batch se si prevede di creare pool di macchine virtuali di Azure da immagini di VM personalizzate o se si prevede di usare una rete virtuale. Creare invece l'account con la configurazione Sottoscrizione utente.
    - I pool di macchine virtuali di cui viene effettuato il provisioning in un account con la configurazione dell'account della sottoscrizione di tipo Servizio Batch devono essere creati da immagini del [Marketplace per Macchine virtuali di Azure][vm_marketplace].

* **Sottoscrizione utente**: con la configurazione dell'account di tipo Sottoscrizione utente, i pool di Batch vengono allocati nella sottoscrizione di Azure in cui viene creato l'account. Tenere presenti questi punti importanti relativi alla configurazione dell'account di tipo Sottoscrizione utente:
     
    - La configurazione dell'account di tipo Sottoscrizione utente supporta solo pool di macchine virtuali. Non supporta pool di Servizi cloud.
    - Per creare pool di macchine virtuali da immagini di VM personalizzate o per usare una rete virtuale con i pool di macchine virtuali, è necessario usare la configurazione di tipo Sottoscrizione utente.  
    - È necessario autenticare le richieste al servizio Batch usando l'[autenticazione di Azure Active Directory](batch-aad-auth.md). 
    - La configurazione dell'account di tipo Sottoscrizione utente richiede l'impostazione di un'istanza di Azure Key Vault per l'account Batch. 
    - È possibile usare solo nodi di calcolo dedicati in pool nella configurazione dell'account di tipo Sottoscrizione utente. I nodi con priorità bassa non sono supportati.
    - I pool di macchine virtuali di cui viene effettuato il provisioning in un account con la configurazione dell'account di tipo Sottoscrizione utente devono essere creati da immagini del [Marketplace per Macchine virtuali di Azure][vm_marketplace] o da immagini personalizzate.

> [!IMPORTANT]
> Batch supporta attualmente solo il tipo di account di archiviazione per utilizzo generico, come descritto nel passaggio 5 di [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) in [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). Le attività di Batch, incluse le attività standard, le attività di avvio, le attività di preparazione del processo e le attività di rilascio del processo, devono specificare file di risorse che si trovano negli account di archiviazione per utilizzo generico.
>
>


## <a name="compute-node"></a>Nodo di calcolo
Un nodo di calcolo è una macchina virtuale (VM) di Azure o una VM del servizio cloud dedicata all'elaborazione di una parte del carico di lavoro dell'applicazione. Le dimensioni di un nodo determinano il numero di core CPU, la capacità di memoria e le dimensioni del file system locale allocati al nodo. È possibile creare pool di nodi Windows o Linux usando le immagini del Marketplace per servizi cloud o macchine virtuali di Azure. Per altre informazioni su queste opzioni, vedere la sezione [Pool](#pool) seguente.

I nodi possono eseguire qualsiasi eseguibile o script supportato dall'ambiente del sistema operativo del nodo, inclusi \*.exe, \*.cmd, \*.bat e script di PowerShell per Windows e file binari, shell e script di Python per Linux.

Tutti i nodi di Calcolo in Batch includono anche:

* Una [struttura di cartelle](#files-and-directories) standard e le [variabili di ambiente](#environment-settings-for-tasks) associate disponibili come riferimento per le attività.
* **firewall** configurate per controllare l'accesso.
* [Accesso remoto](#connecting-to-compute-nodes) ai nodi Windows (Remote Desktop Protocol (RDP)) e Linux (Secure Shell (SSH)).

## <a name="pool"></a>pool
Un pool è una raccolta di nodi in cui viene eseguita l'applicazione. Il pool può essere creato manualmente dall'utente o automaticamente dal servizio Batch quando si specifica il lavoro da eseguire. È possibile creare e gestire un pool che soddisfi i requisiti relativi alle risorse dell'applicazione. Un pool può essere usato solo dall'account Batch in cui è stato creato. Un account Batch può avere più pool.

I pool di Azure Batch sono basati sulla piattaforma di calcolo Azure. Offrono allocazione su larga scala, installazione di applicazioni, distribuzione dei dati, monitoraggio dell'integrità e regolazione flessibile del numero di nodi di calcolo in un pool ([ridimensionamento](#scaling-compute-resources)).

A ogni nodo aggiunto a un pool viene assegnato un nome univoco e un indirizzo IP. Quando un nodo viene rimosso da un pool, vengono perse tutte le modifiche apportate al sistema operativo o ai file e il relativo nome e indirizzo IP vengono rilasciati per uso futuro. Quando un nodo esce da un pool, la sua durata è terminata.

Quando si crea un pool, è possibile specificare gli attributi seguenti. Alcune impostazioni sono diverse, a seconda della modalità di allocazione pool dell'[account](#account) Batch:

- Versione e sistema operativo dei nodi di calcolo
- Tipo di nodo di calcolo e numero di nodi di destinazione
- Dimensioni dei nodi di calcolo
- Criteri di ridimensionamento
- Criteri di pianificazione attività
- Stato delle comunicazioni dei nodi di calcolo
- Attività di avvio per i nodi di calcolo
- Pacchetti dell'applicazione
- Network configuration

Ognuna di queste sezioni è descritta in modo più dettagliato nelle sezioni seguenti.

> [!IMPORTANT]
> Gli account Batch creati con la configurazione Servizio Batch hanno una quota predefinita che limita il numero di core in un account Batch. Il numero di core corrisponde al numero di nodi di calcolo. Le quote predefinite e le istruzioni su come [aumentare una quota](batch-quota-limit.md#increase-a-quota) sono disponibili in [Quote e limiti del servizio Batch](batch-quota-limit.md). Se il pool non raggiungere il numero di nodi di destinazione, la causa potrebbe essere la quota di core.
>
>Gli account Batch creati con la configurazione Sottoscrizione utente non sono limitati dalle quote del servizio Batch. Condividono invece la quota di core per la sottoscrizione specificata. Per altre informazioni, vedere [Limiti relativi a Macchine virtuali](../azure-subscription-service-limits.md#virtual-machines-limits) in [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

### <a name="compute-node-operating-system-and-version"></a>Versione e sistema operativo dei nodi di calcolo

Quando si crea un pool di Batch, è possibile specificare la configurazione delle macchine virtuali di Azure e il tipo di sistema operativo da eseguire in ogni nodo di calcolo nel pool. I due tipi di configurazioni disponibili in Batch sono:

- **Configurazione macchina virtuale**, che specifica che il pool è composto da macchine virtuali di Azure. È possibile creare queste VM da immagini Linux o Windows. 

    Quando si crea un pool basato su Configurazione macchina virtuale, è necessario specificare non solo le dimensioni dei nodi e l'origine delle immagini usate per crearli, ma anche il **riferimento a un'immagine della macchina virtuale** e lo **SKU dell'agente del nodo** Batch da installare nei nodi. Per altre informazioni sulla specifica di queste proprietà del pool, vedere [Effettuare il provisioning di nodi di calcolo Linux nei pool di Azure Batch](batch-linux-nodes.md).

- **Cloud Services Configuration** (Configurazione servizi cloud), che specifica che il pool è composto da nodi di Servizi cloud di Azure. Servizi cloud fornisce *solo* nodi di calcolo di Windows.

    I sistemi operativi disponibili per i pool Configurazione servizi cloud sono elencati in [Rilasci del sistema operativo guest Azure e matrice di compatibilità dell'SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando si crea un pool contenente nodi di Servizi cloud, è necessario specificare dimensioni dei nodi e *Famiglia del sistema operativo*. La distribuzione di Servizi cloud in Azure è più veloce rispetto alle macchine virtuali che eseguono Windows. Se si vuole disporre di pool di nodi di calcolo di Windows, le prestazioni di Servizi cloud in termini di tempo di distribuzione potrebbero essere migliori.

    * La *famiglia del sistema operativo* determina anche le versioni di .NET installate con il sistema operativo.
    * Analogamente ai ruoli di lavoro nei servizi cloud, è possibile specificare una *Versione sistema operativo*. Per altre informazioni sui ruoli di lavoro, vedere la sezione [Informazioni sui servizi cloud](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) in [Perché scegliere Servizi cloud](../cloud-services/cloud-services-choose-me.md).
    * Analogamente ai ruoli di lavoro, è consigliabile specificare `*` per la *Versione sistema operativo*, in modo che i nodi vengano aggiornati automaticamente senza doversi occupare delle nuove versioni rilasciate. Il caso d'uso principale per la selezione di una versione specifica del sistema operativo consiste nell'assicurare la compatibilità delle applicazioni, che permette l'esecuzione del test di compatibilità con le versioni precedenti prima di consentire l'aggiornamento della versione. Dopo la convalida, la *Versione sistema operativo* per il pool può essere aggiornata ed è possibile installare la nuova immagine del sistema operativo. Eventuali attività in esecuzione vengono interrotte e accodate di nuovo.

Vedere la sezione [Account](#account) per informazioni sull'impostazione della modalità di allocazione dei pool quando si crea un account Batch.

#### <a name="custom-images-for-virtual-machine-pools"></a>Immagini personalizzate per pool di macchine virtuali

Per usare immagini personalizzate per i pool di macchine virtuali, creare l'account Batch con la configurazione dell'account Sottoscrizione utente. Con questa configurazione, i pool di Batch vengono allocati nella sottoscrizione in cui si trova l'account. Vedere la sezione [Account](#account) per informazioni sull'impostazione della modalità di allocazione dei pool quando si crea un account Batch.

Per creare un pool con l'opzione Configurazione macchina virtuale usando un'immagine personalizzata, sono necessari uno o più account di Archiviazione di Azure standard per archiviare le immagini del disco rigido virtuale personalizzate. Le immagini personalizzate vengono archiviate come BLOB. Per fare riferimento alle immagini personalizzate quando si crea un pool, specificare gli URI dei BLOB dei dischi rigidi virtuali delle immagini personalizzate per la proprietà [osDisk](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_osdisk) della proprietà [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_vmconf).

Assicurarsi che gli account di archiviazione soddisfino i criteri seguenti:   

- Gli account di archiviazione contenenti i BLOB dei dischi rigidi virtuali delle immagini personalizzate devono trovarsi nella stessa sottoscrizione dell'account Batch (la sottoscrizione utente).
- Gli account di archiviazione specificati devono trovarsi nella stessa regione dell'account Batch.
- Attualmente sono supportati solo gli account di archiviazione standard. Il supporto per Archiviazione Premium di Azure verrà aggiunto in futuro.
- È possibile specificare un account di archiviazione con più BLOB dei dischi rigidi virtuali personalizzati o più account di archiviazione ognuno con un singolo BLOB. È consigliabile usare più account di archiviazione per ottenere prestazioni migliori.
- Un BLOB del disco rigido virtuale di un'immagine personalizzata univoco può supportare fino a 40 istanze di VM Linux o 20 istanze di VM Windows. Per creare pool con più VM, è necessario creare copie del BLOB del disco rigido virtuale. Un pool con 200 VM Windows richiede ad esempio 10 BLOB dei dischi rigidi virtuali univoci specificati per la proprietà **osDisk**.

Quando si crea un pool, è necessario selezionare il valore appropriato di **nodeAgentSkuId**, a seconda del sistema operativo dell'immagine di base del disco rigido virtuale. È possibile ottenere un mapping tra gli ID SKU dell'agente del nodo e i relativi riferimenti all'immagine del sistema operativo chiamando l'operazione di [elenco degli SKU degli agenti dei nodi supportati](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus).

Per creare un pool da un'immagine personalizzata tramite il portale di Azure:

1. Passare all'account Batch nel portale di Azure.
2. Nel pannello **Impostazioni** selezionare la voce di menu **Pool**.
3. Nel pannello **Pool** selezionare il comando **Aggiungi**. Verrà visualizzato il pannello **Aggiungi pool**.
4. Selezionare **Immagine personalizzata (Linux/Windows)** nell'elenco a discesa **Tipo di immagine**. Nel portale verrà visualizzato lo strumento di selezione **Immagine personalizzata**. Scegliere uno o più dischi rigidi virtuali dello stesso contenitore e fare clic sul pulsante **Seleziona**. 
    Il supporto per più dischi rigidi virtuali di account di archiviazione e contenitori diversi verrà aggiunto in futuro.
5. Selezionare il valore appropriato per **Autore/Offerta/SKU** per i dischi rigidi virtuali personalizzati, selezionare la modalità **Caching** desiderata e quindi specificare tutti gli altri parametri per il pool.
6. Per verificare se un pool è basato su un'immagine personalizzata, vedere la proprietà **Sistema operativo** nella sezione di riepilogo delle risorse del pannello **Pool**. Il valore di questa proprietà deve essere **Immagine di macchina virtuale personalizzata**.
7. Tutti i dischi rigidi virtuali personalizzati associati a un pool vengono visualizzati nel pannello **Proprietà** del pool.

### <a name="compute-node-type-and-target-number-of-nodes"></a>Tipo di nodo di calcolo e numero di nodi di destinazione

Quando si crea un pool, è possibile specificare i tipi di nodi di calcolo desiderati e il numero di destinazione di ognuno. I due tipi di nodi di calcolo sono i seguenti:

- **Nodi di calcolo dedicati.** I nodi di calcolo dedicati sono riservati per i carichi di lavoro. Sono più costosi dei nodi con priorità bassa, ma non vengono mai superati.

- **Nodi di calcolo con priorità bassa.** I nodi con priorità bassa sfruttano la capacità in eccesso di Azure per l'esecuzione dei carichi di lavoro di Batch. I nodi con priorità bassa hanno un costo orario inferiore a quello dei nodi dedicati e supportano carichi di lavoro che richiedono una potenza di calcolo elevata. Per altre informazioni, vedere [Usare le VM con priorità bassa in Batch](batch-low-pri-vms.md).

    Può verificarsi il superamento dei nodi di calcolo con priorità bassa quando Azure ha capacità in eccesso insufficiente. In caso di superamento di un nodo durante l'esecuzione di attività, le attività vengono accodate ed eseguite di nuovo quando il nodo di calcolo torna disponibile. I nodi con priorità bassa sono utili per i carichi di lavoro con tempi di completamento del processo flessibili e in cui il lavoro è distribuito tra più nodi. Prima di scegliere di usare nodi con priorità bassa per lo scenario, assicurarsi che l'eventuale quantità di lavoro persa a causa del processo di prelazione sia minima e facile da ricreare.

    I nodi di calcolo con priorità bassa sono disponibili solo per gli account Batch creati con la modalità di allocazione pool impostata su **Servizio Batch**.

Lo stesso pool può includere nodi di calcolo con priorità bassa e nodi di calcolo dedicati. Ogni tipo di nodo, con priorità bassa e dedicato, ha un'impostazione di destinazione propria, per cui è possibile specificare il numero desiderato di nodi. 
    
Il numero di nodi di calcolo viene definito *numero di destinazione*, perché in alcuni casi il pool può non raggiungere il numero desiderato di nodi. Ad esempio, un pool potrebbe non realizzare la destinazione se prima raggiunge la [quota core](batch-quota-limit.md) per l'account Batch. Il pool potrebbe non realizzare la destinazione, inoltre, se al pool è stata applicata una formula di scalabilità automatica che limita il numero massimo di nodi.

Per informazioni sui prezzi per i nodi di calcolo dedicati e con priorità bassa, vedere [Prezzi di Batch](https://azure.microsoft.com/pricing/details/batch/).

### <a name="size-of-the-compute-nodes"></a>Dimensioni dei nodi di calcolo

**Cloud Services Configuration** (Configurazione servizi cloud) sono elencate in [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md). Batch supporta tutte le dimensioni dei servizi cloud tranne `ExtraSmall`, `STANDARD_A1_V2` e `STANDARD_A2_V2`.

Le dimensioni disponibili per i nodi di calcolo **Configurazione macchina virtuale** sono elencate in [Dimensioni delle macchine virtuali in Azure](../virtual-machines/linux/sizes.md) (Linux) e [Dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md) (Windows). Batch supporta tutte le dimensioni delle VM di Azure tranne `STANDARD_A0` e quelle con l'archiviazione Premium (serie `STANDARD_GS`, `STANDARD_DS` e `STANDARD_DSV2`).

Quando si seleziona una dimensione per il nodo di calcolo, tenere in considerazione le caratteristiche e i requisiti delle applicazioni che si eseguiranno nei nodi. Per determinare la dimensioni del nodo più appropriate e convenienti, considerare vari aspetti, ad esempio se si tratta di un'applicazione multithreading e la quantità di memoria che utilizza. Le dimensioni del nodo vengono in genere selezionate presupponendo che in un nodo venga eseguita un'attività alla volta. È possibile,tuttavia, che più attività (e quindi più istanze dell'applicazione) vengano [eseguite in parallelo](batch-parallel-node-tasks.md) nei nodi di calcolo durante l'esecuzione del processo. In questo caso, è normale scegliere una dimensione maggiore per il nodo per soddisfare la richiesta più elevata di esecuzione di attività parallele. Per altre informazioni, vedere [Criteri di pianificazione delle attività](#task-scheduling-policy).

Tutti i nodi in un pool devono hanno le stesse dimensioni. Se si prevede di eseguire applicazioni con requisiti di sistema e/o livelli di carico diversi, è consigliabile usare pool separati.

### <a name="scaling-policy"></a>Criteri di ridimensionamento

Per carichi di lavoro dinamici, è possibile scrivere e applicare una [formula di ridimensionamento automatico](#scaling-compute-resources) a un pool. Il servizio Batch valuta periodicamente la formula e rettifica il numero di nodi nel pool in base a vari parametri relativi a pool, processi e attività specificati.

### <a name="task-scheduling-policy"></a>Criteri di pianificazione attività

L'opzione di configurazione [Numero massimo attività per nodo](batch-parallel-node-tasks.md) determina il numero massimo di attività che è possibile eseguire in parallelo in ogni nodo di calcolo del pool.

La configurazione predefinita specifica che viene eseguita una sola attività alla volta in un nodo, ma in alcuni scenari risulta utile eseguire due o più attività contemporaneamente in un nodo. Vedere lo [scenario di esempio](batch-parallel-node-tasks.md#example-scenario) nell'articolo [Ottimizzare l'utilizzo delle risorse di calcolo di Azure Batch con attività dei nodi simultanee](batch-parallel-node-tasks.md) per informazioni su come sfruttare al meglio più attività per nodo.

È anche possibile specificare un *tipo di riempimento* che determina se Batch distribuisce le attività in modo uniforme a tutti i nodi di un pool o se satura ogni nodo con il numero massimo di attività prima di assegnarle ad altri nodi.

### <a name="communication-status-for-compute-nodes"></a>Stato delle comunicazioni dei nodi di calcolo

Nella maggior parte degli scenari le attività funzionano in modo indipendente e non devono comunicare tra loro. È tuttavia possibile che siano presenti applicazioni in cui le attività devono comunicare, ad esempio negli [scenari MPI](batch-mpi.md).

È possibile configurare un pool per consentire la **comunicazione tra nodi**, di modo che i nodi all'interno di un pool passano comunicare durante l'esecuzione. Quando la comunicazione tra nodi è abilitata, i nodi nei pool Cloud Services Configuration (Configurazione servizi cloud) possono comunicare tra loro tramite porte superiori alla 1100 e i pool Configurazione macchina virtuale non limitano il traffico su nessuna porta.

Si noti che l'abilitazione della comunicazione tra nodi ha effetto anche sul posizionamento dei nodi nel cluster e potrebbe limitare il numero massimo di nodi in un pool a causa di restrizioni relative alla distribuzione. Se l'applicazione non richiede la comunicazione tra nodi, il servizio Batch può allocare al pool un numero potenzialmente elevato di nodi da diversi cluster e data center per consentire una maggiore capacità di elaborazione parallela.

### <a name="start-tasks-for-compute-nodes"></a>Attività di avvio per i nodi di calcolo

L' *attività di avvio* facoltativa viene eseguita in ogni nodo aggiunto al pool e ogni volta che si riavvia o si ricrea l'immagine del nodo. L'attività di avvio è utile soprattutto per preparare i nodi di calcolo per l'esecuzione di attività, ad esempio l'installazione delle applicazioni eseguite dalle attività nei nodi di calcolo.

### <a name="application-packages"></a>Pacchetti dell'applicazione

È possibile specificare [pacchetti dell'applicazione](#application-packages) da distribuire nei nodi di calcolo del pool. I pacchetti dell'applicazione consentono una distribuzione e un controllo delle versioni più semplici delle applicazioni eseguite dalle attività. I pacchetti dell'applicazione specificati per un pool vengono installati in ogni nodo di calcolo aggiunto al pool e ogni volta che un nodo viene riavviato o ne viene ricreata l'immagine.

> [!NOTE]
> I pacchetti dell'applicazione sono supportati in tutti i pool di Batch creati dopo il 5 luglio 2017. Sono supportati nei pool di Batch creati tra il 10 marzo 2016 e il 5 luglio 2017 solo se il pool è stato creato usando una configurazione del servizio cloud. I pool di Batch creati prima del 10 marzo 2016 non supportano i pacchetti dell'applicazione. Per altre informazioni sull'uso di pacchetti dell'applicazione per distribuire applicazioni ai nodi Batch, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).
>
>

### <a name="network-configuration"></a>Network configuration

È possibile specificare la subnet di una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure in cui devono essere creati i nodi di calcolo del pool. Per altre informazioni, vedere la sezione [Pool della configurazione di rete](#pool-network-configuration).


## <a name="job"></a>Processo
Un processo è una raccolta di attività. Gestisce la modalità di esecuzione dei calcoli da parte delle attività nei nodi di calcolo di un pool.

* Il processo specifica il **pool** in cui eseguire il lavoro. È possibile creare un nuovo pool per ogni processo o usare un pool per più processi. È possibile creare un pool per ogni processo associato a una pianificazione o per tutti i processi associati a una pianificazione.
* È possibile specificare una **priorità del processo** facoltativa. Quando si invia un processo con priorità più alta rispetto ad altri processi in corso, le attività del processo con la priorità più alta vengono inserite nella coda prima delle attività del processo con priorità più bassa. Le attività nei processi con priorità più bassa già in esecuzione non vengono messe in attesa.
* È possibile usare i **vincoli** del processo per specificare determinati limiti per i processi:

    È possibile impostare un **tempo massimo** in modo che, se un processo viene eseguito per un periodo più lungo del tempo specificato, il processo e tutte le attività vengano terminati.

    Batch può rilevare e quindi provare a eseguire di nuovo le attività non riuscite. È possibile specificare il **numero massimo di tentativi per l'attività** sotto forma di vincolo, indicando anche se un'attività viene *sempre* ripetuta oppure *mai*. Per nuovo tentativo si intende che l'attività viene riaccodata per essere eseguita di nuovo.
* L'applicazione client può aggiungere attività a un processo oppure è possibile specificare un'[attività del gestore di processi](#job-manager-task). Un'attività del gestore di processi contiene le informazioni necessarie per creare le attività obbligatorie per un processo. L'attività del gestore di processi viene eseguita in uno dei nodi di calcolo del pool. L'attività del gestore di processi viene gestita in modo specifico da Batch, ovvero viene accodata non appena si crea il processo e viene riavviata se l'operazione non riesce. Un'attività del gestore di processi è *obbligatoria* per i processi creati da una [pianificazione di processi](#scheduled-jobs), perché è l'unico modo per definire le attività prima della creazione di istanze del processo.
* Per impostazione predefinita, i processi rimangono nello stato attivo dopo il completamento di tutte le attività del processo. È possibile modificare questo comportamento in modo che il processo venga terminato automaticamente una volta completate tutte le relative attività. Impostare la proprietà **onAllTasksComplete** del processo ([OnAllTasksComplete][net_onalltaskscomplete] in Batch .NET) su *terminatejob* per terminare automaticamente il processo quando tutte le relative attività risultano completate.

    Si noti che il servizio Batch considera un processo *senza* attività quando ha tutte le relative attività completate. Di conseguenza, questa opzione viene usata più comunemente con un' [attività del gestore di processi](#job-manager-task). Se si vuole usare la chiusura automatica di processi senza un gestore di processi, è necessario impostare inizialmente la proprietà **onAllTasksComplete** di un nuovo processo su *noaction*, quindi impostarlo su *terminatejob* solo dopo aver completato l'aggiunta di attività al processo.

### <a name="job-priority"></a>Priorità del processo
È possibile assegnare una priorità ai processi creati in Batch. Il servizio Batch usa il valore di priorità del processo per determinare l'ordine di programmazione dei processi in un account, da non confondere con un [processo pianificato](#scheduled-jobs). I valori di priorità sono compresi in un intervallo da -1000 a 1000, dove -1000 è la priorità più bassa e 1000 la più alta. Per aggiornare la priorità di un processo richiamare l'operazione [Aggiornare le proprietà di un processo][rest_update_job] (Batch REST) o modificare la proprietà [CloudJob.Priority][net_cloudjob_priority] (Batch .NET).

All'interno dello stesso account i processi con priorità più alta hanno precedenza di pianificazione rispetto ai processi con priorità inferiori. Un processo con un valore di priorità più elevato in un account non ha tale precedenza di pianificazione rispetto a un altro processo con un valore di priorità inferiore in un account diverso.

La pianificazione di attività dei pool è indipendente. Tra pool diversi, non è garantito che un processo con priorità più elevato venga pianificato per primo se il relativo pool associato non ha un numero sufficiente di nodi inattivi. Nello stesso pool i processi con lo stesso livello di priorità hanno la stessa probabilità di essere pianificati.

### <a name="scheduled-jobs"></a>Scheduled jobs
Le [programmazioni dei processi][rest_job_schedules] consentono di creare processi ricorrenti nel servizio Batch. Una pianificazione del processo specifica quando eseguire i processi e include le specifiche per i processi da eseguire. È possibile specificare la durata della pianificazione, per quanto tempo e quando è effettiva la pianificazione, e con quale frequenza vengono creati i processi durante quell'intervallo di tempo.

## <a name="task"></a>Attività
Un'attività è un'unità di calcolo che viene associata a un processo e viene eseguita su un nodo. Le attività vengono assegnate a un nodo per l'esecuzione o vengono accodate fino a quando non diventa disponibile un nodo. In parole semplici, un'attività esegue uno o più programmi o script in un nodo di calcolo per eseguire le operazioni necessarie.

Quando si crea un'attività, è possibile specificare:

* La **riga di comando** dell'attività. È la riga di comando che esegue l'applicazione o lo script nel nodo di calcolo.

    È importante notare che la riga di comando non viene effettivamente eseguita in una shell. Non può quindi usare in modo nativo i vantaggi delle funzionalità della shell, ad esempio l'espansione delle [variabili di ambiente](#environment-settings-for-tasks), incluso `PATH`. Per usare queste funzionalità, è necessario richiamare la shell nella riga di comando, ad esempio avviando `cmd.exe` nei nodi Windows o `/bin/sh` in Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Se le attività devono eseguire un'applicazione o uno script non presente nell'elemento `PATH` del nodo o fare riferimento alle variabili di ambiente, richiamare la shell in modo esplicito nella riga di comando dell'attività.
* **File di risorse** contenenti i dati da elaborare. Questi file vengono copiati automaticamente nel nodo dall'archivio BLOB in un account di archiviazione di Azure di uso generico prima che venga eseguita la riga di comando dell'attività. Per altre informazioni, vedere le sezioni [Attività di avvio](#start-task) e [File e directory](#files-and-directories).
* **Variabili di ambiente** richieste dall'applicazione. Per altre informazioni, vedere la sezione [Impostazioni di ambiente per le attività](#environment-settings-for-tasks) .
* **Vincoli** in base ai quali viene eseguita l'attività. Ad esempio, i vincoli includono l'intervallo di tempo massimo in cui l'attività può essere eseguita, il numero massimo di ripetizioni dei tentativi di un'attività non riuscita e l'intervallo di tempo massimo in cui i file vengono mantenuti nella directory di lavoro dell'attività.
* **Pacchetti dell'applicazione** per distribuire il nodo di calcolo in cui è pianificata l'esecuzione dell'attività. [Application packages](#application-packages) consentono una distribuzione e un controllo delle versioni più semplici delle applicazioni eseguite dalle attività. I pacchetti dell'applicazione a livello di attività sono particolarmente utili in ambienti di pool condivisi, in cui diversi processi vengono eseguiti in un pool e il pool non viene eliminato quando viene completato un processo. Se il processo ha meno attività che nodi nel pool, i pacchetti dell'applicazione di attività possono ridurre il trasferimento dei dati, perché l'applicazione viene distribuita solo nei nodi che eseguono le attività.

Oltre alle attività definite dall'utente per eseguire il calcolo in un nodo, il servizio Batch fornisce anche le attività speciali seguenti:

* [Attività di avvio](#start-task)
* [Attività di gestione dei processi](#job-manager-task)
* [Attività di preparazione e rilascio dei processi](#job-preparation-and-release-tasks)
* [Attività a istanze multiple (MPI)](#multi-instance-tasks)
* [Dipendenze dell'attività](#task-dependencies)

### <a name="start-task"></a>Attività di avvio
Associando un' **attività di avvio** a un pool, è possibile preparare l'ambiente operativo dei rispettivi nodi. È ad esempio possibile eseguire azioni quali l'installazione delle applicazioni che vengono eseguite dalle attività o l'avvio dei processi in background. L'attività di avvio viene eseguita a ogni avvio di un nodo per tutto il tempo in cui questa rimane nel pool, incluso il momento in cui il nodo viene aggiunto al pool e in cui viene riavviato o ne viene ricreata l'immagine.

Il vantaggio principale dell'attività di avvio consiste nel fatto che può contenere tutte le informazioni necessarie per configurare un nodo di calcolo e installare le applicazioni necessarie per l'esecuzione dell'attività. Pertanto, l'aumento del numero di nodi in un pool è semplice quanto specificare il nuovo numero di nodi di destinazione. L'attività di avvio fornisce al servizio Batch le informazioni necessarie per configurare i nuovi nodi e prepararli per l'accettazione delle attività.

Come per qualsiasi attività di Azure Batch, è possibile specificare un elenco di **file di risorse** in [Archiviazione di Azure][azure_storage], oltre a una **riga di comando** da eseguire. Il servizio Batch copia prima di tutto i file di risorse nel nodo da Archiviazione di Azure, quindi esegue la riga di comando. Per un'attività di avvio del pool l'elenco di file include in genere l'applicazione dell'attività e le rispettive dipendenze.

L'attività di avvio può tuttavia includere anche dati di riferimento che devono essere usati da tutte le attività in esecuzione nel nodo di calcolo. La riga di comando di un'attività di avvio può ad esempio eseguire un'operazione `robocopy` per copiare i file dell'applicazione, che sono stati specificati come file di risorse e scaricati nel nodo, dalla [directory di lavoro](#files-and-directories) dell'attività di avvio alla [cartella condivisa](#files-and-directories) ed eseguire quindi un file MSI o `setup.exe`.

È in genere consigliabile che il servizio Batch attenda il completamento dell'attività di avvio prima di considerare il nodo pronto per l'assegnazione di attività, ma questo comportamento è configurabile.

Se un'attività di avvio non riesce in un nodo di calcolo, lo stato del nodo viene aggiornato per riflettere l'errore e al nodo non viene assegnata alcuna attività. Un'attività di avvio può non riuscire se si verifica un problema durante la copia dei file di risorse dall'archiviazione o se il processo eseguito dalla riga di comando restituisce un codice di uscita diverso da zero.

Se si aggiunge o si aggiorna l'attività di avvio per un pool esistente, è necessario riavviare i relativi nodi di calcolo per applicare l'attività di avvio ai nodi.

>[!NOTE]
> La dimensione totale di un'attività di avvio deve essere inferiore o uguale a 32768 caratteri, inclusi i file di risorse e le variabili di ambiente. Per assicurarsi che l'attività di avvio soddisfi questo requisito, è possibile usare uno dei due approcci seguenti:
>
> 1. È possibile usare i pacchetti dell'applicazione per distribuire le applicazioni o i dati in ogni nodo del pool di Batch. Per altre informazioni sui pacchetti dell'applicazione, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).
> 2. È possibile creare manualmente un archivio compresso contenente i file delle applicazioni. Caricare l'archivio compresso in Archiviazione di Azure come BLOB. Specificare l'archivio compresso come file di risorse per l'attività di avvio. Prima di eseguire la riga di comando per l'attività di avvio, decomprimere l'archivio dalla riga di comando. 
>
>    Per decomprimere l'archivio, è possibile usare lo strumento di archiviazione preferito. Sarà necessario includere lo strumento usato per decomprimere l'archivio come file di risorse per l'attività di avvio.
>
>

### <a name="job-manager-task"></a>Attività di gestione dei processi
Si usa in genere un' **attività del gestore di processi** per controllare e/o monitorare l'esecuzione dei processi, ad esempio per creare e inviare le attività per un processo, determinare le attività aggiuntive da eseguire e stabilire quando il lavoro è stato completato. Un'attività del gestore di processi, tuttavia, non è limitata a queste attività. Si tratta di un'attività completa, che consente di eseguire qualsiasi azione necessaria per il processo. Un'attività del gestore di processi può ad esempio scaricare un file specificato come parametro, analizzare il contenuto del file e inviare attività aggiuntive in base a quel contenuto.

Un'attività di gestione dei processi viene avviata prima di tutte le altre attività Fornisce le funzionalità seguenti:

* Viene inviata automaticamente come attività dal servizio Batch quando si crea il processo.
* Viene pianificata per l'esecuzione prima di altre attività di un processo.
* Il nodo associato è l'ultimo a essere rimosso da un pool quando il pool viene ridimensionato.
* La sua terminazione può essere associata alla terminazione di tutte le attività del processo.
* A questa attività viene assegnata la priorità più alta quando deve essere riavviata. Se un nodo inattivo non è disponibile, il servizio Batch può terminare una delle altre attività in esecuzione nel pool per consentire l'esecuzione dell'attività del gestore di processi.
* Un'attività del gestore di processi in un processo non ha la priorità sulle attività di altri processi. Tra i processi vengono rispettate solo le priorità a livello di processo.

### <a name="job-preparation-and-release-tasks"></a>Attività di preparazione e rilascio dei processi
Il servizio Batch offre attività di preparazione dei processi per la configurazione dell'esecuzione pre-processo. Le attività di rilascio dei processi vengono usate per manutenzione o pulizia post-processo.

* **Attività di preparazione del processo**: viene eseguita in tutti i nodi di calcolo pianificati per l'esecuzione di attività, prima dell'esecuzione di qualsiasi altra attività di un processo. Per copiare i dati condivisi da tutte le attività, ma univoci per il processo, ad esempio, è possibile usare l'attività di preparazione del processo.
* **Attività di rilascio del processo**: dopo aver completato il processo, viene eseguita un'attività di rilascio del processo in ogni nodo del pool che ha eseguito almeno un'attività. È possibile, ad esempio, usare l'attività di rilascio del processo per eliminare i dati copiati dall'attività di preparazione del processo o comprimere e caricare i dati del log di diagnostica.

Sia le attività di preparazione del processo sia quelle di rilascio del processo consentono di specificare una riga di comando da eseguire quando viene richiamata l'attività. Le attività offrono funzionalità quali download di file, esecuzione con privilegi elevati, variabili di ambiente personalizzate, durata massima di esecuzione, numero di tentativi e periodo di conservazione dei file.

Per altre informazioni sulle attività di preparazione e rilascio dei processi, vedere [Eseguire attività di preparazione e completamento dei processi nei nodi di calcolo di Azure Batch](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Attività a istanze multiple
Un' [attività a istanze multiple](batch-mpi.md) è un'attività configurata per l'esecuzione contemporanea in più nodi di calcolo. Con le attività a istanze multiple è possibile abilitare scenari di tipo che richiedono un gruppo di nodi di calcolo allocati insieme per elaborare un singolo carico di lavoro, ad esempio MPI (Message Passing Interface).

Per una discussione dettagliata sull'esecuzione di processi MPI in Batch usando la libreria Batch .NET, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](batch-mpi.md).

### <a name="task-dependencies"></a>Dipendenze dell'attività
Le [relazioni tra attività](batch-task-dependencies.md), come suggerito dal nome, consentono di specificare che un'attività dipende dal completamento di altre attività prima della rispettiva esecuzione. Questa funzionalità fornisce il supporto nelle situazioni in cui un'attività di tipo "downstream" utilizza l'output di un'attività di tipo "upstream" oppure quando un'attività upstream esegue un'inizializzazione richiesta da un'attività downstream. Per usare questa funzionalità, prima è necessario abilitare le relazioni tra attività nel processo batch. Per ogni attività che dipende da un'altra (o da più altre), specificare quindi le attività da cui dipende tale attività.

Con le relazioni tra attività, è possibile configurare scenari come i seguenti:

* L'*attivitàB* dipende dall'*attivitàA*, ovvero l'esecuzione dell'*attivitàB* inizierà solo dopo il completamento dell'*attivitàA*.
* L'*attivitàC* dipende sia dall'*attivitàA* che dall'*attivitàB*.
* L'*attivitàD* dipende da un intervallo di attività, ad esempio le attività da *1* a *10*, prima che venga eseguita.

Vedere [Relazioni tra attività in Azure Batch](batch-task-dependencies.md) e l'esempio di codice [TaskDependencies][github_sample_taskdeps] nel repository [azure-batch-samples][github_samples] in GitHub per altri dettagli su questa funzionalità.

## <a name="environment-settings-for-tasks"></a>Impostazioni di ambiente per le attività
Ogni attività eseguita dal servizio Batch ha accesso a variabili di ambiente che imposta sui nodi di calcolo. Sono incluse variabili di ambiente definite dal servizio Batch ([service-defined][msdn_env_vars]) e variabili di ambiente personalizzate che è possibile definire per le attività. Le applicazioni e gli script eseguiti dalle attività hanno accesso a queste variabili di ambiente durante l'esecuzione.

È possibile impostare variabili di ambiente personalizzate a livello di attività o di processo popolando le proprietà delle *impostazioni di ambiente* per queste entità. Vedere ad esempio l'operazione [Aggiungere un'attività a un processo][rest_add_task] (API Batch REST) o le proprietà [CloudTask.EnvironmentSettings][net_cloudtask_env] e [CloudJob.CommonEnvironmentSettings][net_job_env] in Batch .NET.

L'applicazione o il servizio client può ottenere le variabili di ambiente di un'attività, sia quelle definite dal servizio che quelle personalizzate, usando l'operazione [Ottenere informazioni su un'attività][rest_get_task_info] (Batch REST) o accedendo alla proprietà [CloudTask.EnvironmentSettings][net_cloudtask_env] (Batch .NET). I processi eseguiti in un nodo di calcolo possono accedere a queste e ad altre variabili di ambiente nel nodo, ad esempio usando la sintassi familiare `%VARIABLE_NAME%` (Windows) o `$VARIABLE_NAME` (Linux).

È possibile trovare un elenco completo di tutte le variabili di ambiente definite dal servizio in [Compute node environment variables][msdn_env_vars] (Variabili di ambiente dei nodi di calcolo).

## <a name="files-and-directories"></a>File e directory
Ogni attività ha una *directory di lavoro* in cui crea zero o più file e directory. Questa directory di lavoro può essere usata per archiviare il programma eseguito dall'attività, i dati elaborati e l'output dell'elaborazione eseguita dall'attività. Tutti i file e le directory di un'attività sono proprietà dell'utente dell'attività.

Il servizio Batch espone una parte del file system in un nodo come *directory radice*. Le attività possono accedere a questa directory facendo riferimento alla variabile di ambiente `AZ_BATCH_NODE_ROOT_DIR` . Per altre informazioni sull'uso delle variabili di ambiente, vedere [Impostazioni di ambiente per le attività](#environment-settings-for-tasks).

La directory radice contiene la struttura di directory seguente:

![Struttura di directory dei nodi di calcolo][1]

* **shared**: questa directory fornisce l'accesso in lettura/scrittura a *tutte* le attività eseguite in un nodo. Le attività eseguite nel nodo possono creare, leggere, aggiornare ed eliminare file in questa directory. Le attività possono accedere a questa directory facendo riferimento alla variabile di ambiente `AZ_BATCH_NODE_SHARED_DIR` .
* **startup**: questa directory viene usata da un'attività di avvio come directory di lavoro. Tutti i file scaricati nel nodo dall'attività di avvio vengono archiviati qui. L'attività di avvio può creare, leggere, aggiornare ed eliminare file in tale directory. Le attività possono accedere a questa directory facendo riferimento alla variabile di ambiente `AZ_BATCH_NODE_STARTUP_DIR` .
* **Attività**: viene creata una directory per ogni attività in esecuzione sul nodo, accessibile tramite un riferimento alla variabile di ambiente `AZ_BATCH_TASK_DIR`.

    All'interno di ogni directory di attività, il servizio Batch crea una directory di lavoro (`wd`), il cui percorso univoco viene specificato dalla variabile di ambiente `AZ_BATCH_TASK_WORKING_DIR`. Questa directory fornisce l'accesso in lettura/scrittura all'attività. L'attività può creare, leggere, aggiornare ed eliminare file in tale directory. Questa directory viene mantenuta in base al vincolo *RetentionTime* specificato per l'attività.

    `stdout.txt` e `stderr.txt`: questi file vengono scritti nella cartella dell'attività durante l'esecuzione di quest'ultima.

> [!IMPORTANT]
> Quando un nodo viene rimosso dal pool, vengono rimossi *tutti* i file archiviati nel nodo.
>
>

## <a name="application-packages"></a>Pacchetti dell'applicazione
La funzionalità relativa ai [pacchetti dell'applicazione](batch-application-packages.md) consente di gestire e distribuire con facilità le applicazioni ai nodi di calcolo nei pool. È possibile caricare e gestire più versioni delle applicazioni eseguite dalle attività, inclusi i relativi file binari e i file di supporto, quindi distribuire automaticamente una o più applicazioni nei nodi di calcolo del pool.

È possibile specificare i pacchetti dell'applicazione a livello di pool e di attività. Quando si specificano pacchetti dell'applicazione del pool, l'applicazione viene distribuita in ogni nodo del pool. Quando si specificano pacchetti dell'applicazione di attività, l'applicazione viene distribuita solo ai nodi pianificati per eseguire almeno una delle attività del processo, appena prima dell'esecuzione della riga di comando dell'attività.

Batch gestisce i dettagli dell'utilizzo di Archiviazione di Azure per archiviare i pacchetti dell'applicazione e distribuirli nei nodi di calcolo, in modo da semplificare il codice e ridurre il sovraccarico in termini di gestione.

Per altre informazioni sulla funzionalità relativa ai pacchetti dell'applicazione, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).

> [!NOTE]
> Se si aggiungono pacchetti dell'applicazione a un pool *esistente* , è necessario riavviare i relativi nodi di calcolo per distribuire i pacchetti dell'applicazione nei nodi.
>
>

## <a name="pool-and-compute-node-lifetime"></a>Durata del pool e dei nodi di calcolo
Quando si progetta una soluzione Azure Batch, è necessario prendere una decisione a livello di progettazione per stabilire come e quando vengono creati i pool e per quanto tempo vengono mantenuti disponibili i nodi di calcolo all'interno dei pool.

Da un lato, è possibile creare un pool per ogni processo inviato ed eliminare il pool al termine dell'esecuzione dell'attività. In questo modo è possibile massimizzare l'utilizzo, perché i nodi vengono allocati solo quando è necessario e vengono arrestati non appena sono inattivi. Mentre ciò significa che il processo deve attendere l'allocazione dei nodi, è importante notare che le attività vengono pianificate per l'esecuzione non appena i nodi risultano disponibili e allocati individualmente e dopo il completamento dell'attività di avvio. Il servizio Batch *not* attende che tutti i nodi di un pool siano disponibili prima di assegnare le attività ai nodi, assicurando quindi il massimo utilizzo di tutti i nodi disponibili.

Dall'altro lato, se l'avvio immediato dei processi ha la priorità più alta, è possibile può creare subito un pool e rendere disponibili i relativi nodi prima dell'invio dei processi. In questo scenario le attività possono essere avviate immediatamente, ma i nodi possono rimanere inattivi in attesa che vengano assegnate.

Un approccio combinato viene in genere usato per la gestione di un carico variabile ma continuo. È possibile inviare più processi a un pool, ma aumentare o ridurre il numero di nodi in base al carico del processo. Vedere [Ridimensionamento delle risorse di calcolo](#scaling-compute-resources) nella prossima sezione. Questa operazione può essere eseguita in modo reattivo in base al carico corrente o in modo proattivo se è possibile prevedere il carico.

## <a name="pool-network-configuration"></a>Pool della configurazione di rete

Quando si crea un pool di nodi di calcolo in Azure Batch, è possibile specificare l'ID subnet di una [rete virtuale (VNet)](../virtual-network/virtual-networks-overview.md) di Azure nella quale devono essere creati i nodi di calcolo del pool.

* La rete virtuale deve:

   * Essere nella stessa **area** di Azure dell'account di Azure Batch.
   * Essere nella stessa **sottoscrizione** dell'account di Azure Batch.

* Il tipo di rete virtuale supportato dipende dal modo in cui vengono allocati i pool per l'account Batch:
    - Se l'account Batch è stato creato con la proprietà **poolAllocationMode** impostata su 'BatchService', la rete virtuale specificata deve essere una rete virtuale classica.
    - Se l'account Batch è stato creato con la proprietà **poolAllocationMode** impostata su 'UserSubscription', la rete virtuale specificata deve essere una rete virtuale classica o una rete virtuale di Azure Resource Manager. I pool devono essere creati con una configurazione di macchina virtuale per usare una rete virtuale. I pool creati con una configurazione di servizio cloud non sono supportati.

* Se l'account Batch è stato creato con la proprietà **poolAllocationMode** impostata su 'BatchService', è necessario specificare le autorizzazioni per consentire all'entità servizio di Batch di accedere alla rete virtuale. L'entità servizio di Batch, denominata 'Microsoft Azure Batch' o 'MicrosoftAzureBatch', deve avere il ruolo [Collaboratore Macchina virtuale classica di Controllo degli accessi in base al ruolo](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/#classic-virtual-machine-contributor) per la rete virtuale specificata. Se il ruolo Controllo degli accessi in base al ruolo specificato non viene indicato, il servizio Batch restituisce 400 (Richiesta non valida).

* La subnet specificata deve avere un numero sufficiente di **indirizzi IP** disponibili per contenere il numero totale di nodi di destinazione, ovvero la somma delle proprietà `targetDedicatedNodes` e `targetLowPriorityNodes` del pool. Se la subnet non ha sufficienti indirizzi IP liberi, il servizio Batch alloca parzialmente i nodi di calcolo nel pool e restituisce un errore di ridimensionamento.

* La subnet specificata deve consentire la comunicazione dal servizio Batch per poter pianificare le operazioni sui nodi di calcolo. Se la comunicazione ai nodi di calcolo viene negata da un **gruppo di sicurezza di rete** associato alle rete virtuale, il servizio Batch imposta lo stato dei nodi di calcolo su **Non utilizzabile**.

* Se la rete virtuale specificata ha gruppi di sicurezza di rete associati, è necessario abilitare alcune porte di sistema riservate per le comunicazioni in ingresso. Per i pool creati con una configurazione di macchina virtuale, abilitare le porte 29876 e 29877, nonché la porta 22 per Linux e la porta 3389 per Windows. Per i pool creati con una configurazione di servizio cloud, abilitare le porte 10100, 20100 e 30100. Abilitare anche le connessioni in uscita verso Archiviazione di Azure sulla porta 443.

    La tabella seguente descrive le porte in ingresso che è necessario abilitare per i pool creati con la configurazione macchina virtuale:

    |    Porte di destinazione    |    Indirizzo IP di origine      |    Batch aggiunge gruppi di sicurezza di rete    |    Necessarie per l'utilizzabilità della VM    |    Azione dell'utente   |
    |---------------------------|---------------------------|----------------------------|-------------------------------------|-----------------------|
    |    <ul><li>Per i pool creati con la configurazione macchina virtuale: 29876, 29877</li><li>Per i pool creati con la configurazione del servizio cloud: 10100, 20100 e 30100</li></ul>         |    Solo indirizzi IP del ruolo del servizio Batch |    Sì. Batch aggiunge gruppi di sicurezza di rete al livello di interfacce di rete collegate alle VM. Questi gruppi di sicurezza di rete consentono il traffico solo dagli indirizzi IP del ruolo del servizio Batch. Anche se si aprono queste porte per l'intero Web, il traffico verrà bloccato nella scheda di interfaccia di rete. |    Sì  |  Non è necessario specificare un gruppo di sicurezza di rete, perché Batch consente solo indirizzi IP Batch. <br /><br /> Se si specifica ugualmente un gruppo di sicurezza di rete, assicurarsi che queste porte siano aperte per il traffico in ingresso. <br /><br /> Se si specifica * come IP di origine nel gruppo di sicurezza di rete, Batch aggiunge ancora gruppi di sicurezza di rete al livello di scheda di interfaccia di rete collegata alle VM. |
    |    3389, 22               |    Computer utente, usati per il debug, per poter accedere in remoto alla VM.    |    No                                    |    No                     |    Aggiungere gruppi di sicurezza di rete per consentire l'accesso remoto (RDP/SSH) alla VM.   |                 

    La tabella seguente indica la porta in uscita che è necessario abilitare per consentire l'accesso ad Archiviazione di Azure:

    |    Porte in uscita    |    Destination    |    Batch aggiunge gruppi di sicurezza di rete    |    Necessarie per l'utilizzabilità della VM    |    Azione dell'utente    |
    |------------------------|-------------------|----------------------------|-------------------------------------|------------------------|
    |    443    |    Archiviazione di Azure    |    No    |    Sì    |    Se si aggiungono gruppi di sicurezza di rete, assicurarsi che questa porta sia aperta per il traffico in uscita.    |


Le impostazioni aggiuntive della rete virtuale dipendono dalla modalità di allocazione pool dell'account Batch.

### <a name="vnets-for-pools-provisioned-in-the-batch-service"></a>Reti virtuali per i pool di cui è stato eseguito il provisioning nel servizio Batch

Nella modalità di allocazione servizio Batch è possibile assegnare una rete virtuale solo ai pool di **configurazione dei servizi cloud**. La rete virtuale specificata deve essere una rete virtuale **classica**. Le reti virtuali create con il modello di distribuzione di Azure Resource Manager non sono supportate.



* L'entità servizio *MicrosoftAzureBatch* deve avere il ruolo di controllo di accesso basato sui ruoli (RBAC) ovvero [Collaboratore Macchina virtuale classica](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor) per la rete virtuale specificata. Nel portale di Azure:

  * Selezionare la **rete virtuale**, scegliere **Controllo di accesso (IAM)** > **Ruoli** > **Collaboratore Macchina virtuale classica** > **Aggiungi**
  * Digitare "MicrosoftAzureBatch" nella casella **Ricerca**
  * Selezionare la casella di controllo **MicrosoftAzureBatch**
  * Fare clic sul pulsante **Seleziona**



### <a name="vnets-for-pools-provisioned-in-a-user-subscription"></a>Reti virtuali per i pool di cui è stato eseguito il provisioning in una sottoscrizione utente

Nella modalità di allocazione sottoscrizione utente è possibile assegnare una rete virtuale solo ai pool di **configurazione macchina virtuale**, che sono gli unici supportati. La rete virtuale specificata deve essere una rete virtuale basata su **Resource Manager**. Le reti virtuali create con il modello di distribuzione classica non sono supportate.



## <a name="scaling-compute-resources"></a>Ridimensionamento delle risorse di calcolo
Con il [ridimensionamento automatico](batch-automatic-scaling.md)è possibile fare in modo che il servizio Batch modifichi dinamicamente il numero di nodi di calcolo in un pool in base al carico di lavoro corrente e all'utilizzo delle risorse dello scenario di calcolo. In questo modo è possibile ridurre il costo complessivo dell'esecuzione dell'applicazione usando solo le risorse necessarie e rilasciando quelle non necessarie.

Per abilitare il ridimensionamento automatico, scrivere una [formula di ridimensionamento automatico](batch-automatic-scaling.md#automatic-scaling-formulas) e associarla a un pool. Il servizio Batch usa la formula per determinare il numero di nodi di destinazione nel pool per l'intervallo di ridimensionamento successivo (un intervallo che è possibile configurare). È possibile specificare le impostazioni di ridimensionamento automatico per un pool quando lo si crea oppure abilitare il ridimensionamento in un pool in seguito. È anche possibile aggiornare le impostazioni di ridimensionamento in un pool abilitato per il ridimensionamento.

Ad esempio, un processo richiede l'invio di un numero molto elevato di attività da eseguire. È possibile assegnare al pool una formula di ridimensionamento che regola il numero di nodi nel pool in base al numero corrente di attività accodate e alla percentuale di completamento delle attività nel processo. Il servizio Batch valuta periodicamente la formula e ridimensiona il pool in base al carico di lavoro e alle altre impostazioni della formula. Il servizio aggiunge nodi in base alle necessità quando c'è un numero elevato di attività in coda e rimuove i nodi quando non sono presenti attività in coda o in esecuzione.

Una formula può essere basata sulle metriche seguenti:

* **Metriche temporali** : basate sulle statistiche raccolte ogni cinque minuti nel numero di ore specificato.
* **Metriche delle risorse** : basate su utilizzo di CPU, larghezza di banda, memoria e numero di nodi.
* **Metriche delle attività**: basate sullo stato delle attività, ad esempio *Attiva* (in coda), *In esecuzione* o *Completata*.

Quando il ridimensionamento automatico riduce il numero di nodi di calcolo in un pool, è necessario considerare come gestire le attività in esecuzione al momento dell'operazione di riduzione. A questo scopo, il servizio Batch offre un' *opzione di deallocazione dei nodi* che è possibile includere nelle formule. Ad esempio, è possibile specificare che le attività in esecuzione devono essere arrestate immediatamente, arrestate immediatamente e quindi riaccodate per l'esecuzione in un altro nodo o che ne deve essere consentita la fine prima della rimozione del nodo dal pool.

Per altre informazioni sulla scalabilità automatica di un'applicazione, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](batch-automatic-scaling.md).

> [!TIP]
> Per massimizzare l'utilizzo delle risorse di calcolo, impostare il numero di nodi su zero alla fine di un processo, ma consentire il completamento delle attività in esecuzione.
>
>

## <a name="security-with-certificates"></a>Sicurezza con certificati
È in genere necessario usare certificati per crittografare o decrittografare informazioni riservate per le attività, ad esempio la chiave per un [account di archiviazione di Azure][azure_storage]. Per supportare questa funzionalità, è possibile installare certificati nei nodi. I segreti crittografati vengono passati alle attività nei parametri della riga di comando o incorporati in una delle risorse dell'attività e i certificati installati possono essere usati per decrittografarli.

Per aggiungere un certificato a un account Batch, usare l'operazione [Aggiungi certificato][rest_add_cert] (Batch REST) o il metodo [CertificateOperations.CreateCertificate][net_create_cert] (Batch .NET). È quindi possibile associare il certificato a un pool nuovo o esistente. Quando un certificato è associato a un pool, il servizio Batch installa il certificato in ogni nodo del pool. Il servizio Batch installa i certificati appropriati all'avvio del nodo, prima di avviare le attività, incluse quelle di avvio e del gestore di processi.

Se si aggiungono certificati a un pool *esistente* , è necessario riavviare i relativi nodi di calcolo per applicare i certificati ai nodi.

## <a name="error-handling"></a>Gestione degli errori
Potrebbe essere necessario gestire sia gli errori delle attività che quelli delle applicazioni nella soluzione Batch.

### <a name="task-failure-handling"></a>Gestione degli errori delle attività
Gli errori delle attività rientrano nelle categorie seguenti:

* **Errori di pre-elaborazione**

    Se un'attività non può essere avviata, viene impostato un errore di pre-elaborazione per l'attività stessa.  

    Gli errori di pre-elaborazione possono essere dovuti a file di risorse dell'attività spostati, un account di archiviazione non più disponibile o un altro problema che ha impedito la copia corretta dei file nel nodo.

* **Errori di caricamento file**

    Se il caricamento dei file specificati per un'attività non riesce per qualsiasi motivo, per l'attività viene impostato un errore di caricamento file.

    Gli errori di caricamento dei file possono verificarsi se la firma di accesso condiviso specificata per l'accesso ad Archiviazione di Azure non è valida oppure non concede le autorizzazioni di scrittura, se l'account di archiviazione non è più disponibile oppure se si è verificato un altro problema che ha impedito la copia corretta dei file dal nodo.    

* **Errori delle applicazioni**

    Anche il processo specificato dalla riga di comando dell'attività può non riuscire. Il processo viene considerato non riuscito quando il processo eseguito dall'attività restituisce un codice di uscita diverso da zero. Vedere *Codici di uscita delle attività* nella prossima sezione.

    Per gli errori delle applicazioni è possibile configurare il servizio Batch in modo che riprovi automaticamente a eseguire l'attività per un numero di volte specificato.

* **Errori relativi ai vincoli**

    È possibile impostare un vincolo che specifichi la durata massima di esecuzione per un processo o un'attività, *maxWallClockTime*. Questa impostazione può essere utile per terminare le attività bloccate.

    Quando viene superata la quantità massima di tempo impostata, l'attività viene contrassegnata come *completata*, ma il codice di uscita viene impostato su `0xC000013A` e il campo *schedulingError* viene contrassegnato come `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Debug degli errori delle applicazioni
* `stderr` e `stdout`

    Durante l'esecuzione un'applicazione può generare un output di diagnostica che può essere usato per la risoluzione dei problemi. Come indicato nella sezione precedente [File e directory](#files-and-directories), il servizio Batch scrive l'output standard e l'output degli errori standard nei file `stdout.txt` e `stderr.txt` nella directory dell'attività nel nodo di calcolo. Per scaricare questi file, è possibile usare il portale di Azure oppure uno degli SDK di Batch. È ad esempio possibile recuperare questi e altri file per la risoluzione dei problemi con [ComputeNode.GetNodeFile][net_getfile_node] e [CloudTask.GetNodeFile][net_getfile_task] nella libreria Batch .NET.

* **Codici di uscita delle attività**

    Come indicato in precedenza, un'attività viene contrassegnata come non riuscita dal servizio Batch se il processo eseguito dall'attività restituisce un codice di uscita non zero. Quando un'attività esegue un processo, il servizio Batch popola la proprietà del codice di uscita dell'attività con il *codice restituito del processo*. È importante notare che il codice di uscita dell'attività **non** è determinato dal servizio Batch. Il codice di uscita di un'attività è determinato dal processo stesso o dal sistema operativo in cui il processo è stato eseguito.

### <a name="accounting-for-task-failures-or-interruptions"></a>Considerazioni sugli errori o sulle interruzioni delle attività
In alcuni casi, le attività non riescono o vengono interrotte. È possibile che si verifichi un errore dell'applicazione dell'attività stessa, che il nodo in cui è in esecuzione l'attività venga riavviato o che il nodo venga rimosso dal pool durante un'operazione di ridimensionamento se nei criteri di deallocazione del pool è impostata la rimozione immediata dei nodi senza attendere il completamento delle attività. In ogni caso, Batch può riaccodare automaticamente l'attività per eseguirla in un altro nodo.

È anche possibile che un problema intermittente provochi il blocco di un'attività o ne renda troppo lunga l'esecuzione. È possibile impostare l'intervallo di esecuzione massimo per un'attività. Se si supera l'intervallo di esecuzione massimo, il servizio Batch consente di interrompere l'applicazione dell'attività.

### <a name="connecting-to-compute-nodes"></a>Connessione ai nodi di calcolo
È possibile eseguire altre operazioni di debug e di risoluzione dei problemi accedendo a un nodo di calcolo in remoto. È possibile usare il portale di Azure per scaricare un file Remote Desktop Protocol (RDP) per i nodi Windows e ottenere informazioni sulla connessione SSH (Secure Shell) per i nodi Linux. È anche possibile eseguire questa operazione usando le API Batch, ad esempio con [Batch .NET][net_rdpfile] o [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Per connettersi a un nodo tramite RDP o SSH, è necessario creare prima di tutto un utente nel nodo. A questo scopo è possibile usare il Portale di Azure, [aggiungere un account utente a un nodo][rest_create_user] con l'API Batch REST, chiamare il metodo [ComputeNode.CreateComputeNodeUser][net_create_user] in Batch .NET o chiamare il metodo [add_user][py_add_user] nel modulo Batch Python.
>
>

### <a name="troubleshooting-problematic-compute-nodes"></a>Risoluzione dei problemi dei nodi di calcolo
Nei casi in cui alcune attività non riescono, il servizio o l'applicazione client Batch può esaminare i metadati delle attività non riuscite per identificare un nodo non correttamente funzionante. A ogni nodo di un pool viene assegnato un ID univoco e il nodo in cui viene eseguita un'attività viene incluso nei metadati dell'attività. Dopo avere identificato un "nodo problematico", è possibile intervenire in diversi modi:

* **Riavviare un nodo** ([REST][rest_reboot] | [.NET][net_reboot])

    Il riavvio del nodo a volte consente di eliminare problemi latenti, ad esempio processi bloccati o arrestati in modo anomalo. Si noti che, se il pool usa un'attività di avvio o il processo usa un'attività di preparazione del processo, vengono eseguite quando il nodo viene riavviato.
* **Ricreare l'immagine di un nodo** ([REST][rest_reimage] | [.NET][net_reimage])

    Il sistema operativo viene reinstallato nel nodo. Come durante il riavvio di un nodo, le attività di avvio e le attività di preparazione del processo vengono rieseguite dopo che è stata ricreata l'immagine del nodo.
* **Rimuovere i nodi di calcolo da un pool** ([REST][rest_remove] | [.NET][net_remove])

    A volte è necessario rimuovere completamente il nodo dal pool.
* **Disabilitare la pianificazione delle attività in un nodo** ([REST][rest_offline] | [.NET][net_offline])

    In questo modo il nodo in realtà passa offline e non è possibile assegnargli altre attività, ma può rimanere in esecuzione e nel pool. È quindi possibile eseguire altre indagini sulla causa degli errori senza perdere i dati delle attività non riuscite e senza che il nodo generi altri errori delle attività. È ad esempio possibile disabilitare la pianificazione delle attività nel nodo, quindi [accedere in remoto](#connecting-to-compute-nodes) per esaminare i registri eventi del nodo o eseguire altre operazioni di risoluzione dei problemi. Al termine dell'indagine, è possibile riportare il nodo online abilitando la pianificazione delle attività ([REST][rest_online] | [.NET][net_online]) o eseguire una delle altre azioni illustrate in precedenza.

> [!IMPORTANT]
> Con ogni azione illustrata in questa sezione, ovvero riavvio, ricreazione dell'immagine, rimozione, disabilitazione della pianificazione delle attività, è possibile specificare come gestire le attività attualmente in esecuzione nel nodo quando si esegue l'azione. Quando ad esempio si disabilita la pianificazione delle attività in un nodo con la libreria client Batch .NET, è possibile specificare un valore enum [DisableComputeNodeSchedulingOption][net_offline_option] per specificare se eseguire l'operazione **Termina** per terminare le attività in esecuzione o **Reinserisci nella coda** per la pianificazione in altri nodi oppure consentire il completamento delle attività in esecuzione prima di eseguire l'azione (**TaskCompletion**).
>
>

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
* Esaminare in dettaglio un'applicazione Batch di esempio in [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md). È disponibile anche una [versione per Python](batch-python-tutorial.md) dell'esercitazione che esegue un carico di lavoro nei nodi di calcolo Linux.
* Scaricare e compilare il progetto di esempio [Batch Explorer][github_batchexplorer] da usare durante lo sviluppo di soluzioni Batch. Con Batch Explorer è possibile eseguire le operazioni seguenti e altre ancora:

  * Monitorare e gestire pool, processi e attività nell'account Batch
  * Scaricare `stdout.txt`, `stderr.txt` e altri file dai nodi
  * Creare utenti nei nodi e scaricare i file RDP per l'accesso remoto
* Leggere le informazioni su come [creare pool di nodi di calcolo Linux](batch-linux-nodes.md).
* Visitare il [forum di Azure Batch][batch_forum] su MSDN. dove sia chi sta imparando a usare Batch sia gli esperti possono porre domande.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

