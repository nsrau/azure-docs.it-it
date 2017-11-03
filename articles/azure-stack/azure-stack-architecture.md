---
title: Architettura del Kit di sviluppo di Microsoft Azure Stack | Documenti Microsoft
description: Consente di visualizzare l'architettura di Microsoft Azure Stack Development Kit.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e8ff17e069bd9237bf06cd79b4c222c50a999eda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architettura del Kit di sviluppo di Microsoft Azure Stack

*Si applica a: Azure Stack Development Kit*

Il Kit di sviluppo dello Stack di Azure è una distribuzione a nodo singolo dello Stack di Azure. Tutti i componenti vengono installati nelle macchine virtuali in esecuzione in un computer singolo host. 

## <a name="logical-architecture-diagram"></a>Diagramma dell'architettura logica
Il diagramma seguente illustra l'architettura logica del kit di sviluppo dello Stack di Azure e i relativi componenti.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Ruoli di macchina virtuale
Il kit di sviluppo dello Stack di Azure offre servizi tramite le seguenti macchine virtuali sull'host:

| Nome | Descrizione |
| ----- | ----- |
| **AzS ACS01** | Servizi di archiviazione di Azure dello Stack.|
| **AzS ADFS01** | Active Directory Federation Services (ADFS).  |
| **AzS BGPNAT01** | Bordo router e fornisce le funzionalità NAT e VPN per lo Stack di Azure. |
| **AzS CA01** | Servizi dell'autorità di certificati per i servizi ruolo di Azure Stack.|
| **AzS-DC01.** | Servizi Directory, DNS e DHCP attivi per lo Stack di Microsoft Azure.|
| **AzS ERCS01** | Ripristino di emergenza Console macchina virtuale. |
| **AzS GWY01** | Gateway di confine servizi, ad esempio le connessioni site-to-site VPN per le reti tenant.|
| **AzS NC01** | Controller di rete, che gestisce i servizi di rete dello Stack di Azure.  |
| **AzS SLB01** | Il bilanciamento del carico di servizi multiplexer nello Stack di Azure per i tenant e servizi di infrastruttura di Azure Stack.  |
| **AzS SQL01** | Archiviano i dati interni per i ruoli di infrastruttura di Azure Stack.  |
| **AzS WAS01** | Servizi di gestione risorse di Azure e Azure portale di amministrazione di Stack.|
| **AzS WASP01**| Portale di Azure Stack utente (tenant) e i servizi di gestione risorse di Azure.|
| **AzS XRP01** | Controller di gestione dell'infrastruttura per lo Stack di Microsoft Azure, inclusi i provider di risorse di calcolo, rete e archiviazione.|


## <a name="next-steps"></a>Passaggi successivi
[Distribuire Azure Stack](azure-stack-deploy.md)

[Scenari principali da provare](azure-stack-first-scenarios.md)

