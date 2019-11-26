---
title: file di inclusione
description: file di inclusione
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ac1687d371630089436640af15cf46491a38ab51
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485544"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Networking limits - Azure Resource Manager The following limits apply only for networking resources managed through **Azure Resource Manager** per region per subscription. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Sono stati aumentati di recente tutti i limiti predefiniti fino al rispettivo massimo. If there's no maximum limit column, the resource doesn't have adjustable limits. If you had these limits increased by support in the past and don't see updated limits in the following tables, [open an online customer support request at no charge](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Gruppi | Default/maximum limit | 
| --- | --- |
| Reti virtuali |1\.000 |
| Subnet per rete virtuale |3\.000 |
| Virtual network peerings per virtual network |500 |
| [Virtual network gateways (VPN Gateways) per virtual network](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| DNS servers per virtual network |20 |
| Private IP addresses per virtual network |65,536 |
| Private IP addresses per network interface |256 |
| Private IP addresses per virtual machine |256 |
| Public IP addresses per network interface |256 |
| Public IP addresses per virtual machine |256 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500.000 |
| Network interface cards |65,536 |
| Gruppi di sicurezza di rete |5\.000 |
| Regole NSG per NSG |1\.000 |
| Indirizzi IP e intervalli specificati per l'origine o la destinazione in un gruppo di sicurezza |4\.000 |
| Gruppi di sicurezza delle applicazioni |3\.000 |
| Gruppi di sicurezza delle applicazioni per ogni configurazione IP, per ogni scheda di interfaccia di rete |20 |
| Configurazioni IP per ogni gruppo di sicurezza delle applicazioni |4\.000 |
| Gruppi di sicurezza delle applicazioni che possono essere specificati in tutte le regole di sicurezza di un gruppo di sicurezza di rete |100 |
| User-defined route tables |200 |
| User-defined routes per route table |400 |
| Point-to-site root certificates per Azure VPN Gateway |20 |
| TAP di rete virtuale |100 |
| Configurazioni TAP di interfaccia di rete per TAP di rete virtuale |100 |

#### <a name="publicip-address"></a>Limiti per gli indirizzi IP pubblici
| Gruppi | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Indirizzi IP pubblici: dinamici | 1,000 for Basic. |Contattare il supporto tecnico. |
| Indirizzi IP pubblici: statici | 1,000 for Basic. |Contattare il supporto tecnico. |
| Indirizzi IP pubblici: statici | 1,000 for Standard.|Contattare il supporto tecnico. |
| Public IP prefix length | /28 | Contattare il supporto tecnico. |

#### <a name="load-balancer"></a>Load balancer limits
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Gruppi | Default/maximum limit |
| --- | --- |
| Servizi di bilanciamento del carico | 1\.000 | 
| Regole per risorsa, Basic | 250 |
| Regole per risorsa, Standard | 1\.500 | 
| Regole per configurazione IP | 299 |
| Regole per NIC | 300 |
| Front-end IP configurations, Basic | 200 |
| Front-end IP configurations, Standard | 600 |
| Back-end pool, Basic | 100, single availability set |
| Back-end pool, Standard | 1,000, single virtual network |
| Back-end resources per load balancer, Standard<sup>1</sup> | 150 |
| High-availability ports, Standard | 1 per internal front-end |

<sup>1</sup>The limit is up to 150 resources, in any combination of standalone virtual machine resources, availability set resources, and virtual machine scale-set resources.

#### <a name="virtual-networking-limits-classic"></a>The following limits apply only for networking resources managed through the **classic** deployment model per subscription. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Gruppi | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |100 |100 |
| Siti di rete locale |20 |50 |
| DNS servers per virtual network |20 |20 |
| Private IP addresses per virtual network |4\.096 |4\.096 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500,000, up to 1,000,000 for two or more NICs. |500,000, up to 1,000,000 for two or more NICs. |
| Gruppi di sicurezza di rete (NGS) |200 |200 |
| Regole NSG per NSG |1\.000 |1\.000 |
| User-defined route tables |200 |200 |
| User-defined routes per route table |400 |400 |
| Indirizzi IP pubblici (dinamici) |500 |500 |
| Indirizzi IP pubblici riservati. |500 |500 |
| VIP pubblico per ogni distribuzione |5 |Contattare il supporto tecnico |
| Private VIP (internal load balancing) per deployment |1 |1 |
| Endpoint access control lists (ACLs) |50 |50 |
