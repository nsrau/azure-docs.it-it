---
title: Come usare kubectl con Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Informazioni su come usare i comandi kubectl in uno spazio di sviluppo in un cluster del servizio Kubernetes di Azure con Azure Dev Spaces abilitato
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960162"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Usare kubectl con uno spazio Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

È possibile accedere al cluster Kubernetes all'interno di uno spazio Azure Dev Spaces e usare gli strumenti Kubernetes esistenti, ad esempio `kubectl`.

L'esecuzione del comando `az aks use-dev-spaces` o determinerà l'aggiunta automatica di un contesto di configurazione `kubectl`. Pertanto, kubectl dovrebbe essere già connesso al cluster Kubernetes di Azure Dev Spaces. Esempi:
- Confermare il contesto corrente: `kubectl config current-context`
- Elencare tutti i contesti disponibili: `kubectl config get-contexts`. 
- Cambiare il contesto: `kubectl config use-context <context-name>`
- Visualizzare il dashboard Kubernetes: eseguire `kubectl proxy`, quindi aprire il browser all'indirizzo emesso da questo comando (aggiungere `/ui` all'URL per passare al dashboard Kubernetes).
- Elencare i servizi in esecuzione nello spazio Azure Dev Spaces predefinito denominato *predefinito*: `kubectl get services --namespace=default`

