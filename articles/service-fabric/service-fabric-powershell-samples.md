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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 1825b2a58e1022f22c71395477a5fca54c715455
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="azure-powershell-samples"></a>Esempi di Azure PowerShell

La tabella seguente include collegamenti a esempi di script PowerShell che creano e gestiscono servizi, applicazioni e cluster di Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Creare cluster** ||
| [Creare un cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Crea un cluster di Azure Service Fabric. |
|[Creare un cluster di test (Azure)](./scripts/service-fabric-powershell-create-test-cluster.md)| Crea un cluster di Service Fabric tre nodi test in Azure.|
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
