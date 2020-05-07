---
title: Concetti-interconnettività di rete
description: Informazioni sugli aspetti chiave e i casi d'uso di rete e interconnettività in Azure VMware Solution (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 82e5497c30bf02313e5ff8ad24167af569a153c2
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740915"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Concetti relativi alla rete e all'interconnettività di Azure VMware Solution (AVS) Preview

L'interconnettività di rete tra i cloud privati della soluzione VMware di Azure (AVS) e gli ambienti locali o le reti virtuali in Azure consente di accedere e usare il cloud privato. In questo articolo vengono descritti alcuni concetti chiave di rete e interconnettività che definiscono la base dell'interconnettività.

Una prospettiva utile sull'interconnettività consiste nel considerare i due tipi di implementazioni di cloud privato AVS. Le implementazioni con interconnettività solo Azure di base e le implementazioni con l'interconnettività completa da locale a cloud privato.

I casi d'uso per i cloud privati AVS includono:
- nuovi carichi di lavoro delle macchine virtuali VMware nel cloud
- Aumento del carico di lavoro della macchina virtuale nel cloud
- Migrazione del carico di lavoro della macchina virtuale nel cloud
- ripristino di emergenza
- uso dei servizi di Azure

 Tutti i casi d'uso per il servizio AVS sono abilitati con la connettività da sito locale a cloud privato. Il modello di interconnettività di base è ideale per le valutazioni AVS o per le implementazioni che non richiedono l'accesso da ambienti locali.

I due tipi di interconnettività del cloud privato AVS sono descritti nelle sezioni riportate di seguito.  L'interconnettività di base è "connettività rete virtuale di Azure" e consente di gestire e usare il cloud privato con una sola rete virtuale in Azure. L'interconnettività descritta in "connettività locale" estende la connettività di rete virtuale per includere anche l'interconnettività tra ambienti locali e cloud privati AVS.

## <a name="azure-virtual-network-interconnectivity"></a>Interconnettività rete virtuale di Azure

Il diagramma seguente illustra l'interconnettività di rete di base stabilita al momento della distribuzione di un cloud privato. Mostra la rete logica basata su ExpressRoute tra una rete virtuale in Azure e un cloud privato. L'interconnettività soddisfa tre casi d'uso principali:
- Accesso in ingresso alle reti di gestione in cui si trovano il server vCenter e la gestione NSX-T.
    - Accessibili dalle macchine virtuali all'interno della sottoscrizione di Azure, non dai sistemi locali.
- Accesso in uscita dalle macchine virtuali ai servizi di Azure.
- Accesso in ingresso e utilizzo dei carichi di lavoro che eseguono un cloud privato.

![Connettività di base da rete virtuale a cloud privato](./media/concepts/adjacency-overview-drawing-single.png)

Il circuito ExpressRoute in questa rete virtuale allo scenario di cloud privato viene stabilito quando si crea una connessione da una rete virtuale nella sottoscrizione al circuito ExpressRoute del cloud privato. Il peering usa una chiave di autorizzazione e un ID di circuito richiesta nell'portale di Azure. La connessione ExpressRoute stabilita tramite il peering è una connessione privata, uno a uno tra il cloud privato e la rete virtuale. È possibile gestire il cloud privato, utilizzare i carichi di lavoro nel cloud privato e accedere ai servizi di Azure tramite la connessione ExpressRoute.

Quando si distribuisce un cloud privato AVS, è necessario un singolo spazio di indirizzi della rete privata/22. Questo spazio di indirizzi non deve sovrapporsi agli spazi di indirizzi usati in altre reti virtuali nella sottoscrizione. All'interno di questo spazio di indirizzi, viene effettuato automaticamente il provisioning delle reti di gestione, provisioning e vMotion. Il routing è basato su BGP e viene automaticamente sottoposta a provisioning e abilitato per impostazione predefinita per ogni distribuzione di cloud privato.

