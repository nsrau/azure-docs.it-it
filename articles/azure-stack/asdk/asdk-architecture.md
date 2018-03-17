---
title: Architettura di Kit sviluppo Stack Azure | Documenti Microsoft
description: Viene descritta l'architettura di Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architettura del Kit di sviluppo di Microsoft Azure Stack
Il Kit di sviluppo dello Stack di Azure (ASDK) è una distribuzione a nodo singolo dello Stack di Azure. Tutti i componenti vengono installati nelle macchine virtuali in esecuzione in un computer singolo host. 

## <a name="logical-architecture-diagram"></a>Diagramma dell'architettura logica
Il diagramma seguente illustra l'architettura logica del ASDK e i relativi componenti.

![Architettura ASDK](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Ruoli di macchina virtuale
Il ASDK offre servizi che usano le seguenti macchine virtuali ospitate nel computer host kit di sviluppo:

| NOME | DESCRIZIONE |
| ----- | ----- |
| **AzS-ACS01** | Servizi di archiviazione di Azure dello Stack.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
| **AzS-BGPNAT01** | Bordo router e fornisce le funzionalità NAT e VPN per lo Stack di Azure. |
| **AzS-CA01** | Servizi dell'autorità di certificati per i servizi ruolo di Azure Stack.|
| **AzS-DC01** | Servizi Directory, DNS e DHCP attivi per lo Stack di Microsoft Azure.|
| **AzS-ERCS01** | Ripristino di emergenza Console macchina virtuale. |
| **AzS-GWY01** | Gateway di confine servizi, ad esempio le connessioni site-to-site VPN per le reti tenant.|
| **AzS-NC01** | Controller di rete, che gestisce i servizi di rete dello Stack di Azure.  |
| **AzS-SLB01** | Il bilanciamento del carico di servizi multiplexer nello Stack di Azure per i tenant e servizi di infrastruttura di Azure Stack.  |
| **AzS-SQL01** | Archiviano i dati interni per i ruoli di infrastruttura di Azure Stack.  |
| **AzS-WAS01** | Servizi di gestione risorse di Azure e Azure portale di amministrazione di Stack.|
| **AzS-WASP01**| Portale di Azure Stack utente (tenant) e i servizi di gestione risorse di Azure.|
| **AzS-XRP01** | Controller di gestione dell'infrastruttura per lo Stack di Microsoft Azure, inclusi i provider di risorse di calcolo, rete e archiviazione.|


## <a name="next-steps"></a>Passaggi successivi
[Informazioni sulle attività di amministrazione ASDK base](asdk-admin-basics.md)
