---
title: (DEPRECATO) Esercitazione sul servizio Azure Container - Distribuire un cluster
description: Esercitazione sul servizio Azure Container - Distribuire un cluster
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 58fef0357a903f2ab1d238bbab7b2d9dca673eb4
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662058"
---
# <a name="deprecated-deploy-a-kubernetes-cluster-in-azure-container-service"></a>(DEPRECATO) Distribuire un cluster Kubernetes nel servizio Azure Container

> [!TIP]
> Per la versione aggiornata di questa esercitazione che usa il servizio Azure Kubernetes, vedere [Esercitazione: Distribuire un cluster di servizio Azure Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes fornisce una piattaforma distribuita per applicazioni in contenitori. Con il servizio Azure Container, il provisioning di un cluster Kubernetes pronto per la produzione è semplice e rapido. In questa esercitazione, parte 3 di 7, viene distribuito un cluster Kubernetes del servizio Azure Container. I passaggi completati comprendono:

> [!div class="checklist"]
> * Distribuzione di un cluster del servizio contenitore di Azure Kubernetes
> * Installazione dell'interfaccia della riga di comando Kubernetes (kubectl)
> * Configurazione di kubectl

Nelle esercitazioni successive, l'applicazione Azure Vote viene distribuita nel cluster, ridimensionata e aggiornata e Log Analytics viene configurato per monitorare il cluster Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti, un'immagine del contenitore è stata creata e caricata in un'istanza di Registro Azure Container. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore).

## <a name="create-kubernetes-cluster"></a>Creare un cluster Kubernetes

Creare un cluster Kubernetes nel servizio Azure Container con il comando [az acs create](/cli/azure/acs#az-acs-create). 

Nell'esempio seguente viene creato un cluster denominato `myK8sCluster` nel gruppo di risorse denominato `myResourceGroup`. Nell'[esercitazione precedente](./container-service-tutorial-kubernetes-prepare-acr.md) è stato creato questo gruppo di risorse.

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

In alcuni casi la sottoscrizione di Azure ha accesso limitato alle risorse di Azure, ad esempio con una versione di valutazione gratuita limitata. Se la distribuzione non riesce a causa di core disponibili limitati, ridurre il numero di agenti predefinito aggiungendo `--agent-count 1` al comando [az acs create](/cli/azure/acs#az-acs-create). 

Dopo alcuni minuti, la distribuzione viene completata e restituisce le informazioni in formato JSON sulla distribuzione del servizio contenitore di Azure.

## <a name="install-the-kubectl-cli"></a>Installare l'interfaccia della riga di comando di kubectl

Per connettersi al cluster Kubernetes dal computer client, usare [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), il client da riga di comando di Kubernetes. 

Se si usa Azure CloudShell, kubectl è già installato. Se lo si vuole installare in locale, usare il comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Se è in esecuzione in Linux o MacOS, potrebbe essere necessario procedere all'esecuzione con sudo. In Windows accertarsi che la shell sia stata eseguita come amministratore.

```azurecli-interactive 
az acs kubernetes install-cli 
```

In Windows l'installazione predefinita è *c:\program files (x86)\kubectl.exe*. Potrebbe essere necessario aggiungere questo file al percorso di Windows. 

## <a name="connect-with-kubectl"></a>Connettersi con kubectl

Per configurare kubectl per connettersi al cluster Kubernetes, eseguire il comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Per verificare la connessione al cluster, eseguire il comando [kubectl get nodes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```azurecli-interactive
kubectl get nodes
```

Output:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Al termine dell'esercitazione, sarà disponibile un cluster Kubernetes del servizio contenitore di Azure pronto per i carichi di lavoro. Nelle esercitazioni successive, in questo cluster viene distribuita un'applicazione multi-contenitore, quindi viene scalata orizzontalmente, aggiornata e monitorata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un cluster Kubernetes del servizio Azure Container. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione di un cluster Kubernets del servizio contenitore di Azure
> * Installazione dell'interfaccia della riga di comando di Kubernetes (kubectl)
> * Configurazione di kubectl

Passare all'esercitazione successiva per apprendere come eseguire l'applicazione nel cluster.

> [!div class="nextstepaction"]
> [Distribuire un'applicazione in Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
