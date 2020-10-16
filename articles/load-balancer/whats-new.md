---
title: Novità di Azure Load Balancer
description: Informazioni sulle novità di Azure Load Balancer, tra cui le note sulla versione più aggiornate, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 3a20b69189226486de8cfceb95e2fa79a976cb12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841034"
---
# <a name="whats-new-in-azure-load-balancer"></a>Novità di Azure Load Balancer

Azure Load Balancer viene aggiornato regolarmente. Restare aggiornati con gli ultimi annunci. Questo articolo illustra quanto segue:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate (se applicabile)

È anche possibile trovare gli aggiornamenti di Azure Load Balancer più recenti e sottoscrivere il feed RSS [qui](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="recent-releases"></a>Versioni recenti

| Type |Nome |Descrizione  |Data di aggiunta  |
| ------ |---------|---------|---------|
| Funzionalità | Supporto per spostamenti tra gruppi di risorse | Supporto per Load Balancer Standard e indirizzi IP pubblici Standard per gli [spostamenti di gruppi di risorse](https://azure.microsoft.com/updates/standard-resource-group-move/). | Ottobre 2020 |
| Funzionalità | Supporto per la gestione del pool back-end in base all'indirizzo IP (anteprima) | Azure Load Balancer supporta l'aggiunta e la rimozione di risorse da un pool back-end tramite indirizzi IPv4 o IPv6. In questo modo si semplifica la gestione di contenitori, macchine virtuali e set di scalabilità di macchine virtuali associati a Load Balancer. È inoltre possibile riservare indirizzi IP come parte di un pool back-end prima che vengano create le risorse associate. Fare clic [qui](backend-pool-management.md) per altre informazioni|Luglio 2020 |
| Funzionalità| Informazioni su Azure Load Balancer con Monitoraggio di Azure | Come parte di Monitoraggio di Azure per reti, i clienti hanno ora mappe topologiche di tutte le configurazioni e i dashboard sull'integrità delle loro istanze di Load Balancer Standard, preconfigurati con le metriche nel portale di Azure. [Introduzione e altre informazioni](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Giugno 2020 |
| Convalida | Aggiunta della convalida per le porte a disponibilità elevata | È stata aggiunta una convalida per garantire che le regole delle porta a disponibilità elevata e non a disponibilità elevata siano configurabili solo quando è abilitata l'opzione IP mobile. In precedenza, questa configurazione era accettata, ma non funzionava come previsto. Non è stata apportata alcuna modifica alla funzionalità. Per altre informazioni, fare clic [qui](load-balancer-ha-ports-overview.md#limitations)| Giugno 2020 |
| Funzionalità| Supporto di IPv6 per Azure Load Balancer (disponibile a livello generale) | È possibile avere indirizzi IPv6 come front-end per Azure Load Balancer. Vedere come [creare un'applicazione dual stack](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |Aprile 2020|
| Funzionalità| Reimpostazioni di TCP al timeout di inattività (disponibile a livello generale)| Usare le reimpostazioni di TCP per creare un comportamento più prevedibile dell'applicazione. [Altre informazioni](load-balancer-tcp-reset.md)| Febbraio 2020 |

## <a name="known-issues"></a>Problemi noti

Il gruppo responsabile dei prodotti sta lavorando attivamente alla risoluzione dei seguenti problemi noti:

|Problema |Descrizione  |Strategia di riduzione del rischio  |
| ---------- |---------|---------|
| Esportazione in Log Analytics | Log Analytics non è in grado di esportare le metriche per Load Balancer Standard né i log di stato del probe di integrità per Load Balancer Basic  | [Usare Monitoraggio di Azure per le metriche multidimensionali per Load Balancer Standard](load-balancer-standard-diagnostics.md). Mentre non è possibile usare Log Analytics per il monitoraggio, Monitoraggio di Azure consente di visualizzare un set completo di metriche multidimensionali. È possibile sfruttare il dashboard delle metriche preconfigurate tramite il sottopannello Informazioni dettagliate di Load Balancer. Se si usa Load Balancer Basic, [eseguire l'aggiornamento alla versione Standard](upgrade-basic-standard.md) per il monitoraggio delle metriche a livello di produzione.

  

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Load Balancer, vedere [Informazioni su Azure Load Balancer](load-balancer-overview.md) e le [domande frequenti](load-balancer-faqs.md).
