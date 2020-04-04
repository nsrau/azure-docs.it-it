---
title: Procedure consigliate - Azure BatchBest practices - Azure Batch
description: Scopri le best practice e suggerimenti utili per sviluppare la tua soluzione Azure Batch.
author: LauraBrenner
ms.author: labrenne
ms.date: 04/03/2020
ms.service: batch
ms.topic: article
manager: evansma
ms.openlocfilehash: 94483f8e15b0cd90f76e369034e987bec6da127c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655839"
---
# <a name="azure-batch-best-practices"></a>Procedure consigliate per Azure BatchAzure Batch best practices

Questo articolo illustra una raccolta di procedure consigliate per usare il servizio Azure Batch in modo efficace ed efficiente. Queste best practice derivano dalla nostra esperienza con Batch e dalle esperienze dei clienti Batch. È importante comprendere questo articolo per evitare insidie di progettazione, potenziali problemi di prestazioni e anti-modelli durante lo sviluppo e l'utilizzo di Batch.It's important to understand this article to avoid design mifalls, potential performance issues, and anti-patterns while developing for, and using, Batch.

In questo articolo si apprenderà:

> [!div class="checklist"]
> - Quali sono le best practice
> - Perché utilizzare le procedure consigliate
> - Cosa potrebbe accadere se non si seguono le procedure consigliate
> - Come seguire le best practice

## <a name="pools"></a>Pool

I pool batch sono le risorse di calcolo per l'esecuzione di processi nel servizio Batch.Batch pools are the compute resources for executing jobs on the Batch service. Nelle sezioni seguenti vengono fornite indicazioni sulle procedure consigliate principali da seguire quando si utilizzano i pool batch.

### <a name="pool-configuration-and-naming"></a>Configurazione e denominazione del pool

