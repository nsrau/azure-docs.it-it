---
title: Domande frequenti su Azure Migrate| Microsoft Docs
description: Risposte alle domande frequenti su Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 08a0312f12b3daab8b7f5e88da118b5bcbeb2f4c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807334"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Domande frequenti su Azure Migrate

Questo articolo include le domande frequenti su Azure Migrate. Eventuali altre domande successive alla lettura di questo articolo possono essere pubblicate nel [forum di Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Generale

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Quali aree geografiche di Azure sono supportate da Azure Migrate?
Azure Migrate supporta attualmente un numero di aree geografiche in cui è possibile creare un progetto Azure Migrate. Anche se è possibile creare solo i progetti in queste aree geografiche, è comunque possibile valutare le macchine per gli altri percorsi di destinazione. L'area geografica del progetto viene usata solo per archiviare i metadati individuati.

**Geografia** | **il percorso di archiviazione di metadati** Azure per enti pubblici | US Gov Virginia Asia | Asia orientale Europa o Asia sud-orientale | Europa occidentale Regno Unito o Europa meridionale | Regno Unito meridionale e Regno Unito occidentale United States | Stati Uniti centrali o Stati Uniti occidentali 2

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Quali sono le differenze tra Azure Migrate e Azure Site Recover?

Azure Migrate fornisce strumenti che consentono di individuare, valutare e la migrazione di carichi di lavoro e le macchine in Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) è una soluzione di ripristino di emergenza. Entrambi i servizi condividono alcuni componenti.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Appliance Azure Migrate (server VMware/fisici)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Modo in cui il dispositivo di Azure Migrate connettersi ad Azure?

La connessione può rimanere su internet oppure è possibile usare ExpressRoute con peering pubblico.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Quali requisiti di connettività di rete sono necessari per eseguire la migrazione di valutazione di Server di Azure e la migrazione

Per l'URL e le porte necessarie per eseguire la migrazione di Azure comunicare con Azure, vedere la [VMWare](migrate-support-matrix-vmware.md) e [Hyper-V](migrate-support-matrix-hyper-v.md) supporta matrici.

### <a name="can-i-harden-the-appliance-vmware-vm-i-set-up-with-the-ova-template"></a>È possibile rafforzare la protezione di VM VMware configurare con il modello con estensione OVA appliance?

È possibile aggiungere i componenti aggiuntivi (ad esempio applicazioni antivirus) nel modello con estensione OVA, purché sia la comunicazione e regole del firewall necessarie per l'appliance re left come eseguire la migrazione di Azure.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Per aumentare l'appliance Azure Migrate, quali sono le esclusioni Antivirus (AV) consigliate?

È necessario escludere le cartelle seguenti dall'analisi nell'appliance:

- Cartella contenente i file binari per il servizio Azure Migrate. Escludere tutte le sottocartelle.
- %ProgramFiles%\ProfilerService  
- Applicazione Web Azure Migrate. Escludere tutte le sottocartelle.
- %SystemDrive%\inetpub\wwwroot
- La cache locale per i file di database e log. Il servizio Azure Migrate deve accesso in lettura/scrittura a questa cartella.
  - %SystemDrive%\Profiler

### <a name="what-data-is-collected-by-azure-migrate"></a>Quali dati vengono raccolti da Azure Migrate?

L'appliance Azure Migrate raccoglie i metadati per le macchine virtuali in locale, tra cui:

**Dati di configurazione della macchina virtuale**
- Nome visualizzato della macchina virtuale (in vCenter)
- Percorso dell'inventario della macchina virtuale (host/cluster/cartella in vCenter)
- Indirizzo IP
- Indirizzo MAC
- Sistema operativo
- Numero di core, dischi, schede di interfaccia di rete
- Dimensione della memoria, dimensioni dei dischi

**Dati sulle prestazioni della macchina virtuale**
- Utilizzo CPU
- Utilizzo memoria
- Per ogni disco collegato alla macchina virtuale:
  - Velocità effettiva lettura da disco
  - Velocità effettiva di scrittura su disco
  - Operazioni di lettura da disco al secondo
  - Operazioni di scrittura su disco al secondo
