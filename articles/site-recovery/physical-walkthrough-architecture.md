---
title: Esaminare l'architettura di replica di server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante la replica di server fisici locali in Azure con il servizio Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 09c9b136-35f5-465e-8d0f-f4c5d5d9f880
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 843c3f1b54f50fe50b162ed242deab717a080830
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017

---

# <a name="step-1-review-the-architecture-for-physical-server-replication-to-azure"></a>Passaggio 1: Esaminare l'architettura per la replica di server fisici in Azure

Questo articolo illustra i componenti e i processi interessati durante la replica di server fisici Windows/Linux locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="architectural-components"></a>Componenti dell'architettura

La tabella riepiloga i componenti necessari.

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Azzurro** | Sono necessari un account di Azure, un account di archiviazione di Azure e una rete di Azure. | I dati replicati vengono archiviati nell'account di archiviazione e le VM di Azure vengono create con i dati replicati quando avviene il failover. Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Server di configurazione** | Un singolo server di gestione locale (server fisico o VM VMware) che esegue tutti i componenti locali di Site Recovery. Sono inclusi un server di configurazione, un server di elaborazione e un server di destinazione master. | Il componente server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
 **Server di elaborazione**  | Installato per impostazione predefinita nel server di configurazione. | Agisce come un gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure.<br/><br/> Il server di elaborazione gestisce anche l'installazione push del servizio Mobility nei computer protetti.<br/><br/> È possibile aggiungere altri server di elaborazione dedicati distinti per gestire volumi più elevati di traffico di replica.
 **Server master di destinazione** | Installato per impostazione predefinita nel server di configurazione locale. | Gestisce i dati di replica durante il failback da Azure.<br/><br/> Se i volumi del traffico di failback sono elevati, è possibile distribuire un server di destinazione master separato per il failback.
**Server replicati** | Il componente servizio Mobility viene installato in ogni server Windows/Linux di cui si vuole eseguire la replica. Può essere installato manualmente in ogni computer o con un'installazione push dal server di elaborazione.
**Failback** | Per il failback è necessaria un'infrastruttura VMware. Non è possibile eseguire il failback a un server fisico.


**Figura 1: Componenti da fisici ad Azure**

![Componenti](./media/physical-walkthrough-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo di replica

1. Si configura la distribuzione, inclusi i componenti locali e di Azure. Nell'insieme di credenziali di Servizi di ripristino si specificano l'origine e la destinazione della replica, si imposta il server di configurazione, si creano criteri di replica, si distribuisce il servizio Mobility, si abilita la replica e si esegue un failover di test.
2.  La replica avviene in base ai criteri di replica e viene eseguita la replica di una copia iniziale dei dati in Archiviazione di Azure.
4. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. Le modifiche rilevate vengono salvate in un file HRL.
    - I computer di replica comunicano con il server di configurazione tramite la porta HTTPS 443 in ingresso, per la gestione delle repliche.
    - I computer di replica inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 in ingresso (configurabile).
    - Il server di configurazione orchestra la gestione delle repliche con Azure tramite la porta HTTPS 443 in uscita.
    - Il server di elaborazione riceve i dati dai computer di origine, li ottimizza e li crittografa e li invia ad Archiviazione di Azure tramite la porta 443 in uscita.
    - Se si abilita la coerenza tra più VM, i computer del gruppo di replica comunicano tra loro tramite la porta 20004. La coerenza tra più VM viene usata se si raggruppano più computer in gruppi di replica che condividono punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. È utile se i computer eseguono lo stesso carico di lavoro e devono essere coerenti.
5. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#public-peering) di Azure ExpressRoute. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.

**Figura 2: Replica dell'ambiente fisico in Azure**

![Avanzato](./media/physical-walkthrough-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo di failover e failback

Dopo aver verificato che il failover di test funzioni come previsto, è possibile eseguire failover non pianificati in Azure in base alle esigenze. Quando si esegue un failover, vengono create VM di Azure dai dati replicati. Quando il sito locale primario è di nuovo disponibile, è quindi possibile effettuare il failback. Si noti che:

- Il failover pianificato non è supportato.
- È possibile effettuare il failover di un solo computer o creare [piani di ripristino](site-recovery-create-recovery-plans.md) per il failover di più computer contemporaneamente.
- Si esegue il commit di un failover per avviare l'accesso al carico di lavoro dalla VM di Azure di replica.
- Quando il sito primario è nuovamente disponibile, eseguire la replica del computer dal sito secondario a quello primario. Eseguire quindi un failover non pianificato dal sito secondario. Dopo il commit di questo failover, i dati saranno di nuovo locali e sarà necessario abilitare ancora la replica in Azure.

I componenti di failback includono:

- **Server di elaborazione temporaneo in Azure**: è necessario configurare una VM di Azure come server di elaborazione per gestire la replica da Azure. Questa VM può essere eliminata al termine del failback.
- **Connessione VPN**: è necessaria una connessione VPN (oppure Azure ExpressRoute) dalla rete di Azure al sito locale.
- **Server di destinazione master locale distinto**: il server di destinazione master locale (installato per impostazione predefinita nel server di configurazione) gestisce il failback. Se si effettua il failback di grandi volumi di traffico, è consigliabile configurare un server di destinazione master locale distinto a questo scopo.
- **Criteri di failback**: sono necessari criteri di failback. che viene creato automaticamente quando si crea il criterio di replica.
- **Infrastruttura VMware**: è necessario eseguire il failback a una macchina virtuale VMware locale. Questo significa che è necessaria un'infrastruttura VMware locale, anche se si esegue la replica di server fisici locali in Azure.

**Figura 3: Failback del server fisico**

![Failback](./media/physical-walkthrough-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 2: Esaminare i prerequisiti e le limitazioni](physical-walkthrough-prerequisites.md)

