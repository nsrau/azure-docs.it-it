---
title: Concetti-interconnettività di rete
description: Informazioni sugli aspetti chiave e i casi d'uso di rete e interconnettività in Azure VMware Solution (AVS)
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 6f1f1f5a089781f1f7e882c9c8692f0c845ae485
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214110"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Concetti relativi alla rete e all'interconnettività della soluzione Azure VMware in anteprima

L'interconnettività di rete tra i cloud privati della soluzione VMware di Azure (AVS) e gli ambienti locali o le reti virtuali in Azure consente di accedere e usare il cloud privato. In questo articolo verranno illustrati alcuni concetti chiave che definiscono le basi per la rete e l'interconnettività.

Una prospettiva utile sull'interconnettività consiste nel considerare i due tipi di implementazioni di cloud privato AVS:

1. L' [**interconnettività di base di Azure**](#azure-virtual-network-interconnectivity) consente di gestire e usare il cloud privato con una sola rete virtuale in Azure. Questa implementazione è più adatta per le valutazioni AVS o le implementazioni che non richiedono l'accesso da ambienti locali.

1. L' [**interconnettività completa da locale a cloud privato**](#on-premises-interconnectivity) estende l'implementazione di base di Azure per includere l'interconnettività tra cloud privati locali e AVS.
 
È possibile trovare altre informazioni sui requisiti e i due tipi di implementazioni di interconnettività del cloud privato AVS descritti nelle sezioni riportate di seguito.

## <a name="avs-private-cloud-use-cases"></a>Casi d'uso del cloud privato AVS

I casi d'uso per i cloud privati AVS includono:
- nuovi carichi di lavoro delle macchine virtuali VMware nel cloud
- Aumento del carico di lavoro della macchina virtuale nel cloud (solo da locale ad AVS)
- Migrazione del carico di lavoro della macchina virtuale nel cloud (solo da locale ad AVS)
- ripristino di emergenza (AVS per AVS o da locale a AVS)
- uso dei servizi di Azure

 Tutti i casi d'uso per il servizio AVS sono abilitati con la connettività da sito locale a cloud privato. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>Requisiti del circuito ExpressRoute e della rete virtuale
 
Quando si crea una connessione da una rete virtuale nella sottoscrizione, il circuito ExpressRoute viene stabilito tramite peering, usa una chiave di autorizzazione e un ID peering che si richiede nell'portale di Azure. Il peering è una connessione privata, uno-a-uno tra il cloud privato e la rete virtuale.

> [!NOTE] 
> Il circuito ExpressRoute non fa parte di una distribuzione di cloud privato. Il circuito ExpressRoute locale esula dall'ambito di questo documento. Se è necessaria la connettività locale al cloud privato, è possibile usare uno dei circuiti ExpressRoute esistenti o acquistarne uno nel portale di Azure.

Quando si distribuisce un cloud privato, si ricevono indirizzi IP per vCenter e NSX-T Manager. Per accedere a tali interfacce di gestione, è necessario creare risorse aggiuntive in una rete virtuale nella sottoscrizione. È possibile trovare le procedure per la creazione di tali risorse e la definizione del peering privato di ExpressRoute nelle esercitazioni.

La rete logica del cloud privato viene fornita con un pre-provisioning di NSX-T. Viene eseguito il pre-provisioning di un gateway di livello 0 e di un gateway di primo livello. È possibile creare un segmento e collegarlo al gateway di primo livello esistente o collegarlo a un nuovo gateway di primo livello definito dall'utente. I componenti di rete logica NSX-T forniscono la connettività East-West tra i carichi di lavoro e forniscono anche la connettività Nord-Sud a Internet e ai servizi di Azure. 

## <a name="routing-and-subnet-requirements"></a>Requisiti di routing e subnet

Il routing è Border Gateway Protocol (BGP), che viene automaticamente sottoposta a provisioning e abilitato per impostazione predefinita per ogni distribuzione di cloud privato. Per i cloud privati AVS, è necessario pianificare gli spazi di indirizzi di rete del cloud privato con una lunghezza minima di/22 blocchi di indirizzi di rete CIDR per le subnet, come illustrato nella tabella seguente. Il blocco di indirizzi non deve sovrapporsi ai blocchi di indirizzi usati in altre reti virtuali presenti nella sottoscrizione e nelle reti locali. All'interno di questo blocco di indirizzi, viene effettuato automaticamente il provisioning delle reti di gestione, provisioning e vMotion.

Esempio di blocco di indirizzi di rete `/22` CIDR: `10.10.0.0/22`

Le subnet:

| Utilizzo di rete             | Subnet | Esempio        |
| ------------------------- | ------ | -------------- |
| Gestione del cloud privato  | `/24`  | `10.10.0.0/24` |
| Rete vMotion           | `/24`  | `10.10.1.0/24` |
| Carichi di lavoro della VM              | `/24`  | `10.10.2.0/24` |
| Peering ExpressRoute      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Interconnettività rete virtuale di Azure

Nell'implementazione della rete virtuale per il cloud privato è possibile gestire il cloud privato AVS, utilizzare i carichi di lavoro nel cloud privato e accedere ai servizi di Azure tramite la connessione ExpressRoute. 

Il diagramma seguente illustra l'interconnettività di rete di base stabilita al momento della distribuzione di un cloud privato. Mostra la rete logica basata su ExpressRoute tra una rete virtuale in Azure e un cloud privato. L'interconnettività soddisfa tre casi d'uso principali:
* Accesso in ingresso al server vCenter e alla gestione NSX-T accessibili dalle macchine virtuali nella sottoscrizione di Azure e non dai sistemi locali. 
* Accesso in uscita dalle macchine virtuali ai servizi di Azure. 
* Accesso in ingresso e utilizzo dei carichi di lavoro che eseguono un cloud privato.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Connettività da rete virtuale di base a cloud privato" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconnettività locale

Nella rete virtuale e nell'implementazione da locale a cloud privato completo è possibile accedere ai cloud privati AVS da ambienti locali. Questa implementazione è un'estensione dell'implementazione di base descritta nella sezione precedente. Analogamente all'implementazione di base, è necessario un circuito ExpressRoute, ma con questa implementazione viene usato per connettersi da ambienti locali al cloud privato in Azure. 

Il diagramma seguente illustra l'interconnettività da locale a cloud privato, che consente i casi d'uso seguenti:
* Cross-vCenter a caldo/freddo, vMotion
* Accesso a gestione cloud privato da sito locale ad AVS

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Rete virtuale e connettività cloud privata completa locale" border="false":::

Per l'interconnettività completa con il cloud privato, abilitare ExpressRoute Copertura globale e quindi richiedere una chiave di autorizzazione e un ID di peering privato per Copertura globale nel portale di Azure. La chiave di autorizzazione e l'ID del peering vengono usati per stabilire Copertura globale tra un circuito ExpressRoute nella sottoscrizione e il circuito ExpressRoute per il nuovo cloud privato. Una volta collegati, i due circuiti ExpressRoute instradano il traffico di rete tra gli ambienti locali al cloud privato.  Vedere l' [esercitazione per la creazione di un ExpressRoute copertura globale peering a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md) per le procedure per richiedere e usare la chiave di autorizzazione e l'ID del peering.


## <a name="next-steps"></a>Passaggi successivi 

Il passaggio successivo consiste nel comprendere i [concetti relativi alla memoria cloud privata](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
