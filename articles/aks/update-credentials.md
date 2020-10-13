---
title: Reimpostare le credenziali per un cluster
titleSuffix: Azure Kubernetes Service
description: Informazioni su come aggiornare o reimpostare l'entità servizio o le credenziali dell'applicazione AAD per un cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: e787322f421094cf9ac6681df0119ba820b654ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871225"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aggiornare o ruotare le credenziali per il servizio Azure Kubernetes (AKS)

Per impostazione predefinita, i cluster del servizio Azure Kubernetes vengono creati con un'entità servizio che dispone di una data di scadenza di un anno. Se la data di scadenza è prossima, è possibile reimpostare le credenziali per estendere l'entità servizio per un ulteriore periodo di tempo. È anche possibile aggiornare, o alternare, le credenziali come parte di criteri di sicurezza definiti. Questo articolo illustra come aggiornare le credenziali per un cluster del servizio Azure Kubernetes.

È anche possibile che sia stato [integrato il cluster AKS con Azure Active Directory][aad-integration]e usarlo come provider di autenticazione per il cluster. In tal caso, sono disponibili altre 2 identità create per il cluster, l'app Server AAD e l'app client AAD. è anche possibile reimpostare le credenziali.

In alternativa, è possibile usare un'identità gestita per le autorizzazioni anziché un'entità servizio. Le identità gestite sono più facili da gestire rispetto alle entità servizio e non richiedono aggiornamenti o rotazioni. Per altre informazioni, vedere [Usare le identità gestite](use-managed-identity.md).

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aggiornare o creare una nuova entità servizio per il cluster AKS

Quando si desidera aggiornare le credenziali per un cluster AKS, è possibile scegliere una delle seguenti modalità:

* Aggiornare le credenziali per l'entità servizio esistente.
* Creare una nuova entità servizio e aggiornare il cluster per usare le nuove credenziali. 

> [!WARNING]
> Se si sceglie di creare una *nuova* entità servizio, l'aggiornamento di un cluster AKS di grandi dimensioni per usare queste credenziali può richiedere molto tempo.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Controllare la data di scadenza dell'entità servizio

Per controllare la data di scadenza dell'entità servizio, usare il comando [AZ ad SP Credential list][az-ad-sp-credential-list] . Nell'esempio seguente viene ottenuto l'ID dell'entità servizio per il cluster denominato *myAKSCluster* nel gruppo di risorse *myResourceGroup* usando il comando [AZ AKS Show][az-aks-show] . L'ID entità servizio è impostato come variabile denominata *SP_ID* per l'uso con il comando [AZ ad SP Credential list][az-ad-sp-credential-list] .

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>Reimposta le credenziali dell'entità servizio esistente

Per aggiornare le credenziali per l'entità servizio esistente, ottenere l'ID dell'entità servizio del cluster usando il comando [az aks show][az-aks-show]. L'esempio seguente ottiene l'ID per il cluster denominato *myAKSCluster* nel gruppo di risorse *myResourceGroup*. L'ID entità servizio è impostato come variabile denominata *SP_ID* per l'uso in un comando aggiuntivo. Questi comandi usano la sintassi bash.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Con un set variabile contenente l'ID dell'entità servizio, reimpostare le credenziali usando [az ad sp credential reset][az-ad-sp-credential-reset]. L'esempio seguente consente alla piattaforma Azure di generare un nuovo segreto sicuro per l'entità servizio. Questo nuovo segreto sicuro viene archiviato anche come una variabile.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Continuare ora ad [aggiornare il cluster AKS con le nuove credenziali dell'entità servizio](#update-aks-cluster-with-new-service-principal-credentials). Questo passaggio è necessario per le modifiche dell'entità servizio per la reflection nel cluster AKS.

### <a name="create-a-new-service-principal"></a>Creare una nuova entità servizio

Se si sceglie di aggiornare le credenziali dell'entità servizio esistente nella sezione precedente, ignorare questo passaggio. Continuare ad [aggiornare il cluster AKS con le nuove credenziali dell'entità servizio](#update-aks-cluster-with-new-service-principal-credentials).

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

Continuare ora ad [aggiornare il cluster AKS con le nuove credenziali dell'entità servizio](#update-aks-cluster-with-new-service-principal-credentials). Questo passaggio è necessario per le modifiche dell'entità servizio per la reflection nel cluster AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aggiornare il cluster AKS con le nuove credenziali dell'entità servizio

> [!IMPORTANT]
> Per i cluster di grandi dimensioni, l'aggiornamento del cluster AKS con una nuova entità servizio può richiedere molto tempo.

Indipendentemente dal fatto che si sia scelto di aggiornare le credenziali per l'entità servizio esistente o di creare un'entità servizio, aggiornare il cluster AKS con le nuove credenziali usando il comando [az aks update-credentials][az-aks-update-credentials]. Vengono usate le variabili per *--service-principal* e *--client-secret*:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret "$SP_SECRET"
```

Per i cluster di piccole e medie dimensioni, sono necessari alcuni istanti per aggiornare le credenziali dell'entità servizio nell'AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aggiornare il cluster AKS con le nuove credenziali dell'applicazione AAD

È possibile creare nuove applicazioni server e client AAD attenendosi alla [procedura di integrazione di AAD][create-aad-app]. In alternativa, reimpostare le applicazioni AAD esistenti seguendo lo [stesso metodo per la reimpostazione dell'entità servizio](#reset-the-existing-service-principal-credential). Dopodiché è sufficiente aggiornare le credenziali dell'applicazione AAD del cluster usando lo stesso comando [AZ AKS Update-Credentials][az-aks-update-credentials] ma usando le variabili *--Reset-AAD* .

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

In questo articolo è stata aggiornata l'entità servizio per il cluster AKS e le applicazioni di integrazione di AAD. Per altre informazioni su come gestire l'identità per i carichi di lavoro all'interno di un cluster, consultare [Procedure consigliate per l'autenticazione e l'autorizzazione nel servizio Azure Kubernetes][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
