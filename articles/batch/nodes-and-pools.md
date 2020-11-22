---
title: Nodi e pool in Azure Batch
description: Informazioni sui nodi di calcolo, sui pool e sul modo in cui vengono usati in un flusso di lavoro di Azure Batch dal punto di vista dello sviluppo.
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: 880a956a2d839483c59578afad1b62146799578a
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95243070"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Nodi e pool in Azure Batch

In un flusso di lavoro Azure Batch un *nodo di calcolo* (o *nodo*) è una macchina virtuale per l'elaborazione di una parte del carico di lavoro dell'applicazione. Un *pool* è una raccolta di questi nodi in cui viene eseguita l'applicazione. Questo articolo illustra in modo più approfondito nodi e pool, insieme alle considerazioni relative alla creazione e all'uso in un flusso di lavoro in Azure Batch.

## <a name="nodes"></a>Nodi

Un nodo è una macchina virtuale (VM) di Azure o una VM del servizio cloud dedicata all'elaborazione di una parte del carico di lavoro dell'applicazione. Le dimensioni di un nodo determinano il numero di core CPU, la capacità di memoria e le dimensioni del file system locale allocati al nodo.

È possibile creare pool di nodi Windows o Linux usando i servizi cloud di Azure, immagini di [Marketplace per Macchine virtuali di Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) oppure immagini personalizzate preparate dall'utente.

I nodi possono eseguire qualsiasi eseguibile o script supportato dall'ambiente del sistema operativo del nodo, I file eseguibili o gli script includono \*.exe, \*.cmd, \*.bat, script di PowerShell (per Windows) e file binari, shell e script di Python (per Linux).

Tutti i nodi di Calcolo in Batch includono anche:

