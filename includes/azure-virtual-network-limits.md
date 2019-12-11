---
title: file di inclusione
description: file di inclusione
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 12/09/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 2596351a0db921276ad74c27e30a8e2af93f2f36
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980760"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Limiti di rete: Azure Resource Manager si applicano i limiti seguenti solo per le risorse di rete gestite tramite **Azure Resource Manager** per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Sono stati aumentati di recente tutti i limiti predefiniti fino al rispettivo massimo. Se non è presente alcuna colonna limite massimo, la risorsa non ha limiti regolabili. Se questi limiti sono aumentati in base al supporto in passato e non vengono visualizzati limiti aggiornati nelle tabelle seguenti, [aprire una richiesta di assistenza clienti online senza alcun addebito](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Gruppi | Limite predefinito/massimo | 
| --- | --- |
| Reti virtuali |1\.000 |
| Subnet per rete virtuale |3\.000 |
| Peering di rete virtuale per ogni rete virtuale |500 |
| [Gateway di rete virtuale (gateway VPN) per rete virtuale](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| Server DNS per ogni rete virtuale |20 |
| Indirizzi IP privati per ogni rete virtuale |65,536 |
| Indirizzi IP privati per interfaccia di rete |256 |
| Indirizzi IP privati per macchina virtuale |256 |
| Indirizzi IP pubblici per interfaccia di rete |256 |
| Indirizzi IP pubblici per macchina virtuale |256 |
| [Flussi TCP o UDP simultanei per NIC di una macchina virtuale o di un'istanza del ruolo](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500.000 |
| Schede di interfaccia di rete |65,536 |
| Gruppi di sicurezza di rete |5\.000 |
| Regole NSG per NSG |1\.000 |
| Indirizzi IP e intervalli specificati per l'origine o la destinazione in un gruppo di sicurezza |4\.000 |
| Gruppi di sicurezza delle applicazioni |3\.000 |
| Gruppi di sicurezza delle applicazioni per ogni configurazione IP, per ogni scheda di interfaccia di rete |20 |
| Configurazioni IP per ogni gruppo di sicurezza delle applicazioni |4\.000 |
| Gruppi di sicurezza delle applicazioni che possono essere specificati in tutte le regole di sicurezza di un gruppo di sicurezza di rete |100 |
| Tabelle di route definite dall'utente |200 |
| Route definite dall'utente per ogni tabella di route |400 |
| Certificati radice da punto a sito per gateway VPN di Azure |20 |
| TAP di rete virtuale |100 |
| Configurazioni TAP di interfaccia di rete per TAP di rete virtuale |100 |

#### <a name="publicip-address"></a>Limiti per gli indirizzi IP pubblici
| Gruppi | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Indirizzi IP pubblici: dinamici | 1\.000 per di base. |Contattare il supporto tecnico. |
| Indirizzi IP pubblici: statici | 1\.000 per di base. |Contattare il supporto tecnico. |
| Indirizzi IP pubblici: statici | 1\.000 per standard.|Contattare il supporto tecnico. |
| Lunghezza prefisso IP pubblico | /28 | Contattare il supporto tecnico. |

#### <a name="load-balancer"></a>Limiti del servizio di bilanciamento del carico
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Gruppi | Limite predefinito/massimo |
| --- | --- |
| Servizi di bilanciamento del carico | 1\.000 | 
| Regole per risorsa, Basic | 250 |
| Regole per risorsa, Standard | 1\.500 | 
| Regole per configurazione IP | 299 |
| Regole per NIC | 300 |
| Configurazioni IP front-end, di base | 200 |
| Configurazioni IP front-end, standard | 600 |
| Pool back-end, di base | 100, singolo set di disponibilità |
| Pool back-end, standard | 1\.000, rete virtuale singola |
| Risorse back-end per ogni servizio di bilanciamento del carico, standard<sup>1</sup> | 150 |
| Porte a disponibilità elevata, standard | 1 per front-end interno |

<sup>1</sup> Il limite è fino a 150 risorse, in qualsiasi combinazione di risorse autonome della macchina virtuale, risorse dei set di disponibilità e risorse del set di scalabilità di macchine virtuali.

#### <a name="virtual-networking-limits-classic"></a>I limiti seguenti si applicano solo alle risorse di rete gestite tramite il modello di distribuzione **classica** per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Gruppi | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |100 |100 |
| Siti di rete locale |20 |50 |
| Server DNS per ogni rete virtuale |20 |20 |
| Indirizzi IP privati per ogni rete virtuale |4\.096 |4\.096 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500.000, fino a 1 milione per due o più schede di rete. |500.000, fino a 1 milione per due o più schede di rete. |
| Gruppi di sicurezza di rete (NGS) |200 |200 |
| Regole NSG per NSG |1\.000 |1\.000 |
| Tabelle di route definite dall'utente |200 |200 |
| Route definite dall'utente per ogni tabella di route |400 |400 |
| Indirizzi IP pubblici (dinamici) |500 |500 |
| Indirizzi IP pubblici riservati. |500 |500 |
| VIP pubblico per ogni distribuzione |5 |Contattare il supporto tecnico |
| VIP privato (bilanciamento del carico interno) per distribuzione |1 |1 |
| Elenchi di controllo di accesso (ACL) endpoint |50 |50 |
