---
title: Reimpostare le credenziali per un cluster del servizio Azure Kubernetes (AKS)
description: Informazioni su come aggiornare o reimpostare le credenziali dell'entità servizio o dell'applicazione AAD per un cluster del servizio Azure Kubernetes (AKS)Learn how update or reset the service principal or AAD Application credentials for an Azure Kubernetes Service (AKS) cluster
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: b7d652be3733cb130a3973909de59489047efe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475545"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aggiornare o ruotare le credenziali per il servizio Azure Kubernetes (AKS)Update or rotate the credentials for Azure Kubernetes Service (AKS)

Per impostazione predefinita, i cluster del servizio Azure Kubernetes vengono creati con un'entità servizio che dispone di una data di scadenza di un anno. Se la data di scadenza è prossima, è possibile reimpostare le credenziali per estendere l'entità servizio per un ulteriore periodo di tempo. È anche possibile aggiornare, o alternare, le credenziali come parte di criteri di sicurezza definiti. Questo articolo illustra come aggiornare le credenziali per un cluster del servizio Azure Kubernetes.

È anche possibile che [il cluster AKS sia integrato con Azure Active Directory][aad-integration]e utilizzarlo come provider di autenticazione per il cluster. In tal caso si diselettronicaranno altre 2 identità create per il cluster, l'app AAD Server e l'app client AAD, è anche possibile reimpostare tali credenziali. 

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aggiornare o creare una nuova entità servizio per il cluster AKSUpdate or create a new Service Principal for your AKS cluster

Quando si desidera aggiornare le credenziali per un cluster del servizio Azure Kubernetes, è possibile scegliere di:

* aggiornare le credenziali per l'entità servizio esistente usata dal cluster o
* creare un'entità servizio e aggiornare il cluster per usare le nuove credenziali.

### <a name="reset-existing-service-principal-credential"></a>Reimpostare le credenziali dell'entità servizio esistente

Per aggiornare le credenziali per l'entità servizio esistente, ottenere l'ID dell'entità servizio del cluster usando il comando [az aks show][az-aks-show]. L'esempio seguente ottiene l'ID per il cluster denominato *myAKSCluster* nel gruppo di risorse *myResourceGroup*. L'ID dell'entità servizio viene impostato come variabile denominata *SP_ID* per l'uso in un comando aggiuntivo.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Con un set variabile contenente l'ID dell'entità servizio, reimpostare le credenziali usando [az ad sp credential reset][az-ad-sp-credential-reset]. L'esempio seguente consente alla piattaforma Azure di generare un nuovo segreto sicuro per l'entità servizio. Questo nuovo segreto sicuro viene archiviato anche come una variabile.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Continuare ora ad aggiornare il cluster AKS con nuove [credenziali dell'entità servizio.](#update-aks-cluster-with-new-service-principal-credentials) Questo passaggio è necessario affinché le modifiche dell'entità servizio riflettano sul cluster AKS.

### <a name="create-a-new-service-principal"></a>Creare una nuova entità servizioCreate a New Service Principal

Se si sceglie di aggiornare le credenziali dell'entità servizio esistente nella sezione precedente, ignorare questo passaggio. Continuare ad aggiornare il cluster AKS con le [nuove credenziali dell'entità servizio.](#update-aks-cluster-with-new-service-principal-credentials)

Per creare un'entità servizio e quindi aggiornare il cluster del servizio Azure Kubernetes in modo da usare le nuove credenziali, usare il comando [az ad sp create-for-rbac][az-ad-sp-create]. Nell'esempio seguente il parametro `--skip-assignment` impedisce il completamento di qualsiasi assegnazione predefinita aggiuntiva:

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

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Continuare ora ad aggiornare il cluster AKS con nuove [credenziali dell'entità servizio.](#update-aks-cluster-with-new-service-principal-credentials) Questo passaggio è necessario affinché le modifiche dell'entità servizio riflettano sul cluster AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aggiornare il cluster AKS con le nuove credenziali dell'entità servizioUpdate AKS cluster with new Service Principal credentials

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

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aggiornare il cluster AKS con le nuove credenziali dell'applicazione AADUpdate AKS Cluster with new AAD Application credentials

È possibile creare nuove applicazioni Client e Server AAD seguendo i [passaggi di integrazione][create-aad-app]di AAD . In alternativa, reimpostare le applicazioni AAD esistenti seguendo [lo stesso metodo della reimpostazione dell'entità servizio.](#reset-existing-service-principal-credential) Dopo di che è sufficiente aggiornare le credenziali dell'applicazione AAD del cluster utilizzando lo stesso comando [az aks update-credentials][az-aks-update-credentials] ma utilizzando le variabili *--reset-aad.*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Passaggi successivi

In questo articolo, l'entità servizio per il cluster AKS stesso e le applicazioni di integrazione AAD sono state aggiornate. Per altre informazioni su come gestire l'identità per i carichi di lavoro all'interno di un cluster, consultare [Procedure consigliate per l'autenticazione e l'autorizzazione nel servizio Azure Kubernetes][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
