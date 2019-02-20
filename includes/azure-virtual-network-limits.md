---
title: File di inclusione
description: File di inclusione
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 02/07/2019
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: fe1993a914ea4d3bd8ab9116748198cbb0c1c43c
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55905146"
---
<a name="virtual-networking-limits-classic"></a>I limiti seguenti si applicano solo per le risorse di rete gestite tramite il modello di distribuzione classica per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |50 |100 |
| Siti di rete locale |20 |contattare il supporto tecnico |
| Server DNS per rete virtuale |20 |20 |
| Indirizzi IP privati per rete virtuale |4096 |4096 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500K |500K |
| Gruppi di sicurezza di rete (NGS) |100 |200 |
| Regole NSG per NSG |200 |1000 |
| Tabelle di route definite dall’utente |100 |200 |
| Le route definite dall’utente per ogni tabella di route |100 |400 |
| Indirizzi IP pubblici (dinamici) |5 |contattare il supporto tecnico |
| Indirizzi IP pubblici riservati. |20 |contattare il supporto tecnico |
| VIP pubblico per ogni distribuzione |5 |contattare il supporto tecnico |
| VIP privato (ILB) per ogni distribuzione |1 |1 |
| Elenchi di controllo di accesso (ACL) per endpoint |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limiti relativi alle reti - Azure Resource Manager
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Sono stati aumentati di recente tutti i limiti predefiniti fino al rispettivo massimo. Se non è presente nessuna colonna **Limite massimo**, per la risorsa specificata non sono disponibili limiti regolabili. Se tali limiti sono stati precedentemente aumentati tramite il supporto e non sono visibili i limiti aggiornati come indicato di seguito, [aprire una richiesta di assistenza clienti online senza costi aggiuntivi](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Risorsa | Limite predefinito | 
| --- | --- |
| Reti virtuali |1000 |
| Subnet per rete virtuale |3000 |
| Peering reti virtuali per rete virtuale |100 |
| Server DNS per rete virtuale |20 |
| Indirizzi IP privati per rete virtuale |65536 |
| Indirizzi IP privati per interfaccia di rete |256 |
| Indirizzi IP privati per macchina virtuale |256 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500K |
| Interfacce di rete (NIC) |65536 |
| Gruppi di sicurezza di rete (NGS) |5000 |
| Regole NSG per NSG |1000 |
| Indirizzi IP e intervalli specificati per l'origine o la destinazione in un gruppo di sicurezza |4000 |
| Gruppi di sicurezza delle applicazioni |3000 |
| Gruppi di sicurezza delle applicazioni per ogni configurazione IP, per ogni scheda di interfaccia di rete |20 |
| Configurazioni IP per ogni gruppo di sicurezza delle applicazioni |4000 |
| Gruppi di sicurezza delle applicazioni che possono essere specificati in tutte le regole di sicurezza di un gruppo di sicurezza di rete |100 |
| Tabelle di route definite dall’utente |200 |
| Le route definite dall’utente per ogni tabella di route |400 |
| Certificati radice Point-to-Site per ogni gateway VPN |20 |
| TAP di rete virtuale |100 |
| Configurazioni TAP di interfaccia di rete per TAP di rete virtuale |100 |

#### <a name="publicip-address"></a>Limiti per gli indirizzi IP pubblici
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Indirizzi IP pubblici: dinamici |(Basic) 1000 |contattare il supporto tecnico |
| Indirizzi IP pubblici: statici |(Basic) 200 |contattare il supporto tecnico |
| Indirizzi IP pubblici: statici |(Standard) 200 |contattare il supporto tecnico |
| Dimensione del prefisso indirizzo IP pubblico (anteprima) | /28 | /28 |

#### <a name="load-balancer"></a>Limiti del bilanciamento del carico
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md)

| Risorsa | Limite predefinito |
| --- | --- |
| Servizi di bilanciamento del carico | 1000 | 
| Regole per risorsa, Basic | 250 |
| Regole per risorsa, Standard | 1500 | 
| Regole per configurazione IP | 299 |
| Regole per NIC | 500 |
| Configurazioni IP front-end, Basic | 200 |
| Configurazioni IP front-end, Standard | 600 |
| Pool back-end, Basic | 100, singolo set di disponibilità |
| Pool back-end, Standard | 1000, singola rete virtuale |
| Risorse di back-end per Load Balancer, Standard* | 150 |
| Porte a disponibilità elevata, Standard | 1 per front-end interno |

**Fino a 150 risorse, qualsiasi combinazione di risorse di macchine virtuali, set di disponibilità e set di scalabilità di macchine virtuali autonome.

