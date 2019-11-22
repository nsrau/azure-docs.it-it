---
title: Come usare kubectl con Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: fea14668c35eddcd01417b417e2239788eddd6d1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279936"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Usare kubectl con uno spazio Azure Dev Spaces

È possibile accedere al cluster Kubernetes all'interno di uno spazio Azure Dev Spaces e usare gli strumenti Kubernetes esistenti, ad esempio `kubectl`.

L'esecuzione del comando `az aks use-dev-spaces` o determinerà l'aggiunta automatica di un contesto di configurazione `kubectl`. Pertanto, kubectl dovrebbe essere già connesso al cluster Kubernetes di Azure Dev Spaces. Esempi:
- Confermare il contesto corrente: `kubectl config current-context`
- Elencare tutti i contesti disponibili: `kubectl config get-contexts`. 
- Cambiare il contesto: `kubectl config use-context <context-name>`
- Visualizzare il dashboard Kubernetes: eseguire `kubectl proxy`, quindi aprire il browser all'indirizzo emesso da questo comando (aggiungere `/ui` all'URL per passare al dashboard Kubernetes).
- Elencare i servizi in esecuzione nello spazio Azure Dev Spaces predefinito denominato *default*: `kubectl get services --namespace=default`

