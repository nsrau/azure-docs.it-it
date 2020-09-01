---
title: Procedure consigliate
description: Procedure consigliate e suggerimenti utili per lo sviluppo di soluzioni Azure Batch.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: ca6e491586fd653f39da7466ea116109000facd6
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146539"
---
# <a name="azure-batch-best-practices"></a>Procedure consigliate per Azure Batch

Questo articolo contiene una raccolta di procedure consigliate per l'uso efficace ed efficiente del servizio Azure Batch, in base a un'esperienza reale con Batch. Leggere questo articolo per evitare errori di progettazione, potenziali problemi di prestazioni e la creazione di antipattern durante lo sviluppo per e l'uso di Batch.

## <a name="pools"></a>Pool

I [pool](nodes-and-pools.md#pools) sono le risorse di calcolo per l'esecuzione di processi nel servizio Batch. Le sezioni seguenti includono raccomandazioni per l'uso di pool di Batch.

### <a name="pool-configuration-and-naming"></a>Configurazione e denominazione del pool

- **Modalità di allocazione pool** Quando si crea un account Batch, è possibile scegliere tra due modalità di allocazione pool: **servizio Batch** o **sottoscrizione utente**. Nella maggior parte dei casi si userà la modalità predefinita del servizio Batch, in cui i pool vengono allocati dietro le quinte nelle sottoscrizioni gestite da Batch. Nella modalità sottoscrizione utente alternativa, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione in fase di creazione di un pool. Gli account di sottoscrizione utente vengono usati principalmente per rendere possibile un sottoinsieme di scenari, piccolo ma importante. Per altre informazioni sulla modalità di sottoscrizione utente, vedere [Configurazione aggiuntiva per la modalità di sottoscrizione utente](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Considerare il tempo di esecuzione di processi e attività per determinare il mapping tra processi e pool.**
    Se i processi sono costituiti prevalentemente da attività di breve durata e il numero totale previsto di attività è ridotto, per cui il tempo di esecuzione complessivo previsto per il processo non è molto lungo, non allocare un nuovo pool per ogni processo. Il tempo di allocazione dei nodi ridurrà il tempo di esecuzione del processo.

- **I pool dovrebbero avere più di un nodo di calcolo.**
    Non è garantito che i singoli nodi siano sempre disponibili. Anche se non comuni, gli errori dell'hardware, gli aggiornamenti del sistema operativo e una serie di altri problemi possono portare offline i singoli nodi. Se il carico di lavoro di Batch richiede un avanzamento deterministico e garantito, è consigliabile allocare pool con più nodi.

- **Non riutilizzare i nomi delle risorse.**
    Le risorse di Batch (processi, pool e così via) non sono in genere fisse nel corso del tempo. Ad esempio, è possibile creare un pool il lunedì, eliminarlo il martedì e quindi crearne un altro il giovedì. A ogni nuova risorsa creata è necessario assegnare un nome univoco che non è mai stato usato prima. A tale scopo, è possibile usare un GUID (per l'intero nome della risorsa o solo per una parte) o incorporare nel nome l'ora in cui è stata creata la risorsa. Batch supporta [DisplayName](/dotnet/api/microsoft.azure.batch.jobspecification.displayname), che è possibile usare per assegnare a una risorsa un nome facilmente leggibile, anche se l'ID risorsa effettiva può essere più complesso. L'uso di nomi univoci consente di distinguere più facilmente la risorsa specifica a cui fanno riferimento log e metriche. Rimuove anche l'ambiguità se è necessario presentare un caso di supporto per una risorsa.

- **Continuità durante la manutenzione e gli errori del pool.**
    È preferibile che i processi usino i pool in modo dinamico. Se i processi usano lo stesso pool per tutte le operazioni, è possibile che non vengano eseguiti se si verificano problemi in tale pool. Questo aspetto è particolarmente importante per i carichi di lavoro dipendenti dal tempo. Per risolvere questo problema, selezionare o creare un pool in modo dinamico quando si pianifica ogni processo oppure ignorare in qualche modo il nome del pool in modo che sia possibile evitare un pool non integro.

- **Continuità aziendale durante la manutenzione e gli errori del pool** Le dimensioni di un pool potrebbero non aumentare fino al livello previsto per una serie di possibili cause, ad esempio errori interni, vincoli di capacità e così via. Per questo motivo, è consigliabile essere pronti a ridestinare i processi a un pool diverso (possibilmente con dimensioni di VM diverse, cosa che Batch supporta tramite [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)), se necessario. Evitare di usare un ID pool statico con l'aspettativa che non verrà mai eliminato e modificato.

### <a name="pool-lifetime-and-billing"></a>Durata e fatturazione del pool

La durata del pool può variare a seconda del metodo di allocazione e delle opzioni applicate alla relativa configurazione. I pool possono avere una durata arbitraria e un numero variabile di nodi di calcolo in qualsiasi momento. È responsabilità dell'utente gestire i nodi di calcolo nel pool in modo esplicito o tramite le funzionalità fornite dal servizio (scalabilità automatica o pool automatici).

- **Mantenere i pool aggiornati.**
    È consigliabile ridimensionare i pool a zero ogni pochi mesi per assicurarsi di ottenere gli aggiornamenti più recenti degli agenti dei nodi e le correzioni di bug. Il pool non riceverà gli aggiornamenti degli agenti dei nodi a meno che non venga ricreato o ridimensionato a 0 nodi di calcolo. Prima di ricreare o ridimensionare il pool, è consigliabile scaricare tutti i log degli agenti dei nodi a scopo di debug, come descritto nella sezione [Nodi](#nodes).

- **Ricreazione del pool** Analogamente, non è consigliabile eliminare e ricreare i pool su base giornaliera. Creare invece un nuovo pool e aggiornare i processi esistenti in modo che puntino a questo pool. Una volta spostate tutte le attività nel nuovo pool, eliminare quello precedente.

- **Efficienza e fatturazione del pool** Batch non comporta costi aggiuntivi di per sé, ma vengono addebitati i costi per le risorse di calcolo usate. Si riceve un addebito per ogni nodo di calcolo del pool, indipendentemente dallo stato in cui si trova. Sono inclusi tutti gli addebiti richiesti per l'esecuzione del nodo, ad esempio i costi di archiviazione e di rete. Per altre informazioni sulle procedure consigliate, vedere [Analisi dei costi e budget per Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Errori di allocazione del pool

Gli errori di allocazione del pool possono verificarsi in qualsiasi momento durante la prima allocazione o con i successivi ridimensionamenti. Questo problema può essere dovuto a un esaurimento temporaneo della capacità in un'area o a errori di altri servizi di Azure su cui si basa Batch. La quota di core non è una garanzia, ma piuttosto un limite.

### <a name="unplanned-downtime"></a>Tempo di inattività non pianificato

È possibile che i pool di Batch riscontrino eventi di tempi di inattività in Azure. Tenere presente questo aspetto quando si pianifica e si sviluppa uno scenario o un flusso di lavoro per Batch.

In caso di errore di un nodo, Batch prova automaticamente a ripristinare i nodi di calcolo per conto dell'utente. Questo tentativo può attivare la ripianificazione di qualsiasi attività in esecuzione nel nodo ripristinato. Per altre informazioni sulle attività interrotte, vedere [Progettazione per la ripetizione di tentativi](#design-for-retries-and-re-execution).

### <a name="custom-image-pools"></a>Pool di immagini personalizzate

Quando si crea un pool in Azure Batch usando la configurazione della macchina virtuale, specificare l'immagine di macchina virtuale (VM) che fornisce la configurazione del sistema operativo per ogni nodo di calcolo nel pool. È possibile creare il pool con un'immagine di Azure Marketplace supportata oppure è possibile [creare un'immagine personalizzata con un'immagine della raccolta immagini condivisa](batch-sig-images.md). Anche se è possibile usare un' [immagine gestita](batch-custom-images.md) per creare un pool di immagini personalizzato, è consigliabile creare immagini personalizzate usando la raccolta di immagini condivise quando possibile. Usando la raccolta di immagini condivise è possibile eseguire il provisioning dei pool più velocemente, ridimensionare quantità maggiori di macchine virtuali e migliorare l'affidabilità durante il provisioning di VM.

### <a name="third-party-images"></a>Immagini di terze parti

I pool possono essere creati usando immagini di terze parti pubblicate in Azure Marketplace. Con gli account batch della modalità di sottoscrizione utente, è possibile che venga visualizzato l'errore "allocazione non riuscita a causa di un controllo di idoneità acquisti nel Marketplace" quando si crea un pool con immagini di terze parti. Per correggere l'errore, accettare le condizioni impostate dall'editore dell'immagine. A tale scopo, è possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) o l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/vm/image/terms).

### <a name="azure-region-dependency"></a>Dipendenza dall'area di Azure

Nel caso di carichi di lavoro di produzione o dipendenti dal tempo, è consigliabile evitare di dipendere da una singola area di Azure. Anche se rari, esistono problemi che possono influire su un'intera area. Se ad esempio l'elaborazione deve essere avviata a un'ora specifica, è consigliabile aumentare il pool nell'area primaria *con molto anticipo rispetto all'ora di inizio*. Se l'aumento del pool non riesce in quell'area, è possibile eseguirne il fallback in una o più aree di backup. I pool distribuiti tra più account in aree diverse forniscono un backup pronto e facilmente accessibile se si verificano problemi in un altro pool. Per altre informazioni, vedere [Progettare l'applicazione per la disponibilità elevata](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Processi

Un [processo](jobs-and-tasks.md#jobs) è un contenitore progettato per includere centinaia, migliaia o anche milioni di attività. Seguire queste linee guida durante la creazione di processi.

### <a name="fewer-jobs-more-tasks"></a>Meno processi, più attività

L'uso di un processo per eseguire una singola attività è inefficiente. Ad esempio, è più efficiente usare un singolo processo contenente 1000 attività invece di creare 100 processi che ne contengono 10 ognuno. L'esecuzione di 1000 processi, ognuno con una singola attività, è l'approccio meno efficiente, più lento e più costoso da intraprendere.

Per questo motivo, assicurarsi di non progettare una soluzione Batch che richiede migliaia di processi attivi simultaneamente. Non è prevista alcuna quota per le attività, quindi l'esecuzione di molte attività nel minor numero possibile di processi comporta un uso efficiente delle [quote di processi e di pianificazioni di processi](batch-quota-limit.md#resource-quotas).

### <a name="job-lifetime"></a>Durata dei processi

Un processo di Batch ha una durata indefinita fino a quando non viene eliminato dal sistema. Il relativo stato indica se può accettare o meno la pianificazione di altre attività.

Un processo non passa automaticamente allo stato completato a meno che non venga terminato esplicitamente. Questa operazione può essere attivata automaticamente tramite la proprietà [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) o [maxWallClockTime](/rest/api/batchservice/job/add#jobconstraints).

Esiste una [quota predefinita per i processi attivi e le pianificazioni di processi](batch-quota-limit.md#resource-quotas). I processi e le pianificazioni di processi nello stato completato non vengono conteggiati ai fini di questa quota.

## <a name="tasks"></a>Attività

Le [attività](jobs-and-tasks.md#tasks) sono singole unità di lavoro che costituiscono un processo. Le attività vengono inviate dall'utente e pianificate da Batch nei nodi di calcolo. Quando si creano ed eseguono attività, è necessario tenere presenti diverse considerazioni di progettazione. Le sezioni seguenti illustrano scenari comuni e descrivono come progettare le attività per gestire i problemi e garantire prestazioni efficienti.

### <a name="save-task-data"></a>Salvare i dati delle attività

I nodi di calcolo sono per loro natura temporanei. Batch include molte funzionalità, come i pool automatici e la scalabilità automatica, per semplificano la rimozione dei nodi. Quando i nodi lasciano il pool (a causa di un ridimensionamento o di un'eliminazione del pool), vengono eliminati anche tutti i file al loro interno. Per questo motivo, l'output di un'attività deve essere spostato dal nodo in cui è in esecuzione in un archivio permanente prima del completamento. Analogamente, se un'attività non riesce, è necessario spostare i log necessari per diagnosticare l'errore in un archivio permanente.

Batch offre il supporto integrato per Archiviazione di Azure per il caricamento dei dati tramite [OutputFiles](batch-task-output-files.md), oltre che per un'ampia gamma di file system condivisi. In alternativa, è possibile eseguire manualmente il caricamento delle attività.

### <a name="manage-task-lifetime"></a>Gestire la durata delle attività

Eliminare le attività quando non sono più necessarie o impostare un vincolo di attività [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime). Se `retentionTime` è impostato, Batch pulisce automaticamente lo spazio su disco usato dall'attività alla scadenza di `retentionTime`.

Con l'eliminazione delle attività, si realizzano due obiettivi. Ci si assicura di non avere un accumulo di attività nel processo, che può rendere più difficile l'esecuzione di query o l'individuazione di un'attività a cui si è interessati (perché sarà necessario filtrare le attività completate). Vengono inoltre puliti i dati dell'attività corrispondenti nel nodo, purché non sia già stato raggiunto il valore di `retentionTime`. In questo modo si evita che i nodi si riempiano di dati di attività ed esauriscano lo spazio.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>Inviare un numero elevato di attività nella raccolta

Le attività possono essere inviate singolarmente o in raccolte. Inviare le attività in [raccolte](/rest/api/batchservice/task/addcollection) che ne contengono fino a 100 alla volta durante l'invio in blocco per ridurre il sovraccarico e i tempi della procedura.

### <a name="set-max-tasks-per-node-appropriately"></a>Impostare il numero massimo di attività per nodo nel modo appropriato

Batch supporta la sovrasottoscrizione di attività nei nodi, ossia l'esecuzione di un numero di attività maggiore del numero di core di un nodo. È responsabilità dell'utente assicurarsi che il numero di attività sia adeguato per i nodi del pool. È ad esempio possibile che si verifichi una riduzione delle prestazioni se si provano a pianificare otto attività, ognuna delle quali utilizza il 25% della CPU in un nodo (in un pool con `maxTasksPerNode = 8`).

### <a name="design-for-retries-and-re-execution"></a>Progettare per la ripetizione di tentativi e di esecuzioni

Batch può provare automaticamente a ripetere le attività. Esistono due tipi di tentativi: controllati dall'utente e interni. I tentativi controllati dall'utente sono specificati dal valore di [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount) dell'attività. Quando un programma specificato nell'attività termina con un codice di uscita diverso da zero, l'attività viene riprovata fino al valore di `maxTaskRetryCount`.

In rari casi, un'attività può essere riprovata internamente a causa di errori del nodo di calcolo, ad esempio se non è possibile aggiornare lo stato interno o si verifica un problema nel nodo durante l'esecuzione dell'attività. L'attività verrà riprovata nello stesso nodo di calcolo, se possibile, fino a un limite interno prima che venga abbandonata e rinviata per la ripianificazione di Batch, possibilmente in un nodo di calcolo diverso.

Non esistono differenze per le attività eseguite in nodi dedicati o con priorità bassa. Sia che un'attività venga interrotta durante l'esecuzione in un nodo con priorità bassa o a causa di un errore in un nodo dedicato, è possibile mitigare gli effetti di entrambe le situazioni progettando l'attività per la tolleranza degli errori.

### <a name="build-durable-tasks"></a>Creare attività permanenti

Le attività dovrebbero essere progettate per tollerare gli errori e supportare la ripetizione di tentativi. Ciò vale soprattutto per le attività a esecuzione prolungata. A tale scopo, assicurarsi che le attività generino lo stesso risultato singolo, anche se vengono eseguite più di una volta. Per ottenere questo risultato, è possibile creare attività finalizzate a un obiettivo. Un altro modo consiste nel verificare che le attività siano idempotenti (le attività avranno lo stesso risultato indipendentemente dal numero di volte in cui vengono eseguite).

Un esempio comune è un'attività per la copia di file in un nodo di calcolo. Sarebbe possibile ad esempio creare un'attività che copia tutti i file specificati ogni volta che viene eseguita, ma questo approccio, pur essendo semplice, non è efficiente e non prevede la tolleranza degli errori. Creare invece un'attività che assicuri che i file si trovino nel nodo di calcolo, ossia un'attività che non ricopi i file già presenti. In questo modo, l'attività riprenderà dal punto in cui è stata interrotta.

### <a name="avoid-short-execution-time"></a>Evitare tempi di esecuzione brevi

Le attività che vengono eseguite solo per uno a due secondi non sono ideali. È consigliabile provare a completare una notevole quantità di operazioni con una singola attività, in almeno 10 secondi oppure in diverse ore o giorni. Se ogni attività è in esecuzione per almeno un minuto, il sovraccarico della pianificazione è minimo rispetto al tempo di calcolo complessivo.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Usare l'ambito del pool per le attività brevi nei nodi Windows

Quando si pianifica un'attività nei nodi batch, è possibile scegliere se eseguirla con ambito attività o ambito pool. Se l'attività viene eseguita solo per un breve periodo di tempo, l'ambito dell'attività può risultare inefficiente a causa delle risorse necessarie per creare l'account utente automatico per tale attività. Per una maggiore efficienza, è consigliabile impostare queste attività sull'ambito del pool. Per altre informazioni, vedere [eseguire un'attività come utente automatico con ambito del pool](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope).

## <a name="nodes"></a>Nodi

Un [nodo di calcolo](nodes-and-pools.md#nodes) è una macchina virtuale (VM) di Azure o del servizio cloud dedicata all'elaborazione di una parte del carico di lavoro dell'applicazione. Per l'uso dei nodi, seguire queste linee guida.

### <a name="idempotent-start-tasks"></a>Attività di avvio idempotenti

Come per le altre attività, l'[attività di avvio](jobs-and-tasks.md#start-task) dovrà essere idempotente, in quanto verrà rieseguita a ogni avvio del nodo. Un'attività idempotente è semplicemente quella che produce un risultato coerente quando viene eseguita più volte.

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>Gestire i servizi a esecuzione prolungata tramite l'interfaccia dei servizi del sistema operativo

A volte è necessario eseguire un altro agente insieme all'agente di Batch nel nodo. È ad esempio possibile che vengano raccolti i dati dal nodo per segnalarli. È consigliabile distribuire questi agenti come servizi del sistema operativo, ad esempio un servizio Windows o un servizio `systemd` Linux.

Questi servizi, quando vengono eseguiti, non devono applicare blocchi sui file nelle directory del nodo gestite da Batch, perché altrimenti Batch non sarà in grado di eliminare tali directory a causa dei blocchi sui file. Ad esempio, se si installa un servizio Windows in un'attività di avvio, invece di avviare il servizio direttamente dalla directory di lavoro dell'attività di avvio, copiare i file altrove (oppure se i file esistono già ignorare semplicemente la copia). Installare quindi il servizio da tale posizione. Quando Batch riesegue l'attività di avvio, eliminerà la directory di lavoro dell'attività di avvio e la creerà di nuovo. Questo approccio funziona perché il servizio ha applicato blocchi sui file nell'altra directory, non nella directory di lavoro dell'attività di avvio.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Evitare di creare giunzioni di directory in Windows

Le giunzioni di directory, talvolta denominate collegamenti reali di directory, sono difficili da gestire durante la pulizia di processi e attività. Usare i collegamenti simbolici (collegamenti temporanei) anziché i collegamenti reali.

### <a name="collect-the-batch-agent-logs"></a>Raccogliere i log dell'agente di Batch

Se si nota un problema che interessa il comportamento di un nodo o di attività in esecuzione al suo interno, raccogliere i log dell'agente di Batch prima di deallocare i nodi in questione. I log dell'agente di Batch possono essere raccolti usando l'API Carica log del servizio Batch. Questi log possono essere forniti come parte di un ticket di supporto a Microsoft e facilitano l'individuazione e la risoluzione dei problemi.

### <a name="manage-os-upgrades"></a>Gestire gli aggiornamenti del sistema operativo

Per gli account batch in modalità sottoscrizione utente, gli aggiornamenti automatici del sistema operativo possono interrompere lo stato dell'attività, soprattutto se le attività hanno un'esecuzione prolungata. La [creazione di attività idempotente](#build-durable-tasks) può aiutare a ridurre gli errori causati da tali interruzioni. Si consiglia inoltre [di pianificare gli aggiornamenti delle immagini del sistema operativo per i periodi in cui non è prevista l'esecuzione delle attività](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades).

## <a name="isolation-security"></a>Sicurezza dell'isolamento

Se lo scenario richiede l'isolamento dei processi uno dall'altro, inserirli in pool separati. Un pool rappresenta il limite di isolamento di sicurezza in Batch e, per impostazione predefinita, due pool non sono visibili o sono in grado di comunicare tra loro. Evitare di usare account Batch distinti come mezzo di isolamento.

## <a name="moving-batch-accounts-across-regions"></a>Spostamento di account Batch tra aree

In alcuni scenari può risultare utile spostare un account Batch da un'area a un'altra. Ad esempio, è possibile scegliere lo spostamento in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

Non è possibile spostare direttamente gli account Azure Batch da un'area a un'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente dell'account Batch. È quindi possibile spostare la risorsa in un'altra area di staging esportando l'account Batch in un modello, modificando i parametri in base all'area di destinazione e quindi distribuendo il modello nella nuova area.

Dopo aver caricato il modello nella nuova area, sarà necessario ricreare i certificati, le pianificazioni dei processi e i pacchetti dell'applicazione. Per eseguire il commit delle modifiche e completare lo spostamento dell'account Batch, assicurarsi di eliminare l'account Batch o il gruppo di risorse originale.

Per altre informazioni su Resource Manager e sui modelli, vedere [Avvio rapido: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="connectivity"></a>Connettività

Considerare le indicazioni seguenti per la connettività delle soluzioni Batch.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Gruppi di sicurezza di rete (NSG) e route definite dall'utente

Quando si effettua il provisioning di [pool di Batch in una rete virtuale](batch-virtual-network.md), assicurarsi di seguire attentamente le linee guida relative all'uso del tag di servizio `BatchNodeManagement`, delle porte, dei protocolli e della direzione della regola.
L'uso del tag di servizio è altamente consigliato al posto degli indirizzi IP sottostanti del servizio Batch. Il motivo è che gli indirizzi IP possono cambiare nel corso del tempo. L'uso diretto degli indirizzi IP del servizio Batch può generare instabilità, interruzioni o disservizi per i pool di Batch.

Per le route definite dall'utente, assicurarsi di implementare un processo per aggiornare periodicamente gli indirizzi IP del servizio Batch nella tabella di route, perché questi indirizzi cambiano nel corso del tempo. Per informazioni su come ottenere l'elenco degli indirizzi IP del servizio Batch, vedere [Tag del servizio in locale](../virtual-network/service-tags-overview.md). Gli indirizzi IP del servizio Batch verranno associati al tag del servizio `BatchNodeManagement` (o alla variante locale corrispondente all'area dell'account Batch).

### <a name="honoring-dns"></a>Rispettare il DNS

Assicurarsi che i sistemi rispettino la durata (TTL) DNS per l'URL del servizio dell'account Batch. Inoltre, assicurarsi che i client del servizio batch e altri meccanismi di connettività al servizio batch non si basino sugli indirizzi IP (o [creare un pool con indirizzi IP pubblici statici](create-pool-public-ip.md) come descritto di seguito).

Se le richieste ricevono risposte HTTP di livello 5xx in cui è presente un'intestazione "Connection: close", il client del servizio Batch deve rispettare la raccomandazione chiudendo la connessione esistente, risolvendo di nuovo il DNS per l'URL del servizio dell'account Batch e provando a eseguire le richieste successive su una nuova connessione.

### <a name="retry-requests-automatically"></a>Riprovare automaticamente le richieste

Assicurarsi che per i client del servizio Batch siano implementati criteri appropriati di ripetizione dei tentativi per riprovare automaticamente le richieste, anche durante il normale funzionamento e non esclusivamente durante i periodi di manutenzione del servizio. Questi criteri di ripetizione dei tentativi devono estendersi in un intervallo di almeno 5 minuti. Le funzionalità di ripetizione automatica dei tentativi sono fornite da vari SDK di Batch, ad esempio la [classe .NET RetryPolicyProvider](/dotnet/api/microsoft.azure.batch.retrypolicyprovider).

### <a name="static-public-ip-addresses"></a>Indirizzi IP pubblici statici

In genere, le macchine virtuali in un pool di batch sono accessibili tramite indirizzi IP pubblici che possono cambiare nel corso della durata del pool. Questo può rendere difficile l'interazione con un database o un altro servizio esterno che limita l'accesso a determinati indirizzi IP. Per assicurarsi che gli indirizzi IP pubblici nel pool non cambino in modo imprevisto, è possibile creare un pool usando un set di indirizzi IP pubblici statici da controllare. Per ulteriori informazioni, vedere [creare un pool di Azure batch con gli indirizzi IP pubblici specificati](create-pool-public-ip.md).

## <a name="batch-node-underlying-dependencies"></a>Dipendenze sottostanti dei nodi di Batch

Quando si progettano le soluzioni Batch, tenere presenti le dipendenze e le restrizioni seguenti.

### <a name="system-created-resources"></a>Risorse create dal sistema

Azure Batch Crea e gestisce una serie di utenti e gruppi nella VM, che non dovranno essere modificati. Ecco quali sono:

#### <a name="windows"></a>Windows

- Un utente denominato **PoolNonAdmin**
- Un gruppo di utenti denominato **WATaskCommon**

#### <a name="linux"></a>Linux

- Un utente denominato **_azbatch**

### <a name="file-cleanup"></a>Pulizia dei file

Batch prova attivamente a pulire la directory di lavoro in cui vengono eseguite le attività, al termine del periodo di conservazione. La pulizia di tutti i file scritti al di fuori di questa directory è [responsabilità dell'utente](#manage-task-lifetime), per evitare di riempire spazio su disco. 

La pulizia automatizzata per la directory di lavoro verrà bloccata se si esegue un servizio in Windows dalla directory di lavoro startTask, perché la cartella è ancora in uso. Questo comporterà una riduzione delle prestazioni. Per risolvere il problema, specificare per tale servizio una directory diversa non gestita da Batch.
