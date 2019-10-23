---
title: Esercitazione su Kubernetes in Azure - Distribuire un cluster
description: In questa esercitazione sul servizio Azure Kubernetes si crea un cluster del servizio Azure Kubernetes e si usa kubectl per connettersi al nodo master Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 8ad542a3614253e11331e9b49513a887aff65890
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512924"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Esercitazione: Distribuire un cluster del servizio Azure Kubernetes

Kubernetes fornisce una piattaforma distribuita per applicazioni in contenitori. Il servizio Azure Kubernetes consente di creare rapidamente un cluster Kubernetes per la produzione. In questa esercitazione, la terza di sette parti, viene distribuito un cluster Kubernetes in servizio Azure Kubernetes. Si apprenderà come:

> [!div class="checklist"]
> * Distribuire un cluster del servizio Azure Kubernetes in grado di eseguire l'autenticazione in un'istanza di Registro Azure Container
> * Installare l'interfaccia della riga di comando di Kubernetes (kubectl)
> * Configurare kubectl per connettersi al cluster servizio Azure Kubernetes

In altre esercitazioni l'applicazione Azure Vote viene distribuita nel cluster, ridimensionata e aggiornata.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti, un'immagine del contenitore è stata creata e caricata in un'istanza di Registro Azure Container. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, iniziare dall'[Esercitazione 1: Creare immagini del contenitore][aks-tutorial-prepare-app].

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.75 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Creare un cluster Kubernetes

I cluster servizio Azure Kubernetes possono usare i controlli degli accessi in base al ruolo di Kubernetes. Questi controlli consentono di definire l'accesso alle risorse in base ai ruoli assegnati agli utenti. È possibile combinare le autorizzazioni, se a un utente sono assegnati più ruoli, e definire l'ambito delle autorizzazioni in un unico spazio dei nomi o nell'intero cluster. Per impostazione predefinita, l'interfaccia della riga di comando di Azure abilita automaticamente il Controllo degli accessi in base al ruolo quando si crea un cluster servizio Azure Kubernetes.

Creare un cluster servizio Azure Kubernetes usando [az servizio Azure Kubernetes create][]. L'esempio seguente crea un cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Questo gruppo di risorse è stato creato nell'[esercitazione precedente][aks-tutorial-prepare-acr]. Per consentire a un cluster del servizio Azure Kubernetes di interagire con altre risorse di Azure viene creata automaticamente un'entità servizio di Azure Active Directory dal momento che non ne è stata specificata una. A questo punto a tale entità servizio viene [concesso il diritto di eseguire il pull delle immagini ][container-registry-integration] dall'istanza di Registro Azure Container creata nell'esercitazione precedente.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Dopo alcuni minuti, la distribuzione viene completata e restituisce le informazioni in formato JSON sulla distribuzione del servizio Azure Kubernetes.

> [!NOTE]
> Per garantire il funzionamento affidabile del cluster, è consigliabile eseguire almeno 2 (due) nodi.

## <a name="install-the-kubernetes-cli"></a>Installare l'interfaccia della riga di comando di Kubernetes

Per connettersi al cluster Kubernetes dal computer locale si usa [kubectl][kubectl], il client da riga di comando di Kubernetes.

Se si usa Azure Cloud Shell, `kubectl` è già installato. È anche possibile installarlo in locale usando il comando [az servizio Azure Kubernetes install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Connettersi al cluster usando kubectl

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az servizio Azure Kubernetes get-credentials][]. L'esempio seguente ottiene le credenziali per il cluster AKS denominato *myAKSCluster* in *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, eseguire il comando [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un cluster Kubernetes in servizio Azure Kubernetes ed è stato configurato `kubectl` per la connessione. Si è appreso come:

> [!div class="checklist"]
> * Distribuire un cluster del servizio Azure Kubernetes in grado di eseguire l'autenticazione in un'istanza di Registro Azure Container
> * Installare l'interfaccia della riga di comando di Kubernetes (kubectl)
> * Configurare kubectl per connettersi al cluster servizio Azure Kubernetes

Passare all'esercitazione successiva per informazioni su come distribuire un'applicazione nel cluster.

> [!div class="nextstepaction"]
> [Distribuire un'applicazione in Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az servizio Azure Kubernetes create]: /cli/azure/aks#az-aks-create
[az servizio Azure Kubernetes install-cli]: /cli/azure/aks#az-aks-install-cli
[az servizio Azure Kubernetes get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
