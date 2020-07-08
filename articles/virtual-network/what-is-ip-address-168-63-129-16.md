---
title: Che cos'è l'indirizzo IP 168.63.129.16? | Microsoft Docs
description: Informazioni sull'indirizzo IP 168.63.129.16 e su come interagisce con le risorse.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: bae1fbc89564ca17938e6a630146be5e5fb5b11f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550851"
---
# <a name="what-is-ip-address-1686312916"></a>Che cos'è l'indirizzo IP 168.63.129.16?

L'indirizzo IP 168.63.129.16 è un indirizzo IP pubblico virtuale usato per facilitare un canale di comunicazione con le risorse della piattaforma Azure. I clienti possono definire qualsiasi spazio di indirizzi per la propria rete virtuale privata in Azure. Le risorse della piattaforma Azure, quindi, devono essere presentate come un indirizzo IP pubblico univoco. Questo indirizzo IP pubblico virtuale offre i vantaggi seguenti:

- Consente all'agente di macchine virtuali di comunicare con la piattaforma Azure per segnalare che si trova in uno stato "Pronto".
- Consente la comunicazione con il server virtuale DNS per fornire la risoluzione dei nomi filtrati alle risorse (ad esempio, alla macchina virtuale) che non hanno un server DNS personalizzato. Questo filtro garantisce che i clienti possano risolvere solo i nomi host delle proprie risorse.
- Consente ai [Probe di integrità](../load-balancer/load-balancer-custom-probe-overview.md) del servizio di bilanciamento del carico di Azure di determinare lo stato di integrità delle macchine virtuali.
- Consente alla macchina virtuale di ottenere un indirizzo IP dinamico dal servizio DHCP in Azure.
- Consente messaggi heartbeat dell'agente guest per il ruolo PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Ambito dell'indirizzo IP 168.63.129.16

L'indirizzo IP pubblico 168.63.129.16 viene usato in tutte le aree e in tutti i cloud nazionali. Questo indirizzo IP pubblico speciale è di proprietà di Microsoft e non verrà modificato. È consigliabile consentire questo indirizzo IP in tutti i criteri del firewall locali (nella VM) (direzione in uscita). La comunicazione tra questo particolare indirizzo IP e le risorse è sicura poiché solo la piattaforma Azure interna può generare un messaggio da questo indirizzo IP. Se questo indirizzo viene bloccato, in alcuni scenari è possibile che si verifichi un comportamento imprevisto. 168.63.129.16 è un [indirizzo IP virtuale del nodo host](../virtual-network/security-overview.md#azure-platform-considerations) e, di conseguenza, non è soggetto alle route definite dall'utente.

- L'agente di macchine virtuali richiede la comunicazione in uscita sulle porte 80, 443, 32526 con WireServer (168.63.129.16). Questi devono essere aperti nel firewall locale della macchina virtuale. La comunicazione su queste porte con 168.63.129.16 non è soggetta ai gruppi di sicurezza di rete configurati.
- 168.63.129.16 può fornire servizi DNS alla macchina virtuale. Se questa operazione non è necessaria, il traffico può essere bloccato nel firewall locale della macchina virtuale. Per impostazione predefinita, la comunicazione DNS non è soggetta ai gruppi di sicurezza di rete configurati, a meno che non si usi specificamente il tag del servizio [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) . Per bloccare il traffico DNS in DNS di Azure tramite NSG, creare una regola in uscita per negare il traffico a [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)e specificare "*" come "intervalli di porte di destinazione" e "any" come protocollo.
- Quando la macchina virtuale fa parte di un pool back-end di bilanciamento del carico, la comunicazione del [Probe di integrità](../load-balancer/load-balancer-custom-probe-overview.md) deve essere consentita da 168.63.129.16. La configurazione del gruppo di sicurezza di rete predefinito include una regola che consente la comunicazione. Questa regola sfrutta il tag del servizio [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) . Se lo si desidera, è possibile bloccare il traffico configurando il gruppo di sicurezza di rete, tuttavia si verificheranno errori di probe.

In uno scenario di rete non virtuale (versione classica), il probe di integrità viene originato da un indirizzo IP privato e 168.63.129.16 non viene utilizzato.

## <a name="next-steps"></a>Passaggi successivi

- [Gruppi di sicurezza](security-overview.md)
- [Creare, modificare o eliminare un gruppo di sicurezza di rete](manage-network-security-group.md)
