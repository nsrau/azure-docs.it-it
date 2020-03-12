---
title: Reimpostare le credenziali per un cluster del servizio Azure Kubernetes (AKS)
description: Informazioni su come aggiornare o reimpostare l'entità servizio o le credenziali dell'applicazione AAD per un cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 5dab9a778653d2ec6e32ddb3833ddcf6a95cae13
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096113"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aggiornare o ruotare le credenziali per il servizio Azure Kubernetes (AKS)

Per impostazione predefinita, i cluster del servizio Azure Kubernetes vengono creati con un'entità servizio che dispone di una data di scadenza di un anno. Se la data di scadenza è prossima, è possibile reimpostare le credenziali per estendere l'entità servizio per un ulteriore periodo di tempo. È anche possibile aggiornare, o alternare, le credenziali come parte di criteri di sicurezza definiti. Questo articolo illustra come aggiornare le credenziali per un cluster del servizio Azure Kubernetes.

È anche possibile che sia stato [integrato il cluster AKS con Azure Active Directory][aad-integration]e usarlo come provider di autenticazione per il cluster. In tal caso, sono disponibili altre 2 identità create per il cluster, l'app Server AAD e l'app client AAD. è anche possibile reimpostare le credenziali. 

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aggiornare o creare una nuova entità servizio per il cluster AKS

Quando si desidera aggiornare le credenziali per un cluster del servizio Azure Kubernetes, è possibile scegliere di:

* aggiornare le credenziali per l'entità servizio esistente usata dal cluster o
* creare un'entità servizio e aggiornare il cluster per usare le nuove credenziali.

### <a name="reset-existing-service-principal-credential"></a>Reimposta le credenziali dell'entità servizio esistente

Per aggiornare le credenziali per l'entità servizio esistente, ottenere l'ID entità servizio del cluster usando il comando [AZ AKS Show][az-aks-show] . L'esempio seguente ottiene l'ID per il cluster denominato *myAKSCluster* nel gruppo di risorse *myResourceGroup*. L'ID entità servizio è impostato come variabile denominata *SP_ID* per l'uso in un comando aggiuntivo.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Con un set di variabili che contiene l'ID dell'entità servizio, è ora possibile reimpostare le credenziali usando [AZ ad SP Credential Reset][az-ad-sp-credential-reset]. L'esempio seguente consente alla piattaforma Azure di generare un nuovo segreto sicuro per l'entità servizio. Questo nuovo segreto sicuro viene archiviato anche come una variabile.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Continuare ora ad [aggiornare il cluster AKS con le nuove credenziali dell'entità servizio](#update-aks-cluster-with-new-service-principal-credentials). Questo passaggio è necessario per le modifiche dell'entità servizio per la reflection nel cluster AKS.

### <a name="create-a-new-service-principal"></a>Creare una nuova entità servizio

Se si sceglie di aggiornare le credenziali dell'entità servizio esistente nella sezione precedente, ignorare questo passaggio. Continuare ad [aggiornare il cluster AKS con le nuove credenziali dell'entità servizio](#update-aks-cluster-with-new-service-principal-credentials).

Per creare un'entità servizio e quindi aggiornare il cluster AKS per usare queste nuove credenziali, usare il comando [AZ ad SP create-for-RBAC][az-ad-sp-create] . Nell'esempio seguente il parametro `--skip-assignment` impedisce il completamento di qualsiasi assegnazione predefinita aggiuntiva:

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

Definire ora le variabili per l'ID dell'entità servizio e il segreto client usando l'output del comando [AZ ad SP create-for-RBAC][az-ad-sp-create] personalizzato, come illustrato nell'esempio seguente. L'*SP_ID* è l'*appId*e l'*SP_SECRET* è la *password*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Continuare ora ad [aggiornare il cluster AKS con le nuove credenziali dell'entità servizio](#update-aks-cluster-with-new-service-principal-credentials). Questo passaggio è necessario per le modifiche dell'entità servizio per la reflection nel cluster AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aggiornare il cluster AKS con le nuove credenziali dell'entità servizio

Indipendentemente dal fatto che si sia scelto di aggiornare le credenziali per l'entità servizio esistente o creare un'entità servizio, è ora necessario aggiornare il cluster AKS con le nuove credenziali usando il comando [AZ AKS Update-Credentials][az-aks-update-credentials] . Vengono usate le variabili per *--service-principal* e *--client-secret*:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Sono necessari alcuni istanti affinché le credenziali dell'entità servizio vengano aggiornate nel servizio Azure Kubernetes.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aggiornare il cluster AKS con le nuove credenziali dell'applicazione AAD

È possibile creare nuove applicazioni server e client AAD attenendosi alla [procedura di integrazione di AAD][create-aad-app]. In alternativa, reimpostare le applicazioni AAD esistenti seguendo lo [stesso metodo per la reimpostazione dell'entità servizio](#reset-existing-service-principal-credential). Dopodiché è sufficiente aggiornare le credenziali dell'applicazione AAD del cluster usando lo stesso comando [AZ AKS Update-Credentials][az-aks-update-credentials] ma usando le variabili *--Reset-AAD* .

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

In questo articolo è stata aggiornata l'entità servizio per il cluster AKS e le applicazioni di integrazione di AAD. Per altre informazioni su come gestire l'identità per i carichi di lavoro all'interno di un cluster, vedere [procedure consigliate per l'autenticazione e l'autorizzazione in AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
