---
title: Procedure consigliate-Azure Batch
description: Informazioni sulle procedure consigliate e suggerimenti utili per lo sviluppo della soluzione Azure Batch.
author: laurenhughes
ms.author: lahugh
ms.date: 11/22/2019
ms.service: batch
ms.topic: article
manager: gwallace
ms.openlocfilehash: 19c5b6acaeddb915af49cf62a884da0678075f15
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535665"
---
# <a name="azure-batch-best-practices"></a>Procedure consigliate Azure Batch

In questo articolo viene illustrata una raccolta di procedure consigliate per l'utilizzo del servizio Azure Batch in modo efficace ed efficiente. Queste procedure consigliate sono derivate dalla nostra esperienza con batch e dalle esperienze dei clienti batch. È importante comprendere questo articolo per evitare trappole di progettazione, potenziali problemi di prestazioni e anti-pattern durante lo sviluppo per e l'uso di batch.

In questo articolo si apprenderà:

> [!div class="checklist"]
> - Quali sono le procedure consigliate
> - Motivi per cui è consigliabile utilizzare le procedure consigliate
> - Cosa può accadere se non si seguono le procedure consigliate
> - Come seguire le procedure consigliate

## <a name="pools"></a>Pool

I pool di batch sono le risorse di calcolo per l'esecuzione di processi nel servizio batch. Nelle sezioni seguenti vengono fornite indicazioni sulle principali procedure consigliate da seguire quando si utilizzano i pool di batch.

### <a name="pool-configuration-and-naming"></a>Configurazione e denominazione del pool