- Per ogni scheda di rete collegata alla macchina virtuale:
  - Rete in ingresso
  - Rete in uscita

Inoltre tn, se si distribuisce il mapping delle dipendenze, gli agenti di mapping delle dipendenze raccolgono informazioni che include computer FQDN, sistema operativo, indirizzo IP, indirizzo MAC, processi in esecuzione la macchina virtuale e le connessioni TCP in ingresso/uscita per la macchina virtuale. Questa individuazione è facoltativa usato solo se si abilita il mapping delle dipendenze per l'individuazione.

### <a name="is-there-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>È previsto alcun impatto sulle prestazioni nell'ambiente host ESXi analizzato?

Con l'analisi continue dei dati sulle prestazioni, i profili di dispositivo di Azure Migrate ai computer locali per misurare i dati sulle prestazioni della macchina virtuale. Ciò ha quasi alcun impatto sulle prestazioni negli host ESXi, oltre che nel Server vCenter.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Dove vengono archiviati i dati raccolti e per quanto tempo?

I dati raccolti dall'appliance Azure Migrate sono archiviati nella posizione di Azure specificati durante la creazione del progetto di migrazione. I dati vengono archiviati in modo sicuro in una sottoscrizione di Microsoft e viene eliminati quando si elimina il progetto Azure Migrate.

Per visualizzare le dipendenze, se si installano gli agenti nelle macchine virtuali, i dati raccolti dagli agenti di dipendenza vengono archiviati negli Stati Uniti, in un'area di lavoro di Log Analitica creato nella sottoscrizione di Azure. Tali dati vengono eliminati quando si elimina l'area di lavoro Log Analytics nella sottoscrizione. [Altre informazioni](concepts-dependency-visualization.md)

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-during-continuous-profiling"></a>Che cos'è il volume di dati caricati da Azure Migrate durante l'analisi continue?

Il volume dei dati inviati ad Azure Migrate varia in base a diversi parametri. Per assegnare un numero indicativo, un progetto Azure Migrate con 10 macchine (ognuno con un disco e una scheda di rete), invia circa 50 MB al giorno. Si tratta di un valore approssimativo, che cambia in base al numero di punti dati per le schede di rete e i dischi (i dati inviati non sono lineare se aumenta il numero di macchine, schede di rete o dischi).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Sia i dati crittografati inattivi e in transito?

Sì per entrambi. I metadati viene inviato in modo sicuro al servizio Azure Migrate in internet, tramite https. I metadati vengono archiviati un [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)e nella [archivio blob di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) in una sottoscrizione di Microsoft, e crittografia dei dati inattivi.

I dati raccolti dagli agenti di dipendenza sono anche crittografati in transito (HTTPS sicuro) e viene archiviati in un'area di lavoro di Log Analitica nella sottoscrizione utente. Si tratta inoltre di crittografia dei dati inattivi.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Come l'appliance Azure Migrate comunicazione con il Server vCenter e il servizio Azure Migrate?

L'appliance si connette al server vCenter Server (porta 443) utilizzando le credenziali fornite quando si configura l'appliance. Esegue una query al Server mediante VMware PowerCLI, per raccogliere i metadati relativi a macchine virtuali gestite da vCenter Server vCenter. Raccoglie sia i dati di configurazione informazioni sulle macchine virtuali (Core, memoria, dischi, interfaccia di rete e così via), nonché la cronologia delle prestazioni di ogni macchina virtuale per l'ultimo mese. I metadati raccolti viene quindi inviato per la valutazione Server eseguire la migrazione di Azure (su internet tramite HTTPS) per la valutazione. 

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>È possibile connettersi nella stessa applicazione a più server vCenter?

Sì, una singola appliance Azure Migrate può essere usata per individuare più vCenter server, ma non contemporaneamente. È necessario eseguire le individuazioni una dopo l'altra.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Il modello OVA usato da Site Recovery è integrato con il modello OVA usato da Azure Migrate?

