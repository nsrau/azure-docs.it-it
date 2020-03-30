---
title: Prefisso indirizzo IPv6 pubblico nella rete virtuale di AzurePublic IPv6 address prefix in Azure virtual network
titlesuffix: Azure Virtual Network
description: Informazioni sul prefisso di indirizzo IPv6 pubblico nella rete virtuale di Azure.Learn about public IPv6 address prefix in Azure virtual network.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965165"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Prefisso indirizzo IPv6 pubblico riservato (anteprima)Reserved public IPv6 address prefix (Preview)

In Azure, le reti virtuali (VNet) a stack doppio (VNet) e le macchine virtuali (VM) sono sicure per impostazione predefinita poiché non dispongono di connettività Internet.In Azure, dual stack (IPv4-IPv6) virtual networks (VNet) and virtual machines (VMs) are secure by default since they have no Internet connectivity. You can easily add an IPv6 Internet connectivity to your Azure Load Balancers and VMs with public IPv6 addresses that you obtain from Azure.

Tutti gli indirizzi IP pubblici prenotati sono associati a un'area di Azure di propria scelta e alla sottoscrizione di Azure.Any public IPs that you reserve are associated with an Azure region of your choice and with your Azure subscription. È possibile spostare un ip pubblico IPv6 riservato (statico) tra uno qualsiasi dei servizi di bilanciamento del carico di Azure o macchine virtuali nella sottoscrizione. È possibile dissociare completamente l'IP pubblico IPv6 e verrà mantenuto per l'uso quando si è pronti.

> [!WARNING]
> Prestare attenzione a non eliminare accidentalmente gli indirizzi IP pubblici. L'eliminazione di un indirizzo IP pubblico lo rimuove dalla sottoscrizione e non sarà possibile ripristinarlo (nemmeno con l'aiuto del supporto di Azure).

Oltre a riservare singoli indirizzi IPv6, è possibile riservare intervalli contigui di indirizzi IPv6 di Azure (noto come prefisso IP) per l'uso.  Analogamente ai singoli indirizzi IP, i prefissi riservati sono associati a un'area di Azure di propria scelta e alla sottoscrizione di Azure.Similar to individual IP addresses, reserved prefixes are associated with an Azure region of your choice and with your Azure subscription. La prenotazione di un intervallo di indirizzi prevedibile e contiguo ha molti usi. Ad esempio, è possibile *whitelisting* semplificare notevolmente l'elenco indirizzi IP delle applicazioni ospitate da Azure dall'azienda e dai clienti, poiché gli intervalli IP statici possono essere facilmente programmati nei firewall locali.  È possibile creare singoli indirizzi IP pubblici dal prefisso IP in base alle esigenze e quando si eliminano i singoli indirizzi IP pubblici vengono *restituiti* all'intervallo riservato in modo da poterli riutilizzare in un secondo momento. Tutti gli indirizzi IP nel prefisso IP sono riservati per l'uso esclusivo fino al momento in cui si elimina il prefisso.

> [!Important]
> IPv6 per la rete virtuale di Azure è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ipv6-prefix-sizes"></a>Dimensioni dei prefissi IPv6
Sono disponibili le seguenti dimensioni del prefisso IP pubblico:

-  Dimensione minima prefisso IPv6: /127 : 2 indirizzi
-  Dimensione massima prefisso IPv6: /124 : 16 indirizzi

La dimensione del prefisso viene specificata come dimensione della maschera CIDR (Classless Inter-Domain Routing). Ad esempio, una maschera di /128 rappresenta un singolo indirizzo IPv6 poiché gli indirizzi IPv6 sono composti da 128 bit.

## <a name="pricing"></a>Prezzi
 
Per i costi associati all'uso di indirizzi IP pubblici di Azure, sia indirizzi IP singoli che intervalli IP, vedere Prezzi degli [indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Limitazioni
IPv6 is supported on Basic Public IPs only with "dynamic" allocation that means that the IPv6 address will change if you delete and redeploy your application (VM's or load balancers) in Azure. L'IP pubblico IPv6 standard supporta esclusivamente l'allocazione statica (riservata) anche se i servizi di bilanciamento del carico INTERNI standard possono supportare anche l'allocazione dinamica dall'interno della subnet a cui sono assegnati.  

È consigliabile usare gli indirizzi IP pubblici standard e i servizi di bilanciamento del carico standard per le applicazioni IPv6.

## <a name="next-steps"></a>Passaggi successivi
- Riservare un [prefisso di indirizzo IPv6](ipv6-reserve-public-ip-address-prefix.md)pubblico.
- Ulteriori informazioni sugli [indirizzi IPv6](ipv6-overview.md).
- Informazioni su come creare e usare indirizzi IP pubblici (sia IPv4 che IPv6) in Azure.Learn about how to create and [use public IPs](virtual-network-public-ip-address.md) (both IPv4 and IPv6) in Azure.
