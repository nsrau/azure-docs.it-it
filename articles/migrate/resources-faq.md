---
title: Domande frequenti su Azure Migrate| Microsoft Docs
description: Risposte alle domande frequenti su Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: snehaa
ms.openlocfilehash: 3f035f38b1ad68e9e39d151ffad3fc650a0a1d80
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952750"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Domande frequenti su Azure Migrate

Questo articolo include le domande frequenti su Azure Migrate. Eventuali altre domande successive alla lettura di questo articolo possono essere pubblicate nel [forum di Azure Migrate](http://aka.ms/AzureMigrateForum).

## <a name="general"></a>Generale

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Azure Migrate supporta la valutazione dei soli carichi di lavoro VMware?

Sì, Azure Migrate supporta attualmente la valutazione dei soli carichi di lavoro VMware. Il supporto per Hyper-V e i server fisici verrà abilitato in futuro.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate richiede che vCenter Server individui un ambiente VMware?

Sì, Azure Migrate richiede che vCenter Server individui un ambiente VMware. Non supporta l'individuazione di host ESXi non gestiti da un server vCenter.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Quali sono le differenze tra Azure Migrate e Azure Site Recover?

Azure Migrate è un servizio di valutazione che consente di individuare i carichi di lavoro locali e pianificare la migrazione ad Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), oltre a essere una soluzione di ripristino di emergenza, consente di eseguire la migrazione dei carichi di lavoro locali a macchine virtuali IaaS di Azure.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Qual è la differenza tra l'uso di Azure Migrate per le valutazioni e lo strumento Map Toolkit?

[Azure Migrate](migrate-overview.md) fornisce la valutazione della migrazione in modo specifico per agevolare la preparazione della migrazione e valutare i carichi di lavoro locali in Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826) ha altre funzionalità. Ad esempio, la pianificazione della migrazione per le versioni più recenti dei sistemi operativi con client e server Windows, il rilevamento dell' uso dei software e così via. Per tali scenari, continuare a usare il MAP Toolkit.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Quali sono le differenze tra Azure Migrate e Azure Site Recovery Deployment Planner?

Azure Migrate è una strumento di pianificazione della migrazione e Azure Site Recovery Deployment Planner è uno strumento di ripristino di emergenza.

**Migrazione da VMware ad Azure**: se si prevede di eseguire la migrazione dei carichi di lavoro locali ad Azure, usare Azure Migrate per la pianificazione della migrazione. Azure Migrate valuta i carichi di lavoro locali e fornisce materiale sussidiario, informazioni dettagliate e meccanismi per la migrazione ad Azure. Quando il piano di migrazione è pronto, è possibile usare servizi come Azure Site Recovery e Servizio Migrazione del database di Azure, per eseguire la migrazione dei computer ad Azure.

**Migrazione da Hyper-V ad Azure**: Azure Migrate attualmente supporta solo la valutazione delle macchine virtuali VMware per la migrazione ad Azure. Il supporto per Hyper-V per Azure Migrate è in programma. Nel frattempo, è possibile usare Azure Site Recovery Deployment Planner. Dopo l'abilitazione del supporto di Hyper-V in Azure Migrate, è possibile usare Azure Migrate per la pianificazione della migrazione dei carichi di lavoro di Hyper-V.

**Ripristino di emergenza da VMware/Hyper-V in Azure**: se si intende eseguire il ripristino di emergenza in Azure mediante Azure Site Recovery (Site Recovery), usare Azure Site Recovery Deployment Planner per la pianificazione del ripristino di emergenza. Azure Site Recovery Deployment Planner esegue una valutazione ASR specifica e completa dell'ambiente locale. Fornisce gli elementi consigliati necessarie per l'esito positivo delle operazioni di ripristino di emergenza di Site Recovery, ad esempio, replica, failover delle macchine virtuali.  

### <a name="which-azure-regions-are-supported-by-azure-migrate"></a>Quali aree di Azure sono supportate da Azure Migrate?

Azure Migrate supporta attualmente Stati Uniti orientali e Stati Uniti centro-occidentali come località per i progetti di migrazione. Si noti che anche se è possibile creare progetti di migrazione solo nelle aree Stati Uniti centro-occidentali e Stati Uniti orientali, è comunque possibile valutare i computer per [più località di destinazione](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). La località per il progetto viene usata solo per archiviare i dati individuati.

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>In che modo il sito locale si connette ad Azure Migrate?

La connessione può avvenire tramite internet o usare ExpressRoute con peering pubblico.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>È possibile rafforzare la macchina virtuale configurata con il. modello .OVA?

È possibile aggiungere componenti aggiuntivi (ad esempio applicazioni antivirus) al modello .OVA, purché la comunicazione e le regole del firewall necessarie perché l'appliance Azure Migrate funzioni vengano lasciate invariate.   

## <a name="discovery-and-assessment"></a>Individuazione e valutazione

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

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Dove vengono archiviati i dati raccolti e per quanto tempo?

I dati raccolti dall'appliance dell'agente di raccolta vengono archiviati nel percorso di Azure che si specifica durante la creazione del progetto di migrazione. I dati vengono archiviati in modo sicuro in una sottoscrizione Microsoft e vengono eliminati quando l'utente elimina il progetto Azure Migrate.

