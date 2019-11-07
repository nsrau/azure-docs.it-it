---
title: Azioni di GitHub & servizio Azure Kubernetes
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 11/04/2019
ms.topic: conceptual
description: Esaminare e testare le modifiche da una richiesta pull direttamente nel servizio Azure Kubernetes usando le azioni di GitHub e Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori, azioni di GitHub, Helm, mesh dei servizi, routing mesh del servizio, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 590d49f4c189ff48f20369d18b17e0f6e4a46fa2
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571599"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Azioni di GitHub & servizio Azure Kubernetes (anteprima)

Azure Dev Spaces fornisce un flusso di lavoro con azioni di GitHub che consente di testare le modifiche da una richiesta pull direttamente in AKS prima che la richiesta pull venga unita al ramo principale del repository. Il fatto che un'applicazione in esecuzione riveda le modifiche di una richiesta pull può aumentare la confidenza sia per lo sviluppatore che per i membri del team. Questa applicazione in esecuzione può anche aiutare i membri del team, ad esempio i responsabili del prodotto e i progettisti, a diventare parte del processo di revisione durante le fasi iniziali dello sviluppo.

In questa guida si apprenderà come:

* Configurare Azure Dev Spaces in un cluster Kubernetes gestito in Azure.
* Distribuire un'applicazione di grandi dimensioni con più microservizi in uno spazio di sviluppo.
* Configurare CI/CD con le azioni di GitHub.
* Testare un singolo microservizio in uno spazio di sviluppo isolato all'interno del contesto dell'applicazione completa.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata][azure-cli-installed].
* [Helm 2.13 o versioni successive installato][helm-installed].
* Un account GitHub con [azioni di GitHub abilitata][github-actions-beta-signup].
* L' [applicazione di esempio Azure Dev Spaces bike sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) in esecuzione in un cluster AKS.

## <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

Creare un Container Registry di Azure (ACR):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Il nome del record di registro di sistema deve essere univoco all'interno di Azure e contenere 5-50 caratteri alfanumerici. Tutte le lettere utilizzate devono essere minuscole.

Salvare il valore *loginServer* dall'output perché verrà usato in un passaggio successivo.

## <a name="create-a-service-principal-for-authentication"></a>Creare un'entità servizio per l'autenticazione

Usare [AZ ad SP create-for-RBAC][az-ad-sp-create-for-rbac] per creare un'entità servizio. Ad esempio:

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Salvare l'output JSON perché verrà usato in un passaggio successivo.


Usare [AZ AKS Show][az-aks-show] per visualizzare l' *ID* del cluster AKS:

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Usare [AZ ACR Show][az-acr-show] per visualizzare l' *ID* dell'ACR:

```cmd
az acr show --name <acrName> --query id
```

Usare [AZ Role Assignment create][az-role-assignment-create] per concedere l'accesso come *collaboratore* al cluster AKS e *AcrPush* l'accesso a ACR.

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Per consentire all'entità servizio di accedere a tali risorse, è necessario essere il proprietario del cluster AKS e del record di registro di sistema.

## <a name="configure-your-github-action"></a>Configurare l'azione GitHub

> [!IMPORTANT]
> È necessario che siano abilitate le azioni di GitHub per il repository. Per abilitare le azioni di GitHub per il repository, passare al repository in GitHub, fare clic sulla scheda azioni e scegliere di abilitare le azioni per questo repository.

Passare al repository con fork e fare clic su *Settings (impostazioni*). Fare clic su *Secrets (segreti* ) nella barra laterale sinistra. Fare clic su *Aggiungi un nuovo segreto* per aggiungere ogni nuovo segreto:

1. *AZURE_CREDENTIALS*: l'intero output della creazione dell'entità servizio.
1. *RESOURCE_GROUP*: il gruppo di risorse per il cluster AKS, che in questo esempio è *MyResourceGroup*.
1. *CLUSTER_NAME*: il nome del cluster AKS, che in questo esempio è *MyAKS*.
1. *CONTAINER_REGISTRY*: *LOGINSERVER* per l'ACR.
1. *Host*: host per lo spazio di sviluppo, che assume il formato *< MASTER_SPACE >. < APP_NAME >. < HOST_SUFFIX >* , che in questo esempio è *dev.bikesharingweb.fedcab0987.EUS.azds.io*.
1. *HOST_SUFFIX*: suffisso host per lo spazio di sviluppo, che in questo esempio è *fedcab0987.EUS.azds.io*.
1. *IMAGE_PULL_SECRET*: il nome del segreto che si vuole usare, ad esempio *demo-Secret*.
1. *MASTER_SPACE*: il nome dello spazio di sviluppo padre, che in questo esempio è *dev*.
1. *REGISTRY_USERNAME*: *ClientID* dall'output JSON della creazione dell'entità servizio.
1. *REGISTRY_PASSWORD*: *CLIENTSECRET* dall'output JSON della creazione dell'entità servizio.

> [!NOTE]
> Tutti questi segreti vengono usati dall'azione GitHub e sono configurati in [. github/workflows/Bikes. yml][github-action-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Creare un nuovo ramo per le modifiche al codice

Passare a `BikeSharingApp/` e creare un nuovo ramo denominato *bike-images*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Modificare [Bikes/server. js][bikes-server-js] per rimuovere le righe 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

La sezione dovrebbe ora essere simile alla seguente:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salvare il file, quindi usare `git add` e `git commit` per organizzare le modifiche.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Eseguire il push delle modifiche

Usare `git push` per eseguire il push del nuovo ramo nel repository con fork:

```cmd
git push origin bike-images
```

Al termine del push, passare al repository con fork in GitHub creare una richiesta pull con lo *sviluppatore* nel repository con fork come Branch di base rispetto al ramo *bike-images* .

Dopo l'apertura della richiesta pull, passare alla scheda *azioni* . Verificare che sia stata avviata una nuova azione che sta creando il servizio *Bikes* .

## <a name="view-the-child-space-with-your-changes"></a>Visualizzare lo spazio figlio con le modifiche

Al termine dell'azione, viene visualizzato un commento con un URL per il nuovo spazio figlio in base alle modifiche apportate alla richiesta pull.

> [!div class="mx-imgBorder"]
> ![URL azione GitHub](../media/github-actions/github-action-url.png)

Passare al servizio *bikesharingweb* aprendo l'URL dal commento. Selezionare *Aurelia Briggs (Customer)* come utente, quindi selezionare una bicicletta da affittare. Verificare che l'immagine segnaposto per la bicicletta non sia più visualizzata.

Se si uniscono le modifiche nel ramo *dev* , viene eseguita un'altra azione per ricompilare ed eseguire l'intera applicazione nello spazio di sviluppo padre. In questo esempio lo spazio padre è *dev*. Questa azione è configurata in [. github/workflows/bikesharing. yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Pulire le risorse di Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Sviluppo in team in Azure Dev Spaces][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