Attualmente non è presente alcuna integrazione. Il modello .OVA in Site Recovery viene usato per configurare un server di configurazione di Site Recovery per la replica di server fisici/macchine Virtuali VMware. Le. Con estensione OVA usato da Azure Migrate consente di individuare le macchine virtuali VMware gestite da un server vCenter, ai fini di valutazione e la migrazione.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Le dimensioni del computer sono state modificate. È possibile eseguire nuovamente una valutazione?
L'appliance Azure Migrate raccoglie costantemente informazioni sull'ambiente locale. Tuttavia, una valutazione è uno snapshot di point-in-time di macchine virtuali locali. Se si modificano le impostazioni in una macchina virtuale si desidera valutare, usare l'opzione 'Ricalcola' per la valutazione degli aggiornamenti con le ultime modifiche.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Come è possibile individuare un ambiente multi-tenant in Azure Migrate?

Per VMware, se si dispone di un ambiente condiviso tra i tenant e non si desidera individuare le macchine virtuali di un tenant nella sottoscrizione di un altro tenant, creare vCenter Server credenziali con accesso solo a tali macchine virtuali che si desiderano individuare. Usare quindi le credenziali quando avviare un processo di individuazione nell'appliance Azure Migrate.

Per Hyper-V, l'individuazione Usa credenziali dell'host Hyper-V, se le macchine virtuali condividono stesso host Hyper-V, non è attualmente possibile separare l'individuazione.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Numero di macchine virtuali può essere individuato tramite un'appliance sola migrazione?

È possibile individuare fino a 10.000 macchine virtuali VMware e fino a 5.000 macchine virtuali Hyper-V Usa un'appliance sola migrazione.  Se si dispone di più computer nell'ambiente locale, informazioni su come ridimensionare [Hyper-V](scale-hyper-v-assessment.md) e [VMware](scale-vmware-assessment.md) assessment.


## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Valutazione dei server

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate: Valutazione server supporta la valutazione di server fisici?

No, Azure Migrate attualmente non supporta la valutazione di server fisici. 

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate richiede che vCenter Server individui un ambiente VMware?

Sì, Azure Migrate deve Server vCenter per individuare un ambiente VMware. Non supporta il rilevamento dell'host ESXi non sono gestiti dal Server vCenter.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual è la differenza tra l'uso di Azure Migrate: Valutazione dei server e lo strumento Map Toolkit?

Azure Migrate: Server Assessment fornisce la valutazione della migrazione per agevolare la preparazione della migrazione e valutazione dei carichi di lavoro per la migrazione ad Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) ha altre funzionalità, come nelle versioni più recenti di sistemi operativi client e server Windows e monitoraggio dell'utilizzo di software di pianificazione della migrazione. Per tali scenari, continuare a usare il MAP Toolkit.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>È come eseguire la migrazione di Azure: Valutazione di server diversa da Azure Site Recovery Deployment Planner?

Azure Migrate: Server Assessment è una migrazione strumento di pianificazione. Azure Site Recovery Deployment Planner è un strumento di pianificazione del ripristino di emergenza.

- **Migrazione da VMware/Hyper-V ad Azure**: Se si prevede di eseguire la migrazione dei server locali in Azure, usare la migrazione di Azure: Strumento di valutazione di server per la pianificazione della migrazione. Lo strumento consente di valutare i carichi di lavoro in locale e fornisce meccanismi per facilitare la migrazione ad Azure, informazioni dettagliate e istruzioni. Quando si è pronti con il piano di migrazione, è possibile usare strumenti come Azure Migrate: Migrazione del server, eseguire la migrazione di macchine in Azure.
- **Ripristino di emergenza da VMware/Hyper-V in Azure**: Per il ripristino di emergenza in Azure usando Site Recovery, usare Site Recovery Deployment Planner per la pianificazione del ripristino di emergenza. Site Recovery Deployment Planner esegue una valutazione approfondita, Site Recovery specifici dell'ambiente locale. Fornisce consigli necessari da Site Recovery per le operazioni di emergenza ha esito positivo, ad esempio, replica e il failover delle macchine virtuali. 

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate supporta la stima dei costi in base al contratto Enterprise Agreement?