- **Modalità di allocazione pool**  
    Quando si crea un account batch, è possibile scegliere tra due modalità di allocazione pool: **servizio batch** o **sottoscrizione utente**. Per la maggior parte dei casi, è consigliabile usare la modalità predefinita per il servizio batch, in cui i pool vengono allocati in background nelle sottoscrizioni gestite da batch. Nella modalità sottoscrizione utente alternativa, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione in fase di creazione di un pool. Gli account di sottoscrizione utente vengono utilizzati principalmente per consentire un subset di scenari importante, ma di piccole dimensioni. Per altre informazioni sulla modalità di sottoscrizione utente, vedere [configurazione aggiuntiva per la modalità di sottoscrizione utente](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Prendere in considerazione il processo e l'esecuzione dell'attività durante la determinazione del mapping del processo al pool.**  
    Se sono presenti processi che prevedono principalmente attività di breve durata e i conteggi delle attività totali previste sono ridotti, in modo che il tempo di esecuzione complessivo previsto del processo non sia lungo, non allocare un nuovo pool per ogni processo. Il tempo di allocazione dei nodi diminuirà il tempo di esecuzione del processo.

- **I pool devono avere più di un nodo di calcolo.**  
    Non è garantito che i singoli nodi siano sempre disponibili. Sebbene non comune, gli errori hardware, gli aggiornamenti del sistema operativo e un host di altri problemi possono causare la offline dei singoli nodi. Se il carico di lavoro batch richiede deterministico, è necessario allocare i pool con più nodi.

- **Non riutilizzare i nomi delle risorse.**  
    Le risorse batch (processi, pool e così via) passano spesso nel tempo. Ad esempio, è possibile creare un pool il lunedì, eliminarlo martedì e quindi creare un altro pool il giovedì. A ogni nuova risorsa creata è necessario assegnare un nome univoco che non è mai stato usato in precedenza. A tale scopo, è possibile usare un GUID (come intero nome della risorsa o come parte di esso) o incorporare l'ora in cui è stata creata la risorsa nel nome della risorsa. Batch supporta [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), che può essere usato per assegnare a una risorsa un nome leggibile anche se l'ID di risorsa effettivo è un elemento che non è facile da usare. L'uso di nomi univoci rende più semplice distinguere la risorsa particolare eseguita in log e metriche. Rimuove anche l'ambiguità se è necessario archiviare un caso di supporto per una risorsa.

- **Continuità durante la manutenzione e l'errore del pool.**  
    È preferibile che i processi usino i pool in modo dinamico. Se i processi usano lo stesso pool per tutti gli elementi, è possibile che i processi non vengano eseguiti se si verificano problemi con il pool. Questa operazione è particolarmente importante per i carichi di lavoro dipendenti dal tempo. Per risolvere questo problema, selezionare o creare un pool in modo dinamico quando si pianifica ogni processo oppure è possibile eseguire l'override del nome del pool in modo che sia possibile ignorare un pool non integro.

- **Continuità aziendale durante la manutenzione e l'errore del pool**  
    Esistono molte possibili cause che possono impedire a un pool di raggiungere le dimensioni richieste, ad esempio errori interni, vincoli di capacità e così via. Per questo motivo, è necessario essere pronti per ridestinare i processi in un pool diverso (possibilmente con una dimensione di macchina virtuale diversa: batch supporta questo tramite [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)), se necessario. Evitare di usare un ID pool statico con la previsione che non verrà mai eliminato e mai modificato.

### <a name="pool-lifetime-and-billing"></a>Durata e fatturazione del pool

La durata del pool può variare a seconda del metodo di allocazione e delle opzioni applicate alla configurazione del pool. I pool possono avere una durata arbitraria e un numero variabile di nodi di calcolo nel pool in qualsiasi momento. È responsabilità dell'utente gestire i nodi di calcolo nel pool in modo esplicito o tramite le funzionalità fornite dal servizio (scalabilità automatica o pool automatico).

- **Mantieni i pool aggiornati.**  
    È necessario ridimensionare i pool a zero ogni pochi mesi per assicurarsi di ottenere gli aggiornamenti più recenti dell'agente del nodo e le correzioni di bug. Il pool non riceverà gli aggiornamenti dell'agente del nodo, a meno che non venga ricreato o ridimensionato in 0 nodi di calcolo. Prima di ricreare o ridimensionare il pool, è consigliabile scaricare tutti i log dell'agente node a scopo di debug, come illustrato nella sezione [Nodes (nodi](#nodes) ).

- **Ricreazione del pool**  
    Con una nota simile, non è consigliabile eliminare e ricreare i pool su base giornaliera. In alternativa, creare un nuovo pool, aggiornare i processi esistenti in modo che puntino al nuovo pool. Una volta che tutte le attività sono state spostate nel nuovo pool, eliminare il pool precedente.

- **Efficienza e fatturazione del pool**  
    Batch non comporta costi aggiuntivi, ma vengono addebitati costi per le risorse di calcolo usate. Viene addebitato ogni nodo di calcolo nel pool, indipendentemente dallo stato in cui si trovano. Sono inclusi tutti gli addebiti necessari per l'esecuzione del nodo, ad esempio i costi di archiviazione e di rete. Per informazioni sulle procedure consigliate, vedere l'articolo [relativo all'analisi dei costi e ai budget per Azure batch](budget.md).

### <a name="pool-allocation-failures"></a>Errori di allocazione pool

Gli errori di allocazione del pool possono verificarsi in qualsiasi momento durante la prima allocazione o le successive dimensioni. Questo problema può essere dovuto a un esaurimento della capacità temporaneo in un'area o a errori in altri servizi di Azure su cui si basa il batch. La quota di core non è una garanzia, bensì un limite.

### <a name="unplanned-downtime"></a>Tempo di inattività non pianificato

È possibile che i pool di batch sperimentino eventi di tempo di inattività in Azure. Questo aspetto è importante da tenere presente quando si pianifica e si sviluppa lo scenario o il flusso di lavoro per batch.

In caso di errore di un nodo, batch tenta automaticamente di ripristinare i nodi di calcolo per conto dell'utente. Questo può attivare la ripianificazione di qualsiasi attività in esecuzione nel nodo recuperato. Per ulteriori informazioni sulle attività interrotte, vedere [progettazione per i tentativi](#designing-for-retries-and-re-execution) .

- **Dipendenza dall'area di Azure**  
    Si consiglia di non dipendere da una singola area di Azure se si ha un carico di lavoro di produzione o dipendente dal tempo. Sebbene rare, esistono problemi che possono interessare un'intera area. Se, ad esempio, l'elaborazione deve essere avviata in un momento specifico, provare a eseguire il ridimensionamento del pool nell'area primaria *prima dell'ora di inizio*. Se la scala del pool ha esito negativo, è possibile eseguire il fallback per la scalabilità verticale di un pool in un'area o in aree di backup. I pool tra più account in aree diverse forniscono un backup pronto e facilmente accessibile se si verificano problemi con un altro pool. Per altre informazioni, vedere [progettare l'applicazione per la disponibilità elevata](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Processi

Un processo è un contenitore progettato per contenere centinaia, migliaia o persino milioni di attività.

- **Inserire molte attività in un processo**  
    L'utilizzo di un processo per l'esecuzione di una singola attività non è efficiente. Ad esempio, è più efficiente usare un singolo processo contenente 1000 attività invece di creare 100 processi che contengono 10 attività ciascuna. L'esecuzione di processi 1000, ognuno con una singola attività, è l'approccio meno efficiente, più lento e più costoso da intraprendere.  

    Non progettare una soluzione batch che richiede migliaia simultaneamente di processi attivi. Non è prevista alcuna quota per le attività, pertanto l'esecuzione di tutte le attività nel minor numero possibile di processi utilizza in modo efficiente le [quote del processo e della pianificazione dei processi](batch-quota-limit.md#resource-quotas).

- **Durata processo**  
    Un processo batch ha una durata indefinita fino a quando non viene eliminato dal sistema. Lo stato di un processo indica se può accettare più attività per la pianificazione o meno. Un processo non passa automaticamente allo stato completato a meno che non venga terminato in modo esplicito. Questa operazione può essere attivata automaticamente tramite la proprietà [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) o [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Esiste una quota predefinita [per la pianificazione di processi e](batch-quota-limit.md#resource-quotas)processi. I processi e le pianificazioni dei processi nello stato completato non vengono conteggiati per questa quota.

## <a name="tasks"></a>Attività

Le attività sono singole unità di lavoro che comprendono un processo. Le attività vengono inviate dall'utente e pianificate da batch nei nodi di calcolo. Quando si creano ed eseguono attività, è necessario tenere presenti diverse considerazioni di progettazione. Le sezioni seguenti illustrano scenari comuni e illustrano come progettare le attività per gestire i problemi ed eseguire in modo efficiente.

- **Salvare i dati dell'attività come parte dell'attività.**  
    I nodi di calcolo sono per natura temporanea. Sono disponibili molte funzionalità in batch, ad esempio il pool automatico e la scalabilità automatica, che semplificano la scomparsa dei nodi. Quando i nodi lasciano il pool (a causa di un ridimensionamento o di un'eliminazione del pool), vengono eliminati anche tutti i file in tali nodi. Per questo motivo, è consigliabile che prima del completamento di un'attività venga spostato l'output del nodo su cui è in esecuzione e in un archivio durevole, allo stesso modo se un'attività non riesce, è necessario spostare i log necessari per diagnosticare l'errore in un archivio durevole. Batch ha integrato il supporto di archiviazione di Azure per caricare i dati tramite [OutputFiles](batch-task-output-files.md), oltre a un'ampia gamma di file system condivisi, oppure è possibile eseguire il caricamento manualmente nelle attività.

### <a name="task-lifetime"></a>Durata dell'attività

- **Elimina le attività quando sono completate.**  
    Eliminare le attività quando non sono più necessarie o impostare un vincolo di attività [retentionTime](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) . Se viene impostata una `retentionTime`, batch pulisce automaticamente lo spazio su disco utilizzato dall'attività quando il `retentionTime` scade.  

    L'eliminazione delle attività esegue due operazioni. Si assicura che non si disponga di una compilazione di attività nel processo, rendendo l'esecuzione di query o la ricerca dell'attività a cui si è interessati, perché sarà necessario filtrare le attività completate. Consente inoltre di pulire i dati delle attività corrispondenti nel nodo (purché `retentionTime` non sia già stato raggiunto). Ciò garantisce che i nodi non riempiano i dati delle attività e esauriscono lo spazio su disco.

### <a name="task-submission"></a>Invio di attività

- **Inviare un numero elevato di attività in una raccolta.**  
    Le attività possono essere inviate in base a singole o raccolte. Invia le attività in [raccolte](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) fino a 100 alla volta durante l'invio in blocco delle attività per ridurre il sovraccarico e il tempo di invio.

### <a name="task-execution"></a>Esecuzione di attività

- **Scelta delle attività massime per nodo**  
    Batch supporta l'oversubscriptioning delle attività nei nodi (esecuzione di più attività rispetto a quelle di un nodo con Core). È compito dell'utente verificare che le attività siano adatte ai nodi del pool. È ad esempio possibile che si verifichi una riduzione delle prestazioni se si tenta di pianificare otto attività, ognuna delle quali utilizza il 25% di utilizzo della CPU su un nodo (in un pool con `maxTasksPerNode = 8`).

### <a name="designing-for-retries-and-re-execution"></a>Progettazione per tentativi e nuova esecuzione

Le attività possono essere ritentate automaticamente dal batch. Esistono due tipi di tentativi: controllati dall'utente e interni. I tentativi controllati dall'utente vengono specificati dal [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)dell'attività. Quando un programma specificato nell'attività termina con un codice di uscita diverso da zero, l'attività viene ritentata fino al valore della `maxTaskRetryCount`.

Sebbene sia raro, un'attività può essere ritentata internamente a causa di errori nel nodo di calcolo, ad esempio non è in grado di aggiornare lo stato interno o un errore nel nodo mentre l'attività è in esecuzione. L'attività verrà ritentata nello stesso nodo di calcolo, se possibile, fino a un limite interno prima di abbandonare l'attività e rinviare l'attività da ripianificare da batch, potenzialmente in un nodo di calcolo diverso.

- **Creazione di attività durevoli**  
    Le attività devono essere progettate per resistere agli errori e riprovare. Questa operazione è particolarmente importante per le attività a esecuzione prolungata. A tale scopo, assicurarsi che le attività generino lo stesso risultato, anche se vengono eseguite più di una volta. Un modo per ottenere questo risultato consiste nel fare in modo che le attività "cerchino". Un altro modo consiste nel verificare che le attività siano idempotente (le attività avranno lo stesso risultato indipendentemente dal numero di volte in cui vengono eseguite).

    Un esempio comune è un'attività per copiare i file in un nodo di calcolo. Un approccio semplice è costituito da un'attività che copia tutti i file specificati ogni volta che viene eseguita, che non è efficiente e non è stata compilata per resistere a un errore. Al contrario, creare un'attività per assicurarsi che i file si trovino nel nodo di calcolo; attività che non ricopia i file già presenti. In questo modo, l'attività riprenderà dal punto in cui è stata interrotta.

- **Nodi con priorità bassa**  
    Non vi sono differenze di progettazione durante l'esecuzione delle attività su nodi dedicati o con priorità bassa. Se un'attività viene annullata durante l'esecuzione in un nodo con priorità bassa o viene interrotta a causa di un errore in un nodo dedicato, entrambe le situazioni vengono mitigate progettando l'attività in modo da sopportare un errore.

- **Tempo di esecuzione dell'attività**  
    Evitare le attività con tempo di esecuzione breve. Le attività che vengono eseguite solo da uno a due secondi non sono ideali. È consigliabile provare a eseguire una quantità significativa di lavoro in una singola attività (almeno 10 secondi, fino a ore o giorni). Se ogni attività è in esecuzione per un minuto (o più), il sovraccarico di pianificazione come frazione del tempo di calcolo complessivo è ridotto.

## <a name="nodes"></a>Nodi

- **Le attività di avvio devono essere idempotente**  
    Analogamente ad altre attività, l'attività di avvio del nodo deve essere idempotente, in quanto verrà rieseguita ogni volta che il nodo viene avviato. Un'attività idempotente è semplicemente una che produce un risultato coerente quando viene eseguita più volte.

- **Gestire servizi con esecuzione prolungata tramite l'interfaccia dei servizi del sistema operativo.**  
    A volte è necessario eseguire un altro agente insieme all'agente batch nel nodo, ad esempio, per raccogliere i dati dal nodo e segnalarli. Si consiglia di distribuire questi agenti come servizi del sistema operativo, ad esempio un servizio Windows o un servizio `systemd` Linux.  

    Quando si eseguono questi servizi, non è necessario che blocchi di file in tutti i file nelle directory gestite da batch nel nodo, perché in caso contrario batch non sarà in grado di eliminare tali directory a causa dei blocchi di file. Ad esempio, se si installa un servizio Windows in un'attività di avvio, anziché avviare il servizio direttamente dalla directory di lavoro di avvio dell'attività, copiare i file in un'altra posizione (se i file esistono semplicemente ignorando la copia). Installare il servizio da tale percorso. Quando batch esegue nuovamente l'attività di avvio, eliminerà la directory di lavoro dell'attività di avvio e la creerà di nuovo. Questa operazione funziona perché il servizio ha blocchi di file nell'altra directory e non nella directory di lavoro dell'attività di avvio.

- **Evitare di creare giunzioni di directory in Windows**  
    Le giunzioni di directory, talvolta denominate collegamenti reali della directory, sono difficili da gestire durante la pulizia dei processi e delle attività. Usare i collegamenti simbolici (collegamenti flessibili) anziché i collegamenti reali.

- **Raccogliere i log dell'agente batch se si verifica un problema**  
    Se si nota un problema che interessa il comportamento di un nodo o di attività in esecuzione in un nodo, è consigliabile raccogliere i log dell'agente batch prima di deallocare i nodi in questione. I log dell'agente batch possono essere raccolti usando l'API di caricamento dei log del servizio batch. Questi log possono essere forniti come parte di un ticket di supporto a Microsoft e consentono di risolvere i problemi e la risoluzione dei problemi.

## <a name="security"></a>Sicurezza

### <a name="security-isolation"></a>Isolamento di sicurezza

Per quanto riguarda l'isolamento, se lo scenario richiede l'isolamento dei processi tra loro, è necessario isolare questi processi in pool separati. Un pool è il limite di isolamento di sicurezza in batch e, per impostazione predefinita, due pool non sono visibili o sono in grado di comunicare tra loro. Evitare di usare account batch distinti come mezzo di isolamento.
