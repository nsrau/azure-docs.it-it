---
title: Azure VMware Solutions (AVS)-scegliere una soluzione di bilanciamento del carico per i cloud privati AVS
description: Descrive le opzioni di bilanciamento del carico per la distribuzione di un'applicazione in un cloud privato AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 405bc9d95b8d82e2181e2fb828d6bcc00c8c4639
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014879"
---
# <a name="choose-a-load-balancing-solution-for-avs-private-clouds"></a>Scegliere una soluzione di bilanciamento del carico per i cloud privati AVS

Quando si distribuisce un'applicazione in un cloud privato AVS, è possibile scegliere una delle diverse opzioni per il bilanciamento del carico.

È possibile scegliere un servizio di bilanciamento del carico basato su software o virtuale nel cloud privato AVS o anche usare il servizio di bilanciamento del carico di Azure L7 in esecuzione nella sottoscrizione di Azure per front-end delle macchine virtuali di livello Web in esecuzione nel cloud privato AVS. Qui sono elencate alcune opzioni:

## <a name="virtual-load-balancers"></a>Bilanciamento del carico virtuale

È possibile distribuire appliance del servizio di bilanciamento del carico virtuale nell'ambiente VMware tramite l'interfaccia vCenter e configurarle in front-end per il traffico dell'applicazione.

Alcuni fornitori più diffusi sono: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Servizio di bilanciamento del carico di Azure L7

Quando si usa applicazione Azure gateway come servizio di bilanciamento del carico L7 per l'applicazione in esecuzione in un cloud privato AVS, non è necessario gestire il software di bilanciamento del carico. Il software di bilanciamento del carico è gestito da Azure. Tutte le VM di livello Web nel cloud privato AVS usano indirizzi IP privati e non richiedono regole NAT aggiuntive o indirizzi IP pubblici per la risoluzione dei nomi. Le macchine virtuali di livello Web comunicano con il gateway applicazione Azure tramite una connessione privata a bassa latenza e larghezza di banda elevata.

Per altre informazioni su come configurare questa soluzione, vedere la guida alla soluzione sull'uso di applicazione Azure gateway come servizio di bilanciamento del carico L7.

## <a name="azure-internal-load-balancer"></a>Servizio di bilanciamento del carico interno di Azure

Se si sceglie di eseguire l'applicazione in una distribuzione ibrida in cui il livello front-end Web è in esecuzione all'interno di una vNet di Azure nella sottoscrizione di Azure e il livello di database dell'applicazione viene eseguito in macchine virtuali VMware in AVS private cloud, è possibile usare il servizio di bilanciamento del carico interno di Azure (L 4 Load Balancer) davanti alle macchine virtuali di livello database per la gestione del traffico.

Per altre informazioni, vedere la documentazione [interna di Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer) di Azure.

## <a name="global-server-load-balancer"></a>Servizio di bilanciamento del carico globale del server

Se si sta cercando un servizio di bilanciamento del carico basato su DNS, è possibile usare soluzioni di terze parti disponibili in Azure Marketplace o accedere con la soluzione nativa di Azure.

Gestione traffico di Azure è un servizio di bilanciamento del carico del traffico basato su DNS che consente di distribuire il traffico in modo ottimale ai servizi nelle aree globali di Azure e in locale, garantendo al tempo stesso la disponibilità elevata e la velocità di risposta. Per altre informazioni, vedere la documentazione di [Gestione traffico](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) di Azure.
