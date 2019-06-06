---
title: Reimpostare le credenziali per un cluster del servizio Azure Kubernetes (AKS)
description: Informazioni su come aggiornare o reimpostazione le credenziali dell'entità servizio per un cluster in servizio Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 189bcf2ddc7d301c8100f74e51374abd217a144f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475493"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Aggiornare o alternare le credenziali per un'entità servizio in servizio Azure Kubernetes (AKS)

Per impostazione predefinita, i cluster AKS vengono creati con un'entità servizio che dispone di una data di scadenza di un anno. Se la data di scadenza è prossima, è possibile reimpostare le credenziali per estendere l'entità servizio per un ulteriore periodo di tempo. È anche possibile aggiornare, o alternare, le credenziali come parte di criteri di sicurezza definiti. Questo articolo illustra come aggiornare le credenziali per un cluster AKS.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario la CLI di Azure versione 2.0.65 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Scegliere di aggiornare o creare un'entità servizio

Quando si desidera aggiornare le credenziali per un cluster AKS, è possibile scegliere di:

* aggiornare le credenziali per l'entità servizio esistente usata dal cluster o
* creare un'entità servizio e aggiornare il cluster per usare le nuove credenziali.

Se si vuole creare un'entità servizio e quindi aggiornare il cluster AKS, ignorare il resto dei passaggi in questa sezione e passare a [create a service principal](#create-a-service-principal) (creare un'entità servizio). Se si vuole aggiornare le credenziali per l'entità servizio esistente usata dal cluster AKS, eseguire i passaggi descritti in questa sezione.

### <a name="get-the-service-principal-id"></a>Ottenere l'ID dell'entità servizio

Per aggiornare le credenziali per l'entità servizio esistente, ottenere l'ID dell'entità servizio del cluster usando il comando [az aks show][az-aks-show]. L'esempio seguente ottiene l'ID per il cluster denominato *myAKSCluster* nel gruppo di risorse *myResourceGroup*. L'ID dell'entità servizio viene impostato come una variabile denominata *SP_ID* per l'uso nel comando aggiuntivo.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>Aggiornare le credenziali dell'entità servizio

Con un set variabile contenente l'ID dell'entità servizio, reimpostare le credenziali usando [az ad sp credential reset][az-ad-sp-credential-reset]. L'esempio seguente consente alla piattaforma Azure di generare un nuovo segreto sicuro per l'entità servizio. Questo nuovo segreto sicuro viene archiviato anche come una variabile.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Passare ora a [update AKS cluster with new credentials](#update-aks-cluster-with-new-credentials) (aggiornare un cluster AKS con nuove credenziali).

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Se si sceglie di aggiornare le credenziali dell'entità servizio esistente nella sezione precedente, ignorare questo passaggio. Continuare con [update AKS cluster with new credentials](#update-aks-cluster-with-new-credentials) (aggiornare un cluster AKS con nuove credenziali).

Per creare un'entità servizio e quindi aggiornare il cluster AKS in modo da usare le nuove credenziali, usare il comando [az ad sp create-for-rbac][az-ad-sp-create]. Nell'esempio seguente il parametro `--skip-assignment` impedisce il completamento di qualsiasi assegnazione predefinita aggiuntiva:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

L'output è simile all'esempio seguente: Prendere nota dei propri valori `appId` e `password`. Questi valori vengono usati nel passaggio successivo.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

A questo punto definire le variabili per l'ID dell'entità servizio e il segreto client usando l'output dal proprio comando [az ad sp create-for-rbac][az-ad-sp-create], come illustrato nell'esempio seguente. L'*SP_ID* è l'*appId*e l'*SP_SECRET* è la *password*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>Aggiornare il cluster AKS con nuove credenziali

Indipendentemente dal fatto che si sia scelto di aggiornare le credenziali per l'entità servizio esistente o di creare un'entità servizio, aggiornare il cluster AKS con le nuove credenziali usando il comando [az aks update-credentials][az-aks-update-credentials]. Vengono usate le variabili per *--service-principal* e *--client-secret*:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Sono necessari alcuni istanti affinché le credenziali dell'entità servizio vengano aggiornate nel servizio Azure Kubernetes.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come aggiornare l'entità servizio per il cluster AKS. Per altre informazioni su come gestire l'identità per i carichi di lavoro all'interno di un cluster, consultare [Procedure consigliate per l'autenticazione e l'autorizzazione nel servizio Azure Kubernetes][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
