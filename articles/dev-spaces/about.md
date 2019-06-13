---
title: Introduzione ad Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Introduzione ad Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, contenitori, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: 3e887409518f5cc97238a0168213a7918e318c04
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394050"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces

Azure Dev Spaces offre un'esperienza di sviluppo Kubernetes rapida e iterativa per i team nei cluster del servizio Azure Kubernetes (AKS). È possibile collaborare con il team in un cluster AKS condiviso. Azure Dev Spaces consente anche di testare tutti i componenti dell'applicazione nel servizio Azure Kubernetes senza replicare o simulare le dipendenze. È possibile eseguire il codice in maniera iterativa ed eseguire il debug dei contenitori direttamente nel servizio Azure Kubernetes con una configurazione minima del computer di sviluppo.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Come Azure Dev Spaces semplifica lo sviluppo in Kubernetes

Con Azure Dev Spaces i team possono concentrarsi sullo sviluppo e sulla rapida iterazione della propria applicazione di microservizi, grazie alla possibilità di lavorare direttamente con l'intera architettura o applicazione di microservizi nel servizio Azure Kubernetes. Azure Dev Spaces consente anche di aggiornare parti dell'architettura di microservizi in modo indipendente e in completo isolamento, senza alcun impatto sul resto del cluster AKS o su altri sviluppatori. Azure Dev Spaces è riservato alle attività di sviluppo e test in ambienti di sviluppo e test di livello inferiore e non è adatto per i cluster AKS in ambiente di produzione.

Dato che i team possono lavorare con l'intera applicazione e collaborare direttamente nel servizio Azure Kubernetes, Azure Dev Spaces:

* Riduce al minimo la configurazione del computer locale
* Riduce i tempi di formazione per i nuovi sviluppatori nel team
* Aumenta la velocità di un team grazie a una più rapida iterazione
* Riduce il numero di ambienti di sviluppo e integrazione ridondanti, dato che i membri del team possono condividere un cluster
* Elimina la necessità di replicare o simulare le dipendenze
* Migliora la collaborazione fra i team di sviluppo, nonché con i team con cui lavorano, come i team DevOps

Azure Dev Spaces fornisce strumenti per generare asset di Docker e Kubernetes per i progetti. Questi strumenti consentono di aggiungere con facilità applicazioni nuove ed esistenti sia a uno spazio di sviluppo che ad altri cluster AKS.

Per altre informazioni sul funzionamento di Azure Dev Spaces, vedere [Funzionamento e configurazione di Azure Dev Spaces][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Aree e configurazioni supportate

Azure Dev Spaces è supportato solo da cluster del servizio Azure Kubernetes nelle aree **Stati Uniti orientali**, **Stati Uniti orientali 2**, **Stati Uniti centrali**, **Stati Uniti occidentali 2**, **Europa settentrionale**, **Europa occidentale**, **Regno Unito meridionale**, **Asia sud-orientale**, **Australia orientale**, **Canada centrale** e **Canada orientale**. Azure Dev Spaces supporta l'uso dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o di [Visual Studio Code](https://code.visualstudio.com/download) con l'[estensione Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installata in Linux, MacOS oppure Windows 8 o versione successiva per compilare ed eseguire applicazioni nel servizio Azure Kubernetes. Supporta anche l'uso di [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) installato in Windows 8 o versione successiva. Per Visual Studio 2019 è necessario il carico di lavoro Sviluppo di Azure. Per Visual Studio 2017 sono necessari il carico di lavoro Sviluppo Web e [Visual Studio Tools per Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sullo sviluppo iterativo rapido per i team con Azure Dev Spaces nella guida di avvio rapido sullo sviluppo in team.

> [!div class="nextstepaction"]
> [Avvio rapido sullo avvio sviluppo in team](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