Quando viene distribuito un cloud privato, vengono forniti gli indirizzi IP per vCenter e NSX-T Manager. Per accedere a tali interfacce di gestione, è necessario creare risorse aggiuntive in una rete virtuale nella sottoscrizione. Le procedure per la creazione di tali risorse e la definizione del peering privato di ExpressRoute sono disponibili nelle esercitazioni.

È possibile progettare la rete logica del cloud privato e implementarla con NSX-T. Il cloud privato viene fornito con il pre-provisioning di NSX-T. Per l'utente è stato eseguito il pre-provisioning di un gateway di livello 0 & gateway di livello 1. È possibile creare un segmento e collegarlo al gateway di primo livello esistente o collegarlo a un nuovo gateway di primo livello che è possibile definire. I componenti di rete logica NSX-T forniscono la connettività East-West tra i carichi di lavoro e forniscono anche la connettività Nord-Sud a Internet e ai servizi di Azure. 

## <a name="on-premises-interconnectivity"></a>Interconnettività locale

È anche possibile connettere ambienti locali ai cloud privati AVS. Questo tipo di interconnettività è un'estensione dell'interconnettività di base descritta nella sezione precedente.

![rete virtuale e connettività cloud privata completa locale](./media/concepts/adjacency-overview-drawing-double.png)

Per stabilire un'interconnettività completa a un cloud privato, è possibile usare la portale di Azure per abilitare il Copertura globale ExpressRoute tra un circuito ExpressRoute del cloud privato e un circuito ExpressRoute locale. Questa configurazione estende la connettività di base per includere l'accesso a cloud privati da ambienti locali.

È necessario un circuito ExpressRoute da locale a rete virtuale di Azure per connettersi da ambienti locali al cloud privato in Azure. Questo circuito ExpressRoute si trova nella sottoscrizione e non fa parte di una distribuzione di cloud privato. Il circuito ExpressRoute locale esula dall'ambito di questo documento. Se è necessaria la connettività locale al cloud privato, è possibile usare uno dei circuiti ExpressRoute esistenti o acquistarne uno nel portale di Azure.

Una volta collegato con Copertura globale, i due circuiti ExpressRoute instraderanno il traffico di rete tra gli ambienti locali e il cloud privato. L'interconnettività da locale a cloud privato è illustrata nel diagramma precedente. L'interconnettività rappresentata nel diagramma consente i casi d'uso seguenti:

- Cross-vCenter a caldo/freddo, vMotion
- Accesso locale per la gestione del cloud privato AVS

Per abilitare la connettività completa, è possibile richiedere una chiave di autorizzazione e un ID di peering privato per Copertura globale nell'portale di Azure. Usare la chiave e l'ID per stabilire Copertura globale tra un circuito ExpressRoute nella sottoscrizione e il circuito ExpressRoute per il nuovo cloud privato. Nell' [esercitazione per la creazione di un cloud privato](tutorial-create-private-cloud.md) vengono fornite le procedure per la richiesta e l'utilizzo della chiave e dell'ID.

Per i requisiti di routing della soluzione è necessario pianificare gli spazi di indirizzi della rete del cloud privato in modo da evitare sovrapposizioni con altre reti virtuali e reti locali. I cloud privati AVS richiedono almeno un `/22` blocco di indirizzi di rete CIDR per le subnet, illustrato di seguito. Questa rete integra le reti locali. Per connettersi ad ambienti locali e reti virtuali, deve trattarsi di un blocco di indirizzi di rete non sovrapposto.

Esempio `/22` di blocco di indirizzi di rete CIDR:`10.10.0.0/22`

Subnet:

| Utilizzo rete             | Subnet | Esempio        |
| ------------------------- | ------ | -------------- |
| Gestione del cloud privato            | `/24`    | `10.10.0.0/24`   |
| rete vMotion       | `/24`    | `10.10.1.0/24`   |
| Carichi di lavoro della VM | `/24`   | `10.10.2.0/24`   |
| Peering ExpressRoute | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Passaggi successivi 

Il passaggio successivo consiste nel comprendere i [concetti relativi alla memoria cloud privata](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

