---
title: Esaminare l'architettura per la replica VMware in Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante la replica di VM VMware locali in Azure con il servizio Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27.017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 2bbab5f1ac0efe9632ad6c818504584e2503cf15
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---

# <a name="step-1-review-the-architecture-for-vmware-replication-to-azure"></a>Passaggio 1: Esaminare l'architettura per la replica VMware in Azure

Questo articolo illustra i componenti e i processi interessati durante la replica di macchine virtuali VMware locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="architectural-components"></a>Componenti dell'architettura

La tabella riepiloga i componenti necessari.

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Azzurro** | Sono necessari una sottoscrizione di Azure, un account di archiviazione di Azure e una rete di Azure. | I dati replicati vengono archiviati nell'account di archiviazione. Quando si verifica un failover dal sito locale, vengono create le VM di Azure con i dati replicati. Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Server di configurazione** | Un singolo server di gestione locale (VM VMware) che esegue tutti i componenti locali di Site Recovery per la distribuzione. Sono inclusi un server di configurazione, un server di elaborazione e un server di destinazione master. | Il componente server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
 **Server di elaborazione**  | Installato per impostazione predefinita nel server di configurazione. | Agisce come un gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure.<br/><br/> Il server di elaborazione gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle VM VMware.<br/><br/> Man mano che la distribuzione cresce, è possibile aggiungere altri server di elaborazione dedicati e distinti per gestire i volumi più elevati di traffico di replica.
 **Server master di destinazione** | Installato per impostazione predefinita nel server di configurazione locale. | Gestisce i dati di replica durante il failback da Azure.<br/><br/> Se i volumi del traffico di failback sono elevati, è possibile distribuire un server di destinazione master separato per il failback.
**Server VMware** | Le macchine virtuali VMware sono ospitate nei server vSphere ESXi e si consiglia un server vCenter per la gestione degli host. | È possibile aggiungere server VMware all'insieme di credenziali di Servizi di ripristino.
**Computer replicati** | Il servizio Mobility verrà installato in ogni VM VMware di cui viene eseguita la replica. Può essere installato manualmente in ogni computer o con un'installazione push dal server di elaborazione.

**Figura 1: Componenti da VMware ad Azure**

![Componenti](./media/vmware-walkthrough-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo di replica

1. Si configura la distribuzione, inclusi i componenti locali e di Azure. Nell'insieme di credenziali di Servizi di ripristino si specificano l'origine e la destinazione della replica, si imposta il server di configurazione, si creano criteri di replica, si distribuisce il servizio Mobility, si abilita la replica e si esegue un failover di test.
2. La replica avviene in base ai criteri di replica e viene eseguita la replica di una copia iniziale dei dati in Archiviazione di Azure.
3. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. Le modifiche rilevate vengono salvate in un file HRL.
    - I computer di replica comunicano con il server di configurazione tramite la porta HTTPS 443 in ingresso, per la gestione delle repliche.
    - I computer di replica inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 in ingresso (configurabile).
    - Il server di configurazione orchestra la gestione delle repliche con Azure tramite la porta HTTPS 443 in uscita.
    - Il server di elaborazione riceve i dati dai computer di origine, li ottimizza e li crittografa e li invia ad Archiviazione di Azure tramite la porta 443 in uscita.
    - Se si abilita la coerenza tra più VM, i computer del gruppo di replica comunicano tra loro tramite la porta 20004. La coerenza tra più VM viene usata se si raggruppano più computer in gruppi di replica che condividono punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. È utile se i computer eseguono lo stesso carico di lavoro e devono essere coerenti.
4. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) di Azure ExpressRoute. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.


**Figura 2: Replica VMware in Azure**

![Avanzato](./media/vmware-walkthrough-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo di failover e failback

1. Dopo aver verificato che il failover di test funzioni come previsto, è possibile eseguire failover non pianificati in Azure in base alle esigenze. Il failover pianificato non è supportato.
2. È possibile effettuare il failover di un solo computer o creare [piani di ripristino](site-recovery-create-recovery-plans.md) per il failover di più macchine virtuali.
3. Quando si esegue un failover, vengono create VM di replica in Azure. Si esegue il commit di un failover per avviare l'accesso al carico di lavoro dalla VM di Azure di replica.
4. Quando il sito locale primario è di nuovo disponibile, è possibile effettuare il failback. Si configura un'infrastruttura di failback, si avvia la replica del computer dal sito secondario a quello primario e si esegue un failover non pianificato dal sito secondario. Dopo il commit di questo failover, i dati saranno di nuovo locali e sarà necessario abilitare ancora la replica in Azure. [Altre informazioni](site-recovery-failback-azure-to-vmware.md)

Esistono alcuni requisiti per il failback:


- **Server di elaborazione temporaneo in Azure**: se si vuole eseguire il failback da Azure dopo il failover, è necessario impostare una VM di Azure configurata come server di elaborazione per gestire la replica da Azure. Questa VM può essere eliminata al termine del failback.
- **Connessione VPN**: per eseguire il failback è necessaria una connessione VPN, oppure Azure ExpressRoute, configurata dalla rete di Azure al sito locale.
- **Server di destinazione master locale distinto**: il server di destinazione master locale gestisce il failback. Il server di destinazione master viene installato per impostazione predefinita nel server di gestione, ma se si esegue il failback di volumi di traffico più grandi, è consigliabile configurare un server di destinazione master locale distinto a questo scopo.
- **Criteri di failback**: per eseguire la replica nel sito locale, è necessario un criterio di failback, che viene creato automaticamente quando si crea il criterio di replica.
- **Infrastruttura VMware**: è necessario eseguire il failback a una macchina virtuale VMware locale. Questo significa che è necessaria un'infrastruttura VMware locale, anche se si esegue la replica di server fisici locali in Azure.

**Figura 3: Failback VMware/fisico**

![Failback](./media/vmware-walkthrough-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 2: Esaminare i prerequisiti e le limitazioni](vmware-walkthrough-prerequisites.md)