- **Modalità di allocazione pool** Quando si crea un account Batch, è possibile scegliere tra due modalità di allocazione pool: **Servizio batch** o **Sottoscrizione utente**. Per la maggior parte dei casi, è consigliabile usare la modalità di servizio Batch predefinita, in cui i pool vengono allocati dietro le quinte nelle sottoscrizioni gestite da Batch.For most cases, you should use the default Batch service mode, in which pools are allocated behind behind in Batch-managed subscriptions. Nella modalità sottoscrizione utente alternativa, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione in fase di creazione di un pool. Gli account di sottoscrizione utente vengono utilizzati principalmente per abilitare un sottoinsieme importante, ma piccolo di scenari. Per ulteriori informazioni sulla modalità di sottoscrizione utente, vedere Configurazione aggiuntiva per la [modalità di sottoscrizione utente](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Considerare il tempo di esecuzione dei processi e delle attività quando si determina il mapping da processo a pool.**
    Se si dispone di processi costituiti principalmente da attività a esecuzione breve e i conteggi totali delle attività previsti sono ridotti, in modo che il tempo di esecuzione previsto complessivo del processo non sia lungo, non allocare un nuovo pool per ogni processo. Il tempo di allocazione dei nodi ridurrà il tempo di esecuzione del processo.

- **I pool devono avere più di un nodo di calcolo.**
    Non è garantito che i singoli nodi siano sempre disponibili. Anche se non comuni, errori hardware, aggiornamenti del sistema operativo e una serie di altri problemi possono causare singoli nodi di essere offline. Se il carico di lavoro Batch richiede un avanzamento deterministico e garantito, è necessario allocare pool con più nodi.

- **Non riutilizzare i nomi delle risorse.**
    Le risorse batch (processi, pool e così via) spesso vanno e vengono nel tempo. Ad esempio, è possibile creare un pool il lunedì, eliminarlo il martedì e quindi creare un altro pool il giovedì. A ogni nuova risorsa creata deve essere assegnato un nome univoco che non è stato usato in precedenza. Questa operazione può essere eseguita usando un GUID (come nome della risorsa o come parte di esso) o incorporando l'ora in cui la risorsa è stata creata nel nome della risorsa. Batch supporta [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), che può essere utilizzato per assegnare a una risorsa un nome leggibile anche se l'ID risorsa effettivo è qualcosa che non è tale da usare. L'uso di nomi univoci semplifica la differenziazione di quale risorsa specifica ha esito più semplice nei log e nelle metriche. Rimuove inoltre l'ambiguità se è necessario presentare un caso di supporto per una risorsa.

- **Continuità durante la manutenzione e l'errore del pool.**
    È meglio che i tuoi lavori utilizzino i pool in modo dinamico. Se i tuoi lavori utilizzano lo stesso pool per tutto, c'è la possibilità che i tuoi lavori non vengano eseguiti se qualcosa va storto con il pool. Ciò è particolarmente importante per i carichi di lavoro sensibili al tempo. Per risolvere questo problema, selezionare o creare un pool in modo dinamico quando si pianifica ogni processo oppure eseguire l'override del nome del pool in modo da poter ignorare un pool non integro.

- **Continuità aziendale durante la manutenzione e l'errore del pool** Esistono molte possibili cause che possono impedire l'aumento di un pool fino alle dimensioni richieste desiderate, ad esempio errori interni, vincoli di capacità e così via. Per questo motivo, è necessario essere pronti a ridestinare i processi in un pool diverso (possibilmente con una dimensione di macchina virtuale diversa: Batch supporta questa funzionalità tramite [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)) se necessario. Evitare di utilizzare un ID pool statico con l'aspettativa che non verrà mai eliminato e non verrà mai modificato.

### <a name="pool-lifetime-and-billing"></a>Durata e fatturazione del pool

La durata del pool può variare a seconda del metodo di allocazione e delle opzioni applicate alla configurazione del pool. I pool possono avere una durata arbitraria e un numero variabile di nodi di calcolo nel pool in qualsiasi momento. È responsabilità dell'utente gestire i nodi di calcolo nel pool in modo esplicito o tramite le funzionalità fornite dal servizio (scalabilità automatica o pool automatico).

- **Mantenere le piscine fresche.**
    È consigliabile ridimensionare i pool a zero ogni pochi mesi per assicurarsi di ottenere gli aggiornamenti più recenti dell'agente di nodo e correzioni di bug. Il pool non riceverà gli aggiornamenti dell'agente di nodi a meno che non venga ricreato o ridimensionato a 0 nodi di calcolo. Prima di ricreare o ridimensionare il pool, è consigliabile scaricare tutti i log dell'agente di nodo a scopo di debug, come descritto nella sezione [Nodi.](#nodes)

- **Ricreazione della piscina** Su una nota simile, non è consigliabile eliminare e ricreare i pool su base giornaliera. Creare invece un nuovo pool, aggiornare i processi esistenti in modo che puntino al nuovo pool. Dopo aver spostato tutte le attività nel nuovo pool, eliminare il pool precedente.

- **Efficienza e fatturazione del pool** Il batch stesso non comporta costi aggiuntivi, ma si incorre in costi aggiuntivi per le risorse di calcolo utilizzate. Viene fatturato per ogni nodo di calcolo nel pool, indipendentemente dallo stato in cui si trovi. Sono inclusi tutti i costi necessari per l'esecuzione del nodo, ad esempio i costi di archiviazione e di rete. Per altre informazioni sulle procedure consigliate, vedere [Analisi dei costi e budget per Azure Batch.](budget.md)

### <a name="pool-allocation-failures"></a>Errori di allocazione pool

Gli errori di allocazione del pool possono verificarsi in qualsiasi momento durante la prima allocazione o le ripartizioni successive. Ciò può essere dovuto all'esaurimento temporaneo della capacità in un'area o agli errori in altri servizi di Azure su cui Batch si basa. La quota principale non è una garanzia, ma piuttosto un limite.

### <a name="unplanned-downtime"></a>Tempo di inattività non pianificato

È possibile che i pool batch sperimentino eventi di tempo di inattività in Azure.It's possible for Batch pools to experience downtime events in Azure. Questo è importante da tenere presente quando si pianifica e si sviluppa lo scenario o il flusso di lavoro per Batch.This is important to keep in mind when planning and developing your scenario or workflow for Batch.

In caso di errore di un nodo, Batch tenta automaticamente di ripristinare questi nodi di calcolo per conto dell'utente. Ciò può attivare la ripianificazione di qualsiasi attività in esecuzione sul nodo che viene ripristinato. Per altre informazioni sulle attività interrotte, vedere [Progettazione di nuovi tentativi.](#designing-for-retries-and-re-execution)

- **Dipendenza dell'area di AzureAzure region dependency** Si consiglia di non dipendere da una singola area di Azure se si dispone di un carico di lavoro sensibile al tempo o di produzione. Anche se rari, ci sono problemi che possono interessare un'intera regione. Ad esempio, se l'elaborazione deve iniziare in un momento specifico, prendere in considerazione la scalabilità verticale del pool nell'area primaria *ben prima dell'ora di inizio.* Se la scalabilità del pool non riesce, è possibile eseguire il ripristino della scalabilità verticale di un pool in un'area di backup o in un'area. I pool in più account in aree diverse forniscono un backup pronto e facilmente accessibile in caso di problemi con un altro pool. Per ulteriori informazioni, vedere [Progettare l'applicazione per](high-availability-disaster-recovery.md)la disponibilità elevata .