Azure Migrate attualmente non supporta la stima dei costi per [offerta Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). La soluzione consiste nel specificare l'offerta con pagamento a consumo e specificare manualmente la percentuale di sconto (applicabile alla sottoscrizione) nel campo 'Discount' della proprietà della valutazione.

  ![Discount](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Che cos'è la differenza tra il ridimensionamento come in locale e basato sulle prestazioni?

- In locale il ridimensionamento, Azure Migrate non considera i dati sulle prestazioni della macchina virtuale. Ridimensiona le macchine virtuali in base alla configurazione in locale. -In di dimensionamento basato sulle prestazioni, dimensionamento è basato sui dati di utilizzo.
- Ad esempio, se una macchina virtuale da sito locale ha 4 core e 8 GB di memoria con il 50% della CPU e utilizzo memoria del 50%, come in locale il ridimensionamento consiglia uno SKU di VM di Azure con 4 core e 8GB di memoria. Basato sulle prestazioni di ridimensionamento, tuttavia, consiglia una SKU di VM 2 core e 4 GB, poiché viene considerata come la percentuale di utilizzo.
- Analogamente, il ridimensionamento del disco dipende da due proprietà di valutazione - tipo di criterio e l'archiviazione di ridimensionamento.
= Se il criterio di dimensionamento è basato sulle prestazioni e tipo di archiviazione è automatico, i valori IOPS e velocità effettiva del disco sono considerati quando si identificano il tipo di disco di destinazione (Standard o Premium).
- Se il criterio di dimensionamento è basato sulle prestazioni e il tipo di archiviazione premium, è consigliabile un disco premium. Il disco premium che SKU è selezionato in base alla dimensione del disco locale. La stessa logica viene utilizzata per il ridimensionamento del disco quando il criterio di dimensionamento è come in locale e il tipo di archiviazione è standard o premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Qual è l'impatto della cronologia delle prestazioni e dell'utilizzo percentile sulle dimensioni consigliate?

Queste proprietà sono applicabili solo per il dimensionamento basato sulle prestazioni.

- Azure Migrate raccoglie la cronologia delle prestazioni dei computer locali e lo usa per consigliare il tipo di dimensione e il disco di macchina virtuale in Azure.
- L'appliance in modo continuo profila l'ambiente locale per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi. L'appliance esegue il rollup dei campioni raccolti ogni 20 secondi e crea un singolo punto dati ogni 15 minuti. Per creare il singolo punto dati, l'appliance seleziona il valore di picco da tutti i campioni raccolti ogni 20 secondi e lo invia ad Azure.
- Quando si crea una valutazione in Azure (basato sulla durata delle prestazioni e valore percentile della cronologia delle prestazioni), Azure Migrate calcola il valore utilizzo efficace e lo usa per il ridimensionamento.
- Ad esempio, se si imposta la durata delle prestazioni per un giorno e il valore percentile di 95 ° percentile, Azure Migrate Usa i punti di esempio relativo ai minuti 15 inviati dall'agente di raccolta per l'ultimo giorno, li ordina in ordine crescente e sceglie il valore di 95 ° percentile come il utilizzo efficace.
- Il valore di 95 ° percentile assicura che ignorati eventuali outlier che potrebbero verificarsi se si usa il 99 ° percentile. Per scegliere il picco nell'utilizzo per il periodo di tempo senza perdere gli outlier, è consigliabile selezionare il 99° percentile.

### <a name="what-is-dependency-visualization"></a>Informazioni sulla visualizzazione delle dipendenze

Visualizzazione delle dipendenze consente di valutare i gruppi di macchine virtuali per la migrazione con maggiore sicurezza. Cross-controlli le dipendenze dei computer prima di eseguire una valutazione. Visualizzazione delle dipendenze consente di garantire che venga tralasciato nulla ed evitare interruzioni impreviste quando esegue la migrazione ad Azure. Azure Migrate Usa la soluzione mapping dei servizi nei registri di monitoraggio di Azure, per consentire la visualizzazione delle dipendenze.

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>È necessario pagare per usare la funzionalità di visualizzazione delle dipendenze?

No. Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>È necessario eseguire eventuali installazioni per usare la visualizzazione delle dipendenze?

Per usare la visualizzazione delle dipendenze è necessario scaricare e installare gli agenti in ogni computer locale da valutare.

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) deve essere installato in ogni computer.
- [Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) deve essere installato in ogni computer.
- Se vi sono computer senza accesso a Internet, è necessario scaricare e installare il gateway di Log Analytics.

