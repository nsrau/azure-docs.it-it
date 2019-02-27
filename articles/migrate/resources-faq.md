---
title: Domande frequenti su Azure Migrate| Microsoft Docs
description: Risposte alle domande frequenti su Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: snehaa
ms.openlocfilehash: 9d1820215dd2b81edb694d71a1b9496237876d05
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416191"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Domande frequenti su Azure Migrate

Questo articolo include le domande frequenti su Azure Migrate. Eventuali altre domande successive alla lettura di questo articolo possono essere pubblicate nel [forum di Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Generale

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Azure Migrate supporta la valutazione dei soli carichi di lavoro VMware?

Sì, Azure Migrate supporta attualmente la valutazione dei soli carichi di lavoro VMware. Il supporto per Hyper-V e i server fisici verrà abilitato in futuro.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate richiede che vCenter Server individui un ambiente VMware?

Sì, Azure Migrate richiede che vCenter Server individui un ambiente VMware. Non supporta l'individuazione di host ESXi non gestiti da un server vCenter.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Quali sono le differenze tra Azure Migrate e Azure Site Recover?

Azure Migrate è un servizio di valutazione che consente di individuare i carichi di lavoro locali e pianificare la migrazione ad Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), oltre a essere una soluzione di ripristino di emergenza, consente di eseguire la migrazione dei carichi di lavoro locali a macchine virtuali IaaS di Azure.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Qual è la differenza tra l'uso di Azure Migrate per le valutazioni e lo strumento Map Toolkit?

[Azure Migrate](migrate-overview.md) fornisce la valutazione della migrazione in modo specifico per agevolare la preparazione della migrazione e valutare i carichi di lavoro locali in Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826) offre altre funzionalità, come la pianificazione della migrazione per le versioni più recenti dei sistemi operativi client e server Windows e il rilevamento dell'utilizzo del software. Per tali scenari, continuare a usare il MAP Toolkit.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Quali sono le differenze tra Azure Migrate e Azure Site Recovery Deployment Planner?

Azure Migrate è una strumento di pianificazione della migrazione e Azure Site Recovery Deployment Planner è uno strumento di ripristino di emergenza.

**Migrazione da VMware ad Azure**: se si prevede di eseguire la migrazione dei carichi di lavoro locali ad Azure, usare Azure Migrate per la pianificazione della migrazione. Azure Migrate valuta i carichi di lavoro locali e fornisce materiale sussidiario, informazioni dettagliate e meccanismi per la migrazione ad Azure. Quando il piano di migrazione è pronto, è possibile usare servizi come Azure Site Recovery e Servizio Migrazione del database di Azure, per eseguire la migrazione dei computer ad Azure.

**Migrazione da Hyper-V ad Azure**: Azure Migrate attualmente supporta solo la valutazione delle macchine virtuali VMware per la migrazione ad Azure. Il supporto per Hyper-V per Azure Migrate è in programma. Nel frattempo, è possibile usare Azure Site Recovery Deployment Planner. Dopo l'abilitazione del supporto di Hyper-V in Azure Migrate, è possibile usare Azure Migrate per la pianificazione della migrazione dei carichi di lavoro di Hyper-V.

**Ripristino di emergenza da VMware/Hyper-V in Azure**: se si intende eseguire il ripristino di emergenza in Azure mediante Azure Site Recovery (Site Recovery), usare Azure Site Recovery Deployment Planner per la pianificazione del ripristino di emergenza. Azure Site Recovery Deployment Planner esegue una valutazione ASR specifica e completa dell'ambiente locale. Fornisce gli elementi consigliati necessarie per l'esito positivo delle operazioni di ripristino di emergenza di Site Recovery, ad esempio, replica, failover delle macchine virtuali.  

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Quali aree geografiche di Azure sono supportate da Azure Migrate?

Azure Migrate attualmente supporta Europa, Stati Uniti e Azure per enti pubblici come aree geografiche di progetto. Anche se si possono creare progetti di migrazione solo in queste aree geografiche, è comunque possibile valutare i computer per [più località di destinazione](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). L'area geografica del progetto viene usata solo per archiviare i metadati individuati.

**Area geografica** | **Posizione di archiviazione dei metadati**
--- | ---
Azure Government | US Gov Virginia
Asia | Asia sud-orientale
Europa | Europa settentrionale o Europa occidentale
Stati Uniti | Stati Uniti orientali o Stati Uniti centro-occidentali

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>In che modo il sito locale si connette ad Azure Migrate?

