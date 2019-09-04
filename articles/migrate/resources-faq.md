---
title: Domande frequenti su Azure Migrate | Microsoft Docs
description: Risposte alle domande frequenti su Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: 46c6ac52e1afb6c1619b814580a1059fd3dfedda
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279491"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate: Domande frequenti

Questo articolo fornisce le risposte alle domande frequenti su Azure Migrate. Se si hanno altre query dopo aver letto questo articolo, pubblicarle nel [forum Azure migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Generale

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Quali aree geografiche di Azure supporta Azure Migrate?

Vedere l' [elenco per VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e l' [elenco per Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Qual è la differenza tra Azure Migrate e Azure Site Recovery?

Azure Migrate offre un hub centralizzato per avviare la migrazione, eseguire e monitorare l'individuazione e la valutazione di computer e carichi di lavoro e per eseguire e tenere traccia della migrazione di computer e carichi di lavoro in Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) è una soluzione di ripristino di emergenza. Azure Migrate migrazione del server utilizza Azure Site Recovery sul back-end per abilitare gli scenari di migrazione per la migrazione in modalità Lift-and-Shift dei computer locali.

## <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>In che modo la Azure Migrate Appliance si connette ad Azure?

La connessione può essere via Internet oppure è possibile usare Azure ExpressRoute con peering pubblico/Microsoft.

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Quali sono i requisiti di connettività di rete per la valutazione e la migrazione di Azure Migrate server?

Per informazioni sugli URL e sulle porte necessarie per consentire a Azure Migrate di comunicare con Azure, vedere le matrici di supporto [VMware](migrate-support-matrix-vmware.md) e [Hyper-V](migrate-support-matrix-hyper-v.md) .

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>È possibile rafforzare la macchina virtuale del dispositivo configurata con il modello?

È possibile aggiungere componenti (ad esempio, antivirus) al modello, purché si lascino le regole di comunicazione e del firewall necessarie per il Azure Migrate Appliance così come sono.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Quali dati vengono raccolti dal dispositivo Azure Migrate?

È possibile visualizzare i dettagli sui dati raccolti dal dispositivo Azure Migrate [nella documentazione di Azure migrate](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Si verifica un impatto sulle prestazioni nell'ambiente VMware o Hyper-V analizzato?

L'appliance Azure Migrate profilare i computer locali in modo continuo per misurare i dati sulle prestazioni della macchina virtuale. Questa profilatura non ha alcun effetto sulle prestazioni sugli host Hyper-V/ESXi o su server vCenter.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Dove vengono archiviati i dati raccolti e per quanto tempo?

I dati raccolti dal dispositivo Azure Migrate vengono archiviati nel percorso di Azure scelto durante la creazione del progetto di migrazione. I dati vengono archiviati in modo sicuro in una sottoscrizione Microsoft e vengono eliminati quando si elimina il progetto Azure Migrate.

Per la visualizzazione delle dipendenze, se si installano gli agenti nelle macchine virtuali, i dati raccolti dagli agenti di dipendenza vengono archiviati negli Stati Uniti, in un'area di lavoro di Log Analytics creata nella sottoscrizione di Azure. Tali dati vengono eliminati quando si elimina l'area di lavoro Log Analytics nella sottoscrizione. Per altre informazioni, vedere [visualizzazione delle dipendenze](concepts-dependency-visualization.md).

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>Quale volume di dati viene caricato dal dispositivo Azure Migrate durante la profilatura continua?

Il volume dei dati inviati a Azure Migrate varia a seconda dei diversi parametri. Per dare un'idea del volume: un progetto Azure Migrate con 10 computer (ognuno con un disco e una NIC) Invia circa 50 MB al giorno. Questo valore è un'approssimazione. Viene modificato in base al numero di punti dati per le schede di rete e i dischi. L'aumento dei dati inviati è non lineare se aumenta il numero di macchine virtuali, nic o dischi.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>I dati sono crittografati inattivi e in transito?

Sì. I metadati vengono inviati in modo sicuro al servizio Azure Migrate tramite Internet tramite HTTPS. I metadati vengono archiviati in un database di [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) e nell' [archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) in una sottoscrizione Microsoft. I metadati vengono crittografati a riposo.

Anche i dati raccolti dagli agenti di dipendenza vengono crittografati in transito (Secure HTTPS). Viene archiviato in un'area di lavoro Log Analytics nella sottoscrizione. Viene anche crittografato a riposo.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>In che modo l'appliance Azure Migrate comunica con server vCenter e il servizio Azure Migrate?

L'appliance si connette a server vCenter (porta 443) usando le credenziali specificate durante la configurazione dell'appliance. USA VMware PowerCLI per eseguire query server vCenter per raccogliere i metadati sulle macchine virtuali gestite da server vCenter. Raccoglie i dati di configurazione sulle VM (core, memoria, dischi, schede di rete e così via) e la cronologia delle prestazioni di ogni macchina virtuale per l'ultimo mese. I metadati raccolti vengono quindi inviati a Azure Migrate server Assessment (via Internet tramite HTTPS) per la valutazione.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>È possibile connettere lo stesso dispositivo a più istanze di server vCenter?

No. Esiste un mapping uno-a-uno tra un appliance e server vCenter. Se è necessario individuare macchine virtuali in più istanze di server vCenter, è necessario distribuire più appliance.


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>Le dimensioni del computer sono state modificate. È possibile eseguire nuovamente la valutazione?

L'appliance Azure Migrate raccoglie continuamente informazioni sull'ambiente locale. Tuttavia, una valutazione è uno snapshot temporizzato delle macchine virtuali locali. Se si modificano le impostazioni in una macchina virtuale che si desidera valutare, utilizzare l'opzione Ricalcola per aggiornare la valutazione con le modifiche più recenti.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Come è possibile eseguire l'individuazione in un ambiente multi-tenant nella valutazione di Azure Migrate server?

Per VMware, se si dispone di un ambiente condiviso tra i tenant e non si vuole individuare le macchine virtuali di un tenant nella sottoscrizione di un altro tenant, creare server vCenter credenziali che possono accedere solo alle macchine virtuali che si desidera individuare. Usare queste credenziali quando si avvia l'individuazione nell'appliance Azure Migrate.

Per Hyper-V, l'individuazione usa le credenziali dell'host Hyper-V. Se le VM condividono lo stesso host Hyper-V, attualmente non è possibile separare l'individuazione.  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>Quante VM è possibile individuare con una singola appliance di migrazione?

È possibile individuare fino a 10.000 VM VMware e fino a 5.000 macchine virtuali Hyper-V con una singola appliance di migrazione. Se si dispone di più computer nell'ambiente locale, informazioni su come ridimensionare [Hyper-V](scale-hyper-v-assessment.md) e [VMware](scale-vmware-assessment.md) assessment.

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>È possibile eliminare l'appliance Azure Migrate dal progetto?
L'eliminazione del dispositivo dal progetto non è attualmente supportata. L'unico modo per eliminare l'appliance consiste nell'eliminare il gruppo di risorse che contiene il progetto Azure Migrate, associato all'appliance, ma che eliminerà anche altre appliance registrate, l'inventario individuato, le valutazioni e tutti gli altri elementi di Azure associato al progetto nel gruppo di risorse.

## <a name="azure-migrate-server-assessment"></a>Valutazione server di Azure Migrate

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate server Assessment supporta la valutazione dei server fisici?

No, Azure Migrate attualmente non supporta la valutazione dei server fisici.

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>Azure Migrate necessario server vCenter per eseguire l'individuazione in un ambiente VMware?

Sì, Azure Migrate necessario server vCenter per eseguire l'individuazione in un ambiente VMware. Non supporta l'individuazione di host ESXi che non sono gestiti da server vCenter.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual è la differenza tra Azure Migrate server assessment e MAP Toolkit?

Azure Migrate server Assessment fornisce la valutazione della migrazione per favorire la preparazione della migrazione e la valutazione dei carichi di lavoro per la migrazione ad Azure. [Microsoft Assessment and Planning (Map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) consente di eseguire altre attività, ad esempio la pianificazione della migrazione per le versioni più recenti dei sistemi operativi client e server Windows e il rilevamento dell'utilizzo del software. Per tali scenari, continuare a usare il MAP Toolkit.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Qual è la differenza tra Azure Migrate Assessment server e il Azure Site Recovery Deployment Planner?

Azure Migrate server assessment è uno strumento di pianificazione della migrazione. Il Azure Site Recovery Deployment Planner è uno strumento di pianificazione del ripristino di emergenza.

**Migrazione da VMware/Hyper-V ad Azure**: Se si prevede di eseguire la migrazione dei server locali in Azure, usare Azure Migrate server assessment per la pianificazione della migrazione. Valuta i carichi di lavoro locali e fornisce indicazioni, informazioni dettagliate e strumenti che consentono di eseguire la migrazione dei server in Azure. Quando si è pronti per il piano di migrazione, è possibile usare strumenti come la migrazione del server Azure Migrate per eseguire la migrazione dei computer in Azure.

**Ripristino di emergenza da VMware/Hyper-V ad Azure**: Per il ripristino di emergenza in Azure tramite Site Recovery, usare il Deployment Planner Site Recovery per la pianificazione. Site Recovery Deployment Planner esegue una valutazione approfondita e specifica Site Recovery dell'ambiente locale. Fornisce consigli necessari Site Recovery per operazioni di emergenza con esito positivo, ad esempio la replica e il failover delle macchine virtuali.

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Azure Migrate supporta la stima dei costi per il programma di Enterprise Agreement (EA)?

Azure Migrate attualmente non supporta la stima dei costi per il [programma di Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). La soluzione alternativa consiste nel specificare il **pagamento in base** al consumo come **offerta** e specificare manualmente la percentuale di sconto (applicabile alla sottoscrizione) nella casella **sconto** delle proprietà di valutazione:

  ![Proprietà valutazione](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Qual è la differenza tra il dimensionamento locale e il dimensionamento basato sulle prestazioni?

Con il ridimensionamento locale, Azure Migrate non considera i dati sulle prestazioni della macchina virtuale. Ridimensiona le macchine virtuali in base alla configurazione locale. Con il dimensionamento basato sulle prestazioni, il dimensionamento è basato sui dati di utilizzo.

Si consideri ad esempio una macchina virtuale locale con 4 core e 8 GB di memoria al 50% di utilizzo della CPU e l'utilizzo della memoria del 50%. Per questa macchina virtuale, il dimensionamento locale consiglia uno SKU di VM di Azure con quattro core e 8 GB di memoria. Il dimensionamento basato sulle prestazioni consiglia uno SKU di VM con due core e 4 GB di memoria, perché la percentuale di utilizzo è considerata.

Analogamente, il ridimensionamento del disco dipende da due proprietà di valutazione: criteri di ridimensionamento e tipo di archiviazione. Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è automatico, Azure Migrate prende in considerazione i valori di IOPS e velocità effettiva del disco quando identifica il tipo di disco di destinazione (standard o Premium).

Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è Premium, Azure Migrate consiglia un disco Premium. Lo SKU del disco Premium è selezionato in base alle dimensioni del disco locale. La stessa logica viene usata per eseguire il dimensionamento del disco quando i criteri di ridimensionamento sono dimensionati in locale e il tipo di archiviazione è standard o Premium.

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>Qual è l'effetto della cronologia delle prestazioni e del percentile di utilizzo sulle dimensioni consigliate?

Queste proprietà sono applicabili solo per il dimensionamento basato sulle prestazioni.

Azure Migrate raccoglie la cronologia delle prestazioni dei computer locali e la usa per consigliare le dimensioni della macchina virtuale e il tipo di disco in Azure.

L'appliance continua a profilare l'ambiente locale per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi. Il dispositivo esegue il rollup degli esempi di 20 secondi e crea un singolo punto dati ogni 15 minuti. Per creare il punto dati, l'appliance seleziona il valore massimo da tutti gli esempi di 20 secondi. Il dispositivo invia questo punto dati ad Azure.

Quando si crea una valutazione in Azure, in base alla durata delle prestazioni e al valore percentile della cronologia delle prestazioni, Azure Migrate calcola il valore di utilizzo effettivo e lo usa per il ridimensionamento.

Si supponga, ad esempio, di impostare la durata delle prestazioni su un giorno e il valore percentile su 95 percentile. Azure Migrate Ordina i punti di campionamento di 15 minuti inviati dall'agente di raccolta per il giorno precedente in ordine crescente e sceglie il valore 95 ° percentile come utilizzo effettivo.

Il valore 95 ° percentile consente di ignorare gli outlier. Gli outlier possono essere inclusi se si usa il 99 ° percentile. Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, selezionare il 99 ° percentile.

### <a name="what-is-dependency-visualization"></a>Informazioni sulla visualizzazione delle dipendenze

La visualizzazione delle dipendenze consente di valutare i gruppi di macchine virtuali per la migrazione con maggiore sicurezza. Controlla le dipendenze tra computer prima di eseguire una valutazione. La visualizzazione delle dipendenze contribuisce a garantire che non venga lasciato nulla per evitare interruzioni impreviste quando si esegue la migrazione ad Azure. Azure Migrate usa la soluzione Mapping dei servizi nei log di monitoraggio di Azure per abilitare la visualizzazione delle dipendenze.

> [!NOTE]
> La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>È necessario pagare per usare la visualizzazione delle dipendenze?

No. Per ulteriori informazioni, vedere [Azure migrate prezzi](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>È necessario eseguire eventuali installazioni per usare la visualizzazione delle dipendenze?

Per usare la visualizzazione delle dipendenze è necessario scaricare e installare gli agenti in ogni computer locale da valutare.

È necessario installare gli agenti seguenti in ogni computer:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Se sono presenti computer senza connettività Internet, è necessario scaricare e installare Log Analytics gateway.

Questi agenti non sono necessari a meno che non si usi la visualizzazione delle dipendenze.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>È possibile usare un'area di lavoro per la visualizzazione delle dipendenze?

Sì, è possibile aggiungere un'area di lavoro esistente al progetto di migrazione e usarla per la visualizzazione delle dipendenze. Per ulteriori informazioni, vedere "come funziona" nell'articolo relativo alla [visualizzazione delle dipendenze](concepts-dependency-visualization.md#how-does-it-work) .

### <a name="can-i-export-the-dependency-visualization-report"></a>È possibile esportare il report di visualizzazione delle dipendenze?

No, la visualizzazione delle dipendenze non può essere esportata. Tuttavia, poiché Azure Migrate USA Mapping dei servizi per la visualizzazione delle dipendenze, è possibile usare l' [API REST mapping dei servizi](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) per ottenere le dipendenze in formato JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Come è possibile automatizzare l'installazione di Microsoft Monitoring Agent (MMA) e Dependency Agent?

Usare questo [script per installare Dependency Agent](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Seguire queste [istruzioni per installare MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) usando la riga di comando o l'automazione. Per MMA, usare [questo script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Oltre agli script, è inoltre possibile utilizzare strumenti di distribuzione come System Center Configuration Manager e [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) per distribuire gli agenti.

### <a name="what-operating-systems-are-supported-by-mma"></a>Quali sistemi operativi sono supportati da MMA?

- Visualizzare l'elenco dei [sistemi operativi Windows supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Visualizzare l'elenco dei [sistemi operativi Linux supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>Quali sistemi operativi sono supportati da Dependency Agent?

Visualizzare l'elenco dei [sistemi operativi Windows e Linux supportati da monitoraggio di Azure per le macchine virtuali](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>È possibile visualizzare le dipendenze in Azure Migrate per più di un'ora?
No. È possibile visualizzare le dipendenze per un massimo di un'ora. È possibile tornare a una data specifica nella cronologia, fino a un mese, ma la durata massima per la visualizzazione è di un'ora. Ad esempio, è possibile usare la durata dell'intervallo di tempo nella mappa delle dipendenze per visualizzare le dipendenze di ieri, ma è possibile visualizzarle solo per una finestra di un'ora. È tuttavia possibile usare i log di monitoraggio di Azure per [eseguire query sui dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per un periodo di tempo più lungo.

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>È possibile usare la visualizzazione delle dipendenze per i gruppi che contengono più di 10 VM?
È possibile [visualizzare le dipendenze per i gruppi](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) che contengono fino a 10 macchine virtuali. Se si dispone di un gruppo con più di 10 macchine virtuali, è consigliabile suddividere il gruppo in gruppi più piccoli e quindi visualizzare le dipendenze.

## <a name="azure-migrate-server-migration"></a>Migrazione server di Azure Migrate

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Qual è la differenza tra Azure Migrate migrazione del server e Azure Site Recovery?

Azure Migrate migrazione del server usa il motore di replica Site Recovery per la migrazione basata su agente di VM VMware, la migrazione di VM Hyper-V e la migrazione di server fisici in Azure. L'opzione senza agente per eseguire la migrazione di macchine virtuali VMware è incorporata in modo nativo nella migrazione del server.

## <a name="next-steps"></a>Passaggi successivi
Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).