- Una [struttura di cartelle](files-and-directories.md) standard e le [variabili di ambiente](jobs-and-tasks.md) associate disponibili come riferimento per le attività.
- Impostazioni **firewall** configurate per controllare l'accesso.
- [Accesso remoto](error-handling.md#connect-to-compute-nodes) ai nodi Windows (Remote Desktop Protocol (RDP) e Linux (Secure Shell (SSH)), a meno che non si [crei il pool con accesso remoto disabilitato](pool-endpoint-configuration.md).

Per impostazione predefinita, i nodi possono comunicare tra loro, ma non possono comunicare con macchine virtuali che non fanno parte dello stesso pool. Per consentire ai nodi di comunicare in modo sicuro con altre macchine virtuali o con una rete locale, è possibile effettuare il provisioning del pool [in una subnet di una rete virtuale di Azure (VNet)](batch-virtual-network.md). Quando si esegue questa operazione, è possibile accedere ai nodi tramite indirizzi IP pubblici. Questi indirizzi IP pubblici vengono creati da batch e possono cambiare nel corso della durata del pool. È anche possibile [creare un pool con indirizzi IP pubblici statici](create-pool-public-ip.md) da controllare, in modo da garantire che non cambino in modo imprevisto.

## <a name="pools"></a>Pool

Un pool è la raccolta di nodi in cui viene eseguita l'applicazione.

I pool di Azure Batch sono basati sulla piattaforma di calcolo Azure. Offrono allocazione su larga scala, installazione di applicazioni, distribuzione dei dati, monitoraggio dell'integrità e regolazione flessibile ([ridimensionamento](#automatic-scaling-policy)) del numero di nodi di calcolo in un pool.

A ogni nodo aggiunto a un pool viene assegnato un nome univoco e un indirizzo IP. Quando un nodo viene rimosso da un pool, vengono perse tutte le modifiche apportate al sistema operativo o ai file e il relativo nome e indirizzo IP vengono rilasciati per uso futuro. Quando un nodo esce da un pool, la sua durata è terminata.

Un pool può essere usato solo dall'account Batch in cui è stato creato. Un account batch può creare più pool per soddisfare i requisiti delle risorse delle applicazioni in cui verrà eseguito.

Il pool può essere creato manualmente o [automaticamente dal servizio batch](#autopools) quando si specifica il lavoro da eseguire. Quando si crea un pool, è possibile specificare gli attributi seguenti:

- [Il sistema operativo dei nodi e la sua versione](#operating-system-and-version)
- [Tipo di nodo e numero di nodi di destinazione](#node-type-and-target)
- [Dimensioni nodo](#node-size)
- [Criteri di ridimensionamento automatico](#automatic-scaling-policy)
- [Criteri di pianificazione di attività](#task-scheduling-policy)
- [Stato della comunicazione](#communication-status)
- [Attività di avvio](#start-tasks)
- [Pacchetti dell'applicazione](#application-packages)
- [Configurazione della rete virtuale e del firewall](#virtual-network-vnet-and-firewall-configuration)
- [Durata](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Gli account Batch hanno una quota predefinita che limita il numero di core in ogni account. Il numero di core corrisponde al numero di nodi di calcolo. Le quote predefinite e le istruzioni su come [aumentare una quota](batch-quota-limit.md#increase-a-quota) sono disponibili in [Quote e limiti del servizio Batch](batch-quota-limit.md). Se il pool non raggiungere il numero di nodi di destinazione, la causa potrebbe essere la quota di core.

## <a name="operating-system-and-version"></a>Sistema operativo e la sua versione

Quando si crea un pool di Batch, specificare la configurazione delle macchine virtuali di Azure e il tipo di sistema operativo da eseguire in ogni nodo di calcolo nel pool.

## <a name="configurations"></a>Configurazioni

Esistono due tipi di configurazioni pool disponibili in Batch.

### <a name="virtual-machine-configuration"></a>Configurazione macchina virtuale

La **Configurazione macchina virtuale**, specifica che il pool è composto da macchine virtuali di Azure. È possibile creare queste VM da immagini Linux o Windows.

L' [agente del nodo batch](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) è un programma che viene eseguito in ogni nodo del pool e fornisce l'interfaccia di comando e controllo tra il nodo e il servizio batch. Sono disponibili diverse implementazioni dell'agente del nodo, definite SKU, per sistemi operativi diversi. Quando si crea un pool basato su Configurazione macchina virtuale, è necessario specificare non solo le dimensioni dei nodi e l'origine delle immagini usate per crearli, ma anche il **riferimento a un'immagine della macchina virtuale** e lo **SKU dell'agente del nodo** Batch da installare nei nodi. Per altre informazioni sulla specifica di queste proprietà del pool, vedere [Effettuare il provisioning di nodi di calcolo Linux nei pool di Azure Batch](batch-linux-nodes.md). È anche possibile collegare uno o più dischi di dati vuoti a VM in pool create da immagini del Marketplace oppure includere i dischi di dati nelle immagini personalizzate usate per creare le macchine virtuali. Quando si includono dischi dati è necessario montare e formattare i dischi all'interno di una macchina virtuale per poterli usare.

### <a name="cloud-services-configuration"></a>Configurazione di Servizi cloud

La **configurazione di Servizi cloud**, specifica che il pool è composto da nodi di Servizi cloud di Azure. Servizi cloud fornisce solo nodi di calcolo Windows.

I sistemi operativi disponibili per i pool di configurazione dei servizi cloud sono elencati nelle [versioni del sistema operativo guest di Azure e nella matrice di compatibilità SDK](../cloud-services/cloud-services-guestos-update-matrix.md)e le dimensioni dei nodi di calcolo disponibili sono elencate in [dimensioni per i servizi cloud](../cloud-services/cloud-services-sizes-specs.md). Quando si crea un pool che contiene nodi di servizi cloud, si specificano le dimensioni del nodo e la rispettiva *famiglia del sistema operativo* (che determina le versioni di .NET installate con il sistema operativo). La distribuzione di Servizi cloud in Azure è più veloce rispetto alle macchine virtuali che eseguono Windows. Se si vuole disporre di pool di nodi di calcolo di Windows, le prestazioni di Servizi cloud in termini di tempo di distribuzione potrebbero essere migliori.

Analogamente ai ruoli di lavoro nei servizi cloud, è possibile specificare una *Versione sistema operativo*. Per altre informazioni sui ruoli di lavoro, vedere la [Panoramica dei Servizi cloud](../cloud-services/cloud-services-choose-me.md). È consigliabile specificare `Latest (*)` per la *Versione sistema operativo*, in modo che i nodi vengano aggiornati automaticamente senza doversi occupare delle nuove versioni rilasciate. Il caso d'uso principale per la selezione di una versione specifica del sistema operativo consiste nell'assicurare la compatibilità delle applicazioni, che permette l'esecuzione del test di compatibilità con le versioni precedenti prima di consentire l'aggiornamento della versione. Dopo la convalida, la *Versione sistema operativo* per il pool può essere aggiornata ed è possibile installare la nuova immagine del sistema operativo. Eventuali attività in esecuzione vengono interrotte e accodate di nuovo.

### <a name="node-agent-skus"></a>SKU dell'agente nodo

Quando si crea un pool, è necessario selezionare il valore appropriato di **nodeAgentSkuId**, a seconda del sistema operativo dell'immagine di base del disco rigido virtuale. È possibile ottenere un mapping tra gli ID SKU dell'agente del nodo e i relativi riferimenti all'immagine del sistema operativo, chiamando l'operazione di [elenco degli SKU degli agenti nodo supportati](/rest/api/batchservice/list-supported-node-agent-skus).

### <a name="custom-images-for-virtual-machine-pools"></a>Immagini personalizzate per pool di macchine virtuali

Per informazioni su come creare un pool con immagini personalizzate, vedere [Usare la Raccolta immagini condivise per creare un pool personalizzato](batch-sig-images.md).

In alternativa è possibile creare un pool personalizzato di macchine virtuali usando una risorsa [immagine gestita](batch-custom-images.md). Per informazioni sulla preparazione di immagini Linux personalizzate da VM di Azure, vedere [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../virtual-machines/linux/capture-image.md). Per informazioni sulla preparazione di immagini Windows personalizzate da macchine virtuali di Azure, vedere [Creare un'immagine gestita di una macchina virtuale generalizzata in Azure](../virtual-machines/windows/capture-image-resource.md).

### <a name="container-support-in-virtual-machine-pools"></a>Supporto dei contenitori nei pool di macchine virtuali

Quando si crea un pool Configurazione macchina virtuale usando le API di Batch, è possibile impostare il pool per eseguire attività in contenitori Docker. Attualmente è necessario creare il pool usando un'immagine che supporti contenitori Docker. Usare l'immagine Windows Server 2016 Datacenter con contenitori di Azure Marketplace oppure specificare un'immagine di VM personalizzata che includa Docker Community Edition o Enterprise Edition e tutti i driver necessari. Le impostazioni del pool devono includere una [configurazione del contenitore](/rest/api/batchservice/pool/add) che copia le immagini del contenitore nelle macchine virtuali quando viene creato il pool. Le attività eseguite nel pool potranno quindi fare riferimento a immagini del contenitore e opzioni di esecuzione del contenitore.

Per altre informazioni, vedere [Eseguire applicazioni contenitore Docker in Azure Batch](batch-docker-container-workloads.md).

## <a name="node-type-and-target"></a>Tipo e destinazione del nodo

Quando si crea un pool, è possibile specificare i tipi di nodo desiderati e il numero di destinazione di ognuno. I due tipi di nodo sono i seguenti:

- **Nodi dedicati.** I nodi di calcolo dedicati sono riservati per i carichi di lavoro. Sono più costosi dei nodi con priorità bassa, ma non vengono mai superati.
- **Nodi a priorità bassa.** I nodi con priorità bassa sfruttano la capacità in eccesso di Azure per l'esecuzione dei carichi di lavoro di Batch. I nodi con priorità bassa hanno un costo orario inferiore a quello dei nodi dedicati e supportano carichi di lavoro che richiedono una potenza di calcolo significativa. Per altre informazioni, vedere [Usare le VM con priorità bassa in Batch](batch-low-pri-vms.md).

Può verificarsi il superamento dei nodi con priorità bassa quando Azure ha capacità in eccesso insufficiente. In caso di superamento di un nodo durante l'esecuzione di attività, le attività vengono accodate ed eseguite di nuovo quando il nodo di calcolo torna disponibile. I nodi con priorità bassa sono utili per i carichi di lavoro con tempi di completamento del processo flessibili e in cui il lavoro è distribuito tra più nodi. Prima di scegliere di usare nodi con priorità bassa per lo scenario, assicurarsi che l'eventuale quantità di lavoro persa a causa della precedenza sia minima e facile da ricreare.

Lo stesso pool può includere nodi di calcolo con priorità bassa e nodi di calcolo dedicati. Ogni tipo di nodo ha un'impostazione di destinazione propria, per cui è possibile specificare il numero desiderato di nodi.

Il numero di nodi di calcolo viene definito *numero di destinazione*, perché in alcuni casi il pool può non raggiungere il numero desiderato di nodi. Ad esempio, un pool potrebbe non realizzare la destinazione se prima raggiunge la [quota core](batch-quota-limit.md) per l'account Batch. Il pool potrebbe non realizzare la destinazione, inoltre, se al pool è stata applicata una formula di ridimensionamento automatico che limita il numero massimo di nodi.

Per informazioni sui prezzi per i nodi dedicati e con priorità bassa, vedere [Prezzi di Batch](https://azure.microsoft.com/pricing/details/batch/).

## <a name="node-size"></a>Dimensioni nodo

Quando si crea un pool di Azure Batch, è possibile scegliere tra quasi tutte le famiglie e le dimensioni di VM disponibili in Azure. Azure offre una serie di dimensioni delle macchine virtuali per diversi carichi di lavoro, incluse le dimensioni specializzate [HPC](../virtual-machines/sizes-hpc.md) o [con supporto per GPU](../virtual-machines/sizes-gpu.md). 

Per altre informazioni, vedere [Scegliere le dimensioni delle macchine virtuali per i nodi di calcolo in un pool di Azure Batch](batch-pool-vm-sizes.md).

## <a name="automatic-scaling-policy"></a>Criteri di ridimensionamento automatico

Per carichi di lavoro dinamici, è possibile applicare un criterio di ridimensionamento automatico a un pool. Il servizio Batch valuta periodicamente la formula e modifica dinamicamente il numero di nodi in un pool in base al carico di lavoro corrente e all'uso delle risorse dello scenario di calcolo. In questo modo è possibile ridurre il costo complessivo dell'esecuzione dell'applicazione usando solo le risorse necessarie e rilasciando quelle non necessarie.

Per abilitare il ridimensionamento automatico, scrivere una [formula di ridimensionamento automatico](batch-automatic-scaling.md#autoscale-formulas) e associarla a un pool. Il servizio Batch usa la formula per determinare il numero di nodi di destinazione nel pool per l'intervallo di ridimensionamento successivo (un intervallo che è possibile configurare). È possibile specificare le impostazioni di ridimensionamento automatico per un pool quando lo si crea oppure abilitare il ridimensionamento in un pool in seguito. È anche possibile aggiornare le impostazioni di ridimensionamento in un pool abilitato per il ridimensionamento.

Ad esempio, un processo richiede l'invio di un numero elevato di attività da eseguire. È possibile assegnare al pool una formula di ridimensionamento che regola il numero di nodi nel pool in base al numero corrente di attività accodate e alla percentuale di completamento delle attività nel processo. Il servizio Batch valuta periodicamente la formula e ridimensiona il pool in base al carico di lavoro e alle altre impostazioni della formula. Il servizio aggiunge nodi in base alle necessità quando c'è un numero elevato di attività in coda e rimuove i nodi quando non sono presenti attività in coda o in esecuzione.

Una formula può essere basata sulle metriche seguenti:

- **Metriche temporali**: basate sulle statistiche raccolte ogni cinque minuti nel numero di ore specificato.
- **Metriche delle risorse**: basate su utilizzo di CPU, larghezza di banda, memoria e numero di nodi.
- **Metriche delle attività**: basate sullo stato delle attività, ad esempio *Attiva* (in coda), *In esecuzione* o *Completata*.

Quando il ridimensionamento automatico riduce il numero di nodi di calcolo in un pool, è necessario considerare come gestire le attività in esecuzione al momento dell'operazione di riduzione. A questo scopo, il servizio Batch offre un'[*opzione di deallocazione dei nodi*](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption) che è possibile includere nelle formule. Ad esempio, è possibile specificare che le attività in esecuzione devono essere arrestate immediatamente e quindi riaccodate per l'esecuzione in un altro nodo o che ne deve essere consentita la fine prima della rimozione del nodo dal pool. Si noti che l'impostazione dell'opzione di deallocazione del nodo come `taskcompletion` o `retaineddata` impedisce le operazioni di ridimensionamento del pool fino al completamento di tutte le attività o fino a che tutti i periodi di memorizzazione delle attività siano scaduti, rispettivamente.

Per altre informazioni sulla scalabilità automatica di un'applicazione, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](batch-automatic-scaling.md).

> [!TIP]
> Per massimizzare l'utilizzo delle risorse di calcolo, impostare il numero di nodi su zero alla fine di un processo, ma consentire il completamento delle attività in esecuzione.

## <a name="task-scheduling-policy"></a>Criteri di pianificazione attività

L'opzione di configurazione [Numero massimo attività per nodo](batch-parallel-node-tasks.md) determina il numero massimo di attività che è possibile eseguire in parallelo in ogni nodo di calcolo del pool.

La configurazione predefinita specifica che viene eseguita una sola attività alla volta in un nodo, ma in alcuni scenari risulta utile eseguire due o più attività contemporaneamente in un nodo. Vedere lo [scenario di esempio](batch-parallel-node-tasks.md#example-scenario) nell'articolo [Ottimizzare l'utilizzo delle risorse di calcolo di Azure Batch con attività dei nodi simultanee](batch-parallel-node-tasks.md) per informazioni su come sfruttare al meglio più attività per nodo.

È anche possibile specificare un *tipo di riempimento* che determina se Batch distribuisce le attività in modo uniforme a tutti i nodi di un pool o se satura ogni nodo con il numero massimo di attività prima di assegnarle ad altri nodi.

## <a name="communication-status"></a>Stato della comunicazione

Nella maggior parte degli scenari le attività funzionano in modo indipendente e non devono comunicare tra loro. È tuttavia possibile che siano presenti applicazioni in cui le attività devono comunicare, ad esempio negli [scenari MPI](batch-mpi.md).

È possibile configurare un pool per consentire la **comunicazione tra nodi** in modo che i nodi all'interno di un pool passano comunicare durante l'esecuzione. Quando la comunicazione tra nodi è abilitata, i nodi nei pool Cloud Services Configuration (Configurazione servizi cloud) possono comunicare tra loro tramite porte superiori alla 1100 e i pool Configurazione macchina virtuale non limitano il traffico su nessuna porta.

L'abilitazione della comunicazione tra nodi ha effetto anche sul posizionamento dei nodi nel cluster e potrebbe limitare il numero massimo di nodi in un pool a causa di restrizioni relative alla distribuzione. Se l'applicazione non richiede la comunicazione tra nodi, il servizio Batch può allocare al pool un numero potenzialmente elevato di nodi da diversi cluster e data center per consentire una maggiore capacità di elaborazione parallela.

## <a name="start-tasks"></a>Attività di avvio

Se lo si desidera, è possibile aggiungere un'[attività di avvio](jobs-and-tasks.md#start-task) facoltativa che viene eseguita in ogni nodo aggiunto al pool e ogni volta che si riavvia o si ricrea l'immagine del nodo. L'attività di avvio è utile soprattutto per preparare i nodi di calcolo per l'esecuzione di attività, ad esempio l'installazione delle applicazioni eseguite dalle attività nei nodi di calcolo.

## <a name="application-packages"></a>Pacchetti dell'applicazione

È possibile specificare pacchetti dell'applicazione da distribuire nei nodi di calcolo del pool. I pacchetti dell'applicazione consentono una distribuzione e un controllo delle versioni più semplici delle applicazioni eseguite dalle attività. I pacchetti dell'applicazione specificati per un pool vengono installati in ogni nodo di calcolo aggiunto al pool e ogni volta che un nodo viene riavviato o ne viene ricreata l'immagine.

Per altre informazioni sull'uso di pacchetti dell'applicazione per distribuire applicazioni ai nodi Batch, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Configurazione della rete virtuale e del firewall

Quando si effettua il provisioning di un pool di nodi di calcolo in Batch di Azure, è possibile associare il pool a una subnet di una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. Per usare una rete virtuale di Azure, l'API client di Batch deve usare l'autenticazione di Azure Active Directory (AD). Il supporto di Azure Batch per Azure AD è documentato in [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).

### <a name="vnet-requirements"></a>Requisiti per la rete virtuale

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Per altre informazioni sulla configurazione di un pool di Batch in una rete virtuale, vedere [Creare un pool di macchine virtuali con la rete virtuale](batch-virtual-network.md).

> [!TIP]
> Per assicurarsi che gli indirizzi IP pubblici usati per accedere ai nodi non cambiano, è possibile [creare un pool con gli indirizzi IP pubblici specificati che si controllano](create-pool-public-ip.md).

## <a name="pool-and-compute-node-lifetime"></a>Durata del pool e dei nodi di calcolo

Quando si progetta una soluzione Azure Batch, bisogna specificare come e quando vengono creati i pool e per quanto tempo vengono mantenuti disponibili i nodi di calcolo all'interno dei pool.

Da un lato, è possibile creare un pool per ogni processo inviato ed eliminare il pool al termine dell'esecuzione dell'attività. In questo modo è possibile massimizzare l'uso, perché i nodi vengono allocati solo quando è necessario e vengono arrestati non appena risultano inattivi. Mentre ciò significa che il processo deve attendere l'allocazione dei nodi, è importante notare che le attività vengono pianificate per l'esecuzione non appena i nodi risultano allocati individualmente e dopo il completamento dell'attività di avvio. Il servizio Batch *non* attende che tutti i nodi di un pool siano disponibili prima di assegnare le attività ai nodi, assicurando quindi il massimo utilizzo di tutti i nodi disponibili.

Dall'altro lato, se l'avvio immediato dei processi ha la priorità più alta, è possibile può creare subito un pool e rendere disponibili i relativi nodi prima dell'invio dei processi. In questo scenario le attività possono essere avviate immediatamente, ma i nodi possono rimanere inattivi in attesa che vengano assegnate.

Un approccio combinato viene in genere usato per la gestione di un carico variabile ma continuo. È possibile disporre di un pool in cui vengono inviati più processi e che può ridimensionare il numero di nodi in base al carico di lavoro. Questa operazione può essere eseguita in modo reattivo in base al carico corrente o in modo proattivo se è possibile prevedere il carico. Per altre informazioni, vedere [Ridimensionamento automatico del sistema operativo](#automatic-scaling-policy).

## <a name="autopools"></a>Pool

Un [pool](/rest/api/batchservice/job/add#autopoolspecification) di automaticamente è un pool creato dal servizio batch quando viene inviato un processo, anziché essere creato prima dei processi che saranno eseguiti nel pool. Il servizio batch gestirà la durata di un pool di autopool in base alle caratteristiche specificate. Spesso, questi pool vengono anche impostati per l'eliminazione automatica dopo il completamento dei processi.

## <a name="security-with-certificates"></a>Sicurezza con certificati

È in genere necessario usare certificati per crittografare o decrittografare informazioni riservate per le attività, ad esempio la chiave per un [account di archiviazione di Azure](accounts.md#azure-storage-accounts). Per supportare questa funzionalità, è possibile installare certificati nei nodi. I segreti crittografati vengono passati alle attività nei parametri della riga di comando o incorporati in una delle risorse dell'attività e i certificati installati possono essere usati per decrittografarli.

Per aggiungere un certificato a un account Batch, usare l'operazione [Aggiungi certificato](/rest/api/batchservice/certificate/add) (Batch REST) o il metodo [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) (Batch .NET). È quindi possibile associare il certificato a un pool nuovo o esistente.

Quando un certificato è associato a un pool, il servizio Batch installa il certificato in ogni nodo del pool. Il servizio Batch installa i certificati appropriati all'avvio del nodo, prima di avviare le attività, incluse quelle di [avvio](jobs-and-tasks.md#start-task) e del [gestore di processi](jobs-and-tasks.md#job-manager-task).

Se si aggiunge un certificato a un pool esistente, è necessario riavviare i relativi nodi di calcolo per applicare il certificato ai nodi.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [processi e attività](jobs-and-tasks.md).
