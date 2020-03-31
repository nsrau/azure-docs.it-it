---
title: Azure VMware Solution by CloudSimple - Choose a load balancing solution for CloudSimple Private Clouds
description: Descrive le opzioni di bilanciamento del carico che distribuiscono un'applicazione in un cloud privato
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014879"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Scegliere una soluzione di bilanciamento del carico per cloud privati semplici

Quando si distribuisce un'applicazione in un cloud privato CloudSimple, è possibile scegliere una delle diverse opzioni per il bilanciamento del carico.

È possibile scegliere un servizio di bilanciamento del carico virtuale o basato su software nel cloud privato CloudSimple o persino usare il servizio di bilanciamento del carico di Azure L7 in esecuzione nella sottoscrizione di Azure per il front-end delle macchine virtuali del livello Web in esecuzione nel cloud privato CloudSimple.You can choose a virtual or software-based load balancer in your CloudSimple private cloud or even use Azure L7 load balancer running in your Azure subscription to front-end your web tier VMs running in the CloudSimple Private Cloud. Qui, elenchiamo alcune opzioni:

## <a name="virtual-load-balancers"></a>Servizi di bilanciamento del carico virtualeVirtual load balancers

È possibile distribuire appliance di bilanciamento del carico virtuale nell'ambiente VMware tramite l'interfaccia vCenter e configurarle nel front-end del traffico dell'applicazione.

Alcuni fornitori popolari sono: http://nginx.org/en/docs/http/load_balancing.html NginX: F5- https://www.f5.com/products/big-ip-services/virtual-editions BigIP - Traffic Manager: Citrix ADC:https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Servizio di bilanciamento del carico di Azure L7Azure L7 load balancer

Quando si usa Il gateway applicazione di Azure come servizio di bilanciamento del carico L7 per l'applicazione in esecuzione in un cloud privato, non è necessario gestire il software del servizio di bilanciamento del carico. Il software di bilanciamento del carico è gestito da Azure.The load balancer software is managed by Azure. Tutte le macchine virtuali del livello Web nel cloud privato usano indirizzi IP privati e non richiedono regole NAT aggiuntive o indirizzi IP pubblici per risolvere i nomi. Le macchine virtuali di livello Web comunicano con il gateway applicazione di Azure tramite una connessione privata, a bassa latenza e a larghezza di banda elevata.

Per altre informazioni su come configurare questa soluzione, vedere la guida alla soluzione in Uso del gateway applicazione di Azure come servizio di bilanciamento del carico L7.To learn more about how to configure this solution, refer to the solution guide on Using Azure Application Gateway as a L7 load balancer.

## <a name="azure-internal-load-balancer"></a>Servizio di bilanciamento del carico interno di AzureAzure internal load balancer

Se si sceglie di eseguire l'applicazione in una distribuzione ibrida in cui il livello web front-end è in esecuzione all'interno di una rete virtuale di Azure nella sottoscrizione di Azure e il livello DB dell'applicazione è in esecuzione nelle macchine virtuali VMware nel cloud privato CloudSimple, è possibile usare il carico interno di Azure bilanciamento del carico (bilanciamento del carico L4) davanti alle macchine virtuali del livello DB per la gestione del traffico.

Per altre informazioni, vedere la documentazione di [Azure Internal Load Balancer.To](../load-balancer/concepts-limitations.md#internalloadbalancer) learn more, see Azure Internal Load Balancer documentation.

## <a name="global-server-load-balancer"></a>Servizio di bilanciamento del carico del server globale

Se si sta cercando un servizio di bilanciamento del carico basato su DNS, è possibile usare soluzioni di terze parti disponibili in Azure Marketplace o usare la soluzione nativa di Azure.If you are looking for a DNS-based load balancer, then you may either use third party solutions available in Azure Marketplace or go with the native Azure solution.

Gestione traffico di Azure è un servizio di bilanciamento del carico basato su DNS che consente di distribuire il traffico in modo ottimale ai servizi nelle aree globali di Azure e in locale, garantendo al contempo disponibilità elevata e velocità di risposta. Per altre informazioni, vedere la documentazione di Gestione traffico di Azure.To learn more, see Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) documentation.
