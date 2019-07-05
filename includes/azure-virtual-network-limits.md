---
title: File di inclusione
description: File di inclusione
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 3e66bf61caf786473d89eab9a6567bb05aff0d19
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457398"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Limiti relativi alle reti - Azure Resource Manager, i limiti seguenti si applicano solo per le risorse gestite tramite sulla rete **Azure Resource Manager** per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Sono stati aumentati di recente tutti i limiti predefiniti fino al rispettivo massimo. Se è presente nessuna colonna limite massimo consentito, la risorsa non è disponibili limiti regolabili. Se si avesse questi limiti aumentati di supporto in passato e non vengono visualizzati i limiti di aggiornato nelle tabelle seguenti, [aprire una richiesta di assistenza clienti online senza costi aggiuntivi](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resource | Limite predefinito o massimo | 
| --- | --- |
| Reti virtuali |1\.000 |
| Subnet per rete virtuale |3,000 |
| Peering reti virtuali per rete virtuale |500 |
| Server DNS per rete virtuale |20 |
| Indirizzi IP privati per ogni rete virtuale |65,536 |
| Indirizzi IP privati per ogni interfaccia di rete |256 |
| Indirizzi IP privati per ogni macchina virtuale |256 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500,000 |
| Schede di interfaccia di rete |65,536 |
| Gruppi di sicurezza di rete |5\.000 |
| Regole NSG per NSG |1\.000 |
| Indirizzi IP e intervalli specificati per l'origine o la destinazione in un gruppo di sicurezza |4\.000 |
| Gruppi di sicurezza delle applicazioni |3,000 |
| Gruppi di sicurezza delle applicazioni per ogni configurazione IP, per ogni scheda di interfaccia di rete |20 |
| Configurazioni IP per ogni gruppo di sicurezza delle applicazioni |4\.000 |
| Gruppi di sicurezza delle applicazioni che possono essere specificati in tutte le regole di sicurezza di un gruppo di sicurezza di rete |100 |
| Tabelle di route definite dall'utente |200 |
| Route definite dall'utente per ogni tabella di route |400 |
| Certificati radice Point-to-site per ogni Gateway VPN di Azure |20 |
| TAP di rete virtuale |100 |
| Configurazioni TAP di interfaccia di rete per TAP di rete virtuale |100 |

#### <a name="publicip-address"></a>Limiti per gli indirizzi IP pubblici
| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Indirizzi IP pubblici: dinamici | 1\.000 per Basic. |Contattare il supporto tecnico. |
| Indirizzi IP pubblici: statici | 1\.000 per Basic. |Contattare il supporto tecnico. |
| Indirizzi IP pubblici: statici | 200 per il livello Standard.|Contattare il supporto tecnico. |
| Lunghezza del prefisso IP pubblico | /28 | Contattare il supporto tecnico. |

#### <a name="load-balancer"></a>Limiti del bilanciamento del carico
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Resource | Limite predefinito o massimo |
| --- | --- |
| Servizi di bilanciamento del carico | 1\.000 | 
| Regole per risorsa, Basic | 250 |
| Regole per risorsa, Standard | 1,500 | 
| Regole per configurazione IP | 299 |
| Regole per NIC | 300 |
| Configurazioni IP front-end, Basic | 200 |
| Configurazioni IP front-end, Standard | 600 |
| Pool back-end, Basic | 100, singolo set di disponibilità |
| Pool back-end, Standard | 1000, singola rete virtuale |
| Risorse di back-end per ogni servizio di bilanciamento del carico, Standard<sup>1</sup> | 150 |
| Porte a disponibilità elevata, Standard | 1 all'interno di front-end |

<sup>1</sup>il limite di risorse fino a 150, in qualsiasi combinazione di risorse delle macchine virtuali autonome, set di disponibilità di risorse e le risorse di set di scalabilità di macchine virtuali.

#### <a name="virtual-networking-limits-classic"></a>I limiti seguenti si applicano solo per rete le risorse gestite tramite il **classico** modello di distribuzione per ogni sottoscrizione. Informazioni su come [visualizzare l'utilizzo delle risorse corrente rispetto ai limiti della sottoscrizione](../articles/networking/check-usage-against-limits.md).

| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |100 |100 |
| Siti di rete locale |20 |Contattare il supporto tecnico. |
| Server DNS per rete virtuale |20 |20 |
| Indirizzi IP privati per ogni rete virtuale |4\.096 |4\.096 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500.000, fino a 1.000.000 per due o più schede di rete. |500.000, fino a 1.000.000 per due o più schede di rete. |
| Gruppi di sicurezza di rete (NGS) |200 |200 |
| Regole NSG per NSG |1\.000 |1\.000 |
| Tabelle di route definite dall'utente |200 |200 |
| Route definite dall'utente per ogni tabella di route |400 |400 |
| Indirizzi IP pubblici (dinamici) |500 |500 |
| Indirizzi IP pubblici riservati. |500 |500 |
| VIP pubblico per ogni distribuzione |5 |Contattare il supporto tecnico |
| VIP privato (bilanciamento del carico interno) per ogni distribuzione |1 |1 |
| Elenchi di controllo di accesso di endpoint (ACL) |50 |50 |
