---
title: Panoramica del bilanciamento del carico interno di Azure | Microsoft Docs
description: Funzionamento del bilanciamento del carico interno in Azure e scenari di configurazione degli endpoint interni.
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
ms.openlocfilehash: 54e390dbdb07cb4c45c801b638099aa0dcc6db1a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="overview-of-azure-internal-load-balancer"></a>Panoramica del bilanciamento del carico interno di Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Il bilanciamento del carico interno (ILB) di Azure indirizza il traffico alle risorse che si trovano all'interno di un servizio cloud o che usano una VPN per accedere all'infrastruttura di Azure. In questo senso il bilanciamento del carico interno è diverso da un bilanciamento del carico con connessione a Internet. L'infrastruttura di Azure limita l'accesso agli indirizzi IP virtuali (VIP) con carico bilanciato di un servizio cloud o a una rete virtuale. Gli indirizzi VIP e le reti virtuali non sono mai esposti direttamente a un endpoint di Internet. Le applicazioni line-of-business interne vengono eseguite in Azure e sono accessibili dall'interno di Azure o da risorse locali.

## <a name="why-you-might-need-an-internal-load-balancer"></a>Perché potrebbe essere necessario un bilanciamento del carico interno

Il bilanciamento del carico interno di Azure consente di bilanciare il carico tra le macchine virtuali (VM) che risiedono in un servizio cloud o una rete virtuale nell'ambito di un'area. Per informazioni sulle reti virtuali con ambito di area, vedere [Reti virtuali di area](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) nel blog di Azure. Le reti virtuali esistenti configurate per un gruppo di affinità non possono usare il bilanciamento del carico interno.

Il bilanciamento del carico interno permette di bilanciare i tipi di carico seguenti:

* All'interno di un servizio cloud: il bilanciamento del carico dalle macchine virtuali a un set di macchine virtuali che risiedono all'interno dello stesso servizio cloud. Vedere questo <a href="#figure1">esempio</a>.
* In una rete virtuale: bilanciamento del carico dalle macchine virtuali nella rete virtuale a un set di macchine virtuali che si trovano nello stesso servizio cloud della rete virtuale. Vedere questo <a href="#figure2">esempio</a>.
* Per una rete virtuale cross-premise: bilanciamento del carico dai computer locali a un set di macchine virtuali che si trovano nello stesso servizio cloud della rete virtuale. Vedere questo <a href="#figure3">esempio</a>.
* Per le applicazioni a più livelli: bilanciamento del carico per le applicazioni multilivello e con connessione a Internet in cui i livelli di back-end non sono esposti a Internet. I livelli di back-end richiedono il bilanciamento del carico per il traffico dal livello con connessione a Internet.
* Per le applicazioni line-of-business: bilanciamento del carico per le applicazioni line-of-business ospitate in Azure senza applicazioni software o componenti hardware aggiuntivi per il bilanciamento del carico. Questo scenario include server locali che si trovano nel set di computer il cui traffico ha il carico bilanciato.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>Bilanciamento del carico per le applicazioni multilivello con connessione a Internet

Il livello Web presenta endpoint con connessione a Internet per i client Internet e fa parte di un set con carico bilanciato. Il sevizio di bilanciamento del carico interno distribuisce il traffico in ingresso dai client Web per la porta TCP 443 (HTTPS) ai server Web.

I server di database si trovano dietro un endpoint di bilanciamento del carico interno utilizzato dai server Web per l'archiviazione. L'endpoint di bilanciamento del carico interno è un endpoint con carico bilancisto del servizio database. Il traffico viene sottoposto a bilanciamento del carico in tutti i server di database del set di bilanciamento del carico interno.

L'immagine seguente illustra il bilanciamento del carico interno per l'applicazione multilivello con connessione a Internet all'interno dello stesso servizio cloud.

<a name="figure1"></a>
![Applicazione multilivello con connessione Internet](./media/load-balancer-internal-overview/IC736321.png)

Per le applicazioni multilivello è disponibile un altro scenario. Il bilanciamento del carico viene distribuito a un servizio cloud diverso da quello che usa il servizio per il bilanciamento del carico interno.

I servizi cloud che usano la stessa rete virtuale possono accedere all'endpoint di bilanciamento del carico interno. Nella figura seguente è possibile vedere che i server Web front-end si trovano in un servizio cloud diverso rispetto al back-end di database. I server front-end usano l'endpoint di bilanciamento del carico interno nella stessa rete virtuale come back-end.

<a name="figure2"></a>
![Server front-end in un altro servizio cloud](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Il bilanciamento del carico per le applicazioni intranet line-of-business

Il traffico dai client nella rete locale viene sottoposto a bilanciamento del carico nel set di server line-of-business che usano una connessione VPN alla rete di Azure.

I client possono accedere a un indirizzo IP dal servizio VPN di Azure tramite una VPN da punto a sito. L'applicazione line-of-business può essere ospitata dietro l'endpoint di bilanciamento del carico interno.

<a name="figure3"></a>
![L'applicazione line-of-business ospitata dietro l'endpoint di bilanciamento del carico interno](./media/load-balancer-internal-overview/IC744148.png)

Un altro scenario per le applicazioni line-of-business prevede l'uso di una rete VPN da sito a sito per la connessione alla rete virtuale in cui è stato configurato l'endpoint di bilanciamento del carico interno. Il traffico di rete locale può essere instradato all'endpoint di bilanciamento del carico interno.

<a name="figure4"></a>
![Il traffico di rete locale può essere instradato all'endpoint di bilanciamento del carico interno](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>Limitazioni

SNAT non è supportato dalle configurazioni del bilanciamento del carico interno. In questo articolo, SNAT fa riferimento a scenari che coinvolgono Network Address Translation di origine con port-masquerading. Una VM in un pool di bilanciamento del carico deve raggiungere l'indirizzo IP front-end del rispettivo bilanciamento del carico interno. Si verificano errori di connessione una volta eseguito il bilanciamento del carico del flusso sulla macchina virtuale che ha originato il flusso. Questi scenari non sono supportati per il bilanciamento del carico interno. Si deve usare invece un bilanciamento del carico di tipo proxy.

## <a name="next-steps"></a>Passaggi successivi

* [Supporto di Azure Resource Manager per Azure Load Balancer](load-balancer-arm.md)
* [Introduzione alla configurazione del bilanciamento del carico con connessione a Internet](load-balancer-get-started-internet-arm-ps.md)
* [Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)
* [Configurare la modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)
* [Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
