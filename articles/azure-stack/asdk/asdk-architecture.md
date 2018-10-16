---
title: Architettura di Azure Stack Development Kit | Microsoft Docs
description: Descrive l'architettura di Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 21c54e2e996bb987f7a27ac3e6333df6f74d6f4b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338625"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architettura di Microsoft Azure Stack Development Kit
Azure Stack Development Kit (ASDK) è una distribuzione a nodo singolo di Azure Stack. Tutti i componenti vengono installati nelle macchine virtuali in esecuzione in un singolo computer host. 

## <a name="logical-architecture-diagram"></a>Diagramma dell'architettura logica
Il diagramma seguente illustra l'architettura logica del ASDK e dei relativi componenti.

![Architettura ASDK](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Ruoli macchina virtuale
Il ASDK offre servizi tramite le seguenti macchine virtuali ospitate nel computer host kit di sviluppo:

| NOME | DESCRIZIONE |
| ----- | ----- |
| **AzS-ACS01** | Servizi di archiviazione di Azure Stack.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
| **AzS-BGPNAT01** | Router perimetrali e offre funzionalità di VPN e NAT per Azure Stack. |
| **AzS-CA01** | Servizi dell'autorità di certificati per i servizi ruolo di Azure Stack.|
| **AzS-DC01** | Servizi Directory, DNS e DHCP attivi per Microsoft Azure Stack.|
| **AzS-ERCS01** | Console di ripristino di emergenza della macchina virtuale. |
| **AzS-GWY01** | I servizi gateway Edge, ad esempio le connessioni site-to-site VPN per le reti tenant.|
| **AzS-NC01** | Controller di rete, che gestisce i servizi di rete di Azure Stack.  |
| **AzS-SLB01** | Il bilanciamento del carico multiplexer servizi in Azure Stack per i tenant e i servizi di infrastruttura di Azure Stack.  |
| **AzS-SQL01** | Archivio dati interni per i ruoli di infrastruttura di Azure Stack.  |
| **AzS-WAS01** | Servizi di Azure Resource Manager e portale amministrativo di Azure Stack.|
| **AzS-WASP01**| Portale di Azure Stack utente (tenant) e i servizi di Azure Resource Manager.|
| **AzS-XRP01** | Controller di gestione dell'infrastruttura per Microsoft Azure Stack, inclusi i provider di risorse di calcolo, rete e archiviazione.|


## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sulle attività di amministrazione ASDK base](asdk-admin-basics.md)
