---
title: Architettura della replica da VMware ad Azure in Azure Site Recovery | Microsoft Docs
description: Questo articolo offre una panoramica dell'architettura e dei componenti usati per la replica di macchine virtuali VMware locali in Azure con Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: c1aa89f14edab7d0e560c20d6bc48480aff1631f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="vmware-to-azure-replication-architecture"></a>Architettura della replica da VMware ad Azure

Questo articolo descrive l'architettura e i processi usati per la replica, il failover e il ripristino di macchine virtuali (VM) VMware tra un sito VMware locale e Azure usando [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componenti dell'architettura

La tabella e il grafico seguenti offrono una visualizzazione generale dei componenti usati per la replica VMware in Azure.

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Azure** | Una sottoscrizione di Azure, un account di archiviazione di Azure e una rete di Azure. | I dati replicati da macchine virtuali locali vengono archiviati nell'account di archiviazione. Le macchine virtuali di Azure vengono create con i dati replicati durante l'esecuzione di un failover dal sito locale ad Azure. Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Computer server di configurazione** | Un singolo computer locale. È consigliabile eseguirlo come macchina virtuale VMware che possa essere distribuita da un modello OVF scaricato.<br/><br/> Il computer esegue tutti componenti di Site Recovery locali, tra cui il server di configurazione, il server di elaborazione e il server di destinazione master. | **Server di configurazione**: coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.<br/><br/> **Server di elaborazione**: installato per impostazione predefinita nel server di configurazione. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Archiviazione di Azure. Il server di elaborazione installa anche il servizio Mobility di Azure Site Recovery nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali locali. Con l'aumentare delle dimensioni della distribuzione, è possibile aggiungere altri server di elaborazione separati per gestire volumi più elevati di traffico di replica.<br/><br/> **Server di destinazione master**: installato per impostazione predefinita nel server di configurazione. Gestisce i dati di replica durante il failback da Azure. Per distribuzioni di grandi dimensioni, è possibile aggiungere un altro server di destinazione master separato per il failback.
**Server VMware** | Le macchine virtuali VMware sono ospitate in server vSphere ESXi locali. È consigliabile usare un server vCenter per gestire gli host. | Durante la distribuzione di Site Recovery, aggiungere i server VMware all'insieme di credenziali di Servizi di ripristino.
**Computer replicati** | Il servizio Mobility viene installato in ogni macchina virtuale VMware da replicare. | È consigliabile consentire l'installazione automatica dal server di elaborazione. In alternativa, è possibile installare manualmente il servizio o usare un metodo di distribuzione automatico, ad esempio System Center Configuration Manager.

**Architetture da VMware ad Azure**

