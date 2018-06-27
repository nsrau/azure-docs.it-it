---
title: File di inclusione
description: File di inclusione
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 06/20/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: b9e06865b4a401cd925cce564b9c30594c912bae
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36297967"
---
<a name="virtual-networking-limits-classic"></a>I limiti seguenti si applicano solo per le risorse di rete gestite tramite il modello di distribuzione classica per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |50 |100 |
| Siti di rete locale |20 |contattare il supporto tecnico |
| Server DNS per rete virtuale |20 |100 |
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

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |50 |1000 |
| Subnet per rete virtuale |1000 |10000 |
| Peering reti virtuali per rete virtuale |10 |50 |
| Server DNS per rete virtuale |9 |25 |
| Indirizzi IP privati per rete virtuale |16384** |16384 |
| Indirizzi IP privati per interfaccia di rete |256 |1024 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500K |500K |
| Interfacce di rete (NIC) |24000** |24000 |
| Gruppi di sicurezza di rete (NGS) |100 |5000 |
| Regole NSG per NSG |1000** |1000 |
| Indirizzi IP e intervalli specificati per l'origine o la destinazione in un gruppo di sicurezza |2000 |4000 |
| Gruppi di sicurezza delle applicazioni |500 |3000 |
| Gruppi di sicurezza delle applicazioni per ogni configurazione IP, per ogni scheda di interfaccia di rete |10 |20 |
| Configurazioni IP per ogni gruppo di sicurezza delle applicazioni |1000 |4000 |
| Gruppi di sicurezza delle applicazioni che possono essere specificati in tutte le regole di sicurezza di un gruppo di sicurezza di rete |50 |100 |
| Tabelle di route definite dall’utente |100 |200 |
| Le route definite dall’utente per ogni tabella di route |100 |400 |
| Indirizzi IP pubblici: dinamici |(Basic) 60 |contattare il supporto tecnico |
| Indirizzi IP pubblici: statici |(Basic) 20 |contattare il supporto tecnico |
| Indirizzi IP pubblici: statici |(Standard) 20 |contattare il supporto tecnico |
| Certificati radice Point-to-Site per ogni gateway VPN |20 |20 |

**Questi limiti predefiniti si applicano alle sottoscrizioni per le quali i limiti non sono stati precedentemente aumentati tramite il supporto

#### <a name="load-balancer"></a>Limiti del bilanciamento del carico
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md)

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Servizi di bilanciamento del carico | 100 | 1000 |
| Regole per risorsa, Basic | 150 | 250 |
| Regole per risorsa, Standard | 1250 | 1500 |
| Regole per configurazione IP | 299 |299 |
| Configurazioni IP front-end, Basic | 10 | 200 |
| Configurazioni IP front-end, Standard | 10 | 600 |
| Pool back-end, Basic | 100, singolo set di disponibilità | 100, singolo set di disponibilità |
| Pool back-end, Standard | 1000, singola rete virtuale | 1000, singola rete virtuale |
| Risorse di back-end per Load Balancer, Standard &ast; | 50 | 150 |
| Porte a disponibilità elevata, Standard | 1 per front-end interno | 1 per front-end interno |

&ast; Fino a 150 risorse, qualsiasi combinazione di macchine virtuali autonome, set di disponibilità e set di scalabilità di macchine virtuali.

Se è necessario aumentare i limiti rispetto all'impostazione predefinita, [contattare il supporto tecnico](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

