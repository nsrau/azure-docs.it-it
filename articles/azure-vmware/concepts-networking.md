---
title: Concetti-interconnettività di rete
description: Informazioni sugli aspetti chiave e i casi d'uso di rete e interconnettività nella soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750576"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Concetti relativi alla rete e alla connettività dell'anteprima della soluzione VMware di Azure

La soluzione VMware di Azure (AVS) offre un ambiente di cloud privato VMware accessibile per gli utenti e le applicazioni da ambienti o risorse locali e basati su Azure. I servizi come Azure ExpressRoute e le connessioni VPN forniscono la connettività. Questi servizi richiedono intervalli di indirizzi di rete e porte del firewall specifici per l'abilitazione dei servizi.  

Quando si distribuisce un cloud privato, vengono create le reti private per la gestione, il provisioning e vMotion. Vengono usati per l'accesso a vCenter e NSX-T Manager e Virtual Machine vMotion o alla distribuzione. Tutte le reti private sono accessibili da un VNet in Azure o da ambienti locali. La funzionalità Copertura globale ExpressRoute viene usata per connettere i cloud privati agli ambienti locali e questa connessione richiede una rete virtuale con un circuito ExpressRoute nella sottoscrizione.

Inoltre, quando si distribuisce un cloud privato, viene effettuato il provisioning e l'accesso a Internet e ai servizi di Azure in modo che le macchine virtuali nelle reti di produzione possano utilizzarle.  Per impostazione predefinita, l'accesso a Internet è disabilitato per i nuovi cloud privati e, in qualsiasi momento, può essere abilitato o disabilitato.

Una prospettiva utile sull'interconnettività consiste nel considerare i due tipi di implementazioni di cloud privato AVS:

1. L' [**interconnettività di base di Azure**](#azure-virtual-network-interconnectivity) consente di gestire e usare il cloud privato con una sola rete virtuale in Azure. Questa implementazione è più adatta per le valutazioni AVS o le implementazioni che non richiedono l'accesso da ambienti locali.

1. L' [**interconnettività completa da locale a cloud privato**](#on-premises-interconnectivity) estende l'implementazione di base di Azure per includere l'interconnettività tra cloud privati locali e AVS.
 
In questo articolo verranno illustrati alcuni concetti chiave che stabiliscono la rete e l'interconnettività, inclusi i requisiti e le limitazioni. Verranno inoltre illustrate altre informazioni sui due tipi di implementazioni di interconnettività del cloud privato AVS. Questo articolo fornisce le informazioni necessarie per configurare la rete in modo che funzioni correttamente con AVS.

## <a name="avs-private-cloud-use-cases"></a>Casi d'uso del cloud privato AVS

I casi d'uso per i cloud privati AVS includono:
- Nuovi carichi di lavoro delle macchine virtuali VMware nel cloud
- Aumento del carico di lavoro della macchina virtuale nel cloud (solo da locale ad AVS)
- Migrazione del carico di lavoro della macchina virtuale nel cloud (solo da locale ad AVS)
- Ripristino di emergenza (AVS per AVS o da locale a AVS)
- Utilizzo di servizi di Azure

> [!TIP]
> Tutti i casi d'uso per il servizio AVS sono abilitati con la connettività da sito locale a cloud privato.

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

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Rete virtuale e connettività cloud privata completa locale" border="false":::

Per l'interconnettività completa con il cloud privato, abilitare ExpressRoute Copertura globale e quindi richiedere una chiave di autorizzazione e un ID di peering privato per Copertura globale nel portale di Azure. La chiave di autorizzazione e l'ID del peering vengono usati per stabilire Copertura globale tra un circuito ExpressRoute nella sottoscrizione e il circuito ExpressRoute per il nuovo cloud privato. Una volta collegati, i due circuiti ExpressRoute instradano il traffico di rete tra gli ambienti locali al cloud privato.  Vedere l' [esercitazione per la creazione di un ExpressRoute copertura globale peering a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md) per le procedure per richiedere e usare la chiave di autorizzazione e l'ID del peering.

## <a name="next-steps"></a>Passaggi successivi 

- Altre informazioni sui [requisiti e le considerazioni sulla connettività di rete](tutorial-network-checklist.md). 
- Informazioni sui [concetti relativi alla memoria cloud privata](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