## <a name="jobs"></a>Processi

Un processo è un contenitore progettato per contenere centinaia, migliaia o addirittura milioni di attività.

- **Inserire molte attività in un processo** L'utilizzo di un processo per eseguire una singola attività è inefficiente. Ad esempio, è più efficiente utilizzare un singolo processo contenente 1000 attività anziché creare 100 processi contenenti 10 attività ciascuna. L'esecuzione di 1000 processi, ognuno con una singola attività, sarebbe l'approccio meno efficiente, più lento e più costoso da adottare.

    Non progettare una soluzione Batch che richiede migliaia contemporaneamente di processi attivi. Non esiste una quota per le attività, pertanto l'esecuzione del maggior numero possibile di attività con il minor numero possibile di processi utilizza le quote di pianificazione dei [processi e dei processi.](batch-quota-limit.md#resource-quotas)

- **Durata del lavoro** Un processo Batch ha una durata indefinita fino a quando non viene eliminato dal sistema. Lo stato di un processo indica se può accettare o meno più attività per la programmazione. Un processo non passa automaticamente allo stato completato a meno che non venga terminato in modo esplicito. Questo può essere attivato automaticamente tramite la proprietà [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) o [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

È disponibile una quota di [pianificazione attiva predefinita.](batch-quota-limit.md#resource-quotas) I processi e le pianificazioni dei processi nello stato completato non vengono conteggiati per questa quota.

## <a name="tasks"></a>Attività

Le attività sono singole unità di lavoro che costituiscono un lavoro. Le attività vengono inviate dall'utente e pianificate da Batch per calcolare i nodi. Quando si creano ed eseguino attività, è necessario tenere presenti diverse considerazioni di progettazione. Nelle sezioni seguenti vengono illustrati gli scenari comuni e viene illustrato come progettare le attività per gestire i problemi ed eseguire in modo efficiente.

- **Salvare i dati dell'attività come parte dell'attività.**
    I nodi di calcolo sono per loro natura effimeri. In Batch sono disponibili molte funzionalità, ad esempio il pool automatico e la scalabilità automatica, che semplificano la scomparsa dei nodi. Quando i nodi lasciano il pool (a causa di un ridimensionamento o di un'eliminazione del pool) vengono eliminati anche tutti i file in tali nodi. Per questo motivo, è consigliabile che prima del completamento di un'attività, sposta l'output all'interno del nodo in cui è in esecuzione e in un archivio durevole, in modo analogo, in modo analogo, se un'attività non riesce, è consigliabile spostare i log necessari per diagnosticare l'errore in un archivio durevole. Batch ha integrato il supporto di Archiviazione di Azure per caricare i dati tramite [OutputFiles](batch-task-output-files.md), nonché una varietà di file system condivisi, oppure è possibile eseguire il caricamento manualmente nelle attività.

### <a name="task-lifetime"></a>Durata dell'attività

- **Eliminare le attività al termine.**
    Eliminare le attività quando non sono più necessarie o impostare un vincolo di attività [retentionTime.Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) tasks when they are no longer needed, or set a retentionTime task constraint. Se `retentionTime` è impostato un oggetto , Batch pulisce automaticamente `retentionTime` lo spazio su disco utilizzato dall'attività alla scadenza.

    L'eliminazione delle attività consente di eseguire due operazioni. Garantisce che non si dispone di un accumulo di attività nel processo, rendendo l'esecuzione di query/ trovare l'attività che si è interessati a più difficile (perché dovrete filtrare attraverso le attività completate). Pulisce inoltre i dati dell'attività corrispondenti nel nodo (a condizione che `retentionTime` non sia già stato raggiunto). In questo modo i nodi non riempiono di dati dell'attività e lo spazio su disco non si esaurisce.

### <a name="task-submission"></a>Invio di attività

- **Inviare un numero elevato di attività in una raccolta.**
    Le attività possono essere inviate su base individuale o in raccolte. Inviare attività in [raccolte](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) fino a 100 alla volta quando si esegue l'invio in blocco di attività per ridurre l'overhead e il tempo di invio.

### <a name="task-execution"></a>Esecuzione delle attività

- **Scelta del numero massimo di attività per nodoChoosing your max tasks per node** Batch supporta l'overscribing di attività sui nodi (l'esecuzione di più attività rispetto a un nodo ha core). Spetta a te assicurarti che le tue attività "si adattino" ai nodi del tuo pool. Ad esempio, è possibile avere un'esperienza degradata se si tenta di pianificare otto attività che `maxTasksPerNode = 8`utilizzano ciascuno il 25% dell'utilizzo della CPU in un nodo (in un pool con ).

### <a name="designing-for-retries-and-re-execution"></a>Progettazione per tentativi e riesecuzioneDesigning for retries and re-execution

Le attività possono essere ritentate automaticamente da Batch.Tasks can be automatically retried by Batch. Esistono due tipi di tentativi: controllato dall'utente e interno. I tentativi controllati dall'utente vengono specificati da [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)dell'attività. Quando un programma specificato nell'attività termina con un codice di uscita diverso da `maxTaskRetryCount`zero, l'attività viene ritentata fino al valore di .

Anche se rara, un'attività può essere ritentata internamente a causa di errori nel nodo di calcolo, ad esempio non essere in grado di aggiornare lo stato interno o un errore nel nodo mentre l'attività è in esecuzione. L'attività verrà ritentata nello stesso nodo di calcolo, se possibile, fino a un limite interno prima di rinunciare all'attività e rinviare la riprogrammazione dell'attività da parte di Batch, potenzialmente in un nodo di calcolo diverso.

- **Creare attività durevoli** Le attività devono essere progettate per resistere ai guasti e gestire i tentativi. Ciò è particolarmente importante per le attività a esecuzione prolungata. A tale scopo, assicurarsi che le attività generino lo stesso risultato singolo anche se vengono eseguite più di una volta. Un modo per raggiungere questo obiettivo è quello di rendere i vostri compiti "ricerca obiettivo". Un altro modo consiste nell'assicurarsi che le attività siano idempotenti (le attività avranno lo stesso risultato indipendentemente dal numero di volte in cui vengono eseguite).

    Un esempio comune è un'attività per copiare i file in un nodo di calcolo. Un approccio semplice è un'attività che copia tutti i file specificati ogni volta che viene eseguito, che è inefficiente e non è progettato per resistere all'errore. Creare invece un'attività per assicurarsi che i file si trovino nel nodo di calcolo. un'attività che non ricopia i file già presenti. In questo modo, l'attività riprende da dove era stata interrotta se è stata interrotta.

- **Nodi con priorità bassa** Non esistono differenze di progettazione durante l'esecuzione delle attività su nodi dedicati o con priorità bassa. Se un'attività viene interrotta durante l'esecuzione in un nodo con priorità bassa o interrotta a causa di un errore in un nodo dedicato, entrambe le situazioni vengono attenuate progettando l'attività per resistere agli errori.

- **Tempo di esecuzione dell'attività** Evitare le attività con tempi di esecuzione brevi. Le attività che vengono eseguite solo per uno o due secondi non sono ideali. Si dovrebbe provare a fare una quantità significativa di lavoro in un singolo compito (10 secondi minimo, fino a ore o giorni). Se ogni attività viene eseguita per un minuto (o più), l'overhead di pianificazione come frazione del tempo di calcolo complessivo è ridotto.

## <a name="nodes"></a>Nodi

- Le attività di **avvio devono essere idempotentiStart tasks should be idempotent** Analogamente ad altre attività, l'attività di avvio del nodo deve essere idempotente in quanto verrà rieseguita a ogni avvio del nodo. Un'attività idempotente è semplicemente un'attività che produce un risultato coerente quando viene eseguita più volte.

- **Gestire i servizi a esecuzione prolungata tramite l'interfaccia dei servizi del sistema operativo.**
    In alcuni stati è necessario eseguire un altro agente insieme all'agente Batch nel nodo, ad esempio per raccogliere dati dal nodo e segnalarlo. È consigliabile distribuire questi agenti come servizi del sistema operativo, ad esempio un servizio Windows o un servizio Linux.We recommend that these agents be deployed as OS services, such as a Windows service or a Linux `systemd` service.

    Quando si eseguono questi servizi, non devono accettare blocchi di file su qualsiasi file nelle directory gestite batch sul nodo, perché in caso contrario Batch non sarà in grado di eliminare tali directory a causa dei blocchi di file. Ad esempio, se si installa un servizio Windows in un'attività di avvio, anziché avviare il servizio direttamente dalla directory di lavoro dell'attività di avvio, copiare i file in un'altra posizione (se i file esistono, ignorare la copia). Installare il servizio da tale posizione. Quando Batch riesegue l'attività di avvio, eliminerà la directory di lavoro dell'attività di avvio e la creerà di nuovo. Questo funziona perché il servizio ha blocchi di file sull'altra directory e non la directory di lavoro dell'attività di avvio.

- **Evitare di creare nodi di directory in Windows** I nodi di directory, talvolta denominati collegamenti rigidi di directory, sono difficili da gestire durante la pulizia delle attività e dei processi. Utilizzare collegamenti simbolici (collegamenti soft) anziché collegamenti fissi.

- **Raccogliere i registri dell'agente Batch se si verifica un problemaCollect the Batch agent logs if there's an issue** Se si nota un problema relativo al comportamento di un nodo o alle attività in esecuzione su un nodo, è consigliabile raccogliere i registri dell'agente Batch prima di deallocare i nodi in questione. I log dell'agente Batch possono essere raccolti tramite l'API dei log del servizio Batch di caricamento. Questi registri possono essere forniti come parte di un ticket di supporto a Microsoft e consentono la risoluzione dei problemi e la risoluzione dei problemi.

## <a name="security"></a>Security

### <a name="security-isolation"></a>Isolamento di sicurezza

Ai fini dell'isolamento, se lo scenario richiede l'isolamento dei processi l'uno dall'altro, è necessario isolare questi processi facendoli in pool separati. Un pool è il limite di isolamento di sicurezza in Batch e, per impostazione predefinita, due pool non sono visibili o in grado di comunicare tra loro. Evitare di utilizzare account Batch separati come mezzo di isolamento.

## <a name="moving"></a>spostarsi

### <a name="move-batch-account-across-regions"></a>Spostare l'account Batch tra aree

Esistono vari scenari in cui si desidera spostare l'account Batch esistente da un'area all'altra. Ad esempio, è possibile passare a un'altra area come parte della pianificazione del ripristino di emergenza.

Gli account batch di Azure non possono essere spostati da un'area a un'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente dell'account Batch.You can however, use an Azure Resource Manager template to export the existing configuration of your Batch account.  È quindi possibile creare temporaneamente la risorsa in un'altra area esportando l'account Batch in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area. Dopo aver caricato il modello nella nuova area, sarà necessario ricreare certificati, pianificazioni dei processi e pacchetti di applicazioni. Per eseguire il commit delle modifiche e completare lo spostamento dell'account Batch, ricordarsi di eliminare l'account Batch o il gruppo di risorse originale.

Per altre informazioni su Resource Manager e sui modelli, vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure.For more information on Resource Manager and templates, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)

## <a name="connectivity-to-the-batch-service"></a>Connettività al servizio Batch

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Gruppi di sicurezza di rete (NSG) e route definite dall'utente (UDR)

Quando si esegue il provisioning di [pool Batch in una rete virtuale,](batch-virtual-network.md)assicurarsi di seguire attentamente le linee guida relative all'utilizzo del tag di `BatchNodeManagement` servizio, delle porte, dei protocolli e della direzione della regola.
L'utilizzo del tag del servizio è altamente consigliato e non gli indirizzi IP del servizio Batch sottostante, poiché questi possono cambiare nel tempo. L'uso diretto degli indirizzi IP del servizio Batch può manifestarsi come instabilità, interruzioni o interruzioni per i pool Batch, poiché il servizio Batch aggiorna gli indirizzi IP utilizzati nel tempo. Se si utilizzano attualmente gli indirizzi IP del servizio Batch nelle regole del gruppo di sicurezza di rete, si consiglia di passare all'utilizzo del tag del servizio.

Per le route definite dall'utente, assicurarsi di disporre di un processo per aggiornare periodicamente gli indirizzi IP del servizio Batch nella tabella di route man mano che questi cambiano nel tempo. Per informazioni su come ottenere l'elenco degli indirizzi IP del servizio Batch, vedere Tag del servizio [in locale](../virtual-network/service-tags-overview.md). Gli indirizzi IP del servizio `BatchNodeManagement` Batch verranno associati al tag del servizio (o alla variante regionale che corrisponde all'area dell'account Batch).

### <a name="honoring-dns"></a>Onorare DNS

Assicurarsi che i sistemi rispettino l'ora di live (TTL) DNS per l'URL del servizio account Batch. Inoltre, assicurarsi che i client del servizio Batch e altri meccanismi di connettività al servizio Batch non si basino sugli indirizzi IP.

Se le richieste ricevono risposte HTTP di livello 5xx ed è presente un'intestazione "Connessione: chiusura" nella risposta, il client del servizio Batch deve osservare la raccomandazione chiudendo la connessione esistente, ririsolvendo il DNS per l'URL del servizio account Batch e tentare di seguire le richieste su una nuova connessione.

### <a name="retrying-requests-automatically"></a>Riprovare automaticamente le richieste

Assicurarsi che i client del servizio Batch dispongano di criteri di ripetizione dei tentativi appropriati per ritentare automaticamente le richieste, anche durante il normale funzionamento e non esclusivamente durante eventuali periodi di manutenzione del servizio. Questi criteri di ripetizione dei tentativi devono estendersi su un intervallo di almeno 5 minuti. Le funzionalità di ripetizione automatica dei tentativi vengono fornite con vari SDK Batch, ad esempio la [classe RetryPolicyProvider .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet).

