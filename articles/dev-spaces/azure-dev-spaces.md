---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: 93440b8a1c9fd1b386931e5998c70133071a079e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823045"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Azure Dev Spaces offre un'esperienza di sviluppo Kubernetes rapida e iterativa per i team. Con una configurazione minima del computer di sviluppo, è possibile eseguire il codice in maniera iterativa ed eseguire il debug dei contenitori direttamente in Azure Kubernetes Service (AKS). È possibile sviluppare in Windows, Mac o Linux usando strumenti familiari come Visual Studio, Visual Studio Code o la riga di comando.

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Come Azure Dev Spaces semplifica lo sviluppo in Kubernetes 

Azure Dev Spaces aumenta la produttività dei team di sviluppo in Kubernetes nei modi seguenti:
- Riduce al minimo la configurazione del computer di sviluppo locale per ogni membro del team e interagisce direttamente con AKS, un cluster Kubernetes gestito in Azure.
- Esegue rapidamente l'iterazione e il debug del codice direttamente in Kubernetes tramite Visual Studio 2017 o Visual Studio Code.
- Genera asset di configurazione come codice Docker e Kubernetes da usare dallo sviluppo alla produzione. 
- Consente di condividere un cluster Kubernetes gestito con il team e di collaborare. Consente di sviluppare il codice in modalità isolata ed eseguire test end-to-end con altri componenti senza replicare o simulare dipendenze.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>Vedere anche

[Azure Kubernetes Service](/azure/aks)