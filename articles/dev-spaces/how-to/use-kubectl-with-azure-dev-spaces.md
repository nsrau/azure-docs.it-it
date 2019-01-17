---
title: Come usare kubectl con Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: b522ab9177d963f65ac6ea75538ddc46331875da
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706790"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Usare kubectl con uno spazio Azure Dev Spaces

È possibile accedere al cluster Kubernetes all'interno di uno spazio Azure Dev Spaces e usare gli strumenti Kubernetes esistenti, ad esempio `kubectl`.

L'esecuzione del comando `az aks use-dev-spaces` o determinerà l'aggiunta automatica di un contesto di configurazione `kubectl`. Pertanto, kubectl dovrebbe essere già connesso al cluster Kubernetes di Azure Dev Spaces. Esempi:
- Confermare il contesto corrente: `kubectl config current-context`
- Elencare tutti i contesti disponibili: `kubectl config get-contexts`. 
- Cambiare il contesto: `kubectl config use-context <context-name>`
- Visualizzare il dashboard Kubernetes: eseguire `kubectl proxy`, quindi aprire il browser all'indirizzo emesso da questo comando (aggiungere `/ui` all'URL per passare al dashboard Kubernetes).
- Elencare i servizi in esecuzione nello spazio Azure Dev Spaces predefinito denominato *default*: `kubectl get services --namespace=default`

