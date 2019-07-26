---
title: Domande frequenti su Azure Migrate| Microsoft Docs
description: Risposte alle domande frequenti su Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: snehaa
ms.openlocfilehash: 0708502087ae6880d9559cf17f0ba9982b2ba040
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372480"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Domande frequenti su Azure Migrate

Questo articolo include le domande frequenti su Azure Migrate. Eventuali altre domande successive alla lettura di questo articolo possono essere pubblicate nel [forum di Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Generale

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Quali aree geografiche di Azure sono supportate da Azure Migrate?

Qui è possibile trovare l'elenco per [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e per [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Quali sono le differenze tra Azure Migrate e Azure Site Recover?

Azure Migrate offre un hub centralizzato per l'avvio, l'esecuzione e il monitoraggio dell'individuazione, della valutazione e della migrazione di computer e carichi di lavoro in Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) è una soluzione di ripristino di emergenza. Azure Migrate migrazione del server utilizza Azure Site Recovery nel back-end per abilitare scenari di migrazione per la migrazione in modalità Lift-and-Shift dei computer locali.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Appliance Azure Migrate (server VMware/fisici)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>In che modo la Azure Migrate Appliance si connette ad Azure?

La connessione può essere via Internet oppure è possibile usare ExpressRoute con peering pubblico/Microsoft.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Quali sono i requisiti di connettività di rete necessari per la valutazione e la migrazione di Azure Migrate server

Per gli URL e le porte necessari per la comunicazione di Azure Migrate con Azure, esaminare le matrici di supporto [VMware](migrate-support-matrix-vmware.md) e [Hyper-V](migrate-support-matrix-hyper-v.md) .

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>È possibile rafforzare la VM del dispositivo configurata con il modello?

È possibile aggiungere componenti aggiuntivi, ad esempio antivirus, nel modello, purché le regole di comunicazione e del firewall necessarie per l'appliance Azure Migrate rimangano invariate.   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Quali dati vengono raccolti da Azure Migrate Appliance?

[Qui](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)è possibile ottenere informazioni dettagliate sui dati raccolti da Azure migrate Appliance.

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Si verifica un impatto sulle prestazioni nell'ambiente VMware o Hyper-V analizzato?

Con la profilatura continua dei dati sulle prestazioni, il Azure Migrate i profili dell'appliance computer locali per misurare i dati sulle prestazioni della macchina virtuale. Ciò ha un effetto quasi zero sulle prestazioni degli host Hyper-V/ESXi, nonché sul server vCenter.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Dove vengono archiviati i dati raccolti e per quanto tempo?

I dati raccolti dal dispositivo Azure Migrate vengono archiviati nella località di Azure specificata durante la creazione del progetto di migrazione. I dati vengono archiviati in modo sicuro in una sottoscrizione Microsoft e vengono eliminati quando si elimina il progetto Azure Migrate.

Per la visualizzazione delle dipendenze, se si installano gli agenti nelle macchine virtuali, i dati raccolti dagli agenti di dipendenza vengono archiviati negli Stati Uniti, in un'area di lavoro di Log Analytics creata nella sottoscrizione di Azure. Tali dati vengono eliminati quando si elimina l'area di lavoro Log Analytics nella sottoscrizione. [Altre informazioni](concepts-dependency-visualization.md)

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>Qual è il volume di dati caricati dal dispositivo Azure Migrate durante la profilatura continua?

Il volume dei dati inviati a Azure Migrate varia in base a diversi parametri. Per fornire un numero indicativo, un progetto Azure Migrate con 10 computer (ognuno con un disco e una scheda di interfaccia di rete) Invia circa 50 MB al giorno. Si tratta di un valore approssimativo, che cambia in base al numero di punti dati per le schede di rete e i dischi (i dati inviati sono non lineari se il numero di macchine virtuali, nic o dischi aumenta).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>I dati sono crittografati a riposo e in transito?

Sì per entrambi. I metadati vengono inviati in modo sicuro al servizio Azure Migrate tramite Internet tramite HTTPS. I metadati vengono archiviati in una [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)e nell' [archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) in una sottoscrizione Microsoft e vengono crittografati a riposo.

I dati raccolti dagli agenti di dipendenza vengono inoltre crittografati in transito (Secure HTTPS) e archiviati in un'area di lavoro Log Analytics nella sottoscrizione utente. Viene anche crittografato a riposo.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>In che modo l'appliance Azure Migrate comunica con l'server vCenter e il servizio Azure Migrate?

L'appliance si connette al server vCenter (porta 443) usando le credenziali specificate durante la configurazione dell'appliance. Esegue query sul server vCenter usando VMware PowerCLI per raccogliere i metadati sulle macchine virtuali gestite da server vCenter. Raccoglie sia i dati di configurazione sulle VM (core, memoria, dischi, NIC e così via), sia la cronologia delle prestazioni di ogni macchina virtuale per l'ultimo mese. I metadati raccolti vengono quindi inviati a Azure Migrate server Assessment (via Internet tramite HTTPS) per la valutazione.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>È possibile connettere lo stesso dispositivo a più server vCenter?

Sì, è possibile usare una singola appliance Azure Migrate per individuare più server vCenter, ma non contemporaneamente. È necessario eseguire le individuazioni una dopo l'altra.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Le dimensioni del computer sono state modificate. È possibile rieseguire una valutazione?

L'appliance Azure Migrate raccoglie continuamente informazioni sull'ambiente locale. Tuttavia, una valutazione è uno snapshot temporizzato delle macchine virtuali locali. Se si modificano le impostazioni in una macchina virtuale che si desidera valutare, utilizzare l'opzione "ricalcola" per aggiornare la valutazione con le modifiche più recenti.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>Come è possibile individuare un ambiente multi-tenant in Azure Migrate valutazione del server?

Per VMware, se si dispone di un ambiente condiviso tra i tenant e non si vuole individuare le macchine virtuali di un tenant nella sottoscrizione di un altro tenant, creare server vCenter credenziali con accesso solo alle macchine virtuali che si desidera individuare. Usare quindi le credenziali per avviare l'individuazione nell'appliance Azure Migrate.

Per Hyper-V, l'individuazione usa le credenziali dell'host Hyper-V, se le VM condividono lo stesso host Hyper-V, attualmente non esiste alcun modo per separare l'individuazione.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Quante VM possono essere individuate con una singola appliance di migrazione?

È possibile individuare fino a 10.000 VM VMware e fino a 5.000 macchine virtuali Hyper-V usando un'unica appliance di migrazione.  Se si dispone di più computer nell'ambiente locale, informazioni su come ridimensionare [Hyper-V](scale-hyper-v-assessment.md) e [VMware](scale-vmware-assessment.md) assessment.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate: Valutazione del supporto server assessment dei server fisici

No, Azure Migrate attualmente non supporta la valutazione dei server fisici.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate richiede che vCenter Server individui un ambiente VMware?

Sì, Azure Migrate necessario server vCenter per individuare un ambiente VMware. Non supporta l'individuazione di host ESXi che non sono gestiti da server vCenter.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual è la differenza tra l'uso di Azure Migrate: Server assessment e MAP Toolkit?

Azure Migrate: Server Assessment fornisce la valutazione della migrazione per facilitare la preparazione alla migrazione e la valutazione dei carichi di lavoro per la migrazione in Azure. [Microsoft Assessment and Planning (Map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) presenta altre funzionalità, ad esempio la pianificazione della migrazione per le versioni più recenti dei sistemi operativi client e server Windows e il rilevamento dell'utilizzo del software. Per tali scenari, continuare a usare il MAP Toolkit.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Modalità di Azure Migrate: Valutazione del server diversa da Azure Site Recovery Deployment Planner?

Azure Migrate: Server assessment è uno strumento di pianificazione della migrazione. Azure Site Recovery Deployment Planner è uno strumento di pianificazione del ripristino di emergenza.

- **Migrazione da VMware/Hyper-V ad Azure**: Se si intende eseguire la migrazione dei server locali in Azure, usare la Azure Migrate: Strumento di valutazione server per la pianificazione della migrazione. Lo strumento valuta i carichi di lavoro locali e fornisce indicazioni, informazioni dettagliate e meccanismi per facilitare la migrazione ad Azure. Quando si è pronti per il piano di migrazione, è possibile usare strumenti come Azure Migrate: Migrazione del server, per eseguire la migrazione dei computer in Azure.
- **Ripristino di emergenza da VMware/Hyper-V in Azure**: Per il ripristino di emergenza in Azure con Site Recovery, usare il Deployment Planner Site Recovery per la pianificazione del ripristino di emergenza. Site Recovery Deployment Planner esegue una valutazione approfondita e specifica Site Recovery dell'ambiente locale. Fornisce consigli necessari Site Recovery per operazioni di emergenza, ad esempio la replica e il failover delle macchine virtuali.

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate supporta la stima dei costi basata sulla Enterprise Agreement (EA)?

Azure Migrate attualmente non supporta la stima dei costi per [Enterprise Agreement offerta](https://azure.microsoft.com/offers/enterprise-agreement-support/). La soluzione alternativa consiste nel specificare l'offerta con pagamento in base al consumo e specificare manualmente la percentuale di sconto (applicabile alla sottoscrizione) nel campo "Discount" delle proprietà di valutazione.

  ![Discount](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Qual è la differenza tra il dimensionamento locale e il dimensionamento basato sulle prestazioni?

- In come ridimensionamento locale, Azure Migrate non considera i dati sulle prestazioni della macchina virtuale. Ridimensiona le macchine virtuali in base alla configurazione locale. -In un dimensionamento basato sulle prestazioni, il dimensionamento è basato sui dati di utilizzo.
- Se, ad esempio, una macchina virtuale locale ha 4 core e 8 GB di memoria con un utilizzo della CPU del 50% e un utilizzo della memoria del 50%, come il dimensionamento locale consiglia uno SKU di VM di Azure con quattro core e 8 GB di memoria. Il dimensionamento basato sulle prestazioni, tuttavia, consiglia uno SKU di VM di due core e 4 GB, perché la percentuale di utilizzo è considerata.
- Analogamente, il ridimensionamento del disco dipende da due proprietà di valutazione: criterio di dimensionamento e tipo di archiviazione.
= Se il criterio di dimensionamento è basato sulle prestazioni e il tipo di archiviazione è automatico, i valori di IOPS e velocità effettiva del disco vengono considerati quando si identifica il tipo di disco di destinazione (standard o Premium).
- Se il criterio di dimensionamento è basato sulle prestazioni e il tipo di archiviazione è Premium, è consigliabile usare un disco Premium. Lo SKU del disco Premium è selezionato in base alle dimensioni del disco locale. La stessa logica viene usata per eseguire il dimensionamento del disco quando il criterio di dimensionamento è come il dimensionamento locale e il tipo di archiviazione è standard o Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Qual è l'impatto della cronologia delle prestazioni e dell'utilizzo percentile sulle dimensioni consigliate?

Queste proprietà sono applicabili solo per il dimensionamento basato sulle prestazioni.

- Azure Migrate raccoglie la cronologia delle prestazioni dei computer locali e la usa per consigliare le dimensioni della macchina virtuale e il tipo di disco in Azure.
- L'appliance continua a profilare l'ambiente locale per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi. L'appliance esegue il rollup dei campioni raccolti ogni 20 secondi e crea un singolo punto dati ogni 15 minuti. Per creare il singolo punto dati, l'appliance seleziona il valore di picco da tutti i campioni raccolti ogni 20 secondi e lo invia ad Azure.
- Quando si crea una valutazione in Azure, in base alla durata delle prestazioni e al valore percentile della cronologia delle prestazioni, Azure Migrate calcola il valore di utilizzo effettivo e lo usa per il ridimensionamento.
- Se, ad esempio, si imposta la durata delle prestazioni su un giorno e il valore percentile su 95 percentile, Azure Migrate usa i punti di campionamento di 15 minuti inviati dall'agente di raccolta per l'ultimo giorno, li ordina in ordine crescente e sceglie il valore del 95 ° percentile come utilizzo effettivo.
- Il valore del 95 ° percentile garantisce che vengano ignorati gli outlier, che possono verificarsi se si usa il 99 ° percentile. Per scegliere il picco nell'utilizzo per il periodo di tempo senza perdere gli outlier, è consigliabile selezionare il 99° percentile.

### <a name="what-is-dependency-visualization"></a>Informazioni sulla visualizzazione delle dipendenze

La visualizzazione delle dipendenze consente di valutare i gruppi di macchine virtuali per la migrazione con maggiore sicurezza. Controlla le dipendenze tra computer prima di eseguire una valutazione. La visualizzazione delle dipendenze contribuisce a garantire che non venga lasciato nulla ed evitare interruzioni impreviste quando si esegue la migrazione ad Azure. Azure Migrate sfrutta la soluzione Mapping dei servizi nei log di monitoraggio di Azure per abilitare la visualizzazione delle dipendenze.

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>È necessario pagare per usare la funzionalità di visualizzazione delle dipendenze?

No. Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>È necessario eseguire eventuali installazioni per usare la visualizzazione delle dipendenze?

Per usare la visualizzazione delle dipendenze è necessario scaricare e installare gli agenti in ogni computer locale da valutare.

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) deve essere installato in ogni computer.
- [Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) deve essere installato in ogni computer.
- Se vi sono computer senza accesso a Internet, è necessario scaricare e installare il gateway di Log Analytics.

Questi agenti non sono necessari a meno che non si usi la visualizzazione delle dipendenze.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>È possibile usare un'area di lavoro per la visualizzazione delle dipendenze?

Sì, è possibile alleghi un'area di lavoro esistente al progetto di migrazione e sfruttarla per la visualizzazione delle dipendenze. [Altre informazioni](concepts-dependency-visualization.md#how-does-it-work)

### <a name="can-i-export-the-dependency-visualization-report"></a>È possibile esportare il report di visualizzazione delle dipendenze?

No, la visualizzazione delle dipendenze non può essere esportata. Tuttavia, poiché Azure Migrate usa il Mapping dei servizi per la visualizzazione delle dipendenze, è possibile usare le [API REST di Mapping dei servizi](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) per acquisire le dipendenze in formato JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Come è possibile automatizzare l'installazione di Microsoft Monitoring Agent, MMA, e di Dependency Agent?

[Utilizzare questo script](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) per l'installazione degli agenti. [Seguire queste istruzioni](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) per installare MMA usando la riga di comando o l'automazione. Per MMA, sfruttare [questo script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Oltre agli script, è possibile utilizzare strumenti di distribuzione come System Center Configuration Manager, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) e così via per distribuire gli agenti.

### <a name="what-operating-systems-are-supported-by-mma"></a>Quali sistemi operativi sono supportati da MMA?

- [Esaminare](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) l'elenco dei sistemi operativi Windows supportati da MMA.
- [Esaminare](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) l'elenco dei sistemi operativi Linux supportati da MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Quali sono i sistemi operativi supportati da Dependency Agent?

[Esaminare](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) i sistemi operativi Windows supportati da Dependency Agent.
[Esaminare](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) l'elenco dei sistemi operativi Linux supportati da Dependency Agent.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>È possibile visualizzare le dipendenze in Azure Migrate per più di un'ora?
No, è possibile visualizzare le dipendenze per un massimo di un'ora. È possibile tornare a una data specifica nella cronologia, fino all'ultimo mese, ma la durata massima per la visualizzazione è di un'ora. Ad esempio, è possibile usare la durata dell'ora nella mappa delle dipendenze per visualizzare le dipendenze per ieri, ma solo per una finestra di un'ora. È tuttavia possibile usare i log di monitoraggio di Azure per [eseguire query sui dati](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) delle dipendenze per un periodo di tempo più lungo.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>La visualizzazione delle dipendenze è supportata per i gruppi con più di 10 macchine virtuali?
È possibile [visualizzare le dipendenze per i gruppi](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) con un massimo di 10 macchine virtuali. Se si dispone di un gruppo con più di 10 macchine virtuali, è consigliabile suddividere il gruppo in gruppi più piccoli, quindi visualizzare le dipendenze.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: Server Migration

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Modalità di Azure Migrate: La migrazione del server è diversa da Azure Site Recovery?

Azure Migrate: La migrazione del server utilizza il motore di replica di Site Recovery per la migrazione basata su agente di VM VMware, la migrazione di VM Hyper-V e la migrazione di server fisici in Azure. L'opzione senza agente per eseguire la migrazione di macchine virtuali VMware è compilata in modo nativo nella migrazione del server.

## <a name="next-steps"></a>Passaggi successivi
Leggere la [Panoramica di Azure Migrate](migrate-services-overview.md)
