---
title: Soluzione VMware di Azure di CloudSimple-scegliere una soluzione di bilanciamento del carico per i cloud privati CloudSimple
description: Descrive le opzioni di bilanciamento del carico per la distribuzione di un'applicazione in un cloud privato
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d26eb0160316737c9ad31d98c8cf23bdcad42d32
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206509"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Scegliere una soluzione di bilanciamento del carico per i cloud privati CloudSimple

Quando si distribuisce un'applicazione in un cloud privato CloudSimple, è possibile scegliere una delle diverse opzioni per il bilanciamento del carico.

È possibile scegliere un servizio di bilanciamento del carico basato su software o virtuale nel cloud privato CloudSimple o anche usare il servizio di bilanciamento del carico di Azure L7 in esecuzione nella sottoscrizione di Azure per il front-end delle macchine virtuali di livello Web in esecuzione nel cloud privato CloudSimple. Qui sono elencate alcune opzioni:

## <a name="virtual-load-balancers"></a>Bilanciamento del carico virtuale

È possibile distribuire appliance del servizio di bilanciamento del carico virtuale nell'ambiente VMware tramite l'interfaccia vCenter e configurarle in front-end per il traffico dell'applicazione.

Alcuni fornitori più diffusi sono: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Servizio di bilanciamento del carico di Azure L7

Quando si usa applicazione Azure gateway come servizio di bilanciamento del carico L7 per l'applicazione in esecuzione in un cloud privato, non è necessario gestire il software di bilanciamento del carico. Il software di bilanciamento del carico è gestito da Azure. Tutte le VM di livello Web nel cloud privato usano indirizzi IP privati e non richiedono regole NAT aggiuntive o indirizzi IP pubblici per la risoluzione dei nomi. Le macchine virtuali di livello Web comunicano con il gateway applicazione Azure tramite una connessione privata a bassa latenza e larghezza di banda elevata.

Per altre informazioni su come configurare questa soluzione, vedere la guida alla soluzione sull'uso di applicazione Azure gateway come servizio di bilanciamento del carico L7.

## <a name="azure-internal-load-balancer"></a>Servizio di bilanciamento del carico interno di Azure

Se si sceglie di eseguire l'applicazione in una distribuzione ibrida in cui il livello front-end Web è in esecuzione all'interno di una vNet di Azure nella sottoscrizione di Azure e il livello di database dell'applicazione viene eseguito in macchine virtuali VMware in CloudSimple cloud privato, è possibile usare il caricamento interno di Azure Balancer (servizio di bilanciamento del carico L4) davanti alle macchine virtuali di livello database per la gestione del traffico.

Per altre informazioni, vedere la documentazione [interna di Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer) di Azure.

## <a name="global-server-load-balancer"></a>Servizio di bilanciamento del carico globale del server

Se si sta cercando un servizio di bilanciamento del carico basato su DNS, è possibile usare soluzioni di terze parti disponibili in Azure Marketplace o accedere con la soluzione nativa di Azure.

Gestione traffico di Azure è un servizio di bilanciamento del carico del traffico basato su DNS che consente di distribuire il traffico in modo ottimale ai servizi nelle aree globali di Azure e in locale, garantendo al tempo stesso la disponibilità elevata e la velocità di risposta. Per altre informazioni, vedere la documentazione di [Gestione traffico](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) di Azure.