![Componenti](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Procedura di configurazione

Di seguito sono indicati i principali passaggi da eseguire per configurare il ripristino di emergenza o la migrazione da VMware ad Azure:

1. **Configurare i componenti di Azure**. Sono necessari un account di Azure con le autorizzazioni appropriate, un account di archiviazione di Azure, una rete virtuale di Azure e un insieme di credenziali di Servizi di ripristino. [Altre informazioni](tutorial-prepare-azure.md).
2. **Configurare il sistema locale**. A questo scopo è necessario configurare un account sul server VMware per consentire a Site Recovery di individuare automaticamente le macchine virtuali da replicare, configurare un account che può essere usato per installare il componente del servizio Mobility nelle macchine virtuali da replicare e verificare che i server VMware e le macchine virtuali siano conformi ai prerequisiti. È anche possibile svolgere le attività preliminari per la connessione a queste macchine virtuali di Azure dopo il failover. Site Recovery replica i dati delle macchine virtuali in un account di archiviazione di Azure e crea le macchine virtuali di Azure usando questi dati quando si esegue un failover in Azure. [Altre informazioni](vmware-azure-tutorial-prepare-on-premises.md).
3. **Configurare la replica**. Scegliere l'ambiente di destinazione della replica. Configurare l'ambiente di origine impostando una singola macchina virtuale VMware locale (il server di configurazione) che esegue tutti i componenti locali di Site Recovery necessari. Al termine dell'operazione, registrare il server di configurazione nell'insieme di credenziali di Servizi di ripristino e quindi selezionare le impostazioni di destinazione. [Altre informazioni](vmware-azure-tutorial.md).
4. **Creare i criteri di replica**. Definire i criteri di replica che specificano la modalità di esecuzione della replica. 
    - **Soglia RPO**: questa impostazione di monitoraggio determina la generazione di un avviso (e, facoltativamente, di un messaggio di posta elettronica) se la replica non viene eseguita entro il tempo specificato. Se ad esempio si imposta la soglia RPO su 30 minuti e un problema impedisce l'esecuzione della replica per 30 minuti, viene generato un evento. Questa impostazione non influisce sulla replica. La replica viene eseguita in modo continuo e i punti di ripristino vengono creati a intervalli di pochi minuti.
    - **Conservazione**: questa impostazione specifica l'intervallo di tempo in cui devono essere conservati i punti di ripristino in Azure. È possibile impostare un valore tra 0 e 24 ore per l'archiviazione Premium o fino a 72 ore per l'archiviazione Standard. È possibile eseguire il failover nel punto di ripristino più recente o in un punto memorizzato se si imposta un valore maggiore di zero. Al termine del periodo di conservazione, i punti di ripristino vengono eliminati.
    - **Snapshot coerenti con l'arresto anomalo del sistema**: per impostazione predefinita, Site Recovery acquisisce snapshot coerenti con l'arresto anomalo del sistema e contemporaneamente crea punti di ripristino a intervalli di pochi minuti. Un punto di ripristino è coerente con l'arresto anomalo del sistema se tutti i componenti dati correlati sono coerenti con l'ordine di scrittura, esattamente come nell'istante in cui è stato creato il punto di ripristino. Per comprendere meglio questo concetto, è possibile pensare allo stato dei dati sul disco rigido del PC dopo un'interruzione dell'alimentazione o un evento simile. Un punto di ripristino coerente con l'arresto anomalo del sistema è in genere sufficiente se l'applicazione è stata progettata per il ripristino da un arresto anomalo senza incoerenze dei dati.
    - **Snapshot coerenti con le app**: se questo valore è diverso da zero, il servizio Mobility in esecuzione sulla macchina virtuale prova a generare snapshot e punti di ripristino coerenti con il file system. Il primo snapshot viene acquisito al termine della replica iniziale. Quelli successivi vengono acquisiti in base alla frequenza specificata. Un punto di ripristino è coerente con le applicazioni se, oltre a essere coerenti con l'ordine di scrittura, le applicazioni in esecuzione completano tutte le operazioni in corso e scaricano i relativi buffer sul disco (disattivazione delle applicazioni). I punti di ripristino coerenti con le app sono consigliati per le applicazioni di database, ad esempio SQL, Oracle ed Exchange. Se è sufficiente uno snapshot coerente con l'arresto anomalo del sistema, questo valore può essere impostato su 0.  
    - **Coerenza di più macchine virtuali**: è possibile creare, facoltativamente, un gruppo di replica. In questo modo, quando si abilita la replica, è possibile raccogliere le macchine virtuali in tale gruppo. Le macchine virtuali in un gruppo di replica vengono replicate tutte insieme e hanno punti di ripristino condivisi coerenti con l'arresto anomalo del sistema e con le app nel caso di un failover. È consigliabile usare questa opzione con cautela poiché può influire sulle prestazioni del carico di lavoro, considerato che gli snapshot devono essere raccolti in più macchine virtuali. Impostarla solo se le macchine virtuali eseguono lo stesso carico di lavoro, devono essere coerenti e hanno varianze simili. È possibile aggiungere un massimo di otto macchine virtuali a un gruppo. 
5. **Abilitare la replica delle macchine virtuali**. Abilitare infine la replica per le macchine virtuali VMware in locale. Se si è creato un account per installare il servizio Mobility e si è specificato che Site Recovery deve eseguire un'installazione push, il servizio Mobility verrà installato in ogni macchina virtuale per cui si abilita la replica. [Altre informazioni](vmware-azure-tutorial.md#enable-replication). Se si è creato un gruppo di replica per la coerenza di più macchine virtuali, è possibile aggiungere altre macchine a tale gruppo.
6. **Eseguire un failover di test**. Al termine della configurazione, è possibile eseguire un failover di test per verificare che il failover delle macchine virtuali in Azure avvenga come previsto. [Altre informazioni](tutorial-dr-drill-azure.md).
7. **Eseguire il failover**. Se si esegue semplicemente la migrazione di macchine virtuali in Azure, è possibile eseguire un failover completo a questo scopo. Se si configura il ripristino di emergenza, è possibile eseguire un failover completo, in base alle esigenze. Per il ripristino di emergenza completo, dopo il failover in Azure è possibile eseguire il failback nel sito locale, come e quando è disponibile. [Altre informazioni](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale, la replica viene avviata in conformità ai criteri di replica. 
2. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare Azure ExpressRoute con il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.
3. Una copia iniziale dei dati della macchina virtuale viene replicata in Archiviazione di Azure.
4. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. Le modifiche rilevate vengono salvate in un file HRL.
5. Le comunicazioni avvengono nel modo seguente:

    - Le macchine virtuali comunicano con il server di configurazione locale sulla porta HTTPS 443 in ingresso, per la gestione della replica.
    - Il server di configurazione orchestra la replica con Azure attraverso la porta HTTPS 443 in uscita.
    - Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
    - Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.


**Processo di replica da VMware ad Azure**

![Processo di replica](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo di failover e failback

Dopo aver configurato la replica e aver eseguito un'analisi di ripristino di emergenza (failover di test) per verificare che funzioni tutto come previsto, è possibile eseguire il failover e il failback in base alle esigenze.

1. È possibile eseguire il failover di una singola macchina virtuale o creare piani di ripristino per eseguire contemporaneamente il failover di più macchine virtuali. Rispetto al failover di una singola macchina virtuale, un piano di ripristino offre i vantaggi seguenti:
    - È possibile modellare le dipendenze di un'app includendo tutte le macchine virtuali dell'app in un solo piano di ripristino.
    - È possibile aggiungere script e runbook di Azure e sospendere il failover per eseguire operazioni manuali.
2. Dopo aver attivato il failover iniziale, è necessario eseguirne il commit per iniziare ad accedere al carico di lavoro dalla macchina virtuale di Azure.
3. Quando il sito locale primario è di nuovo disponibile, è possibile eseguire le attività preliminari al failback. A tale scopo, è necessario configurare un'infrastruttura di failback con i componenti seguenti:

    * **Server di elaborazione temporaneo in Azure**: per eseguire il failback da Azure, configurare una macchina virtuale di Azure perché funga da server di elaborazione per gestire la replica da Azure. Questa VM può essere eliminata al termine del failback.
    * **Connessione VPN**: per eseguire il failback, è necessaria una connessione VPN (o ExpressRoute) dalla rete di Azure al sito locale.
    * **Server di destinazione master separato**: per impostazione predefinita, il server di destinazione master installato con il server di configurazione nella macchina virtuale VMware locale gestisce il failback. Se è necessario eseguire il failback di grandi volumi di traffico, configurare un server di destinazione master locale separato a questo scopo.
    * **Criteri di failback**: per eseguire la replica nel sito locale, è necessario un criterio di failback, che viene creato automaticamente quando si creano i criteri di replica dal sito locale ad Azure.
4. Una volta predisposti i componenti, il failback avviene in tre fasi:

    - Fase 1: riproteggere le macchine virtuali di Azure in modo da eseguirne di nuovo la replica da Azure alle macchine virtuali VMware locali.
    -  Fase 2: eseguire un failover nel sito locale.
    - Fase 3: al termine del failback dei carichi di lavoro, riabilitare la replica per le macchine virtuali locali.
    
 
**Failback di VMware da Azure**

![Failback](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passaggi successivi

Seguire [questa esercitazione](vmware-azure-tutorial.md) per abilitare la replica da VMware ad Azure.
