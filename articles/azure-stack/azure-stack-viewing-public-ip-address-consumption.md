---
title: Visualizzare l'uso di indirizzo IP pubblico nello Stack di Azure | Documenti Microsoft
description: Gli amministratori possono visualizzare il consumo di indirizzi IP pubblici in un'area
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 50bf01d6de6105d3041c6bb88e803f3d110f751d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Visualizzare l'uso di indirizzo IP pubblico nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Come un amministratore del cloud, è possibile visualizzare:
 - Il numero di indirizzi IP pubblici che sono stati assegnati ai tenant.
 - Il numero di indirizzi IP pubblici che sono ancora disponibili per l'allocazione.
 - La percentuale di indirizzi IP pubblici che sono state allocate in quella posizione.

Il **utilizzo pool di IP pubblico** riquadro mostra il numero di indirizzi IP pubblici utilizzati tra i pool di indirizzi IP pubblici. Per ogni indirizzo IP, il riquadro mostra l'utilizzo per tenant VM IaaS istanze, i servizi di infrastruttura dell'infrastruttura e le risorse indirizzo IP pubbliche che sono state create in modo esplicito dai tenant.

Lo scopo del riquadro è per gli operatori di Azure Stack un'idea del numero di indirizzi IP pubblici utilizzati in questa posizione. Il numero consente agli amministratori di determinare se eseguono basse per questa risorsa.

Il **gli indirizzi IP pubblici** voce di menu in **risorse Tenant** Elenca solo gli indirizzi IP pubblici che sono stati *create in modo esplicito dai tenant*. È possibile trovare la voce di menu nel **i provider di risorse**, **rete** riquadro. Il numero di **utilizzato** gli indirizzi IP pubblici nel **utilizzo pool di IP pubblico** riquadro sempre è diverso da (maggiore di) sul numero di **gli indirizzi IP pubblici** riquadro sotto  **Risorse del tenant**.

## <a name="view-the-public-ip-address-usage-information"></a>Visualizzare le informazioni sull'utilizzo di indirizzi IP pubbliche
Per visualizzare il numero totale di indirizzi IP pubblici utilizzati nell'area:

1. Nel portale di amministrazione di Azure Stack selezionare **più servizi**in **risorse amministrative**selezionare **i provider di risorse**.
2. Dall'elenco di **i provider di risorse**selezionare **rete**.
3. Il **rete** riquadro viene visualizzato il **utilizzo pool di IP pubblico** riquadro nel **Panoramica** sezione.

![Riquadro di Provider di risorse di rete](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Il **utilizzato** numero rappresenta il numero di assegnati indirizzi IP da pool di indirizzi IP pubblici. Il **libero** numero rappresenta il numero degli indirizzo IP pubblico indirizzi IP pubblici indirizzo pool che non sono stati assegnati e sono ancora disponibili. Il **% utilizzate** numero rappresenta il numero di utilizzato o assegnati indirizzi come percentuale del numero totale di indirizzi IP pubblici nel pool di indirizzi IP pubblici in tale percorso.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Visualizzare gli indirizzi IP pubblici che sono stati creati dalle sottoscrizioni di tenant
Selezionare **gli indirizzi IP pubblici** in **risorse Tenant**. Esaminare l'elenco di indirizzi IP pubblici creati in modo esplicito dalle sottoscrizioni tenant in un'area specifica.

![Indirizzi IP pubblici tenant](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

È possibile notare che alcuni indirizzi IP pubblici che sono stati assegnati in modo dinamico vengono visualizzate nell'elenco. Tuttavia, un indirizzo non è stato associato a essi ancora. La risorsa di indirizzo è stata creata nel Provider di risorse di rete, ma non ancora nel Controller di rete.

Il Controller di rete non assegna un indirizzo per la risorsa fino a quando non è associata a un'interfaccia, una scheda di interfaccia di rete (NIC), un bilanciamento del carico o un gateway di rete virtuale. Quando l'indirizzo IP pubblico viene associato a un'interfaccia, Controller di rete consente di allocare un indirizzo IP. L'indirizzo viene visualizzato nel **indirizzo** campo.

## <a name="view-the-public-ip-address-information-summary-table"></a>Visualizzare il pubblico informazioni riepilogo tabella di indirizzi IP
In diversi casi, vengono assegnati indirizzi IP pubblici che determinano se l'indirizzo viene visualizzato un elenco o un altro.

| **Caso di assegnazione indirizzo IP pubblico** | **Viene visualizzata nel riepilogo di utilizzo** | **Viene visualizzata nell'elenco di indirizzi IP pubblico tenant** |
| --- | --- | --- |
| Dinamica indirizzo IP pubblico non è ancora assegnato a una scheda di rete o di carico bilanciamento (temporaneo) |No  |Sì |
| Pubblica indirizzo IP dinamico assegnato a un servizio di bilanciamento NIC o di carico. |Sì |Sì |
| Indirizzo IP pubblico statico assegnato a un bilanciamento NIC o di caricamento del tenant. |Sì |Sì |
| Indirizzo IP pubblico statico assegnato a un endpoint di servizio di infrastruttura dell'infrastruttura. |Sì |No  |
| Indirizzo IP pubblico, in modo implicito, creato per le istanze di VM IaaS e utilizzato per NAT in uscita sulla rete virtuale. Dietro le quinte vengono creati ogni volta che un tenant crea un'istanza di macchina virtuale in modo che le macchine virtuali possono inviare informazioni a Internet. |Sì |No  |

## <a name="next-steps"></a>Passaggi successivi
[Gestire gli account di archiviazione in Azure Stack](azure-stack-manage-storage-accounts.md)