La connessione può avvenire tramite internet o usare ExpressRoute con peering pubblico.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>È possibile rafforzare la macchina virtuale configurata con il modello OVA?

È possibile aggiungere componenti aggiuntivi (ad esempio applicazioni antivirus) al modello OVA, purché la comunicazione e le regole del firewall necessarie perché l'appliance Azure Migrate funzioni vengano lasciate invariate.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Per aumentare l'appliance Azure Migrate, quali sono le esclusioni Antivirus (AV) consigliate?

È necessario escludere le cartelle seguenti nell'appliance per l'analisi antivirus:

- Cartella che contiene i file binari per il servizio Azure Migrate. Escludere tutte le sottocartelle.
  %ProgramFiles%\ProfilerService  
- Applicazione Web Azure Migrate. Escludere tutte le sottocartelle.
  %SystemDrive%\inetpub\wwwroot
- Cache locale per i file di database e log. Il servizio Azure Migrate necessita dell'accesso in lettura/scrittura a questa cartella.
  %SystemDrive%\Profiler

## <a name="discovery"></a>Individuazione

### <a name="what-data-is-collected-by-azure-migrate"></a>Quali dati vengono raccolti da Azure Migrate?

Azure Migrate supporta due tipi di individuazione, l'individuazione basata su appliance e l'individuazione basata su agenti.
L'individuazione basata su appliance raccoglie i metadati relativi alle macchine virtuali locali. L'elenco completo dei metadati raccolti dall'appliance è riportato di seguito:

**Dati di configurazione della macchina virtuale**
- Nome visualizzato della macchina virtuale (in vCenter)
- Percorso dell'inventario della macchina virtuale (host/cluster/cartella in vCenter)
- Indirizzo IP
- Indirizzo MAC
- Sistema operativo
- Numero di core, dischi, schede di interfaccia di rete
- Dimensione della memoria, dimensioni dei dischi

**Dati sulle prestazioni della macchina virtuale**
- Utilizzo di CPU
- Utilizzo della memoria
- Per ogni disco collegato alla macchina virtuale:
  - Velocità effettiva lettura da disco
  - Velocità effettiva di scrittura su disco
  - Operazioni di lettura da disco al secondo
  - Operazioni di scrittura su disco al secondo
- Per ogni scheda di rete collegata alla macchina virtuale:
  - Rete in ingresso
  - Rete in uscita

L'individuazione basata su agenti è un'opzione disponibile oltre all'individuazione basata su appliance e consente ai clienti di [visualizzare le dipendenze](how-to-create-group-machine-dependencies.md) delle macchine virtuali locali. Gli agenti di dipendenze raccolgono dettagli quali, FQDN, sistema operativo, indirizzo IP, indirizzo MAC, processi in esecuzione all'interno della macchina virtuale e connessioni TCP in ingresso/in uscita dalla macchina virtuale. L'individuazione basata su agenti è facoltativa ed è possibile scegliere di non installare gli agenti se non si desidera visualizzare le dipendenze delle macchine virtuali.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Ci sono conseguenze sulle prestazioni dell'ambiente host di ESXi analizzato?

Nel caso dell'[approccio di individuazione una tantum](https://docs.microsoft.com/azure/migrate/concepts-collector), per raccogliere i dati sulle prestazioni, il livello delle statistiche nel server vCenter deve essere impostato su 3. Impostando il livello in questo modo viene raccolta una grande quantità di dati di risoluzione dei problemi che viene archiviata nel database del server vCenter. Ciò potrebbe causare problemi di prestazioni del server vCenter, mentre l'impatto sull'host ESXi sarebbe trascurabile.

È stata introdotta la profilatura continua dei dati sulle prestazioni (disponibile in anteprima). Con la profilatura continua, non è più necessario modificare il livello delle statistiche del server vCenter per eseguire una valutazione basata sulle prestazioni. L'appliance dell'agente di raccolta esegue ora la profilatura delle macchine virtuali locali per misurarne i dati sulle prestazioni. Ciò ha un impatto quasi nullo sulle prestazioni degli host ESXi, nonché del server vCenter.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Dove vengono archiviati i dati raccolti e per quanto tempo?

I dati raccolti dall'appliance dell'agente di raccolta vengono archiviati nel percorso di Azure che si specifica durante la creazione del progetto di migrazione. I dati vengono archiviati in modo sicuro in una sottoscrizione Microsoft e vengono eliminati quando l'utente elimina il progetto Azure Migrate.

