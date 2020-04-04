---
title: Architettura di ripristino di emergenza delle macchine virtuali VMware in Azure Site Recovery
description: Questo articolo offre una panoramica dell'architettura e dei componenti usati per configurare il ripristino di emergenza di macchine virtuali VMware locali in Azure con Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 77b4dd4c0efbe6d03e64865f18c2c87614aaecb5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632518"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architettura del ripristino di emergenza da VMware ad Azure

Questo articolo descrive l'architettura e i processi usati per implementare il ripristino di emergenza con replica, failover e ripristino di macchine virtuali (VM) VMware tra un sito VMware locale e Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componenti dell'architettura

La tabella e l'immagine seguenti offrono una visualizzazione generale dei componenti usati per il ripristino di emergenza da VMware ad Azure.

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Azure** | Una sottoscrizione di Azure, un account di archiviazione di Azure per la cache, il disco gestito e la rete di Azure.An Azure subscription, Azure Storage account for cache, Managed Disk, and Azure network. | I dati replicati dalle macchine virtuali locali vengono archiviati nell'archiviazione di Azure.Replicated data from on-premises VMs is stored in Azure storage. Le macchine virtuali di Azure vengono create con i dati replicati durante l'esecuzione di un failover dal sito locale ad Azure. Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Computer server di configurazione** | Un singolo computer locale. È consigliabile eseguirlo come macchina virtuale VMware che possa essere distribuita da un modello OVF scaricato.<br/><br/> Il computer esegue tutti componenti di Site Recovery locali, tra cui il server di configurazione, il server di elaborazione e il server di destinazione master. | **Server di configurazione**: coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.<br/><br/> **Server di elaborazione**: installato per impostazione predefinita nel server di configurazione. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Archiviazione di Azure. Il server di elaborazione installa anche il servizio Mobility di Azure Site Recovery nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali locali. Con l'aumentare delle dimensioni della distribuzione, è possibile aggiungere altri server di elaborazione separati per gestire volumi più elevati di traffico di replica.<br/><br/> **Server di destinazione master**: installato per impostazione predefinita nel server di configurazione. Gestisce i dati di replica durante il failback da Azure. Per distribuzioni di grandi dimensioni, è possibile aggiungere un altro server di destinazione master separato per il failback.
**Server VMware** | Le macchine virtuali VMware sono ospitate in server vSphere ESXi locali. È consigliabile usare un server vCenter per gestire gli host. | Durante la distribuzione di Site Recovery, aggiungere i server VMware all'insieme di credenziali di Servizi di ripristino.
**Computer replicati** | Il servizio Mobility viene installato in ogni macchina virtuale VMware da replicare. | È consigliabile consentire l'installazione automatica dal server di elaborazione. In alternativa, è possibile installare il servizio manualmente o usare un metodo di distribuzione automatica, ad esempio Configuration Manager.Alternatively, you can install the service manually or use an automated deployment method, such as Configuration Manager.

**Architetture da VMware ad Azure**

![Componenti](./media/vmware-azure-architecture/arch-enhanced.png)


## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale, viene avviata la replica iniziale nell'archiviazione di Azure con i criteri di replica specificati. Tenere presente quanto segue:
    - Per le macchine virtuali VMware, la replica è a livello di blocco, quasi continua, e usa l'agente del servizio Mobility in esecuzione nella macchina virtuale.
    - Vengono applicate tutte le impostazioni dei criteri di replica:
        - **Soglia RPO**. Questa impostazione non influisce sulla replica. È utile con il monitoraggio. Viene generato un evento e, facoltativamente, viene inviato un messaggio di posta elettronica, se l'obiettivo RPO corrente supera la soglia specificata.
        - **Conservazione del punto di ripristino**. Questa impostazione specifica quanto indietro nel tempo si vuole andare quando si verifica un'interruzione. Il periodo massimo di conservazione per l'archiviazione Premium è di 24 ore. Per l'archiviazione standard è 72 ore. 
        - **Snapshot coerenti con l'app**. Lo snapshot coerente con le app può essere creato ogni 1 o 12 ore, a seconda delle esigenze dell'app. Si tratta di snapshot BLOB di Azure standard. L'agente di mobilità in esecuzione in una macchina virtuale richiede uno snapshot VSS conforme a questa impostazione e fa riferimento a quel momento come punto coerente con l'applicazione nel flusso di replica.

2. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare Azure ExpressRoute con [il peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.
3. L'operazione di replica iniziale garantisce che tutti i dati nel computer al momento dell'abilitazione della replica vengano inviati ad Azure.Initial replication operation ensures that entire data on the machine at the time of enable replication is sent to Azure. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. Le modifiche rilevate per un computer vengono inviate al server di elaborazione.
4. Le comunicazioni avvengono nel modo seguente:

    - Le macchine virtuali comunicano con il server di configurazione locale sulla porta HTTPS 443 in ingresso, per la gestione della replica.
    - Il server di configurazione orchestra la replica con Azure attraverso la porta HTTPS 443 in uscita.
    - Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
    - Il server di elaborazione riceve i dati di replica, li ottimizza e li crittografa e li invia all'archiviazione di Azure tramite la porta 443 in uscita.
5. I log dei dati di replica vengono prima eseguiti in un account di archiviazione della cache in Azure.The replication data logs first arate in a cache storage account in Azure. Questi log vengono elaborati e i dati vengono archiviati in un disco gestito di Azure (denominato disco di seed asr). I punti di ripristino vengono creati su questo disco.

**Processo di replica da VMware ad Azure**

![Processo di replica](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Processo di risincronizzazione

1. A volte, durante la replica iniziale o durante il trasferimento delle modifiche delta, possono esserci problemi di connettività di rete tra il computer di origine per elaborare il server o tra il server di elaborazione ad Azure.In times, during initial replication or while transfertransfer delta changes, there can be network connectivity issues between source machine to process server or between process server to Azure. Uno di questi può causare errori nel trasferimento dei dati in Azure momentaneamente.
2. Per evitare problemi di integrità dei dati e ridurre al minimo i costi di trasferimento dei dati, Site Recovery contrassegna un computer per la risincronizzazione.
3. Un computer può anche essere contrassegnato per la risincronizzazione in situazioni come le seguenti per mantenere la coerenza tra il computer di origine e i dati archiviati in AzureA machine can also be marked for resynchronization in situations like following to maintain consistency of between source machine and data stored in Azure
    - Se una macchina subisce forza di spegnimento
    - Se un computer subisce modifiche di configurazione come il ridimensionamento del disco (modificando la dimensione del disco da 2 TB a 4 TB)
4. La risincronizzazione invia solo dati differenziali ad Azure.Resynchronization sends only delta data to Azure. Data transfer between on-premises and Azure by minimized by computing checksums of data between source machine and data stored in Azure.
5. Per impostazione predefinita, la risincronizzazione è pianificata per l'esecuzione automatica al di fuori degli orari lavorativi. Se non si vuole attendere la risincronizzazione predefinita al di fuori degli orari di lavoro, è possibile risincronizzare una macchina virtuale manualmente, A tale scopo, passare al portale di Azure, selezionare la macchina virtuale > **Risincronizza**.
6. Se la risincronizzazione predefinita non riesce al di fuori dell'orario di ufficio ed è necessario un intervento manuale, viene generato un errore nel computer specifico nel portale di Azure.If default resynchronization fails outside office hours and a manual intervention is required, then an error is generated on the specific machine in Azure portal. È possibile risolvere l'errore e attivare la risincronizzazione manualmente.
7. Al termine della risincronizzazione, riprenderà la replica delle modifiche delta.

## <a name="failover-and-failback-process"></a>Processo di failover e failback

Dopo aver configurato la replica e aver eseguito un'analisi di ripristino di emergenza (failover di test) per verificare che funzioni tutto come previsto, è possibile eseguire il failover e il failback in base alle esigenze.

1. È possibile eseguire il failover di una singola macchina virtuale o creare piani di ripristino per eseguire contemporaneamente il failover di più macchine virtuali. Rispetto al failover di una singola macchina virtuale, un piano di ripristino offre i vantaggi seguenti:
    - È possibile modellare le dipendenze di un'app includendo tutte le macchine virtuali dell'app in un solo piano di ripristino.
    - È possibile aggiungere script e runbook di Azure e sospendere il failover per eseguire operazioni manuali.
2. Dopo aver attivato il failover iniziale, è necessario eseguirne il commit per iniziare ad accedere al carico di lavoro dalla macchina virtuale di Azure.
3. Quando il sito locale primario è di nuovo disponibile, è possibile eseguire le attività preliminari al failback. A tale scopo, è necessario configurare un'infrastruttura di failback con i componenti seguenti:

    * **Server di elaborazione temporaneo in Azure:** per eseguire il failback da Azure, è possibile configurare una macchina virtuale di Azure in modo che funga da server di elaborazione per gestire la replica da Azure.Temporary process server in Azure : To fail back from Azure, you set up an Azure VM to act as a process server to handle replication from Azure. Questa VM può essere eliminata al termine del failback.
    * **Connessione VPN**: per eseguire il failback, è necessaria una connessione VPN (o ExpressRoute) dalla rete di Azure al sito locale.
    * **Server di destinazione master separato:** per impostazione predefinita, il server di destinazione master installato con il server di configurazione nella macchina virtuale VMware locale gestisce il failback. Se è necessario eseguire il failback di grandi volumi di traffico, configurare un server di destinazione master locale separato a questo scopo.
    * **Criteri di failback**: per eseguire la replica nel sito locale, è necessario un criterio di failback, che viene creato automaticamente quando si creano i criteri di replica dal sito locale ad Azure.
4. Una volta predisposti i componenti, il failback avviene in tre fasi:

    - Fase 1: riproteggere le macchine virtuali di Azure in modo da eseguirne di nuovo la replica da Azure alle macchine virtuali VMware locali.
    -  Fase 2: eseguire un failover nel sito locale.
    - Fase 3: al termine del failback dei carichi di lavoro, riabilitare la replica per le macchine virtuali locali.
    
 
**Failback di VMware da Azure**

![Failback](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passaggi successivi

Seguire [questa esercitazione](vmware-azure-tutorial.md) per abilitare la replica da VMware ad Azure.
