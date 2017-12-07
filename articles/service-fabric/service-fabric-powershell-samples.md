---
title: Esempi di Azure PowerShell - Service Fabric | Microsoft Docs
description: Esempi di Azure PowerShell - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: 
tags: 
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 738677def8b0eb70cfcab46e6fe57f9a344867a5
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-powershell-samples"></a>Esempi di Azure PowerShell

La tabella seguente include collegamenti a esempi di script PowerShell che creano e gestiscono servizi, applicazioni e cluster di Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Creare cluster** ||
| [Creare un cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Crea un cluster di Azure Service Fabric. |
| **Gestire cluster, nodi e infrastruttura** ||
| [Aggiungere un certificato dell'applicazione](./scripts/service-fabric-powershell-add-application-certificate.md)| Aggiunge un certificato X.509 dell'applicazione a tutti i nodi di un cluster. |
| [Aggiornare l'intervallo di porte RDP per le macchine virtuali del cluster](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Modifica l'intervallo di porte RDP nelle macchine virtuali del nodo del cluster in un cluster distribuito.|
| [Aggiornare l'utente e la password dell'amministratore per le macchine virtuali del nodo cluster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Aggiorna il nome utente e la password dell'amministratore per le macchine virtuali del nodo cluster. |
| [Aprire una porta nel servizio di bilanciamento del carico](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Apre una porta dell'applicazione nel servizio di bilanciamento del carico di Azure per consentire traffico in ingresso su una porta specifica. |
| [Creare una regola del gruppo di sicurezza di rete di ingresso](./scripts/service-fabric-powershell-add-nsg-rule.md) | Crea una regola del gruppo di sicurezza di rete in ingresso per consentire traffico in ingresso nel cluster su una porta specifica. |
| **Gestire le applicazioni** ||
| [Distribuire un'applicazione](./scripts/service-fabric-powershell-deploy-application.md)| Distribuire un'applicazione in un cluster.|
| [Aggiornare un'applicazione](./scripts/service-fabric-powershell-upgrade-application.md)| Aggiorna un'applicazione.|
| [Rimuovere un'applicazione](./scripts/service-fabric-powershell-remove-application.md)| Rimuovere un'applicazione da un cluster.|
