---
title: Esercitazione su Kubernetes in Azure - Distribuire un cluster
description: In questa esercitazione sul servizio Kubernetes di Azure (AKS) si crea un cluster AKS e si usa kubectl per connettersi al nodo master Kubernetes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7e5c78e1b30b311c6ce918453fe728ae86060dda
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720663"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Esercitazione: Distribuire un cluster Azure Kubernetes Service (AKS)

Kubernetes fornisce una piattaforma distribuita per applicazioni in contenitori. Il servizio Azure Kubernetes consente di creare rapidamente un cluster Kubernetes per la produzione. In questa esercitazione, la terza di sette parti, viene distribuito un cluster Kubernetes in AKS. Si apprenderà come:

> [!div class="checklist"]
> * Creare un'entità servizio per le interazioni delle risorse
> * Distribuire un cluster Kubernetes AKS
> * Installare l'interfaccia della riga di comando di Kubernetes (kubectl)
> * Configurare kubectl per connettersi al cluster AKS

In altre esercitazioni l'applicazione Azure Vote viene distribuita nel cluster, ridimensionata e aggiornata.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti, un'immagine del contenitore è stata creata e caricata in un'istanza di Registro contenitori di Azure. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, iniziare dall'[Esercitazione 1: Creare immagini del contenitore][aks-tutorial-prepare-app].

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.53 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Per consentire a un cluster AKS di interagire con altre risorse di Azure viene usata un'entità servizio di Azure Active Directory. Questa entità servizio può essere creata automaticamente dall'interfaccia della riga di comando di Azure o dal portale di Azure oppure è possibile crearne una in anticipo e assegnarle autorizzazioni aggiuntive. In questa esercitazione viene creata un'entità servizio, viene concesso l'accesso all'istanza del Registro contenitori di Azure creata nell'esercitazione precedente e viene quindi creato un cluster AKS.

Creare un'entità servizio usando il comando [az ad sp create-for-rbac][]. Il parametro `--skip-assignment` limita l'assegnazione di autorizzazioni aggiuntive. Per impostazione predefinita, questa entità servizio è valida per un anno.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

L'output è simile all'esempio seguente:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Prendere nota di *appId* e *password*. Questi valori vengono usati nei passaggi successivi.

## <a name="configure-acr-authentication"></a>Configurare l'autenticazione del record di controllo di accesso

Per accedere alle immagini archiviate nel record di controllo di accesso è necessario concedere all'entità servizio del servizio contenitore di Azure i diritti corretti per eseguire il pull delle immagini dal record di controllo di accesso.

Ottenere prima l'ID della risorsa di Registro contenitori di Azure usando [az acr show][]. Aggiornare il nome di registro `<acrName>` a quello dell'istanza del record di controllo di accesso e del gruppo di risorse in cui si trova l'istanza.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Per concedere l'accesso corretto al cluster AKS per l'uso delle immagini archiviate in Registro contenitori di Azure, creare un'assegnazione di ruolo usando il comando [az role assignment create][]. Sostituire `<appId`> e `<acrId>` con i valori raccolti nei due passaggi precedenti.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role Reader
```

## <a name="create-a-kubernetes-cluster"></a>Creare un cluster Kubernetes

I cluster AKS possono usare i controlli degli accessi in base al ruolo di Kubernetes. Questi controlli consentono di definire l'accesso alle risorse in base ai ruoli assegnati agli utenti. È possibile combinare le autorizzazioni, se a un utente sono assegnati più ruoli, e definire l'ambito delle autorizzazioni in un unico spazio dei nomi o nell'intero cluster. Per impostazione predefinita, l'interfaccia della riga di comando di Azure abilita automaticamente il Controllo degli accessi in base al ruolo quando si crea un cluster AKS.

Creare un cluster AKS usando [az aks create][]. L'esempio seguente crea un cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Questo gruppo di risorse è stato creato nell'[esercitazione precedente][aks-tutorial-prepare-acr]. Specificare `<appId>` e `<password>`, definiti nel passaggio precedente in cui è stata creata l'entità servizio.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Dopo alcuni minuti, la distribuzione viene completata e restituisce le informazioni in formato JSON sulla distribuzione del servizio Azure Kubernetes.

## <a name="install-the-kubernetes-cli"></a>Installare l'interfaccia della riga di comando di Kubernetes

Per connettersi al cluster Kubernetes dal computer locale, si usa [kubectl][kubectl], il client da riga di comando di Kubernetes.

Se si usa Azure Cloud Shell, `kubectl` è già installato. È anche possibile installarlo in locale usando il comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Connettersi al cluster usando kubectl

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][]. L'esempio seguente ottiene le credenziali per il cluster AKS denominato *myAKSCluster* in *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, eseguire il comando [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un cluster Kubernetes in AKS ed è stato configurato `kubectl` per la connessione. Si è appreso come:

> [!div class="checklist"]
> * Creare un'entità servizio per le interazioni delle risorse
> * Distribuire un cluster Kubernetes AKS
> * Installare l'interfaccia della riga di comando di Kubernetes (kubectl)
> * Configurare kubectl per connettersi al cluster AKS

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
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