Per la visualizzazione delle dipendenze, se si installano gli agenti nelle macchine virtuali, i dati raccolti dagli agenti di dipendenza vengono archiviati negli Stati Uniti in un'area di lavoro di Log Analytics creata nella sottoscrizione dell'utente. Tali dati vengono eliminati quando si elimina l'area di lavoro di Log Analytics nella sottoscrizione. [Altre informazioni](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)

### <a name="what-is-the-volume-of-data-which-is-uploaded-by-azure-migrate-in-the-case-of-continuous-profiling"></a>Qual è il volume di dati che viene caricato da Azure Migrate in caso di profilatura continua?

Il volume di dati che viene inviato ad Azure Migrate potrebbe variare in base a diversi parametri. Per fornire un numero indicativo, un progetto con dieci computer (ognuno con un disco e una scheda di rete) invierebbe circa 50 MB al giorno. Si tratta di un valore approssimativo che potrebbe cambiare in base al numero di punti dati per le schede di rete e i dischi (i dati inviati sarebbero non lineari se aumentasse il numero di computer, schede di rete o dischi). 

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>I dati inattivi e in transito vengono crittografati?

Sì, i dati inattivi e in transito raccolti vengono crittografati. I metadati raccolti dall'appliance vengono inviati in modo sicuro al servizio Azure Migrate su Internet tramite https. I metadati raccolti vengono archiviati [Cosmos DB ](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) e in [Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) in una sottoscrizione Microsoft e vengono crittografati a riposo.

I dati raccolti dagli agenti di dipendenze vengono crittografati anche in transito (canale https sicuro) e vengono archiviati in un'area di lavoro di Log Analytics nella sottoscrizione dell'utente. Vengono crittografati anche se inattivi.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>In che modo l'agente di raccolta comunica con il server vCenter e il servizio Azure Migrate?

L'appliance dell'agente di raccolta si connette al server vCenter (porta 443) usando le credenziali fornite dall'utente nell'appliance. Viene eseguita una query sul server vCenter mediante VMware PowerCLI per raccogliere i metadati relativi alle macchine virtuali gestite dal server vCenter. Vengono raccolti i dati di configurazione relativi alle macchine virtuali (core, memoria, dischi, scheda di interfaccia di rete e così via) e la cronologia delle prestazioni di ogni macchina virtuale per l'ultimo mese dal server vCenter. I metadati raccolti vengono quindi inviati al servizio Azure Migrate (su Internet tramite https) per la valutazione. [Altre informazioni](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>L'appliance dello stesso agente di raccolta può connettersi a più server vCenter?

Sì, l'appliance d un singolo agente di raccolta può essere usata per individuare più server vCenter, ma non contemporaneamente. È necessario eseguire l'individuazione una di seguito all'altra.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Il modello OVA usato da Site Recovery è integrato con il modello OVA usato da Azure Migrate?

Attualmente non è presente alcuna integrazione. Il modello .OVA in Site Recovery viene usato per configurare un server di configurazione di Site Recovery per la replica di server fisici/macchine Virtuali VMware. Il modello .OVA usato da Azure Migrate consente di individuare le macchine virtuali VMware gestite da un server vCenter, a fini di valutazione della migrazione.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Le dimensioni del computer sono state modificate. È possibile eseguire nuovamente la valutazione?

Se si modificano le impostazioni in una macchina virtuale a cui si vuole accedere, attivare di nuovo l'individuazione tramite l'appliance dell'agente di raccolta. Nell'appliance, usare l'opzione **Avvia di nuovo la raccolta** nell'agente di raccolta. Al termine della raccolta, selezionare l'opzione **Ricalcola** per la valutazione nel portale in modo da ottenere i risultati della valutazione aggiornati.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Come è possibile individuare un ambiente multi-tenant in Azure Migrate?

Se si dispone di un ambiente condiviso da più tenant e non si desidera individuare le macchine virtuali di un tenant nella sottoscrizione di un altro tenant, è possibile usare il campo Ambito nell'appliance dell'agente di raccolta per impostare l'ambito di individuazione. Se i tenant condividono gli host, creare una credenziale con accesso in sola lettura alle macchine virtuali che appartengono al tenant specifico e quindi usare le credenziali nell'appliance dell'agente di raccolta e specificare Ambito come host per eseguire l'individuazione. In alternativa, è inoltre possibile creare cartelle nel server vCenter (si supponga cartella1 per tenant1 e cartella2 per tenant2), nell'host condiviso, spostare le macchine virtuali per tenant1 nella cartella1 e per tenant2 nella cartella2 e quindi impostare di conseguenza l'ambito delle individuazioni nell'agente di raccolta specificando la cartella appropriata.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Quante macchine virtuali è possibile individuare in un singolo progetto di migrazione?