Per la visualizzazione delle dipendenze, se si installano gli agenti nelle macchine virtuali, i dati raccolti dagli agenti di dipendenza vengono archiviati negli Stati Uniti in un'area di lavoro OMS creata nella sottoscrizione dell'utente. Tali dati vengono eliminati quando si elimina l'area di lavoro OMS nella sottoscrizione. [Altre informazioni](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>I dati inattivi e in transito vengono crittografati?

Sì, i dati inattivi e in transito raccolti vengono crittografati. I metadati raccolti dall'appliance vengono inviati in modo sicuro al servizio Azure Migrate su Internet tramite https. I metadati raccolti vengono archiviati [Cosmos DB ](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) e in [Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) in una sottoscrizione Microsoft e vengono crittografati a riposo.

I dati raccolti dagli agenti di dipendenze vengono crittografati anche in transito (canale https sicuro) e vengono archiviati in un'area di lavoro di Log Analytics nella sottoscrizione dell'utente. Vengono crittografati anche se inattivi.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>In che modo l'agente di raccolta comunica con il server vCenter e il servizio Azure Migrate?

L'appliance dell'agente di raccolta si connette al server vCenter (porta 443) usando le credenziali fornite dall'utente nell'appliance. Viene eseguita una query sul server vCenter mediante VMware PowerCLI per raccogliere i metadati relativi alle macchine virtuali gestite dal server vCenter. Vengono raccolti i dati di configurazione relativi alle macchine virtuali (core, memoria, dischi, scheda di interfaccia di rete e così via) e la cronologia delle prestazioni di ogni macchina virtuale per l'ultimo mese dal server vCenter. I metadati raccolti vengono quindi inviati al servizio Azure Migrate (su Internet tramite https) per la valutazione. [Altre informazioni](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>L'appliance dello stesso agente di raccolta può connettersi a più server vCenter?

Sì, l'appliance d un singolo agente di raccolta può essere usata per individuare più server vCenter, ma non contemporaneamente. È necessario eseguire le individuazioni una di seguito all'altra.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Il modello .OVA usato da Site Recovery è integrato con il modello .OVA usato da Azure Migrate?

Attualmente non è presente alcuna integrazione. Il modello .OVA in Site Recovery viene usato per configurare un server di configurazione di Site Recovery per la replica di server fisici/macchine Virtuali VMware. Il modello .OVA usato da Azure Migrate consente di individuare le macchine virtuali VMware gestite da un server vCenter, a fini di valutazione della migrazione.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Le dimensioni del computer sono state modificate. È possibile eseguire nuovamente la valutazione?

Se si modificano le impostazioni in una macchina virtuale a cui si vuole accedere, attivare di nuovo l'individuazione tramite l'appliance dell'agente di raccolta. Nell'appliance, usare l'opzione **Avvia di nuovo la raccolta** nell'agente di raccolta. Al termine della raccolta, selezionare l'opzione **Ricalcola** per la valutazione nel portale in modo da ottenere i risultati della valutazione aggiornati.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Come è possibile individuare un ambiente multi-tenant in Azure Migrate?

Se si dispone di un ambiente condiviso da più tenant e non si desidera individuare le macchine virtuali di un tenant nella sottoscrizione di un altro tenant, è possibile usare il campo Ambito nell'appliance dell'agente di raccolta per impostare l'ambito di individuazione. Se i tenant condividono gli host, creare una credenziale con accesso in sola lettura alle macchine virtuali che appartengono al tenant specifico e quindi usare le credenziali nell'appliance dell'agente di raccolta e specificare Ambito come host per eseguire l'individuazione. In alternativa, è inoltre possibile creare cartelle nel server vCenter (si supponga cartella1 per tenant1 e cartella2 per tenant2), nell'host condiviso, spostare le macchine virtuali per tenant1 nella cartella1 e per tenant2 nella cartella2 e quindi impostare di conseguenza l'ambito delle individuazioni nell'agente di raccolta specificando la cartella appropriata.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Quante macchine virtuali è possibile individuare in un singolo progetto di migrazione?

È possibile individuare 1500 macchine virtuali in un singolo progetto di migrazione. Se nell'ambiente locale sono presenti più macchine, sono disponibili [altre informazioni](how-to-scale-assessment.md) sul modo in cui è possibile individuare un ambiente di grandi dimensioni in Azure Migrate.

## <a name="dependency-visualization"></a>Visualizzazione delle dipendenze

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>È necessario pagare per usare la funzionalità di visualizzazione delle dipendenze?

Azure Migrate è disponibile senza costi aggiuntivi. Altre informazioni sui prezzi di Azure Migrate sono disponibili [qui](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>È possibile usare un'area di lavoro per la visualizzazione delle dipendenze?

Azure Migrate non supporta l'utilizzo di un'area di lavoro esistente per la visualizzazione delle dipendenze. Microsoft Monitoring Agent (MMA) supporta tuttavia il multihoming e consente di inviare dati a più aree di lavoro. Se pertanto si dispone già di agenti distribuiti e configurati per un'area di lavoro, è possibile sfruttare il multihoming nell'agente MMA e configurarlo per l'area di lavoro di Azure Migrate (in aggiunta all'area di lavoro esistente) ed eseguirlo. [Qui](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) è disponibile un blog con informazioni sull'abilitazione del multihoming in un agente MMA.

## <a name="next-steps"></a>Passaggi successivi

- Leggere la [Panoramica di Azure Migrate](migrate-overview.md)
- Informazioni su come è possibile [individuare e valutare](tutorial-assessment-vmware.md) un ambiente VMware
