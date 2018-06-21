---
title: Gruppi di sicurezza di rete con Azure Site Recovery | Microsoft Docs
description: Descrive come usare i gruppi di sicurezza di rete con Azure Site Recovery per il ripristino di emergenza e la migrazione
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/21/2018
ms.author: manayar
ms.openlocfilehash: 2b35578e2dc49cd47689f62fc47c5341ea8767a4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659654"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Gruppi di sicurezza di rete con Azure Site Recovery

I gruppi di sicurezza di rete consentono di limitare il traffico di rete verso le risorse in una rete virtuale. Un [gruppo di sicurezza di rete (NSG)](../virtual-network/security-overview.md#network-security-groups) contiene un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in ingresso o in uscita in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo.

In base al modello di distribuzione Azure Resource Manager, i gruppi di sicurezza di rete possono essere associati a subnet o singole interfacce di rete. Quando un gruppo di sicurezza di rete è associato a una subnet, le regole si applicano a tutte le risorse connesse alla subnet. Il traffico può essere ulteriormente limitato associando un gruppo di sicurezza di rete anche a singole interfacce di rete all'interno di una subnet che ha già un NSG associato.

Questo articolo descrive come usare i gruppi di sicurezza di rete con Azure Site Recovery.

## <a name="using-network-security-groups"></a>Uso dei gruppi di sicurezza di rete

A una singola subnet può essere associato uno o nessun gruppo di sicurezza di rete. Analogamente, a una singola interfaccia di rete può essere associato uno o nessun gruppo di sicurezza di rete. È quindi possibile disporre di una duplice ed efficace limitazione del traffico per una macchina virtuale associando un NSG a una subnet e quindi un secondo NSG all'interfaccia di rete della macchina virtuale. In questo caso, l'applicazione delle regole NSG dipende dalla direzione del traffico e dalla priorità delle regole di sicurezza applicate.

Si consideri un semplice esempio con una macchina virtuale, come descritto di seguito:
-   La macchina virtuale viene inserita all'interno della **Subnet Contoso**.
-   La **Subnet Contoso** è associata al **NSG della subnet**.
-   L'interfaccia di rete della VM è associata al **NSG della VM**.

![NSG con Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

In questo esempio, per il traffico in ingresso, il NSG della subnet viene valutato per primo. Tutto il traffico consentito tramite il NSG della subnet viene quindi valutato dal NSG della VM. Lo stesso meccanismo viene applicato in ordine inverso per il traffico in uscita, con il NSG della VM che viene valutato per primo. Tutto il traffico consentito tramite il NSG della VM viene quindi valutato dal NSG della subnet.

Questo consente l'applicazione granulare delle regole di sicurezza. Ad esempio, può essere utile consentire l'accesso a Internet in ingresso per alcune macchine virtuali dell'applicazione (ad esempio VM front-end) all'interno di una subnet, ma limitare l'accesso a Internet in ingresso per altre macchine virtuali (ad esempio database e altre macchine virtuali back-end). In questo caso si può predisporre una regola più flessibile per il NSG della subnet, che consenta il traffico Internet, e limitare l'accesso a specifiche macchine virtuali negando l'accesso tramite il NSG della VM. Lo stesso può essere applicato al traffico in uscita.

Quando si impostano configurazioni di NSG di questo tipo, assicurarsi che alle [regole di sicurezza](../virtual-network/security-overview.md#security-rules) siano applicate le priorità corrette. Le regole vengono elaborate in ordine di priorità. I numeri più bassi vengono elaborati prima di quelli più elevati perché hanno priorità più alta. Quando il traffico corrisponde a una regola, l'elaborazione viene interrotta. Di conseguenza, le regole con priorità più bassa (numeri più elevati) che hanno gli stessi attributi di regole con priorità più elevata non vengono elaborate.

È possibile che non sempre si conosca quando i gruppi di sicurezza di rete sono applicati sia a un'interfaccia di rete che a una subnet. Le regole di aggregazione applicate a un'interfaccia di rete possono essere verificate visualizzando le [regole di sicurezza effettive](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) per un'interfaccia di rete. È anche possibile usare la funzionalità di [verifica del flusso IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) in [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) per determinare se è consentita la comunicazione da o verso un'interfaccia di rete. Lo strumento indica se la comunicazione è consentita e quale regola di sicurezza di rete consente o impedisce il traffico.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Replica da sito locale ad Azure con gruppi di sicurezza di rete

Azure Site Recovery consente il ripristino di emergenza e la migrazione in Azure per [macchine virtuali Hyper-V](hyper-v-azure-architecture.md) locali, [macchine virtuali VMware](vmware-azure-architecture.md) e [server fisici](physical-azure-architecture.md). Per tutti gli scenari da ambiente locale ad Azure, i dati di replica vengono inviati e archiviati in un account di archiviazione di Azure. Durante la replica, non vengono addebitati i costi di macchine virtuali. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure.

Dopo la creazione delle macchine virtuali a seguito del failover in Azure, è possibile usare i NSG per limitare il traffico di rete verso la rete virtuale e le macchine virtuali. Site Recovery non crea NSG come parte dell'operazione di failover. È consigliabile creare i gruppi di sicurezza di Azure necessari prima di avviare il failover. Sarà quindi possibile associare automaticamente NSG alle macchine virtuali con failover durante il failover, usando script di automazione con i potenti [piani di ripristino](site-recovery-create-recovery-plans.md) di Site Recovery.

Ad esempio, se la configurazione della macchina virtuale dopo il failover è simile allo [scenario di esempio](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) descritto in precedenza:
-   È possibile creare la **VNet Contoso** e la **Subnet Contoso** come parte della pianificazione del ripristino di emergenza nell'area di Azure di destinazione.
-   Nell'ambito della stessa pianificazione del ripristino di emergenza è anche possibile creare e configurare sia il **NSG della subnet** che il **NSG della VM**.
-   Il **NSG della subnet** può quindi essere immediatamente associato alla **Subnet Contoso**, in quanto sia il gruppo di sicurezza di rete che la subnet sono già disponibili.
-   Il **NSG della VM** può essere associato alle macchine virtuali durante il failover usando piani di ripristino.

Una volta creati e configurati i NSG, è consigliabile eseguire un [failover di test](site-recovery-test-failover-to-azure.md) per verificare le associazioni dei gruppi di sicurezza controllate da script e la connettività delle macchine virtuali dopo il failover.

## <a name="azure-to-azure-replication-with-nsg"></a>Replica da Azure ad Azure con gruppi di sicurezza di rete

Azure Site Recovery consente il ripristino di emergenza di [macchine virtuali di Azure](azure-to-azure-architecture.md). Quando si abilita la replica per le macchine virtuali di Azure, Site Recovery è in grado di creare le reti virtuali di replica (incluse subnet e subnet gateway) nell'area di destinazione e creare i mapping necessari tra le reti virtuali di origine e di destinazione. È anche possibile creare precedentemente le reti e le subnet lato destinazione e usarle durante l'abilitazione della replica. Site Recovery non crea alcuna macchine virtuale nell'area di Azure di destinazione prima del [failover](azure-to-azure-tutorial-failover-failback.md).

Per la replica di VM di Azure, assicurarsi che le regole NSG nell'area di Azure di origine consentano la [connettività in uscita](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) per il traffico di replica. È anche possibile testare e verificare queste regole necessarie tramite questa [configurazione NSG di esempio](azure-to-azure-about-networking.md#example-nsg-configuration).

Site Recovery non crea né replica NSG come parte dell'operazione di failover. È consigliabile di creare i gruppi di sicurezza necessari nell'area di Azure di destinazione prima di avviare il failover. Sarà quindi possibile associare automaticamente NSG alle macchine virtuali con failover durante il failover, usando script di automazione con i potenti [piani di ripristino](site-recovery-create-recovery-plans.md) di Site Recovery.

Considerando lo [scenario di esempio](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) descritto in precedenza:
-   Site Recovery può creare repliche della **VNet Contoso** e della **Subnet Contoso** nell'area di Azure di destinazione quando per la macchina virtuale è abilitata la replica.
-   È possibile creare le repliche desiderate del **NSG della subnet** e del **NSG della VM** (denominandoli ad esempio, rispettivamente, **NSG subnet destinazione** e **NSG VM destinazione**) nell'area di Azure di destinazione, consentendo qualunque regola aggiuntiva necessaria sull'area di destinazione.
-   **NSG subnet destinazione** può quindi essere immediatamente associato alla subnet dell'area di destinazione, in quanto sia il gruppo di sicurezza di rete che la subnet sono già disponibili.
-   **NSG VM destinazione** può essere associato alle macchine virtuali durante il failover usando piani di ripristino.

Una volta creati e configurati i NSG, è consigliabile eseguire un [failover di test](azure-to-azure-tutorial-dr-drill.md) per verificare le associazioni dei gruppi di sicurezza controllate da script e la connettività delle macchine virtuali dopo il failover.

## <a name="next-steps"></a>Passaggi successivi
-   Vedere altre informazioni sui [gruppi di sicurezza di rete](../virtual-network/security-overview.md#network-security-groups).
-   Altre informazioni sulle [regole di sicurezza](../virtual-network/security-overview.md#security-rules) NSG.
-   Altre informazioni sulle [regole di sicurezza efficaci](../virtual-network/diagnose-network-traffic-filter-problem.md) per un NSG.
-   Leggere altre informazioni sui [piani di ripristino](site-recovery-create-recovery-plans.md) per automatizzare il failover delle applicazioni.