È possibile individuare 1500 macchine virtuali in un singolo progetto di migrazione. Se nell'ambiente locale sono presenti più macchine, sono disponibili [altre informazioni](how-to-scale-assessment.md) sul modo in cui è possibile individuare un ambiente di grandi dimensioni in Azure Migrate.


## <a name="assessment"></a>Valutazione

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate supporta il Contratto Enterprise Agreement (EA) basato sulla stima dei costi?

Azure Migrate attualmente non supporta la stima dei costi per [offerta del Contratto Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). La soluzione temporanea consiste nello specificare Con pagamento in base al consumo come l'offerta e specificando manualmente la percentuale di sconto (applicabile alla sottoscrizione) nel campo 'Sconto' delle proprietà della valutazione.

  ![Discount](./media/resources-faq/discount.png)

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Qual è la differenza tra il dimensionamento locale e il dimensionamento basato sulle prestazioni?

Quando si specifica che il criterio di dimensionamento deve essere locale, Azure Migrate non considera i dati delle prestazioni delle macchine virtuali e le ridimensiona in base alla configurazione locale. Se il criterio di dimensionamento è basato sulle prestazioni, il dimensionamento viene eseguito in base ai dati di utilizzo. Ad esempio se è presente una macchina virtuale locale con 4 core e 8 GB di memoria, con un utilizzo della CPU e della memoria del 50%. Se il criterio di dimensionamento è il dimensionamento locale, è consigliato una SKU di macchina virtuale di Azure con 4 core e 8 GB di memoria, ma, se il criterio di dimensionamento è basato sulle prestazioni, è consigliato una SKU di macchina virtuale con 2 core e 4 GB perché, pur consigliando le dimensioni, va considerata la percentuale di utilizzo. Analogamente, per i dischi, il dimensionamento dei dischi dipende da due proprietà di valutazione: i criteri di dimensionamento e il tipo di archiviazione. Se il criterio di dimensionamento è basato sulle prestazioni e il tipo di archiviazione è automatico, vengono considerati i valori relativi alle operazioni di I/O al secondo e alla velocità effettiva del disco per identificare il tipo di disco di destinazione (Standard o Premium). Se il criterio di dimensionamento è basato sulle prestazioni e il tipo di archiviazione è Premium, è consigliabile un disco Premium. La SKU del disco Premium in Azure viene selezionato in base alle dimensioni del disco locale. La stessa logica si applica al dimensionamento del disco quando il criterio di dimensionamento è quello locale e il tipo di archiviazione è Standard o Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Qual è l'impatto della cronologia delle prestazioni e dell'utilizzo percentile sulle dimensioni consigliate?

Queste proprietà sono applicabili solo per il dimensionamento basato sulle prestazioni. Azure Migrate raccoglie la cronologia delle prestazioni dei computer locali e la usa per consigliare le dimensioni della macchina virtuale e il tipo di disco in Azure. L'appliance dell'agente di raccolta esegue continuamente una profilatura dell'ambiente locale per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi. L'appliance esegue il rollup dei campioni raccolti ogni 20 secondi e crea un singolo punto dati ogni 15 minuti. Per creare il singolo punto dati, l'appliance seleziona il valore di picco da tutti i campioni raccolti ogni 20 secondi e lo invia ad Azure. Quando si crea una valutazione in Azure, in base alla durata delle prestazioni e al valore percentile della cronologia delle prestazioni, Azure Migrate calcola il valore di utilizzo effettivo e lo usa per il dimensionamento. Se ad esempio la durata delle prestazioni è stata impostata su 1 giorno e il valore percentile è stato impostato su 95, Azure Migrate usa i punti campione da 15 minuti inviati dall'agente di raccolta per l'ultimo giorno, li ordina in senso crescente e sceglie il valore del 95° percentile come utilizzo effettivo. Il valore del 95° percentile assicura che vengano ignorati eventuali outlier, come potrebbe invece verificarsi se si selezionasse il 99° percentile. Per scegliere il picco nell'utilizzo per il periodo di tempo senza perdere gli outlier, è consigliabile selezionare il 99° percentile.

