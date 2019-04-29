---
title: Informazioni sull'uso di Azure ExpressRoute con Azure Site Recovery per il ripristino di emergenza e la migrazione | Microsoft Docs
description: Questo articolo descrive come usare Azure ExpressRoute con Azure Site Recovery per il ripristino di emergenza e la migrazione.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/18/2019
ms.author: mayg
ms.openlocfilehash: bf4cce8a224db81b8db7fae6a69b8b578bb3d47a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772302"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Microsoft Azure ExpressRoute con Azure Site Recovery

Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365.

Questo articolo descrive come usare Microsoft Azure ExpressRoute con Azure Site Recovery per il ripristino di emergenza e la migrazione.

## <a name="expressroute-circuits"></a>Circuiti ExpressRoute

Un circuito ExpressRoute rappresenta una connessione logica tra un'infrastruttura locale e i servizi cloud Microsoft tramite un provider di connettività. È possibile ordinare più circuiti ExpressRoute. Ciascun circuito può essere nella stessa regione o in regioni diverse, e può essere collegato ai locali tramite provider di connettività diversi. Per altre informazioni sui circuiti ExpressRoute, fare clic [qui](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Domini di routing ExpressRoute

A un circuito ExpressRoute sono associati più domini di routing:
-   [Peering privato di Azure](../expressroute/expressroute-circuit-peerings.md#privatepeering) - I servizi di calcolo di Azure, ovvero le macchine virtuali (IaaS) e i servizi cloud (PaaS), che sono distribuiti all'interno di una rete virtuale possono essere connessi tramite il dominio di peering privato. Il dominio di peering privato viene considerato un'estensione attendibile della rete di base in Microsoft Azure.
-   [Peering pubblico di Azure](../expressroute/expressroute-circuit-peerings.md#publicpeering) - I servizi quali database SQL, Siti Web e Archiviazione di Azure vengono offerti su indirizzi IP pubblici. È possibile connettersi privatamente ai servizi ospitati su indirizzi IP pubblici, inclusi gli indirizzi VIP dei servizi cloud, tramite il dominio di routing di peering pubblico. Il peering pubblico è stato deprecato per le nuove creazioni, pertanto per i servizi PaaS di Azure occorre usare il peering Microsoft.
-   [Peering Microsoft ](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) - La connettività verso Microsoft Online Services (i servizi Office 365, Dynamics 365 e Azure PaaS) viene attivata tramite peering Microsoft. Il peering Microsoft è il dominio di routing consigliato per la connessione ai servizi PaaS di Azure.

Per altre informazioni e per un confronto dei domini di routing ExpressRoute, fare clic [qui](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replica da locale ad Azure con ExpressRoute

Azure Site Recovery consente il ripristino di emergenza e la migrazione in Azure per [macchine virtuali Hyper-V](hyper-v-azure-architecture.md) locali, [macchine virtuali VMware](vmware-azure-architecture.md) e [server fisici](physical-azure-architecture.md). Per tutti gli scenari da ambiente locale ad Azure, i dati di replica vengono inviati e archiviati in un account di archiviazione di Azure. Durante la replica, non vengono addebitati i costi di macchine virtuali. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure.

Site Recovery replica i dati in un account di archiviazione di Azure su un endpoint pubblico. Per usare ExpressRoute per la replica di Site Recovery, è possibile usare [peering pubblico](../expressroute/expressroute-circuit-peerings.md#publicpeering) (deprecato per le nuove creazioni) o [peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Il peering Microsoft è il dominio di routing consigliato per la replica. Verificare che i [requisiti di rete](vmware-azure-configuration-server-requirements.md#network-requirements) siano soddisfatti anche per la replica. Dopo il failover delle macchine virtuali o dei server in una rete virtuale di Azure, è possibile accedervi usando il [peering privato](../expressroute/expressroute-circuit-peerings.md#privatepeering). La replica non è supportata su peering privato.

Nel caso in cui si utilizza proxy in locale e si vuole usare ExpressRoute per il traffico di replica, è necessario configurare l'elenco Proxy da ignorare nel Server di configurazione e i server di elaborazione. Attenersi ai passaggi indicati di seguito:

- Scaricare strumento PsExec [qui](https://aka.ms/PsExec) accedere contesto utente del sistema.
- Aprire Internet Explorer nel contesto utente di sistema eseguendo la seguente riga di comando psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Aggiungere le impostazioni del proxy in Internet Explorer
- Nell'elenco di esclusione, aggiungere l'URL di archiviazione di Azure *. blob.core.windows.net

Ciò garantisce che solo il traffico di replica passa attraverso ExpressRoute, mentre la comunicazione può attraversare i proxy.

Lo scenario combinato è rappresentato nel diagramma seguente: ![Dall'ambiente locale ad Azure con ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replica da Azure ad Azure con ExpressRoute

Azure Site Recovery consente il ripristino di emergenza di [macchine virtuali di Azure](azure-to-azure-architecture.md). I dati di replica vengono inviati a un account di archiviazione di Azure o a un disco gestito di replica nella regione di Azure di destinazione, a seconda che le macchine virtuali di Azure macchine usino o meno [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). Anche se gli endpoint di replica sono pubblici, per impostazione predefinita il traffico di replica per la replica di VM di Azure non attraversa Internet, indipendentemente dall'area di Azure in cui è presente la rete virtuale di origine. È possibile eseguire l'override della route di sistema predefinita di Azure per il prefisso dell'indirizzo 0.0.0.0/0 con una [route personalizzata](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e deviare il traffico delle macchine virtuali a un'appliance virtuale di rete locale (NVA), ma questa configurazione non è consigliata per la replica Site Recovery. Se si usano route personalizzate, [creare un endpoint servizio di rete virtuale](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) nella rete virtuale per "Archiviazione" in modo che il traffico di replica non lasci il limite di Azure.

Per il ripristino di emergenza di VM di Azure, per impostazione predefinita ExpressRoute non è necessario per la replica. Dopo il failover delle macchine virtuali all'area di Azure di destinazione, è possibile accedervi tramite [peering privato](../expressroute/expressroute-circuit-peerings.md#privatepeering).

Se si sta già usando ExpressRoute per connettersi dal data center locale alle macchine virtuali di Azure nell'area di origine, è possibile pianificare il ripristino della connettività ExpressRoute con l'area di destinazione del failover. È possibile usare lo stesso circuito ExpressRoute per connettersi all'area di destinazione tramite una nuova connessione di rete virtuale o si possono usare un circuito ExpressRoute e una connessione distinti per il ripristino di emergenza. I diversi scenari possibili sono descritti [qui](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

È possibile replicare macchine virtuali di Azure in qualsiasi area di Azure all'interno dello stesso cluster geografico, come descritto in dettaglio [qui](../site-recovery/azure-to-azure-support-matrix.md#region-support). Se l'area di Azure di destinazione scelta non si trova nella stessa area geopolitica dell'origine, può essere necessario abilitare ExpressRoute Premium. Per altre informazioni, vedere le [località](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e i [prezzi di ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [circuiti di ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Altre informazioni sui [domini di routing di ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Altre informazioni sulle [località di ExpressRoute](../expressroute/expressroute-locations.md).
- Altre informazioni sul ripristino di emergenza [macchine virtuali di Azure con ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
