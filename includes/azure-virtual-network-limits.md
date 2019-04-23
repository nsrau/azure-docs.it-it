---
title: File di inclusione
description: File di inclusione
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 04/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ff6e08929d5ede86ba0632e7cfb38d81d21ca7ec
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804309"
---
<a name="virtual-networking-limits-classic"></a>I limiti seguenti si applicano solo per le risorse di rete gestite tramite il modello di distribuzione classica per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |50 |100 |
| Siti di rete locale |20 |Contattare il supporto tecnico. |
| Server DNS per rete virtuale |20 |20 |
| Indirizzi IP privati per ogni rete virtuale |4.096 |4.096 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500.000, fino a 1.000.000 per due o più schede di rete. |500.000, fino a 1.000.000 per due o più schede di rete. |
| Gruppi di sicurezza di rete (NGS) |100 |200 |
| Regole NSG per NSG |200 |1.000 |
| Tabelle di route definite dall'utente |100 |200 |
| Route definite dall'utente per ogni tabella di route |100 |400 |
| Indirizzi IP pubblici (dinamici) |5 |Contattare il supporto tecnico. |
| Indirizzi IP pubblici riservati. |20 |Contattare il supporto tecnico. |
| VIP pubblico per ogni distribuzione |5 |Contattare il supporto tecnico. |
| VIP privato (bilanciamento del carico interno) per ogni distribuzione |1 |1 |
| Elenchi di controllo di accesso di endpoint (ACL) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limiti relativi alle reti - Azure Resource Manager
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Sono stati aumentati di recente tutti i limiti predefiniti fino al rispettivo massimo. Se è presente nessuna colonna limite massimo consentito, la risorsa non è disponibili limiti regolabili. Se si avesse questi limiti aumentati di supporto in passato e non vengono visualizzati i limiti di aggiornato nelle tabelle seguenti, [aprire una richiesta di assistenza clienti online senza costi aggiuntivi](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Risorsa | Limite predefinito | 
| --- | --- |
| Reti virtuali |1.000 |
| Subnet per rete virtuale |3,000 |
| Peering reti virtuali per rete virtuale |500 |
| Server DNS per rete virtuale |20 |
| Indirizzi IP privati per ogni rete virtuale |65,536 |
| Indirizzi IP privati per ogni interfaccia di rete |256 |
| Indirizzi IP privati per ogni macchina virtuale |256 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500.000 |
| Schede di interfaccia di rete |65,536 |
| Gruppi di sicurezza di rete |5.000 |
| Regole NSG per NSG |1.000 |
| Indirizzi IP e intervalli specificati per l'origine o la destinazione in un gruppo di sicurezza |4.000 |
| Gruppi di sicurezza delle applicazioni |3,000 |
| Gruppi di sicurezza delle applicazioni per ogni configurazione IP, per ogni scheda di interfaccia di rete |20 |
| Configurazioni IP per ogni gruppo di sicurezza delle applicazioni |4.000 |
| Gruppi di sicurezza delle applicazioni che possono essere specificati in tutte le regole di sicurezza di un gruppo di sicurezza di rete |100 |
| Tabelle di route definite dall'utente |200 |
| Route definite dall'utente per ogni tabella di route |400 |
| Certificati radice Point-to-site per ogni Gateway VPN di Azure |20 |
| TAP di rete virtuale |100 |
| Configurazioni TAP di interfaccia di rete per TAP di rete virtuale |100 |

#### <a name="publicip-address"></a>Limiti per gli indirizzi IP pubblici
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Indirizzi IP pubblici: dinamici | 1.000 per Basic. |Contattare il supporto tecnico. |
| Indirizzi IP pubblici: statici | 1.000 per Basic. |Contattare il supporto tecnico. |
| Indirizzi IP pubblici: statici | 200 per il livello Standard.|Contattare il supporto tecnico. |
| Dimensione del prefisso IP pubblico (anteprima) | /28 | /28 |

#### <a name="load-balancer"></a>Limiti del bilanciamento del carico
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Risorsa | Limite predefinito |
| --- | --- |
| Servizi di bilanciamento del carico | 1.000 | 
| Regole per risorsa, Basic | 250 |
| Regole per risorsa, Standard | 1.500 | 
| Regole per configurazione IP | 299 |
| Regole per NIC | 500 |
| Configurazioni IP front-end, Basic | 200 |
| Configurazioni IP front-end, Standard | 600 |
| Pool back-end, Basic | 100, singolo set di disponibilità |
| Pool back-end, Standard | 1000, singola rete virtuale |
| Risorse di back-end per ogni servizio di bilanciamento del carico, Standard<sup>1</sup> | 150 |
| Porte a disponibilità elevata, Standard | 1 all'interno di front-end |

<sup>1</sup>il limite di risorse fino a 150, in qualsiasi combinazione di risorse delle macchine virtuali autonome, set di disponibilità di risorse e le risorse di set di scalabilità di macchine virtuali.

