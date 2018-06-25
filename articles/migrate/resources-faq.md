---
title: Domande frequenti su Azure Migrate| Microsoft Docs
description: Risposte alle domande frequenti su Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 06/06/2018
ms.author: snehaa
ms.openlocfilehash: b18d2cecfd7556ad3f05d0f63435d16bc29ebab1
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826134"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Domande frequenti su Azure Migrate

Questo articolo include le domande frequenti su Azure Migrate. Eventuali altre domande successive alla lettura di questo articolo possono essere pubblicate nel [forum di Azure Migrate](http://aka.ms/AzureMigrateForum).

## <a name="general"></a>Generale

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Quali sono le differenze tra Azure Migrate e Azure Site Recover?

Azure Migrate è un servizio di valutazione che consente di individuare i carichi di lavoro locali e pianificare la migrazione ad Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), oltre a essere una soluzione di ripristino di emergenza, consente di eseguire la migrazione dei carichi di lavoro locali a macchine virtuali IaaS di Azure. 

### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Quali sono le differenze tra Azure Migrate e Azure Site Recovery Deployment Planner?

Azure Migrate è una strumento di pianificazione della migrazione e Azure Site Recovery Deployment Planner è uno strumento di ripristino di emergenza.

**Migrazione da VMware ad Azure**: se si prevede di eseguire la migrazione dei carichi di lavoro locali ad Azure, usare Azure Migrate per la pianificazione della migrazione. Azure Migrate valuta i carichi di lavoro locali e fornisce materiale sussidiario, informazioni dettagliate e meccanismi per la migrazione ad Azure. Quando il piano di migrazione è pronto, è possibile usare servizi come Azure Site Recovery e Servizio Migrazione del database di Azure, per eseguire la migrazione dei computer ad Azure.

**Migrazione da Hyper-V ad Azure**: Azure Migrate attualmente supporta solo la valutazione delle macchine virtuali VMware per la migrazione ad Azure. Il supporto per Hyper-V per Azure Migrate è in programma. Nel frattempo, è possibile usare ASR Deployment Planner. Dopo l'abilitazione del supporto di Hyper-V in Azure Migrate, è possibile usare Azure Migrate per la pianificazione della migrazione dei carichi di lavoro di Hyper-V.

**Ripristino di emergenza da VMware/Hyper-V in Azure**: se si intende eseguire il ripristino di emergenza in Azure mediante Azure Site Recovery (ASR), usare Azure Site Recovery Deployment Planner per la pianificazione del ripristino di emergenza. Azure Site Recovery Deployment Planner esegue una valutazione ASR specifica e completa dell'ambiente locale. Fornisce gli elementi consigliati necessarie per l'esito positivo delle operazioni di ripristino di emergenza di ASR, ad esempio, replica, failover delle macchine virtuali.  

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate richiede che vCenter Server individui un ambiente VMware?

Sì, Azure Migrate richiede che vCenter Server individui un ambiente VMware. Non supporta l'individuazione di host ESXi non gestiti da un server vCenter.

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
  - Velocità effettiva scrittura su disco
  - Operazioni di lettura da disco al secondo
  - Operazioni di scrittura su disco al secondo
- Per ogni scheda di rete collegata alla macchina virtuale:
  - Rete in ingresso
  - Rete in uscita

L'individuazione basata su agenti è un'opzione disponibile oltre all'individuazione basata su appliance e consente ai clienti di [visualizzare le dipendenze](how-to-create-group-machine-dependencies.md) delle macchine virtuali locali. Gli agenti di dipendenze raccolgono dettagli quali, FQDN, sistema operativo, indirizzo IP, indirizzo MAC, processi in esecuzione all'interno della macchina virtuale e connessioni TCP in ingresso/in uscita dalla macchina virtuale. L'individuazione basata su agenti è facoltativa ed è possibile scegliere di non installare gli agenti se non si desidera visualizzare le dipendenze delle macchine virtuali.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Dove vengono archiviati i dati raccolti e per quanto tempo?

I dati raccolti dall'appliance dell'agente di raccolta vengono archiviati nel percorso di Azure che si specifica durante la creazione del progetto di migrazione. I dati vengono archiviati in modo sicuro in una sottoscrizione Microsoft e vengono eliminati quando l'utente elimina il progetto Azure Migrate.

Per la visualizzazione delle dipendenze, se si installano gli agenti nelle macchine virtuali, i dati raccolti dagli agenti di dipendenza vengono archiviati negli Stati Uniti in un'area di lavoro OMS creata nella sottoscrizione dell'utente. Tali dati vengono eliminati dopo che l'utente elimina l'area di lavoro OMS nella propria sottoscrizione. [Altre informazioni](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>In che modo l'agente di raccolta comunica con il server vCenter e il servizio Azure Migrate?

L'appliance dell'agente di raccolta si connette al server vCenter (porta 443) usando le credenziali fornite dall'utente nell'appliance. Viene eseguita una query sul server vCenter mediante VMware PowerCLI per raccogliere i metadati relativi alle macchine virtuali gestite dal server vCenter. Vengono raccolti i dati di configurazione relativi alle macchine virtuali (core, memoria, dischi, scheda di interfaccia di rete e così via) e la cronologia delle prestazioni di ogni macchina virtuale per l'ultimo mese dal server vCenter. I metadati raccolti vengono quindi inviati al servizio Azure Migrate (su Internet tramite https) per la valutazione. [Altre informazioni](concepts-collector.md)

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>I dati inattivi e in transito vengono crittografati?

Sì, i dati inattivi e in transito raccolti vengono crittografati. I metadati raccolti dall'appliance vengono inviati in modo sicuro al servizio Azure Migrate su Internet tramite https. I metadati raccolti vengono archiviati [Cosmos DB ](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) e in [Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) in una sottoscrizione Microsoft e vengono crittografati a riposo.

I dati raccolti dagli agenti di dipendenze vengono crittografati anche in transito (canale https sicuro) e vengono archiviati in un'area di lavoro di Log Analytics nella sottoscrizione dell'utente. Vengono crittografati anche se inattivi.

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
