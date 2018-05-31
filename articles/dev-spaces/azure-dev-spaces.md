---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198759"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Azure Dev Spaces consente di sviluppare con rapidità in Kubernetes. Il servizio aggiunge inoltre funzionalità di sviluppo complete, ad esempio il debug per i contenitori Kubernetes di Azure, e permette lo sviluppo iterativo di contenitori nel cloud con strumenti familiari come VS Code, Visual Studio o la riga di comando. Azure Dev Spaces è particolarmente utile nello sviluppo in team, dove l'isolamento di singoli rami di codice nei propri spazi costituisce una parte essenziale del ciclo di vita di sviluppo.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Come Azure Dev Spaces semplifica lo sviluppo in Kubernetes 

Questo approccio offre diversi vantaggi:

* Offre un ambiente di sviluppo senza infrastruttura rappresentativo dell'ambiente di produzione, con accesso completo alle risorse cloud.
* Consente di eseguire il debug dei contenitori Node.js e .NET Core direttamente in Kubernetes con VS Code o Visual Studio. Tutti gli altri linguaggi possono essere sviluppati con l'interfaccia della riga di comando.
* Permette di condividere un'istanza di Kubernetes in tutto il team di sviluppo per diminuire i costi e ridurre al minimo l'installazione di computer locali per nuovi membri del team.
* Consente di sviluppare il codice in modalità isolata ed eseguire test end-to-end con altri componenti senza replicare o simulare dipendenze.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)