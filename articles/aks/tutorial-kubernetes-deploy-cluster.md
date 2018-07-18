---
title: 'Esercitazione su Kubernetes in Azure: Distribuire un cluster'
description: 'Esercitazione sul servizio contenitore di Azure: Distribuire un cluster'
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8698f16138e9baeb9c9c1142a5d0c8937a69d1b
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341400"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Esercitazione: Distribuire un cluster del servizio Kubernetes di Azure (AKS)

Kubernetes fornisce una piattaforma distribuita per applicazioni in contenitori. Il servizio contenitore di Azure consente di eseguire rapidamente il provisioning di un cluster Kubernetes per la produzione. In questa esercitazione, la terza di sette parti, viene distribuito un cluster Kubernetes in AKS. I passaggi completati comprendono:

> [!div class="checklist"]
> * Creazione di un'entità servizio per le interazioni delle risorse
> * Distribuzione di un cluster del servizio Kubernetes di Azure
> * Installazione dell'interfaccia della riga di comando Kubernetes (kubectl)
> * Configurazione di kubectl

Nelle esercitazioni successive l'applicazione Azure Vote viene distribuita nel cluster, ridimensionata e aggiornata.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti, un'immagine del contenitore è stata creata e caricata in un'istanza di Registro contenitori di Azure. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Esercitazione 1: Creare immagini del contenitore][aks-tutorial-prepare-app].

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Per consentire a un cluster AKS di interagire con altre risorse di Azure viene usata un'entità servizio di Azure Active Directory. Questa entità servizio può essere creata automaticamente dall'interfaccia della riga di comando di Azure o dal portale di Azure oppure è possibile crearne una in anticipo e assegnarle autorizzazioni aggiuntive. In questa esercitazione viene creata un'entità servizio, viene concesso l'accesso all'istanza del Registro contenitori di Azure creata nell'esercitazione precedente e viene quindi creato un cluster AKS.

Creare un'entità servizio con [az ad sp create-for-rbac][]. Il parametro `--skip-assignment` limita l'assegnazione di autorizzazioni aggiuntive.

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

Ottenere prima l'ID della risorsa del record di controllo di accesso con [az acr show][]. Aggiornare il nome di registro `<acrName>` a quello dell'istanza del record di controllo di accesso e del gruppo di risorse in cui si trova l'istanza.

```azurecli
az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv
```

Per concedere l'accesso corretto al cluster AKS per l'uso delle immagini archiviate nel record di controllo di accesso, creare un'assegnazione di ruolo con [az role assignment create][]. Sostituire `<appId`> e `<acrId>` con i valori raccolti nei due passaggi precedenti.

```azurecli
az role assignment create --assignee <appId> --role Reader --scope <acrId>
```

## <a name="create-kubernetes-cluster"></a>Creare un cluster Kubernetes

Ora creare un cluster AKS con [az aks create][]. L'esempio seguente crea un cluster denominato *myAKSCluster* in un gruppo di risorse denominato *myResourceGroup*. Questo gruppo di risorse è stato creato nell'[esercitazione precedente][aks-tutorial-prepare-acr]. Specificare `<appId>` e `<password>`, definiti nel passaggio precedente in cui è stata creata l'entità servizio.

```azurecli
az aks create \
    --name myAKSCluster \
    --resource-group myResourceGroup \
    --node-count 1 \
    --generate-ssh-keys \
    --service-principal <appId> \
    --client-secret <password>
```

Dopo alcuni minuti, la distribuzione viene completata e restituisce le informazioni in formato JSON sulla distribuzione del servizio contenitore di Azure.

## <a name="install-the-kubectl-cli"></a>Installare l'interfaccia della riga di comando di kubectl

Per connettersi al cluster Kubernetes dal computer client, usare [kubectl][kubectl], il client da riga di comando di Kubernetes.

Se si usa Azure Cloud Shell, kubectl è già installato. È anche possibile installarlo in locale con [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Connettersi con kubectl

Per configurare kubectl per la connessione al cluster Kubernetes, usare [az aks get-credentials][]. L'esempio seguente ottiene le credenziali per il nome del cluster AKS *myAKSCluster* in *myResourceGroup*:

```azurecli
az aks get-credentials --name myAKSCluster --resource-group myResourceGroup
```

Per verificare la connessione al cluster, eseguire il comando [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Output:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.6
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un cluster Kubernetes in AKS. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Creazione di un'entità servizio per le interazioni delle risorse
> * Distribuzione di un cluster del servizio Kubernetes di Azure
> * Installazione dell'interfaccia della riga di comando di Kubernetes (kubectl)
> * Configurazione di kubectl

Passare all'esercitazione successiva per apprendere come eseguire l'applicazione nel cluster.

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
