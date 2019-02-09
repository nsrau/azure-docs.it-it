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
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9157acc7517aea56f087a3dbff0fe7114f8b4c87
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958806"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architettura di Microsoft Azure Stack Development Kit
Azure Stack Development Kit (ASDK) è una distribuzione a nodo singolo di Azure Stack in esecuzione in un singolo computer host. Compontents routing Edge vengono installati nel computer host per fornire funzionalità NAT e VPN per Azure Stack. I ruoli di infrastruttura di Azure Stack eseguire nel livello di Hyper-V del computer host fisico.


## <a name="virtual-machine-roles"></a>Ruoli macchina virtuale
Il ASDK offre servizi tramite le seguenti macchine virtuali ospitate nel computer host kit di sviluppo:

| Name | DESCRIZIONE |
| ----- | ----- |
| **AzS-ACS01** | Servizi di archiviazione di Azure Stack.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
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
