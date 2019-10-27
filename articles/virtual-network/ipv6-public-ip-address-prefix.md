---
title: Prefisso dell'indirizzo IPv6 pubblico nella rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni sul prefisso dell'indirizzo IPv6 pubblico nella rete virtuale di Azure.
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
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965165"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Prefisso dell'indirizzo IPv6 pubblico riservato (anteprima)

In Azure le reti virtuali (VNet) dual stack (IPv4 + IPv6) e le macchine virtuali (VM) sono sicure per impostazione predefinita, poiché non dispongono di connettività Internet. È possibile aggiungere facilmente una connettività Internet IPv6 ai bilanciamento del carico e alle macchine virtuali di Azure con indirizzi IPv6 pubblici ottenuti da Azure.

Tutti gli indirizzi IP pubblici riservati sono associati a un'area di Azure di propria scelta e alla sottoscrizione di Azure. È possibile spostare un indirizzo IP pubblico IPv6 riservato (statico) tra qualsiasi servizio di bilanciamento del carico di Azure o le macchine virtuali nella sottoscrizione. È possibile annullare l'associazione dell'IP pubblico IPv6 completamente e verrà mantenuto per l'uso quando si è pronti.

> [!WARNING]
> Prestare attenzione a non eliminare accidentalmente gli indirizzi IP pubblici. L'eliminazione di un indirizzo IP pubblico ne comporta la rimozione dalla sottoscrizione e non sarà possibile recuperarla (anche con il supporto tecnico di Azure).

Oltre a riservare i singoli indirizzi IPv6, è possibile riservare intervalli contigui di indirizzi IPv6 di Azure (noti come prefisso IP) per l'uso.  Analogamente ai singoli indirizzi IP, i prefissi riservati sono associati a un'area di Azure di propria scelta e alla sottoscrizione di Azure. Per riservare un intervallo di indirizzi prevedibile e contiguo sono presenti molti utilizzi. È possibile, ad esempio, semplificare notevolmente la *lista* degli indirizzi IP consentiti delle applicazioni ospitate in Azure dall'azienda e i clienti, in quanto gli intervalli di indirizzi IP statici possono essere prontamente programmati in firewall locali.  È possibile creare singoli IP pubblici dal prefisso IP in base alle esigenze e quando si eliminano i singoli indirizzi IP pubblici *restituiti* nell'intervallo riservato, in modo che sia possibile riutilizzarli in un secondo momento. Tutti gli indirizzi IP nel prefisso IP sono riservati per l'uso esclusivo fino al momento in cui si elimina il prefisso.

> [!Important]
> IPv6 per la rete virtuale di Azure è attualmente disponibile in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ipv6-prefix-sizes"></a>Dimensioni prefisso IPv6
Sono disponibili le seguenti dimensioni del prefisso IP pubblico:

-  Dimensioni minime prefisso IPv6:/127 = 2 indirizzi
-  Dimensioni massime prefisso IPv6:/124 = 16 indirizzi

La dimensione del prefisso viene specificata come una maschera CIDR (Inter-Domain Routing) senza classi. Ad esempio, una maschera di/128 rappresenta un singolo indirizzo IPv6 poiché gli indirizzi IPv6 sono costituiti da 128 bit.

## <a name="pricing"></a>Prezzi
 
Per i costi associati all'uso di indirizzi IP pubblici di Azure, sia per gli indirizzi IP individuali che per gli intervalli IP, vedere [prezzi degli indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Limitazioni
IPv6 è supportato solo in indirizzi IP pubblici di base con allocazione dinamica che indica che l'indirizzo IPv6 verrà modificato se si elimina e si ridistribuisce l'applicazione (bilanciamento del carico o della VM) in Azure. L'IP pubblico IPv6 standard supporta solo l'allocazione statica (riservata), sebbene i bilanciamento del carico interno standard possano supportare anche l'allocazione dinamica dall'interno della subnet a cui sono assegnati.  

Come procedura consigliata, è consigliabile usare gli indirizzi IP pubblici standard e i bilanciamenti del carico standard per le applicazioni IPv6.

## <a name="next-steps"></a>Passaggi successivi
- Riservare un [prefisso di indirizzo IPv6](ipv6-reserve-public-ip-address-prefix.md)pubblico.
- Altre informazioni sugli [indirizzi IPv6](ipv6-overview.md).
- Informazioni su [come creare e usare indirizzi IP pubblici](virtual-network-public-ip-address.md) (sia IPv4 che IPv6) in Azure.
