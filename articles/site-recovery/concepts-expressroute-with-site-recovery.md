---
title: Microsoft Azure ExpressRoute con Azure Site Recovery | Microsoft Docs
description: Descrive come usare Microsoft Azure ExpressRoute con Azure Site Recovery per il ripristino di emergenza e la migrazione
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: ac64c73ac26c610c08dceff13cbd524916853146
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916422"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Microsoft Azure ExpressRoute con Azure Site Recovery

Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365.

Questo articolo descrive come usare Microsoft Azure ExpressRoute con Azure Site Recovery per il ripristino di emergenza e la migrazione.

## <a name="expressroute-circuits"></a>Circuiti ExpressRoute

Un circuito ExpressRoute rappresenta una connessione logica tra un'infrastruttura locale e i servizi cloud Microsoft tramite un provider di connettività. È possibile ordinare più circuiti ExpressRoute. Ciascun circuito può essere nella stessa regione o in regioni diverse, e può essere collegato ai locali tramite provider di connettività diversi. Per altre informazioni sui circuiti ExpressRoute, fare clic [qui](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Domini di routing ExpressRoute

A un circuito ExpressRoute sono associati più domini di routing:
-   [Peering privato di Azure](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) - I servizi di calcolo di Azure, ovvero le macchine virtuali (IaaS) e i servizi cloud (PaaS), che sono distribuiti all'interno di una rete virtuale possono essere connessi tramite il dominio di peering privato. Il dominio di peering privato viene considerato un'estensione attendibile della rete di base in Microsoft Azure.
-   [Peering pubblico di Azure](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) - I servizi quali database SQL, Siti Web e Archiviazione di Azure vengono offerti su indirizzi IP pubblici. È possibile connettersi privatamente ai servizi ospitati su indirizzi IP pubblici, inclusi gli indirizzi VIP dei servizi cloud, tramite il dominio di routing di peering pubblico. Il peering pubblico è stato deprecato per le nuove creazioni, pertanto per i servizi PaaS di Azure occorre usare il peering Microsoft.
-   [Peering Microsoft ](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) - La connettività verso Microsoft Online Services (i servizi Office 365, Dynamics 365 e Azure PaaS) viene attivata tramite peering Microsoft. Il peering Microsoft è il dominio di routing consigliato per la connessione ai servizi PaaS di Azure.

Per altre informazioni e per un confronto dei domini di routing ExpressRoute, fare clic [qui](../expressroute/expressroute-circuit-peerings.md#routing-domain-comparison).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replica da locale ad Azure con ExpressRoute

Azure Site Recovery consente il ripristino di emergenza e la migrazione in Azure per [macchine virtuali Hyper-V](hyper-v-azure-architecture.md) locali, [macchine virtuali VMware](vmware-azure-architecture.md) e [server fisici](physical-azure-architecture.md). Per tutti gli scenari da ambiente locale ad Azure, i dati di replica vengono inviati e archiviati in un account di archiviazione di Azure. Durante la replica, non vengono addebitati i costi di macchine virtuali. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure.

Site Recovery replica i dati in un account di archiviazione di Azure su un endpoint pubblico. Per usare ExpressRoute per la replica di Site Recovery, è possibile servirsi del [peering pubblico](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) o del [peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). Il peering Microsoft è il dominio di routing consigliato per la replica. Dopo il failover delle macchine virtuali o dei server in una rete virtuale di Azure, è possibile accedervi usando il [peering privato](../expressroute/expressroute-circuit-peerings.md#azure-private-peering). La replica non è supportata su peering privato.

Lo scenario combinato è rappresentato nel diagramma seguente: ![Da locale ad Azure con ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replica da Azure ad Azure con ExpressRoute

Azure Site Recovery consente il ripristino di emergenza di [macchine virtuali di Azure](azure-to-azure-architecture.md). I dati di replica vengono inviati a un account di archiviazione di Azure o a un disco gestito di replica nella regione di Azure di destinazione, a seconda che le macchine virtuali di Azure macchine usino o meno [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). Anche se gli endpoint di replica sono pubblici, per impostazione predefinita il traffico di replica per la replica di VM di Azure non attraversa Internet, indipendentemente dall'area di Azure in cui è presente la rete virtuale di origine. È possibile eseguire l'override della route di sistema predefinita di Azure per il prefisso dell'indirizzo 0.0.0.0/0 con una [route personalizzata](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e deviare il traffico delle macchine virtuali a un'appliance virtuale di rete locale (NVA), ma questa configurazione non è consigliata per la replica Site Recovery. Se si usano route personalizzate, [creare un endpoint del servizio di rete virtuale](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) nella rete virtuale per "Archiviazione" in modo che il traffico di replica non lasci il limite di Azure.

Per il ripristino di emergenza di VM di Azure, per impostazione predefinita ExpressRoute non è necessario per la replica. Dopo il failover delle macchine virtuali all'area di Azure di destinazione, è possibile accedervi tramite [peering privato](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

Se si sta già usando ExpressRoute per connettersi dal data center locale alle macchine virtuali di Azure nell'area di origine, è possibile pianificare il ripristino della connettività ExpressRoute con l'area di destinazione del failover. È possibile usare lo stesso circuito ExpressRoute per connettersi all'area di destinazione tramite una nuova connessione di rete virtuale o si possono usare un circuito ExpressRoute e una connessione distinti per il ripristino di emergenza. I diversi scenari possibili sono descritti [qui](azure-vm-disaster-recovery-with-expressroute.md#failover-models-with-expressroute).

È possibile replicare macchine virtuali di Azure in qualsiasi area di Azure all'interno dello stesso cluster geografico, come descritto in dettaglio [qui](../site-recovery/azure-to-azure-support-matrix.md#region-support). Se l'area di Azure di destinazione scelta non si trova nella stessa area geopolitica dell'origine, può essere necessario abilitare ExpressRoute Premium. Per altre informazioni, vedere le [località](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e i [prezzi di ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [circuiti di ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Altre informazioni sui [domini di routing di ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Altre informazioni sulle [località di ExpressRoute](../expressroute/expressroute-locations.md).
- Altre informazioni sul ripristino di emergenza delle [macchine virtuali di Azure con ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
