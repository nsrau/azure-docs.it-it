---
title: Panoramica del bilanciamento del carico interno | Documentazione Microsoft
description: "Panoramica del bilanciamento del carico interno e delle relative funzionalità. Modalità di funzionamento del bilanciamento del carico di Azure e possibili scenari per la configurazione di endpoint interni"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ec07c77119c2da408da21fbdc7877d0b43d16556
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="internal-load-balancer-overview"></a>Panoramica del bilanciamento del carico interno

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

A differenza del bilanciamento del carico Internet, il bilanciamento del carico interno indirizza il traffico solo alle risorse all'interno del servizio cloud o che accedono all'infrastruttura di Azure tramite una rete VPN. L'infrastruttura limita l'accesso agli indirizzi IP virtuali con carico bilanciato di un servizio cloud o una rete virtuale, senza esposizione diretta a un endpoint Internet. Ciò consente di eseguire le applicazioni line-of-business (LOB) interne in Azure e di accedervi dal cloud o dalle risorse locali.

## <a name="why-you-may-need-an-internal-load-balancer"></a>Perché potrebbe servire il bilanciamento del carico interno

Il bilanciamento del carico interno di Azure consente di bilanciare il carico tra macchine virtuali che si trovano in un servizio cloud o una rete virtuale nell'ambito di un'area. Per informazioni sull'uso e sulla configurazione di reti virtuali nell'ambito di un'area, vedere [Reti virtuali di area](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) nel blog di Azure. Le reti virtuali esistenti che sono state configurate per un gruppo di affinità non possono usare il bilanciamento del carico interno.

Il bilanciamento del carico interno permette di bilanciare i tipi di carico seguenti:

* In un servizio cloud, dalle macchine virtuali a un set di macchine virtuali che si trovano nello stesso servizio cloud (vedere la figura 1).
* In una rete virtuale, dalle macchine virtuali nella rete virtuale a un set di macchine virtuali che si trovano nello stesso servizio cloud della rete virtuale (vedere la figura 2).
* Per una rete virtuale cross-premise, dai computer locali a un set di macchine virtuali che si trovano nello stesso servizio cloud della rete virtuale (vedere la figura 3).
* Applicazioni multilivello con connessione Internet in cui i livelli di back-end non sono connessi a Internet, ma che richiedono il bilanciamento del carico per il traffico dal livello con connessione Internet.
* Bilanciamento del carico per applicazioni LOB ospitate in Azure senza la necessità di applicazioni software o componenti hardware aggiuntivi per il bilanciamento del carico. Inserimento di server locali nel set di computer il cui traffico viene sottoposto a bilanciamento del carico.

## <a name="internet-facing-multi-tier-applications"></a>Applicazioni multilivello con connessione Internet

Il livello Web presenta endpoint con connessione Internet per i client Internet e fa parte di un set con carico bilanciato. Il bilanciamento del carico distribuisce il traffico in ingresso dai client Web per la porta TCP 443 (HTTPS) ai server Web.

I server di database si servono di un endpoint di bilanciamento del carico interno usato dai server Web per l'archiviazione. Questo database serve l'endpoint con carico bilanciato, il cui traffico viene sottoposto a bilanciamento del carico tra i server di database nel set di bilanciamento del carico interno.

L'immagine seguente illustra l'applicazione multilivello con connessione Internet all'interno dello stesso servizio cloud.

![Bilanciamento del carico interno di un singolo servizio cloud](./media/load-balancer-internal-overview/IC736321.png)

Figura 1. Applicazione multilivello con connessione Internet

Un altro possibile uso per un'applicazione multilivello consiste nella distribuzione del bilanciamento del carico interno in un servizio cloud diverso rispetto a quello che utilizza il servizio per il bilanciamento del carico interno.

I servizi cloud che usano la stessa rete virtuale avranno accesso all'endpoint di bilanciamento del carico interno. Nella figura seguente è possibile vedere che i server Web front-end si trovano in un servizio cloud diverso rispetto al back-end di database e usano l'endpoint di bilanciamento del carico interno nella stessa rete virtuale.

![Bilanciamento del carico interno tra servizi cloud](./media/load-balancer-internal-overview/IC744147.png)

Figura 2. Server front-end in un altro servizio cloud

## <a name="intranet-line-of-business-applications"></a>Applicazioni Intranet line-of-business

Il traffico dai client nella rete locale viene sottoposto a bilanciamento del carico nel set di server line-of-business usando la connessione VPN alla rete di Azure.

I client avranno accesso a un indirizzo IP dal servizio VPN di Azure tramite una connessione VPN da punto a sito. È quindi possibile usare l'applicazione LOB ospitata dietro l'endpoint di bilanciamento del carico interno.

![Bilanciamento del carico interno tramite VPN da punto a sito](./media/load-balancer-internal-overview/IC744148.png)

Figura 3. Applicazioni LOB ospitate dietro l'endpoint di bilanciamento carico

Un altro scenario di tipo line-of-business prevede l'uso di una rete VPN da sito a sito per la connessione alla rete virtuale in cui è stato configurato l'endpoint di bilanciamento del carico interno. In questo modo, il traffico di rete locale può essere instradato all'endpoint di bilanciamento del carico interno.

![Bilanciamento del carico interno tramite VPN da sito a sito](./media/load-balancer-internal-overview/IC744150.png)

Figura 4. Il traffico di rete locale può essere instradato all'endpoint di bilanciamento del carico interno

## <a name="limitations"></a>Limitazioni

SNAT non è supportato dalle configurazioni del servizio di bilanciamento del carico interno. Nel contesto di questo documento, SNAT fa riferimento alla traduzione dell'indirizzo di rete di origine mascherato della porta.  Questo vale per gli scenari in cui una macchina virtuale in un pool di bilanciamento del carico deve raggiungere l'indirizzo IP front-end del rispettivo servizio di bilanciamento del carico interno. Questo scenario non è supportato per il servizio di bilanciamento del carico interno. Si verificheranno errori di connessione una volta eseguito il bilanciamento del carico del flusso sulla macchina virtuale che ha originato il flusso. È necessario usare un servizio di bilanciamento del carico di tipo proxy per questi scenari.

## <a name="next-steps"></a>Passaggi successivi

[Supporto di Azure Resource Manager per Azure Load Balancer](load-balancer-arm.md)

[Introduzione alla configurazione del bilanciamento del carico Internet](load-balancer-get-started-internet-arm-ps.md)

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