Questi agenti non è necessario solo se si usa la visualizzazione delle dipendenze.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>È possibile usare un'area di lavoro per la visualizzazione delle dipendenze?

Sì, è possibile collegare un'area di lavoro esistente al progetto di migrazione e usarlo per visualizzare le dipendenze. [Altre informazioni](concepts-dependency-visualization.md#how-does-it-work)

### <a name="can-i-export-the-dependency-visualization-report"></a>È possibile esportare il report di visualizzazione delle dipendenze?

No, non è possibile esportare la visualizzazione delle dipendenze. Tuttavia, poiché Azure Migrate usa il Mapping dei servizi per la visualizzazione delle dipendenze, è possibile usare le [API REST di Mapping dei servizi](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) per acquisire le dipendenze in formato JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Come è possibile automatizzare l'installazione di Microsoft Monitoring Agent, MMA, e di Dependency Agent?

[Usare questo script](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) per l'installazione degli agenti. [Seguire queste istruzioni](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) installare MMA usando la riga di comando o l'automazione. Per MMA, sfruttare [questo script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Oltre a script, è possibile usare gli strumenti di distribuzione come System Center Configuration Manager [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) e così via per distribuire gli agenti.

### <a name="what-operating-systems-are-supported-by-mma"></a>Quali sistemi operativi sono supportati da MMA?

- [Revisione](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) l'elenco dei sistemi operativi Windows supportati da MMA.
- [Riesame] https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) l'elenco dei sistemi operativi Linux supportati da MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Quali sono i sistemi operativi supportati da Dependency agent?

[Revisione](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) i sistemi operativi Windows supportati da Dependency agent.
[Revisione](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) l'elenco dei sistemi operativi Linux supportati da Dependency agent.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>È possibile visualizzare le dipendenze in Azure Migrate per più di un'ora?
No, è possibile visualizzare le dipendenze per al massimo un'ora. È possibile tornare a una determinata data la cronologia, con l'ultimo mese, ma la durata massima per la visualizzazione è un'ora. Ad esempio, è possibile usare l'intervallo di tempo nella mappa delle dipendenze per visualizzare le dipendenze di ieri, ma possono solo visualizzarlo per un intervallo di un'ora. Tuttavia, è possibile usare i log di monitoraggio di Azure per [eseguire query sui dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) su periodi di tempo.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-ten-vms"></a>Visualizzazione delle dipendenze è supportata per i gruppi con più di dieci macchine virtuali?
È possibile [visualizzare le dipendenze per i gruppi](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) con fino a dieci macchine virtuali. Se si dispone di un gruppo con più di dieci macchine virtuali, è consigliabile dividere il gruppo di nel gruppi più piccoli e quindi visualizzare le dipendenze.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: Migrazione del server

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>È come eseguire la migrazione di Azure: Migrazione del server diversa da Azure Site Recovery?

Azure Migrate: Migrazione del server si avvale di motore di replica di Site Recovery per la migrazione basata su agente di server in Azure.
## <a name="next-steps"></a>Passaggi successivi
Leggere la [Panoramica di Azure Migrate](migrate-services-overview.md)
