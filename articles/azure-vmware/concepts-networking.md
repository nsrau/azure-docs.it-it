---
title: Concetti-interconnettività di rete
description: Informazioni sugli aspetti chiave e i casi d'uso di rete e interconnettività nella soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: f8e9ed143d53afe2f7a24c832c69390c6ffcb36b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575759"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Concetti relativi alla rete e alla connettività della soluzione VMware di Azure

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Una prospettiva utile sull'interconnettività consiste nel considerare i due tipi di implementazioni di cloud privato della soluzione VMware di Azure:

1. L' [**interconnettività di base di Azure**](#azure-virtual-network-interconnectivity) consente di gestire e usare il cloud privato con una sola rete virtuale in Azure. Questa implementazione è più adatta per le valutazioni delle soluzioni VMware di Azure o per le implementazioni che non richiedono l'accesso da ambienti locali.

1. L' [**interconnettività completa da locale a cloud privato**](#on-premises-interconnectivity) estende l'implementazione di base di Azure per includere l'interconnettività tra cloud privati della soluzione VMware locale e Azure.
 
In questo articolo verranno illustrati alcuni concetti chiave che stabiliscono la rete e l'interconnettività, inclusi i requisiti e le limitazioni. Verranno inoltre illustrate altre informazioni sui due tipi di implementazioni di interconnettività del cloud privato della soluzione VMware di Azure. Questo articolo fornisce le informazioni necessarie per configurare la rete per l'uso corretto della soluzione VMware di Azure.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Casi d'uso del cloud privato della soluzione VMware di Azure

I casi d'uso per i cloud privati della soluzione VMware di Azure includono:
- Nuovi carichi di lavoro delle macchine virtuali VMware nel cloud
- Aumento del carico di lavoro della macchina virtuale nel cloud (solo per la soluzione VMware da sito locale ad Azure)
- Migrazione del carico di lavoro della macchina virtuale nel cloud (solo per la soluzione VMware da sito locale ad Azure)
- Ripristino di emergenza (soluzione VMware di Azure alla soluzione VMware di Azure o alla soluzione VMware da sito locale ad Azure)
- Utilizzo di servizi di Azure

> [!TIP]
> Tutti i casi d'uso per il servizio della soluzione VMware di Azure sono abilitati con la connettività da sito locale a cloud privato.

## <a name="azure-virtual-network-interconnectivity"></a>Interconnettività rete virtuale di Azure

Nell'implementazione della rete virtuale per il cloud privato, è possibile gestire il cloud privato della soluzione VMware di Azure, utilizzare i carichi di lavoro nel cloud privato e accedere ai servizi di Azure tramite la connessione ExpressRoute. 

Il diagramma seguente illustra l'interconnettività di rete di base stabilita al momento della distribuzione di un cloud privato. Mostra la rete logica basata su ExpressRoute tra una rete virtuale in Azure e un cloud privato. L'interconnettività soddisfa tre casi d'uso principali:
* Accesso in ingresso al server vCenter e alla gestione NSX-T accessibili dalle macchine virtuali nella sottoscrizione di Azure e non dai sistemi locali. 
* Accesso in uscita dalle macchine virtuali ai servizi di Azure. 
* Accesso in ingresso e utilizzo dei carichi di lavoro che eseguono un cloud privato.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Connettività da rete virtuale di base a cloud privato" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconnettività locale

Nella rete virtuale e nell'implementazione da locale a cloud privato completo è possibile accedere ai cloud privati della soluzione VMware di Azure da ambienti locali. Questa implementazione è un'estensione dell'implementazione di base descritta nella sezione precedente. Analogamente all'implementazione di base, è necessario un circuito ExpressRoute, ma con questa implementazione viene usato per connettersi da ambienti locali al cloud privato in Azure. 

Il diagramma seguente illustra l'interconnettività da locale a cloud privato, che consente i casi d'uso seguenti:
* Cross-vCenter a caldo/freddo, vMotion
* Accesso alla gestione cloud privata della soluzione VMware da sito locale ad Azure

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Connettività da rete virtuale di base a cloud privato" border="false":::

Per l'interconnettività completa con il cloud privato, abilitare ExpressRoute Copertura globale e quindi richiedere una chiave di autorizzazione e un ID di peering privato per Copertura globale nel portale di Azure. La chiave di autorizzazione e l'ID del peering vengono usati per stabilire Copertura globale tra un circuito ExpressRoute nella sottoscrizione e il circuito ExpressRoute per il nuovo cloud privato. Una volta collegati, i due circuiti ExpressRoute instradano il traffico di rete tra gli ambienti locali al cloud privato.  Vedere l' [esercitazione per la creazione di un ExpressRoute copertura globale peering a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md) per le procedure per richiedere e usare la chiave di autorizzazione e l'ID del peering.



## <a name="next-steps"></a>Passaggi successivi 
Informazioni sui [concetti relativi alla memoria cloud privata](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

