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
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114366"
---
# <a name="what-is-ip-address-1686312916"></a>Che cos'è l'indirizzo IP 168.63.129.16?

L'indirizzo IP 168.63.129.16 è un indirizzo IP pubblico virtuale usato per facilitare un canale di comunicazione con le risorse della piattaforma Azure. I clienti possono definire qualsiasi spazio di indirizzi per la propria rete virtuale privata in Azure. Le risorse della piattaforma Azure, quindi, devono essere presentate come un indirizzo IP pubblico univoco. Questo indirizzo IP pubblico virtuale offre i vantaggi seguenti:

- Consente all'agente di macchine virtuali di comunicare con la piattaforma Azure per segnalare che si trova in uno stato "Pronto".
- Consente la comunicazione con il server virtuale DNS per fornire la risoluzione dei nomi filtrati alle risorse (ad esempio, alla macchina virtuale) che non hanno un server DNS personalizzato. Questo filtro garantisce che i clienti possano risolvere solo i nomi host delle proprie risorse.
- Abilita i probe di integrità dal servizio di [bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md) di Azure per determinare lo stato di integrità delle macchine virtuali.
- Consente alla macchina virtuale di ottenere un indirizzo IP dinamico dal servizio DHCP in Azure.
- Consente messaggi heartbeat dell'agente guest per il ruolo PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Ambito dell'indirizzo IP 168.63.129.16

L'indirizzo IP pubblico 168.63.129.16 viene utilizzato in tutte le regioni e in tutte le nuvole nazionali. Questo indirizzo IP pubblico speciale è di proprietà di Microsoft e non cambierà. È consigliabile consentire questo indirizzo IP in tutti i criteri del firewall locali (nella macchina virtuale) (direzione in uscita). La comunicazione tra questo particolare indirizzo IP e le risorse è sicura poiché solo la piattaforma Azure interna può generare un messaggio da questo indirizzo IP. Se questo indirizzo viene bloccato, in alcuni scenari è possibile che si verifichi un comportamento imprevisto. 168.63.129.16 è un [IP virtuale del nodo host](../virtual-network/security-overview.md#azure-platform-considerations) e come tale non è soggetto a route definite dall'utente.

- L'agente di macchine virtuali richiede la comunicazione in uscita sulle porte 80, 443, 32526 con WireServer (168.63.129.16). Questi dovrebbero essere aperti nel firewall locale nella macchina virtuale. La comunicazione su queste porte con 168.63.129.16 non è soggetta ai gruppi di sicurezza di rete configurati.
- 168.63.129.16 può fornire servizi DNS alla macchina virtuale. Se questa operazione non è desiderata, questo traffico può essere bloccato nel firewall locale nella macchina virtuale. Per impostazione predefinita, la comunicazione DNS non è soggetta ai gruppi di sicurezza di rete configurati a meno che non venga noto uno stile di mira specifico sfruttando il tag del servizio [AzurePlatformDNS.By](../virtual-network/service-tags-overview.md#available-service-tags) default DNS communication is not subject to the configured network security groups unless specifically targeted leveraging the AzurePlatformDNS service tag.
- Quando la macchina virtuale fa parte di un pool back-end del servizio di bilanciamento del carico, la comunicazione del probe di integrità deve avere origine da 168.63.129.16.When the VM is part of a load balancer backend pool, [health probe](../load-balancer/load-balancer-custom-probe-overview.md) communication should be allowed to originate from 168.63.129.16. La configurazione del gruppo di sicurezza di rete predefinito ha una regola che consente questa comunicazione. Questa regola sfrutta il tag del servizio [AzureLoadBalancer.This rule](../virtual-network/service-tags-overview.md#available-service-tags) leverages the AzureLoadBalancer service tag. Se lo si desidera, questo traffico può essere bloccato configurando il gruppo di sicurezza di rete, tuttavia ciò comporterà probe con esito negativo.

In uno scenario di rete non virtuale (Classic), il probe di integrità proviene da un IP privato e 168.63.129.16 non viene utilizzato.

## <a name="next-steps"></a>Passaggi successivi

- [Gruppi di sicurezza](security-overview.md)
- [Creare, modificare o eliminare un gruppo di sicurezza di rete](manage-network-security-group.md)
