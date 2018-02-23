---
title: Visualizzare l'uso di indirizzo IP pubblico nello Stack di Azure | Documenti Microsoft
description: Gli amministratori possono visualizzare il consumo di indirizzi IP pubblici in un'area
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Visualizzare l'uso di indirizzo IP pubblico nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Come un amministratore del cloud, è possibile visualizzare il numero di indirizzi IP pubblici che sono stati assegnati ai tenant, il numero di indirizzi IP pubblici che sono ancora disponibili per l'allocazione e la percentuale di indirizzi IP pubblici che sono state allocate in quella posizione.

Il **utilizzo pool di IP pubblico** riquadro mostra il numero totale di indirizzi IP pubblici utilizzati in tutti i pool di indirizzi IP pubblici nell'infrastruttura, se sono stati usati per il tenant istanze VM IaaS, infrastruttura servizi o le risorse indirizzo IP pubbliche che sono state create in modo esplicito dai tenant.

Lo scopo di questo riquadro è per fornire agli amministratori di Azure Stack senso del numero complessivo di indirizzi IP pubblici utilizzati in questa posizione. Consente agli amministratori di determinare se eseguono basse per questa risorsa.

Nel **i provider di risorse**, **rete** Pannello di **gli indirizzi IP pubblici** voce di menu in **risorse Tenant** Elenca solo tali pubblico Gli indirizzi IP che sono stati *create in modo esplicito dai tenant*. Questa situazione, il numero di **utilizzato** gli indirizzi IP pubblici nel **utilizzo pool di IP pubblico** riquadro sempre è diverso da (maggiore di) numero sul **gli indirizzi IP pubblici** riquadro in **risorse del Tenant**.

## <a name="view-the-public-ip-address-usage-information"></a>Visualizzare le informazioni sull'utilizzo di indirizzi IP pubbliche
Per visualizzare il numero totale di indirizzi IP pubblici utilizzati nell'area:

1. Nel portale di amministrazione di Azure Stack, fare clic su **più servizi**in **risorse amministrative**, fare clic su **i provider di risorse**.
2. Dall'elenco di **i provider di risorse**selezionare **rete**.
3. Il **rete** pannello viene visualizzato il **utilizzo pool di IP pubblico** riquadro nel **Panoramica** sezione.

![Pannello di Provider di risorse di rete](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Tenere presente che il **utilizzato** numero rappresenta il numero di indirizzi IP pubblici da tutte pubbliche pool di indirizzi IP in tale percorso assegnati. Il **libero** numero rappresenta il numero degli indirizzo IP pubblico indirizzi dall'indirizzo IP pubblico a tutti i pool che non siano stati assegnati e sono ancora disponibili di indirizzi. Il **% utilizzate** numero rappresenta il numero di utilizzato o assegnati indirizzi come percentuale del numero totale di indirizzi IP pubblici in tutti i pool di indirizzi IP pubblici in tale percorso.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Visualizzare gli indirizzi IP pubblici che sono stati creati dalle sottoscrizioni di tenant
Per visualizzare un elenco di indirizzi IP pubblici che sono stati creati in modo esplicito dalle sottoscrizioni tenant in un'area specifica, fare clic su **gli indirizzi IP pubblici** in **risorse Tenant**.

![Indirizzi IP pubblici tenant](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

È possibile notare che alcuni indirizzi IP pubblici che sono stati assegnati in modo dinamico vengono visualizzate nell'elenco ma non dispone di un indirizzo al ancora. Questo avviene perché la risorsa di indirizzo è ancora stata creata nel Provider di risorse di rete, ma non nel Controller di rete.

Il Controller di rete non assegna un indirizzo per questa risorsa fino a quando non è effettivamente associato a un'interfaccia, una scheda di interfaccia di rete (NIC), un bilanciamento del carico o un gateway di rete virtuale. Quando l'indirizzo IP pubblico è associato a un'interfaccia, Controller di rete consente di allocare un indirizzo IP e viene visualizzato nel **indirizzo** campo.

## <a name="view-the-public-ip-address-information-summary-table"></a>Visualizzare il pubblico informazioni riepilogo tabella di indirizzi IP
Esistono una serie di diversi casi in cui vengono assegnati indirizzi IP pubblici che determinano se l'indirizzo viene visualizzato un elenco o un altro.

| **Caso di assegnazione indirizzo IP pubblico** | **Viene visualizzata nel riepilogo di utilizzo** | **Viene visualizzata nell'elenco di indirizzi IP pubblico tenant** |
| --- | --- | --- |
| Dinamica indirizzo IP pubblico non è ancora assegnato a una scheda di rete o di carico bilanciamento (temporaneo) |No  |Sì |
| Pubblica indirizzo IP dinamico assegnato a un servizio di bilanciamento NIC o di carico. |Sì |Sì |
| Indirizzo IP pubblico statico assegnato a un bilanciamento NIC o di caricamento del tenant. |Sì |Sì |
| Indirizzo IP pubblico statico assegnato a un endpoint di servizio di infrastruttura dell'infrastruttura. |Sì |No  |
| Indirizzo IP pubblico, in modo implicito, creato per le istanze di VM IaaS e utilizzato per NAT in uscita sulla rete virtuale. Dietro le quinte vengono creati ogni volta che un tenant crea un'istanza di macchina virtuale in modo che le macchine virtuali possono inviare informazioni a Internet. |Sì |No  |

## <a name="next-steps"></a>Passaggi successivi
[Gestire gli account di archiviazione in Azure Stack](azure-stack-manage-storage-accounts.md)