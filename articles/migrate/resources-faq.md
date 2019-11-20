---
title: Domande frequenti su Azure Migrate
description: Risposte alle domande più comuni sul servizio Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: snehaa
ms.openlocfilehash: a622a5793e817be2445e60d925354ab37bb8e331
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185773"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: domande comuni

Questo articolo risponde alle domande più comuni su Azure Migrate. Se si hanno altre query dopo aver letto questo articolo, pubblicarle nel [forum Azure migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>General

### <a name="which-azure-geographies-are-supported"></a>Quali aree geografiche di Azure sono supportate?

Esaminare le aree geografiche supportate Azure Migrate per le macchine virtuali [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Qual è la differenza tra Azure Migrate e Site Recovery?

Azure Migrate offre un hub centralizzato per gestire e monitorare l'individuazione, la valutazione e la migrazione di computer e carichi di lavoro locali in Azure. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) è una soluzione di ripristino di emergenza. Azure Migrate: lo strumento di migrazione del server usa alcune funzionalità di Site Recovery back-end per la migrazione in modalità Lift-and-Shift di alcuni computer locali.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Ricerca per categorie eliminare un progetto di Azure Migrate?

Seguire [queste istruzioni](how-to-delete-project.md) per eliminare un progetto. [Esaminare le risorse](how-to-delete-project.md#created-resources) create durante l'individuazione, la valutazione e la migrazione di computer e carichi di lavoro in un progetto Azure migrate.


## <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

### <a name="how-does-the-appliance-connect-to-azure"></a>In che modo l'appliance si connette ad Azure?

La connessione può essere via Internet o usare Azure ExpressRoute con peering pubblico/Microsoft.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Quale connettività di rete è necessaria per la valutazione e la migrazione di Azure Migrate server?

Esaminare le matrici di supporto [VMware](migrate-support-matrix-vmware.md) e [Hyper-V](migrate-support-matrix-hyper-v.md) per informazioni sugli URL e sulle porte necessarie per la comunicazione di Azure migrate con Azure.

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>È possibile rafforzare la macchina virtuale dell'appliance creata con il modello?

È possibile aggiungere componenti (ad esempio, antivirus) al modello, purché si lascino le regole di comunicazione e del firewall necessarie per il Azure Migrate Appliance così come sono.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Quali dati vengono raccolti dal dispositivo Azure Migrate?

Esaminare i dati raccolti dal dispositivo come segue:
- [Dati sulle prestazioni](migrate-appliance.md#collected-performance-data-vmware) e [metadati](migrate-appliance.md#collected-metadata-vmware) per le macchine virtuali VMware.
- [Dati sulle prestazioni](migrate-appliance.md#collected-performance-data-hyper-v) e [metadati](migrate-appliance.md#collected-metadata-hyper-v) per le macchine virtuali Hyper-V.


### <a name="does-appliance-analysis-impact-performance"></a>L'analisi del dispositivo ha un effetto sulle prestazioni?

L'appliance Azure Migrate profilare i computer locali in modo continuo per misurare i dati sulle prestazioni della macchina virtuale. Questa profilatura non ha alcun effetto sulle prestazioni sugli host Hyper-V/ESXi o su server vCenter.

### <a name="where-and-how-long-is-collected-data-stored"></a>Dove e per quanto tempo vengono archiviati i dati raccolti?

I dati raccolti dal dispositivo Azure Migrate vengono archiviati nel percorso di Azure scelto durante la creazione del progetto di migrazione. I dati vengono archiviati in modo sicuro in una sottoscrizione Microsoft e vengono eliminati quando si elimina il progetto Azure Migrate.

Per la visualizzazione delle dipendenze, i dati raccolti vengono archiviati nella Stati Uniti, in un'area di lavoro Log Analytics creata nella sottoscrizione di Azure. Tali dati vengono eliminati quando si elimina l'area di lavoro Log Analytics nella sottoscrizione. [Altre](concepts-dependency-visualization.md) informazioni sulla visualizzazione delle dipendenze.

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>Quale volume di dati viene caricato durante la profilatura continua?

Il volume dei dati inviati a Azure Migrate varia a seconda dei diversi parametri. Per dare un'idea del volume, un progetto Azure Migrate con 10 computer (ognuno con un disco e una NIC) Invia circa 50 MB al giorno. Questo valore è approssimativo e cambia in base al numero di punti dati per le schede di rete e i dischi. L'aumento dei dati inviati è non lineare se aumenta il numero di macchine virtuali, nic o dischi.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>I dati sono crittografati inattivi e in transito?

Sì.
- I metadati vengono inviati in modo sicuro al servizio Azure Migrate tramite Internet tramite HTTPS.
- I metadati vengono archiviati in un database di [Azure Cosmos database](../cosmos-db/database-encryption-at-rest.md) e in un [Archivio BLOB di Azure](../storage/common/storage-service-encryption.md) in una sottoscrizione Microsoft. I metadati vengono crittografati a riposo.
- I dati per l'analisi delle dipendenze vengono crittografati anche in transito (Secure HTTPS). Viene archiviato in un'area di lavoro Log Analytics nella sottoscrizione. Viene anche crittografato a riposo.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>In che modo l'appliance comunica con server vCenter e Azure Migrate?

1. L'appliance si connette a server vCenter (porta 443), usando le credenziali specificate durante la configurazione dell'appliance.
2. L'appliance usa VMware PowerCLI per eseguire query server vCenter per raccogliere i metadati sulle macchine virtuali gestite da server vCenter. Raccoglie i dati di configurazione sulle VM (core, memoria, dischi, schede di rete) e la cronologia delle prestazioni di ogni macchina virtuale per l'ultimo mese.
3. I metadati raccolti vengono quindi inviati a Azure Migrate: server Assessment (via Internet tramite HTTPS) per la valutazione.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>È possibile connettere lo stesso dispositivo a più istanze di server vCenter?

No. Esiste un mapping uno-a-uno tra un appliance e server vCenter. Per individuare le macchine virtuali in più istanze di server vCenter, è necessario distribuire più appliance.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>Dimensioni del computer modificate. È possibile eseguire nuovamente la valutazione?

L'appliance Azure Migrate raccoglie continuamente informazioni sull'ambiente locale. Tuttavia, una valutazione è uno snapshot temporizzato delle macchine virtuali locali. Se si modificano le impostazioni in una macchina virtuale che si desidera valutare, utilizzare l'opzione Ricalcola per aggiornare la valutazione con le modifiche più recenti.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>Come è possibile eseguire l'individuazione in un ambiente multi-tenant?

- Per VMware, se l'ambiente è condiviso tra i tenant e non si vuole individuare le macchine virtuali di un tenant nella sottoscrizione di un altro tenant, creare server vCenter credenziali che possono accedere solo alle macchine virtuali che si desidera individuare. Quindi, usare queste credenziali quando si avvia l'individuazione nell'appliance Azure Migrate.
- Per Hyper-V, l'individuazione usa le credenziali dell'host Hyper-V. Se le VM condividono lo stesso host Hyper-V, attualmente non è possibile separare l'individuazione.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>Quante VM è possibile individuare con una singola appliance?

È possibile individuare fino a 10.000 VM VMware e fino a 5.000 macchine virtuali Hyper-V con una singola appliance di migrazione. Se si dispone di più computer nell'ambiente locale, informazioni su come ridimensionare [Hyper-V](scale-hyper-v-assessment.md) e [VMware](scale-vmware-assessment.md) assessment.

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>È possibile eliminare l'appliance Azure Migrate dal progetto?

L'eliminazione del dispositivo dal progetto non è attualmente supportata.

- L'unico modo per eliminare l'appliance consiste nell'eliminare il gruppo di risorse che contiene il Azure Migrate progetto associato all'appliance.
- Tuttavia, l'eliminazione del gruppo di risorse eliminerà anche altre appliance registrate, l'inventario individuato, le valutazioni e tutti gli altri componenti di Azure associati al progetto nel gruppo di risorse.

## <a name="azure-migrate-server-assessment"></a>Valutazione server di Azure Migrate


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>Azure Migrate necessario server vCenter per l'individuazione di macchine virtuali VMWare?

Sì, Azure Migrate necessario server vCenter per eseguire l'individuazione in un ambiente VMware. Non supporta l'individuazione di host ESXi che non sono gestiti da server vCenter.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual è la differenza tra Azure Migrate server assessment e MAP Toolkit?

Server Assessment offre una valutazione che aiuta la preparazione alla migrazione e la valutazione dei carichi di lavoro per la migrazione ad Azure. [Microsoft Assessment and Planning (Map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) consente di eseguire altre attività, tra cui la pianificazione della migrazione per le versioni più recenti dei sistemi operativi client/server Windows e il rilevamento dell'utilizzo del software. Per questi scenari, continuare a usare MAP Toolkit.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual è la differenza tra la valutazione del server e la Site Recovery Deployment Planner?

Server assessment è uno strumento di pianificazione della migrazione. Il Site Recovery Deployment Planner è uno strumento di pianificazione del ripristino di emergenza. 

- **Pianificare la migrazione locale ad Azure**: se si prevede di eseguire la migrazione dei server locali in Azure, usare la valutazione del server per la pianificazione della migrazione. Valuta i carichi di lavoro locali e fornisce indicazioni e strumenti per semplificare la migrazione. Dopo aver installato il piano di migrazione, è possibile usare gli strumenti, tra cui Azure Migrate migrazione del server, per eseguire la migrazione dei computer in Azure.
- **Pianificare il ripristino di emergenza in Azure**: se si prevede di configurare il ripristino di emergenza da locale ad azure con Site Recovery, usare il Deployment Planner Site Recovery. Il Deployment Planner fornisce una valutazione approfondita Site Recovery specifica dell'ambiente locale allo scopo del ripristino di emergenza. Fornisce consigli sul ripristino di emergenza, ad esempio la replica e il failover.

### <a name="does-azure-migrate-estimate-costs-for-the-enterprise-agreement-ea-program"></a>Azure Migrate stimare i costi per il programma di Enterprise Agreement (EA)?

Azure Migrate server Assessment attualmente non supporta le stime dei costi per il [programma di Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). Come soluzione alternativa, quando si crea una valutazione, è possibile specificare l' **offerta**con **pagamento in base** al consumo e aggiungere manualmente la percentuale di sconto (applicabile alla sottoscrizione) come **sconto** nelle proprietà di valutazione:

  ![Proprietà valutazione](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>Qual è la differenza tra il dimensionamento locale e quello basato sulle prestazioni?

Con il ridimensionamento locale, Azure Migrate non considera i dati sulle prestazioni della macchina virtuale per la valutazione. Valuta le dimensioni delle macchine virtuali in base alla configurazione locale. Con il dimensionamento basato sulle prestazioni, il dimensionamento è basato sui dati di utilizzo.

- Se, ad esempio, una macchina virtuale locale ha 4 core e 8 GB di memoria al 50% di utilizzo della CPU e il 50% di utilizzo della memoria, si verificheranno le condizioni seguenti:
    - Il dimensionamento locale consiglierà uno SKU di VM di Azure con quattro core e 8 GB di memoria.
    - Il dimensionamento basato sulle prestazioni consiglierà uno SKU di VM con due core e 4 GB di memoria, perché la percentuale di utilizzo è considerata.

- Analogamente, il ridimensionamento del disco dipende da due proprietà di valutazione: criteri di ridimensionamento e tipo di archiviazione.
    - Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è automatico, Azure Migrate prende in considerazione i valori di IOPS e velocità effettiva del disco quando identifica il tipo di disco di destinazione (standard o Premium).
    - Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è Premium, Azure Migrate consiglia uno SKU del disco Premium, in base alle dimensioni del disco locale. La stessa logica viene applicata al dimensionamento del disco, quando il dimensionamento è locale e il tipo di archiviazione è standard o Premium.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>La cronologia delle prestazioni e il percentile di utilizzo sono consigliati?

Queste proprietà sono applicabili solo per il dimensionamento basato sulle prestazioni.

- Azure Migrate raccoglie la cronologia delle prestazioni dei computer locali e la usa per consigliare le dimensioni della macchina virtuale e il tipo di disco in Azure.
- L'appliance continua a profilare l'ambiente locale, per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi.
- Il dispositivo esegue il rollup degli esempi di 20 secondi e crea un singolo punto dati ogni 15 minuti.
- Per creare il punto dati, l'appliance seleziona il valore massimo da tutti gli esempi di 20 secondi.
- Il dispositivo invia questo punto dati ad Azure.

Quando si crea una valutazione in Azure, in base alla durata delle prestazioni e al valore percentile cronologia prestazioni, Azure Migrate calcola il valore di utilizzo effettivo e lo usa per il ridimensionamento.

- Se, ad esempio, si imposta la durata delle prestazioni su un giorno e il valore percentile su 95 percentile, Azure Migrate Ordina i punti di campionamento di 15 minuti inviati dall'agente di raccolta per il giorno precedente in ordine crescente e sceglie il valore 95 ° percentile come valido utilizzo.
- L'utilizzo del valore 95 ° percentile garantisce che gli outlier vengano ignorati. Gli outlier possono essere inclusi se si usa il 99 ° percentile. Se si desidera selezionare l'utilizzo di picco per il periodo, senza eventuali outlier, selezionare il 99 ° percentile.

## <a name="server-assessment---dependency-visualization"></a>Valutazione del server-visualizzazione delle dipendenze


### <a name="what-is-dependency-visualization"></a>Informazioni sulla visualizzazione delle dipendenze

Usare la visualizzazione delle dipendenze per valutare i gruppi di macchine virtuali per la migrazione con maggiore sicurezza. La visualizzazione delle dipendenze controlla le dipendenze tra computer prima di eseguire una valutazione. Consente di garantire che non venga lasciato nulla e di conseguenza consente di evitare interruzioni impreviste quando si esegue la migrazione ad Azure. Azure Migrate usa la soluzione Mapping dei servizi in Monitoraggio di Azure per abilitare la visualizzazione delle dipendenze. [altre informazioni](concepts-dependency-visualization.md).

> [!NOTE]
> La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>È necessario pagare per usare la visualizzazione delle dipendenze?
No. Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>È necessario eseguire eventuali installazioni per usare la visualizzazione delle dipendenze?

Per usare la visualizzazione delle dipendenze è necessario scaricare e installare gli agenti in ogni computer locale da valutare.

È necessario installare gli agenti seguenti in ogni computer:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Se sono presenti computer senza connettività Internet, è necessario scaricare e installare Log Analytics gateway.

Questi agenti non sono necessari a meno che non si usi la visualizzazione delle dipendenze.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>È possibile usare un'area di lavoro per la visualizzazione delle dipendenze?

Sì, è possibile aggiungere un'area di lavoro esistente al progetto di migrazione e usarla per la visualizzazione delle dipendenze. [Altre informazioni](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>È possibile esportare il report di visualizzazione delle dipendenze?

No, la visualizzazione delle dipendenze non può essere esportata. Tuttavia, Azure Migrate USA Mapping dei servizi ed è possibile usare l' [API REST mapping dei servizi](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) per recuperare le dipendenze in formato JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Come è possibile automatizzare l'installazione di Microsoft Monitoring Agent (MMA) e Dependency Agent?

Usare questo [script per installare Dependency Agent](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Seguire queste [istruzioni per installare MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) usando la riga di comando o l'automazione. Per MMA, usare [questo script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Oltre agli script, è inoltre possibile utilizzare strumenti di distribuzione come System Center Configuration Manager e [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) per distribuire gli agenti.


### <a name="what-operating-systems-are-supported-by-mma"></a>Quali sistemi operativi sono supportati da MMA?

- Visualizzare l'elenco dei [sistemi operativi Windows supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Visualizzare l'elenco dei [sistemi operativi Linux supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>È possibile visualizzare le dipendenze per più di un'ora?
No. È possibile visualizzare le dipendenze per un massimo di un'ora. È possibile tornare a una data specifica nella cronologia, fino a un mese, ma la durata massima per la visualizzazione è di un'ora. Ad esempio, è possibile usare la durata dell'ora nella mappa delle dipendenze per visualizzare le dipendenze per ieri, ma è possibile visualizzare le dipendenze solo per una finestra di un'ora. È tuttavia possibile usare i log di monitoraggio di Azure per [eseguire query sui dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per un periodo di tempo più lungo.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>È possibile usare la visualizzazione delle dipendenze per gruppi di più di 10 VM?
È possibile [visualizzare le dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) per i gruppi contenenti fino a 10 macchine virtuali. Se si dispone di un gruppo con più di 10 macchine virtuali, è consigliabile suddividere il gruppo in gruppi più piccoli, quindi visualizzare le dipendenze.

## <a name="azure-migrate-server-migration"></a>Migrazione server di Azure Migrate

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Qual è la differenza tra Azure Migrate migrazione del server e Site Recovery?

- La migrazione senza agenti di macchine virtuali VMware locali è nativa in Azure Migrate migrazione del server.
- Per la migrazione di VM Hyper-V, server fisici e macchine virtuali VMware basate su agenti, Azure Migrate migrazione del server usa il motore di replica di Azure Site Recovery.


## <a name="next-steps"></a>Passaggi successivi
Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).
