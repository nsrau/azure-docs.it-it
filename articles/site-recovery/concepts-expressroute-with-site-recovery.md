---
title: Informazioni sull'uso di Azure ExpressRoute con Azure Site Recovery per il ripristino di emergenza e la migrazione | Microsoft Docs
description: Questo articolo descrive come usare Azure ExpressRoute con Azure Site Recovery per il ripristino di emergenza e la migrazione.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: 97aea824fac60f8bed71971a416f12e8df0e5e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333062"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Microsoft Azure ExpressRoute con Azure Site Recovery

Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365.

Questo articolo descrive come usare Microsoft Azure ExpressRoute con Azure Site Recovery per il ripristino di emergenza e la migrazione.

## <a name="expressroute-circuits"></a>Circuiti ExpressRoute

Un circuito ExpressRoute rappresenta una connessione logica tra un'infrastruttura locale e i servizi cloud Microsoft tramite un provider di connettività. È possibile ordinare più circuiti ExpressRoute. Ciascun circuito può essere nella stessa regione o in regioni diverse, e può essere collegato ai locali tramite provider di connettività diversi. Per altre informazioni sui circuiti ExpressRoute, fare clic [qui](../expressroute/expressroute-circuit-peerings.md).

A un circuito ExpressRoute sono associati più domini di routing. Per altre informazioni e per un confronto dei domini di routing ExpressRoute, fare clic [qui](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replica da locale ad Azure con ExpressRoute

Azure Site Recovery consente il ripristino di emergenza e la migrazione in Azure per [macchine virtuali Hyper-V](hyper-v-azure-architecture.md) locali, [macchine virtuali VMware](vmware-azure-architecture.md) e [server fisici](physical-azure-architecture.md). Per tutti gli scenari da ambiente locale ad Azure, i dati di replica vengono inviati e archiviati in un account di archiviazione di Azure. Durante la replica, non vengono addebitati i costi di macchine virtuali. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure.

Site Recovery replica i dati in un account di archiviazione di Azure o in un disco gestito di replica nell'area di Azure di destinazione tramite un endpoint pubblico. Per usare ExpressRoute per il traffico di replica Site Recovery, è possibile usare il [peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) o un [peering pubblico](../expressroute/expressroute-circuit-peerings.md#publicpeering) esistente (deprecato per le nuove creazioni). Il peering Microsoft è il dominio di routing consigliato per la replica. Si noti che la replica non è supportata sul peering privato.

Verificare che siano soddisfatti anche i [requisiti di rete](vmware-azure-configuration-server-requirements.md#network-requirements) per il server di configurazione. La connettività a URL specifici è richiesta dal server di configurazione per l'orchestrazione della replica Site Recovery. Non è possibile usare ExpressRoute per questa connettività. 

Se si usa il proxy in locale e si vuole usare ExpressRoute per il traffico di replica, è necessario configurare l'elenco proxy bypass nel server di configurazione e nei server di elaborazione. Attenersi ai passaggi indicati di seguito:

- Scaricare lo strumento PsExec da [qui](https://aka.ms/PsExec) per accedere al contesto utente del sistema.
- Aprire Internet Explorer nel contesto utente del sistema eseguendo la seguente riga di comando psexec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
- Aggiungere impostazioni proxy in Internet Explorer
- Nell'elenco di bypass aggiungere l'URL di archiviazione di Azure *. blob.core.windows.net

In questo modo si garantisce che solo il traffico di replica scorra attraverso ExpressRoute mentre la comunicazione può passare attraverso il proxy.

Dopo il failover delle macchine virtuali o dei server in una rete virtuale di Azure, è possibile accedervi usando il [peering privato](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Lo scenario combinato è rappresentato nel diagramma seguente: ![Da locale ad Azure con ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replica da Azure ad Azure con ExpressRoute

Azure Site Recovery consente il ripristino di emergenza di [macchine virtuali di Azure](azure-to-azure-architecture.md). I dati di replica vengono inviati a un account di archiviazione di Azure o a un disco gestito di replica nella regione di Azure di destinazione, a seconda che le macchine virtuali di Azure macchine usino o meno [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). Anche se gli endpoint di replica sono pubblici, per impostazione predefinita il traffico di replica per la replica delle macchine virtuali di Azure non attraversa Internet, indipendentemente dall'area di Azure in cui è presente la rete virtuale di origine. È possibile eseguire l'override della route di sistema predefinita di Azure per il prefisso dell'indirizzo 0.0.0.0/0 con una [route personalizzata](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e deviare il traffico delle macchine virtuali a un'appliance virtuale di rete locale (NVA), ma questa configurazione non è consigliata per la replica Site Recovery. Se si usano route personalizzate, [creare un endpoint servizio di rete virtuale](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) nella rete virtuale per "Archiviazione" in modo che il traffico di replica non lasci il limite di Azure.

Per il ripristino di emergenza di VM di Azure, per impostazione predefinita ExpressRoute non è necessario per la replica. Dopo il failover nell'area di Azure di destinazione, è possibile accedere alle macchine virtuali tramite [peering privato](../expressroute/expressroute-circuit-peerings.md#privatepeering). Si noti che i prezzi per il trasferimento dei dati si applicano indipendentemente dalla modalità di replica dei dati tra aree di Azure.

Se si sta già usando ExpressRoute per connettersi dal data center locale alle macchine virtuali di Azure nell'area di origine, è possibile pianificare il ripristino della connettività ExpressRoute con l'area di destinazione del failover. È possibile usare lo stesso circuito ExpressRoute per connettersi all'area di destinazione tramite una nuova connessione di rete virtuale o si possono usare un circuito ExpressRoute e una connessione distinti per il ripristino di emergenza. I diversi scenari possibili sono descritti [qui](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

È possibile replicare macchine virtuali di Azure in qualsiasi area di Azure all'interno dello stesso cluster geografico, come descritto in dettaglio [qui](../site-recovery/azure-to-azure-support-matrix.md#region-support). Se l'area di Azure di destinazione scelta non si trova nella stessa area geopolitica dell'origine, può essere necessario abilitare ExpressRoute Premium. Per altre informazioni, vedere le [località](../expressroute/expressroute-locations.md) e i [prezzi di ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [circuiti di ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Altre informazioni sui [domini di routing di ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Altre informazioni sulle [località di ExpressRoute](../expressroute/expressroute-locations.md).
- Scopri di più sul ripristino di emergenza di [macchine virtuali di Azure con ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
