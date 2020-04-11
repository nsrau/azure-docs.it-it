---
title: File di inclusione
description: File di inclusione
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ee4d629d80ff8265d58a1d05b8dca1652ddac6fb
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81112855"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Limiti di rete - Azure Resource ManagerNetworking limits - Azure Resource Manager
I limiti seguenti si applicano solo alle risorse di rete gestite tramite **Azure Resource Manager** per ogni sottoscrizione. Informazioni su come [visualizzare l'utilizzo corrente delle risorse rispetto](../articles/networking/check-usage-against-limits.md)ai limiti della sottoscrizione.

> [!NOTE]
> Sono stati aumentati di recente tutti i limiti predefiniti fino al rispettivo massimo. Se non è presente alcuna colonna limite massimo, la risorsa non dispone di limiti regolabili. Se in passato questi limiti sono aumentati dal supporto e non si vedono i limiti aggiornati nelle tabelle seguenti, aprire una richiesta di [assistenza clienti online senza alcun costo aggiuntivo](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Risorsa | Limite | 
| --- | --- |
| Reti virtuali |1\.000 |
| Subnet per rete virtuale |3,000 |
| Peering di rete virtuale per ogni rete virtuale |500 |
| [Gateway di rete virtuale (gateway VPN) per rete virtualeVirtual network gateways (VPN gateways) per virtual network](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Gateway di rete virtuale (gateway ExpressRoute) per rete virtualeVirtual network gateways (ExpressRoute gateways) per virtual network](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Server DNS per rete virtuale |20 |
| Indirizzo IP privati per ogni rete virtuale |65,536 |
| Indirizzi IP privati per interfaccia di retePrivate IP addresses per network interface |256 |
| Indirizzi IP privati per macchina virtualePrivate IP addresses per virtual machine |256 |
| Indirizzi IP pubblici per interfaccia di rete |256 |
| Indirizzi IP pubblici per macchina virtualePublic IP addresses per virtual machine |256 |
| [Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500.000 |
| Schede di interfaccia di rete |65,536 |
| Gruppi di sicurezza di rete |5.000 |
| Regole NSG per NSG |1\.000 |
| Indirizzi IP e intervalli specificati per l'origine o la destinazione in un gruppo di sicurezza |4.000 |
| Gruppi di sicurezza delle applicazioni |3,000 |
| Gruppi di sicurezza delle applicazioni per ogni configurazione IP, per ogni scheda di interfaccia di rete |20 |
| Configurazioni IP per ogni gruppo di sicurezza delle applicazioni |4.000 |
| Gruppi di sicurezza delle applicazioni che possono essere specificati in tutte le regole di sicurezza di un gruppo di sicurezza di rete |100 |
| Tabelle di route definite dall'utente |200 |
| Route definite dall'utente per tabella di routeUser-defined routes per route table |400 |
| Certificati radice da punto a sito per ogni gateway VPN di Azure |20 |
| TAP di rete virtuale |100 |
| Configurazioni TAP di interfaccia di rete per TAP di rete virtuale |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limiti per gli indirizzi IP pubblici
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Indirizzi IP pubblici<sup>1</sup> | 10 per Basic. | Contattare il supporto tecnico. |
| Indirizzi IP pubblici statici<sup>1</sup> | 10 per Basic. | Contattare il supporto tecnico. |
| Indirizzi IP pubblici standard<sup>1</sup> | 10 | Contattare il supporto tecnico. |
| Prefissi per indirizzi IP pubblici | numero di indirizzi IP pubblici standard in una sottoscrizione | Contattare il supporto tecnico. |
| Lunghezza prefisso IP pubblico | /28 | Contattare il supporto tecnico. |

<sup>1 : il</sup> nome del I limiti predefiniti per gli indirizzi IP pubblici variano in base al tipo di categoria dell'offerta, ad esempio Prova gratuita, Con pagamento in base al costo e CSP. Ad esempio, l'impostazione predefinita per le sottoscrizioni con contratto Enterprise Agreement è 1000.For example, the default for Enterprise Agreement subscriptions is 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limiti del servizio di bilanciamento del carico
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo corrente delle risorse rispetto](../articles/networking/check-usage-against-limits.md)ai limiti della sottoscrizione.

**Load Balancer Standard**

| Risorsa                                | Limite         |
|-----------------------------------------|-------------------------------|
| Servizi di bilanciamento del carico                          | 1\.000                         |
| Regole per risorsa                      | 1.500                         |
| Regole per scheda di interfaccia di rete (tra tutti gli indirizzi IP in una scheda di interfaccia di rete)Rules per NIC (across all IPs on a NIC) | 300                           |
| Configurazioni IP front-end              | 600                           |
| Dimensioni del pool back-end                       | 1.000 configurazioni IP, singola rete virtuale |
| Risorse back-end <sup>per Load Balancer 1<sup> | 150                   |
| Porte ad alta disponibilità                 | 1 per front-end interno       |
| Regole in uscita per Load BalancerOutbound rules per Load Balancer        | 600                           |
| [Timeout di inattività TCP](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 minuti/30 minuti          |

<sup>1 : il</sup> nome del Il limite è fino a 150 risorse, in qualsiasi combinazione di risorse di macchine virtuali autonome, risorse del set di disponibilità e risorse del set di scalabilità delle macchine virtuali.

**Bilanciamento del carico di baseBasic Load Balancer**

| Risorsa                                | Limite        |
|-----------------------------------------|------------------------------|
| Servizi di bilanciamento del carico                          | 1\.000                        |
| Regole per risorsa                      | 250                          |
| Regole per scheda di interfaccia di rete (tra tutti gli indirizzi IP in una scheda di interfaccia di rete)Rules per NIC (across all IPs on a NIC) | 300                          |
| Configurazioni IP front-end              | 200                          |
| Dimensioni del pool back-end                       | 300 configurazioni IP, set di disponibilità singolo |
| Set di disponibilità per Load BalancerAvailability sets per Load Balancer     | 150                          |

<a name="virtual-networking-limits-classic"></a>I limiti seguenti si applicano solo alle risorse di rete gestite tramite il modello di distribuzione **classica** per ogni sottoscrizione. Informazioni su come [visualizzare l'utilizzo corrente delle risorse rispetto](../articles/networking/check-usage-against-limits.md)ai limiti della sottoscrizione.

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |100 |100 |
| Siti di rete locale |20 |50 |
| Server DNS per rete virtuale |20 |20 |
| Indirizzo IP privati per ogni rete virtuale |4.096 |4.096 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500.000, fino a 1.000.000 per due o più schede di interfaccia di rete. |500.000, fino a 1.000.000 per due o più schede di interfaccia di rete. |
| Gruppi di sicurezza di rete (NGS) |200 |200 |
| Regole NSG per NSG |1\.000 |1\.000 |
| Tabelle di route definite dall'utente |200 |200 |
| Route definite dall'utente per tabella di routeUser-defined routes per route table |400 |400 |
| Indirizzi IP pubblici (dinamici) |500 |500 |
| Indirizzi IP pubblici riservati. |500 |500 |
| VIP pubblico per ogni distribuzione |5 |Contattare il supporto tecnico |
| VIP privato (bilanciamento del carico interno) per distribuzionePrivate VIP (internal load balancing) per deployment |1 |1 |
| Elenchi di controllo di accesso (ACL) degli endpointEndpoint access control lists (ACLs) |50 |50 |
