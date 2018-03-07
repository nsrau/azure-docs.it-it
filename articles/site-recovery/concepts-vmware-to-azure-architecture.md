---
title: Architettura della replica da VMware ad Azure in Azure Site Recovery | Microsoft Docs
description: Questo articolo offre una panoramica dell'architettura e dei componenti usati per la replica di macchine virtuali VMware locali in Azure con Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: raynew
ms.openlocfilehash: 4dd9d4f5f26d70f9130f48e2bf40ce71943a6c6d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
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

![Componenti](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo di replica

1. Preparare le risorse di Azure e i componenti locali.
2. Nell'insieme di credenziali di Servizi di ripristino specificare le impostazioni della replica di origine. Come parte di questo processo, impostare il server di configurazione locale. Per distribuire questo server come macchina virtuale VMware, scaricare un modello OVF preconfigurato e importarlo in VMware per creare la macchina virtuale.
3. Specificare le impostazioni della replica di destinazione, creare criteri di replica e abilitare la replica per le macchine virtuali VMware.
4. La replica delle macchine virtuali avviene in base ai criteri di replica, eseguendo la replica di una copia iniziale dei dati delle macchine virtuali in Archiviazione di Azure.
5. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. Le modifiche rilevate vengono salvate in un file HRL.

    * I computer comunicano con il server di configurazione sulla la porta HTTPS 443 in ingresso, per la gestione delle repliche.

    * I computer inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 in ingresso (che può essere modificata).

    * Il server di configurazione orchestra la gestione delle repliche con Azure tramite la porta HTTPS 443 in uscita.

    * Il server di elaborazione riceve i dati dai computer di origine, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure tramite la porta 443 in uscita.

    * Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004. La coerenza tra più VM viene usata se si raggruppano più computer in gruppi di replica che condividono punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. Questo metodo è utile se i computer eseguono lo stesso carico di lavoro e devono essere coerenti.

6. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) di Azure ExpressRoute. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.


**Processo di replica da VMware ad Azure**

![Processo di replica](./media/concepts-vmware-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo di failover e failback

Dopo aver configurato la replica e aver eseguito un'analisi di ripristino di emergenza (failover di test) per verificare che funzioni tutto come previsto, è possibile eseguire il failover e il failback in base alle esigenze.

1. È possibile eseguire il failover di una sola macchina virtuale o creare piani di ripristino per il failover di più macchine virtuali.

2. Quando si esegue un failover, le macchine virtuali di Azure vengono create dai dati replicati nella risorsa di archiviazione di Azure.

3. Dopo aver attivato il failover iniziale, è necessario eseguirne il commit per iniziare ad accedere al carico di lavoro dalla macchina virtuale di Azure.

Quando il sito locale primario è di nuovo disponibile, è possibile effettuare il failback.
1. È necessario configurare un'infrastruttura di failback, che includa:

    * **Server di elaborazione temporaneo in Azure**: per eseguire il failback da Azure, configurare una macchina virtuale di Azure perché funga da server di elaborazione per gestire la replica da Azure. Questa VM può essere eliminata al termine del failback.

    * **Connessione VPN**: per eseguire il failback, è necessaria una connessione VPN (o ExpressRoute) dalla rete di Azure al sito locale.

    * **Server di destinazione master separato**: per impostazione predefinita, il server di destinazione master installato con il server di configurazione nella macchina virtuale VMware locale gestisce il failback. Se è necessario eseguire il failback di grandi volumi di traffico, configurare un server di destinazione master locale separato a questo scopo.

    * **Criteri di failback**: per eseguire la replica nel sito locale, è necessario un criterio di failback, che viene creato automaticamente quando si creano i criteri di replica dal sito locale ad Azure.
2. Dopo aver predisposto i componenti, il failback avviene in tre fasi:

    a. Fase 1: riproteggere le macchine virtuali di Azure in modo da eseguirne di nuovo la replica da Azure alle macchine virtuali VMware locali.

    b. Fase 2: eseguire un failover nel sito locale.

    c. Fase 3: al termine del failback dei carichi di lavoro, riabilitare la replica per le macchine virtuali locali.

**Failback di VMware da Azure**

![Failback](./media/concepts-vmware-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passaggi successivi

Seguire [questa esercitazione](tutorial-vmware-to-azure.md) per abilitare la replica da VMware ad Azure.