## <a name="dependency-visualization"></a>Visualizzazione delle dipendenze

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

### <a name="what-is-dependency-visualization"></a>Informazioni sulla visualizzazione delle dipendenze

La visualizzazione delle dipendenze consente di valutare in modo più attendibile i gruppi di macchine virtuali per la migrazione, eseguendo il controllo incrociato delle dipendenze dei computer prima di eseguire una valutazione. La visualizzazione delle dipendenze consente di garantire che nulla venga tralasciato, evitando interruzioni impreviste durante la migrazione ad Azure. Per abilitare la visualizzazione delle dipendenze, Azure Migrate usa la soluzione Mapping dei servizi in Log Analytics.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>È necessario pagare per usare la funzionalità di visualizzazione delle dipendenze?

 No. Altre informazioni sui prezzi di Azure Migrate sono disponibili [qui](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>È necessario eseguire eventuali installazioni per usare la visualizzazione delle dipendenze?

Per usare la visualizzazione delle dipendenze è necessario scaricare e installare gli agenti in ogni computer locale da valutare.

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) deve essere installato in ogni computer.
- [Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) deve essere installato in ogni computer.
- Se vi sono computer senza accesso a Internet, è necessario scaricare e installare il gateway di Log Analytics.

Questi agenti non sono necessari sui computer da valutare, a meno che non si usi la visualizzazione delle dipendenze.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>È possibile usare un'area di lavoro per la visualizzazione delle dipendenze?

Sì, Azure Migrate consente ora di collegare un'area di lavoro esistente al progetto di migrazione e usarla per visualizzare le dipendenze. [Altre informazioni](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work)

### <a name="can-i-export-the-dependency-visualization-report"></a>È possibile esportare il report di visualizzazione delle dipendenze?

No, la visualizzazione delle dipendenze non può essere esportata. Tuttavia, poiché Azure Migrate usa il Mapping dei servizi per la visualizzazione delle dipendenze, è possibile usare le [API REST di Mapping dei servizi](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) per acquisire le dipendenze in formato JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Come è possibile automatizzare l'installazione di Microsoft Monitoring Agent, MMA, e di Dependency Agent?

[Qui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) si può trovare uno script che è possibile usare per l'installazione di Dependency Agent. [Qui](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sono disponibili le istruzioni su come installare MMA tramite la riga di comando o metodi automatici. Per MMA, è anche possibile usare lo script disponibile [qui](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab) su Technet.

Oltre agli script, per distribuire gli agenti è anche possibile sfruttare gli strumenti di distribuzione come System Center Configuration Manager, SCCM, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) e così via.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quali sono i sistemi operativi supportati da MMA?

L'elenco dei sistemi operativi Windows supportati da MMA è reperibile [qui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
L'elenco dei sistemi operativi Linux supportati da MMA è reperibile [qui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quali sono i sistemi operativi supportati dal Dependency Agent?

L'elenco dei sistemi operativi Windows supportati dal Dependency Agent è reperibile [qui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
L'elenco dei sistemi operativi Linux supportati dal Dependency Agent è reperibile [qui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>È possibile visualizzare le dipendenze in Azure Migrate per più di un'ora?
No, Azure Migrate consente di visualizzare le dipendenze per la durata massima di un'ora. Azure Migrate consente di tornare a una determinata data nella cronologia fino al mese precedente, ma il tempo massimo per cui è possibile visualizzare le dipendenze è un'ora. Ad esempio, è possibile usare la funzionalità di durata nella mappa delle dipendenze per visualizzare le dipendenze di ieri ma possono essere visualizzate solo per un'ora. È tuttavia possibile usare Log Analytics per [eseguire query sui dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#query-dependency-data-from-log-analytics) per periodi di tempo più lunghi.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>La visualizzazione delle dipendenze è supportata per i gruppi con più di 10 macchine virtuali?
È possibile [visualizzare le dipendenze per i gruppi](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) con un massimo di 10 macchine virtuali. Se si ha un gruppo con più di 10 macchine virtuali, è consigliabile dividere il gruppo in gruppi più piccoli e visualizzarne le dipendenze.


## <a name="next-steps"></a>Passaggi successivi

- Leggere la [Panoramica di Azure Migrate](migrate-overview.md)
- Informazioni su come è possibile [individuare e valutare](tutorial-assessment-vmware.md) un ambiente VMware
