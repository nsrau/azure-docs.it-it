---
title: Visualizzare il consumo di indirizzi IP pubblico in Azure Stack | Microsoft Docs
description: Gli amministratori possono visualizzare il consumo di indirizzi IP pubblici in un'area
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 6207a77555d0d4656c0728dddec769e483f4d762
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378245"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Visualizzare il consumo di indirizzi IP pubblico in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Come un amministratore del cloud, è possibile visualizzare:
 - Il numero di indirizzi IP pubblici che sono stati allocati ai tenant.
 - Il numero di indirizzi IP pubblici che sono ancora disponibili per l'allocazione.
 - La percentuale di indirizzi IP pubblici che sono stati allocati in tale percorso.

Il **utilizzo di pool di IP pubblici** riquadro Visualizza il numero di indirizzi IP pubblici utilizzati tra i pool di indirizzi IP pubblici. Per ogni indirizzo IP, il riquadro mostra l'utilizzo per il tenant VM IaaS di istanze, i servizi di infrastruttura dell'infrastruttura e risorse di indirizzo IP pubblico che sono state create in modo esplicito dai tenant.

Lo scopo del riquadro è per consentire agli operatori di Azure Stack un'idea del numero di indirizzi IP pubblici usati in questa posizione. Il numero consente agli amministratori di determinare se sono in esecuzione bassa per questa risorsa.

Il **indirizzi IP pubblici** nel menu **risorse del Tenant** Elenca solo gli indirizzi IP pubblici che sono stati *create in modo esplicito dai tenant*. È possibile trovare la voce di menu sul **provider di risorse**, **rete** riquadro. Il numero di **viene utilizzata** indirizzi IP pubblici nel **utilizzo pool di IP pubblico** riquadro è sempre diverso da (maggiore di) il numero sul **gli indirizzi IP pubblici** riquadro sotto  **Le risorse del tenant**.

## <a name="view-the-public-ip-address-usage-information"></a>Visualizzare le informazioni sull'utilizzo di indirizzi IP pubbliche
Per visualizzare il numero totale di indirizzi IP pubblici utilizzate nell'area:

1. Nel portale di amministrazione di Azure Stack, selezionare **tutti i servizi**. Quindi, sotto il **ADMINISTRATION** categoria, selezionare **rete**.
1. Il **rete** riquadro vengono visualizzate le **utilizzo pool di IP pubblico** riquadro nel **Panoramica** sezione.

![Riquadro del Provider di risorse di rete](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Il **utilizzato** numero rappresenta il numero di assegnare indirizzi IP pubblici dal pool di indirizzi IP pubblici. Il **gratuito** pool che non sono stati assegnati e sono ancora disponibili indirizzi numero rappresenta il numero di IP pubblici gli indirizzi IP pubblici. Il **% utilizzato** numero rappresenta il numero di usato o indirizzi assegnati come percentuale del numero totale di indirizzi IP pubblici nel pool di indirizzi IP pubblici in tale percorso.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Visualizzare gli indirizzi IP pubblici creati dalle sottoscrizioni di tenant
Selezionare **indirizzi IP pubblici** sotto **alle risorse Tenant**. Esaminare l'elenco di indirizzi IP pubblici creati in modo esplicito dalle sottoscrizioni di tenant in un'area specifica.

![Indirizzi IP pubblici di tenant](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

È possibile notare che alcuni indirizzi IP pubblici che sono stati allocati in modo dinamico visualizzato nell'elenco. Tuttavia, un indirizzo non è stato associato a essi ancora. La risorsa di indirizzo è stata creata nel Provider di risorse di rete, ma non ancora nel Controller di rete.

Il Controller di rete non assegna un indirizzo per la risorsa fino a quando non si esegue l'associazione a un'interfaccia, una scheda di interfaccia di rete (NIC), un servizio di bilanciamento del carico o un gateway di rete virtuale. Quando l'indirizzo IP pubblico viene associato a un'interfaccia, il Controller di rete alloca un indirizzo IP. L'indirizzo viene visualizzato nei **indirizzo** campo.

## <a name="view-the-public-ip-address-information-summary-table"></a>Visualizzare il pubblico informazioni riepilogo tabella di indirizzi IP
In diversi casi, vengono assegnati indirizzi IP pubblici che determinano se l'indirizzo viene visualizzato in un unico elenco o un altro.

| **Caso di assegnazione indirizzo IP pubblico** | **Viene visualizzato nel riepilogo di utilizzo** | **Viene visualizzata nell'elenco di indirizzi IP pubblico tenant** |
| --- | --- | --- |
| Indirizzo IP pubblico dinamico non ancora assegnato a un bilanciamento del carico o interfaccia di rete (temporaneo) |No  |Sì |
| Indirizzo IP pubblico dinamico assegnato a un servizio di bilanciamento del carico o interfaccia di rete. |Sì |Sì |
| Indirizzo IP pubblico statico assegnato a un bilanciamento del carico o interfaccia di rete tenant. |Sì |Sì |
| Indirizzo IP pubblico statico assegnato a un endpoint di service fabric dell'infrastruttura. |Sì |No  |
| Indirizzo IP pubblico creato per le istanze di VM IaaS in modo implicito e usato per NAT in uscita nella rete virtuale. Questi vengono creati dietro le quinte, ogni volta che un tenant crea un'istanza di macchina virtuale in modo che le macchine virtuali possono inviare informazioni a Internet. |Sì |No  |

## <a name="next-steps"></a>Passaggi successivi
[Gestire gli account di archiviazione in Azure Stack](azure-stack-manage-storage-accounts.md)