---
title: Architettura del ripristino di emergenza del server fisico in Azure Site Recovery
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante il ripristino di emergenza di server fisici locali in Azure con il servizio Azure Site Recovery.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: f2184654a8169cb353fb40fa76f0a7fe9b3df6f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87422658"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architettura del ripristino di emergenza da server fisici ad Azure

Questo articolo descrive l'architettura e i processi usati per la replica, il failover e il ripristino di server fisici Windows e Linux tra un sito locale e Azure, usando il servizio [Azure Site Recovery](site-recovery-overview.md).

## <a name="architectural-components"></a>Componenti dell'architettura

La tabella e il grafico seguenti offrono una visualizzazione di alto livello dei componenti usati per la replica di server fisici in Azure.

| **Componente** | **Requisito** | **Dettagli** |
| --- | --- | --- |
| **Azure** | Una sottoscrizione di Azure e una rete di Azure. | I dati replicati da computer fisici locali vengono archiviati in Azure Managed Disks. Le macchine virtuali di Azure vengono create con i dati replicati durante l'esecuzione di un failover dal sito locale ad Azure. Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create. |
| **Server di elaborazione** | Installato per impostazione predefinita insieme al server di configurazione. | Agisce come un gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure.<br/><br/> Il server di elaborazione installa anche il servizio Mobility nei server di cui si vuole eseguire la replica.<br/><br/> Con l'aumentare delle dimensioni della distribuzione, è possibile aggiungere altri server di elaborazione separati per gestire volumi più elevati di traffico di replica. |
| **Server master di destinazione** | Installato per impostazione predefinita insieme al server di configurazione. | Gestisce i dati di replica durante il failback da Azure.<br/><br/> Per distribuzioni di grandi dimensioni, è possibile aggiungere un altro server di destinazione master separato per il failback. |
| **Server replicati** | Il servizio Mobility viene installato in ogni server di cui si esegue la replica. | È consigliabile consentire l'installazione automatica dal server di elaborazione. In alternativa, è possibile installare manualmente il servizio o usare un metodo di distribuzione automatizzato, ad esempio Configuration Manager. |

**Architettura della replica dall'ambiente fisico ad Azure**

![Componenti](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Configurare la connettività di rete in uscita

Affinché Site Recovery funzioni come previsto, è necessario modificare la connettività di rete in uscita per consentire la replica dell'ambiente.

> [!NOTE]
> Site Recovery non supporta l'uso di un proxy di autenticazione per controllare la connettività di rete.

### <a name="outbound-connectivity-for-urls"></a>Connettività in uscita per gli URL

Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi URL:

| **Nome**                  | **Commerciale**                               | **Enti pubblici**                                 | **Descrizione** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Archiviazione                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`               | Consente la scrittura di dati dalla macchina virtuale nell'account di archiviazione della cache all'area di origine. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornisce l'autenticazione e l'autorizzazione per gli URL del servizio Site Recovery. |
| Replica               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Consente alla macchina virtuale di comunicare con il servizio Site Recovery. |
| Bus di servizio               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Consente alla macchina virtuale di scrivere i dati di diagnostica e monitoraggio di Site Recovery. |


## <a name="replication-process"></a>Processo di replica

1. Si configura la distribuzione, inclusi i componenti locali e di Azure. Nell'insieme di credenziali di Servizi di ripristino è necessario specificare l'origine e la destinazione della replica, impostare il server di configurazione, creare criteri di replica e abilitare la replica.
1. I computer vengono replicati usando i criteri di replica e una copia iniziale dei dati del server viene replicata in archiviazione di Azure.
1. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. Le modifiche rilevate per un computer vengono mantenute in un file con estensione _HRL_ .
   - I computer comunicano con il server di configurazione sulla porta HTTPS 443 in ingresso, per la gestione della replica.
   - I computer inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 in ingresso (possono essere modificati).
   - Il server di configurazione orchestra la gestione della replica con Azure tramite la porta HTTPS 443 in uscita.
   - Il server di elaborazione riceve i dati dai computer di origine, li ottimizza e li crittografa e li invia ad archiviazione di Azure tramite la porta HTTPS 443 in uscita.
   - Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004. La coerenza tra più VM viene usata se si raggruppano più computer in gruppi di replica che condividono punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. Questi gruppi sono utili se i computer eseguono lo stesso carico di lavoro e devono essere coerenti.
1. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare il [peering pubblico](../expressroute/about-public-peering.md) di Azure ExpressRoute.

   > [!NOTE]
   > La replica non è supportata su una VPN da sito a sito da un sito locale o dal [peering privato](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)di Azure ExpressRoute.

**Processo di replica dall'ambiente fisico ad Azure**

![Processo di replica](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo di failover e failback

Dopo aver configurato la replica, è possibile eseguire un'esercitazione sul ripristino di emergenza (failover di test) per verificare che tutto funzioni come previsto. Quindi, è possibile eseguire il failover e il failback in base alle esigenze. Prendere in considerazione quanto segue:

- Il failover pianificato non è supportato.
- È necessario eseguire il failback a una macchina virtuale VMware locale. È necessaria un'infrastruttura VMware locale, anche quando si esegue la replica di server fisici locali in Azure.
- È possibile eseguire il failover di un solo computer o creare piani di ripristino per il failover di più computer insieme.
- Quando si esegue un failover, le macchine virtuali di Azure vengono create dai dati replicati nella risorsa di archiviazione di Azure.
- Dopo l'attivazione del failover iniziale, è necessario eseguirne il commit per avviare l'accesso al carico di lavoro dalla macchina virtuale di Azure.
- Quando il sito locale primario è di nuovo disponibile, è possibile effettuare il failback.
- Configurare un'infrastruttura di failback che includa:
  - **Server di elaborazione temporaneo in Azure**: per eseguire il failback da Azure, configurare una macchina virtuale di Azure perché funga da server di elaborazione per gestire la replica da Azure. È possibile eliminare questa macchina virtuale dopo il completamento del failback.
  - **Connessione VPN**: per eseguire il failback, è necessaria una connessione VPN (o Azure ExpressRoute) dalla rete di Azure al sito locale.
  - **Server di destinazione master separato**: per impostazione predefinita, il failback viene gestito dal server di destinazione master installato con il server di configurazione nella macchina virtuale VMware locale. Se è necessario eseguire il failback di grandi volumi di traffico, è consigliabile configurare un server di destinazione master locale separato.
  - **Criteri di failback**: per eseguire la replica nel sito locale, è necessario un criterio di failback, Il criterio è stato creato automaticamente durante la creazione dei criteri di replica da locale ad Azure.
  - **Infrastruttura VMware**: per eseguire il failback, è necessaria un'infrastruttura VMware. Non è possibile eseguire il failback a un server fisico.
- Una volta attivati i componenti, il failback avviene in tre fasi:
  - **Fase 1**: riproteggere le macchine virtuali di Azure in modo che vengano replicate da Azure alle macchine virtuali VMware locali.
  - **Fase 2**: eseguire un failover nel sito locale.
  - **Fase 3**: dopo aver eseguito il failback dei carichi di lavoro, riabilitare la replica.

**Failback di VMware da Azure**

![Failback](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Passaggi successivi

Per configurare il ripristino di emergenza per i server fisici in Azure, vedere la [Guida alle procedure](physical-azure-disaster-recovery.md).
