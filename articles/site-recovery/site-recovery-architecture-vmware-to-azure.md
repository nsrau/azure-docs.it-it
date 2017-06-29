---
title: Funzionamento della replica VMware in Azure in Azure Site Recovery | Documentazione Microsoft
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante la replica di macchine virtuali VMware locali e di server fisici in Azure con il servizio Azure Site Recovery.
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
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: vmware-walkthrough-architecture
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 1ebf6e562c99c5113cc33bc8cb87416c663029c6
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017

---



# <a name="how-does-vmware-replication-to-azure-work-in-site-recovery"></a>Funzionamento della replica VMware in Azure in Site Recovery

Questo articolo illustra i componenti e i processi interessati durante la replica di macchine virtuali VMware locali e server fisici Windows o Linux in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

Quando si esegue la replica di server fisici locali in Azure, vengono usati gli stessi componenti e processi della replica di macchine virtuali VMware, con le differenze seguenti:

- È possibile usare un server fisico per il server di configurazione, anziché una VM VMware.
- Sarà necessaria un'infrastruttura VMware locale per il failback. Non è possibile eseguire il failback a un computer fisico.

È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="architectural-components"></a>Componenti dell'architettura

Sono diversi i componenti interessati durante la replica di macchine virtuali VMware e server fisici in Azure.

**Componente** | **Posizione** | **Dettagli**
--- | --- | ---
**Azzurro** | In Azure sono necessari un account Azure, un account di archiviazione di Azure e una rete di Azure. | I dati replicati vengono salvati nell'account di archiviazione e,quando si verifica un failover dal sito locale, vengono create VM di Azure con i dati replicati. Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Server di configurazione** | Un singolo server di gestione locale (VM VMWare) che esegue tutti i componenti locali necessari per la distribuzione, inclusi il server di configurazione, il server di elaborazione e il server master di destinazione | Il componente server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
 **Server di elaborazione**  | Installato per impostazione predefinita nel server di configurazione. | Agisce come un gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure.<br/><br/> Il server di elaborazione gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle VM VMware.<br/><br/> Man mano che la distribuzione cresce, è possibile aggiungere altri server di elaborazione dedicati e distinti per gestire i volumi più elevati di traffico di replica.
 **Server master di destinazione** | Installato per impostazione predefinita nel server di configurazione locale. | Gestisce i dati di replica durante il failback da Azure.<br/><br/> Se i volumi del traffico di failback sono elevati, è possibile distribuire un server di destinazione master separato per il failback.
**Server VMware** | Le macchine virtuali VMware sono ospitate nei server vSphere ESXi e si consiglia un server vCenter per la gestione degli host. | È possibile aggiungere server VMware all'insieme di credenziali di Servizi di ripristino.
**Computer replicati** | Il servizio Mobility verrà installato in ogni macchina virtuale VMware da replicare. Può essere installato manualmente in ogni computer o con un'installazione push dal server di elaborazione.

Informazioni sui prerequisiti di distribuzione e i requisiti per ognuno di questi componenti sono disponibili nella [matrice di supporto](site-recovery-support-matrix-to-azure.md).

**Figura 1: Componenti da VMware ad Azure**

![Componenti](./media/site-recovery-components/arch-enhanced.png)

## <a name="replication-process"></a>Processo di replica

1. Si configurano la distribuzione, inclusi i componenti di Azure, e un insieme di credenziali di Servizi di ripristino. Nell'insieme di credenziali si specificano l'origine e la destinazione della replica, si imposta il server di configurazione, si aggiungono i server VMware, si crea un criterio di replica, si distribuisce il servizio Mobility, si abilita la replica e si esegue un failover di test.
2.  I computer avviano la replica in base al criterio di replica e una copia iniziale dei dati viene replicata in Archiviazione di Azure.
4. La replica delle modifiche differenziali in Azure viene avviata al termine della replica iniziale. Le modifiche rilevate vengono salvate in un file HRL.
    - I computer di replica comunicano con il server di configurazione tramite la porta HTTPS 443 in ingresso, per la gestione delle repliche.
    - I computer di replica inviano i dati delle repliche al server di elaborazione tramite la porta HTTPS 9443 in ingresso (configurabile).
    - Il server di configurazione orchestra la gestione delle repliche con Azure tramite la porta HTTPS 443 in uscita.
    - Il server di elaborazione riceve i dati dai computer di origine, li ottimizza e li crittografa e li invia ad Archiviazione di Azure tramite la porta 443 in uscita.
    - Se si abilita la coerenza tra più VM, i computer del gruppo di replica comunicano tra loro tramite la porta 20004. La coerenza tra più VM viene usata se si raggruppano più computer in gruppi di replica che condividono punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. È utile se i computer eseguono lo stesso carico di lavoro e devono essere coerenti.
5. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#public-peering) di Azure ExpressRoute. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.

**Figura 2: Replica da VMware ad Azure**

![Avanzato](./media/site-recovery-components/v2a-architecture-henry.png)

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

![Failback](./media/site-recovery-components/enhanced-failback.png)


## <a name="next-steps"></a>Passaggi successivi

Rivedere la [matrice di supporto](site-recovery-support-matrix-to-azure.md)

