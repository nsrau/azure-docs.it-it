---
title: includere file
description: Includere file
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 8d5f9b360e1d81bae62e34a8e20d61636e1d22ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859586"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Limiti relativi alla rete - Azure Resource Manager
I limiti seguenti si applicano solo alle risorse di rete gestite tramite **Azure Resource Manager** per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Sono stati aumentati di recente tutti i limiti predefiniti fino al rispettivo massimo. Se non è presente una colonna Limite massimo, la risorsa non è associata a limiti modificabili. Se tali limiti sono stati precedentemente aumentati dal supporto e nelle tabelle seguenti non sono visibili i limiti aggiornati, [aprire una richiesta di assistenza clienti online senza costi aggiuntivi](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Risorsa | Limite | 
| --- | --- |
| Reti virtuali |1.000 |
| Subnet per ogni rete virtuale |3.000 |
| Peering di rete virtuale per ogni rete virtuale |500 |
| [Gateway di rete virtuale (gateway VPN) per rete virtuale](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Gateway di rete virtuale (gateway ExpressRoute) per rete virtuale](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Server DNS per rete virtuale |20 |
| Indirizzo IP privati per ogni rete virtuale |65.536 |
| Indirizzi IP privati per interfaccia di rete |256 |
| Indirizzi IP privati per macchina virtuale |256 |
| Indirizzi IP pubblici per interfaccia di rete |256 |
| Indirizzi IP pubblici per macchina virtuale |256 |
| [Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500.000 |
| Schede di interfaccia di rete |65.536 |
| Gruppi di sicurezza di rete |5\.000 |
| Regole NSG per NSG |1\.000 |
| Indirizzi IP e intervalli specificati per l'origine o la destinazione in un gruppo di sicurezza |4.000 |
| Gruppi di sicurezza delle applicazioni |3,000 |
| Gruppi di sicurezza delle applicazioni per ogni configurazione IP, per ogni scheda di interfaccia di rete |20 |
| Configurazioni IP per ogni gruppo di sicurezza delle applicazioni |4.000 |
| Gruppi di sicurezza delle applicazioni che possono essere specificati in tutte le regole di sicurezza di un gruppo di sicurezza di rete |100 |
| Tabelle di route definite dall'utente |200 |
| Route definite dall'utente per tabella di route |400 |
| Certificati radice da punto a sito per gateway VPN di Azure |20 |
| TAP di rete virtuale |100 |
| Configurazioni TAP di interfaccia di rete per TAP di rete virtuale |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limiti per gli indirizzi IP pubblici
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Indirizzi IP pubblici<sup>1</sup> | 10 per Basic. | Contattare il supporto tecnico. |
| Indirizzi IP pubblici statici<sup>1</sup> | 10 per Basic. | Contattare il supporto tecnico. |
| Indirizzi IP pubblici standard<sup>1</sup> | 10 | Contattare il supporto tecnico. |
| Prefissi per indirizzi IP pubblici | Limiti basati sul numero di indirizzi IP pubblici standard di una sottoscrizione | Contattare il supporto tecnico. |
| Lunghezza dei prefissi di indirizzi IP pubblici | /28 | Contattare il supporto tecnico. |

<sup>1</sup>I limiti predefiniti per gli indirizzi IP pubblici variano in base al tipo di categoria dell'offerta, ad esempio versione di valutazione gratuita, pagamento in base al consumo, CSP. Ad esempio, il valore predefinito per le sottoscrizioni con contratto Enterprise è 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limiti del bilanciamento del carico
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

**Load Balancer Standard**

| Risorsa                                | Limite         |
|-----------------------------------------|-------------------------------|
| Servizi di bilanciamento del carico                          | 1\.000                         |
| Regole per risorsa                      | 1\.500                         |
| Regole per scheda di interfaccia di rete (per tutti gli indirizzi IP in una scheda di interfaccia di rete) | 300                           |
| Configurazioni IP front-end              | 600                           |
| Dimensioni del pool back-end                       | 1\.000 configurazioni IP, singola rete virtuale |
| Risorse di back-end per Load Balancer<sup>1<sup> | 150                   |
| Porte a disponibilità elevata                 | 1 per front-end interno       |
| Regole in uscita per Load Balancer        | 600                           |

<sup>1</sup>Il limite è fino a 150 risorse, in qualsiasi combinazione di risorse macchine virtuali autonome, risorse set di disponibilità e gruppi di selezione host per set di scalabilità di macchine virtuali.

**Load Balancer di base**

| Risorsa                                | Limite        |
|-----------------------------------------|------------------------------|
| Servizi di bilanciamento del carico                          | 1\.000                        |
| Regole per risorsa                      | 250                          |
| Regole per scheda di interfaccia di rete (per tutti gli indirizzi IP in una scheda di interfaccia di rete) | 300                          |
| Configurazioni IP front-end              | 200                          |
| Dimensioni del pool back-end                       | 300 configurazioni IP, singolo set di disponibilità |
| Set di disponibilità per Load Balancer     | 1                            |

<a name="virtual-networking-limits-classic"></a>I limiti seguenti si applicano solo alle risorse di rete gestite tramite il modello di distribuzione **classico** per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |100 |100 |
| Siti di rete locale |20 |50 |
| Server DNS per rete virtuale |20 |20 |
| Indirizzo IP privati per ogni rete virtuale |4\.096 |4\.096 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500.000, fino a 1.000.000 per due o più schede di interfaccia di rete. |500.000, fino a 1.000.000 per due o più schede di interfaccia di rete. |
| Gruppi di sicurezza di rete (NGS) |200 |200 |
| Regole NSG per NSG |200 |1\.000 |
| Tabelle di route definite dall'utente |200 |200 |
| Route definite dall'utente per tabella di route |400 |400 |
| Indirizzi IP pubblici (dinamici) |500 |500 |
| Indirizzi IP pubblici riservati. |500 |500 |
| VIP pubblico per ogni distribuzione |5 |Contattare il supporto tecnico |
| Indirizzo VIP privato (bilanciamento del carico interno) per distribuzione |1 |1 |
| Elenchi di controllo di accesso (ACL) per endpoint |50 |50 |
