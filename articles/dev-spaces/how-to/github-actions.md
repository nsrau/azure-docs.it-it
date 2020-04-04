---
title: Azioni GitHub & servizio Azure Kubernetes (anteprima)GitHub Actions & Azure Kubernetes Service (preview)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Esaminare e testare le modifiche da una richiesta pull direttamente nel servizio Azure Kubernetes usando le azioni GitHub e gli spazi di sviluppo di AzureReview and test changes from a pull request directly in Azure Kubernetes Service using GitHub Actions and Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, azioni GitHub, Helm, rete mesh del servizio, routing mesh del servizio, kubectl, k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637937"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Azioni GitHub & servizio Azure Kubernetes (anteprima)GitHub Actions & Azure Kubernetes Service (preview)

Azure Dev Spaces offre un flusso di lavoro usando le azioni GitHub che consente di testare le modifiche da una richiesta pull direttamente in AKS prima che la richiesta pull venga unita nel ramo principale del repository. La presenza di un'applicazione in esecuzione per esaminare le modifiche di una richiesta pull può aumentare la sicurezza sia dello sviluppatore che dei membri del team. Questa applicazione in esecuzione può anche aiutare i membri del team, ad esempio product manager e progettisti, a entrare a far parte del processo di revisione durante le prime fasi di sviluppo.

In questa guida si apprenderà come:

* Configurare Azure Dev Spaces in un cluster Kubernetes gestito in Azure.
* Distribuire un'applicazione di grandi dimensioni con più microservizi in uno spazio di sviluppo.
* Configurare CI/CD con le azioni GitHub.
* Testare un singolo microservizio in uno spazio di sviluppo isolato all'interno del contesto dell'applicazione completa.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata][azure-cli-installed].
* [Helm 3 installato][helm-installed].
* Un account GitHub con [azioni GitHub abilitate.][github-actions-beta-signup]
* [L'applicazione di esempio Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) in esecuzione in un cluster AKS.

## <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

Creare un Registro Azure Container (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Il nome del file ACR deve essere univoco all'interno di Azure e contenere da 5 a 50 caratteri alfanumerici. Tutte le lettere che usi devono essere minuscole.

Salvare il valore *loginServer* dall'output perché viene utilizzato in un passaggio successivo.

## <a name="create-a-service-principal-for-authentication"></a>Creare un'entità servizio per l'autenticazioneCreate a service principal for authentication

Usare [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] per creare un'entità servizio. Ad esempio:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Salvare l'output JSON perché viene usato in un passaggio successivo.

Utilizzare [az aks show][az-aks-show] per visualizzare l'ID del cluster AKS: *ID*

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Utilizzare [az acr show][az-acr-show] per visualizzare l'ID dell'ACR: *ID*

```azurecli
az acr show --name <acrName> --query id
```

Usare [az role assignment create][az-role-assignment-create] per concedere a *Collaboratore* l'accesso al cluster AKS e l'accesso *AcrPush* all'ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Per concedere all'entità servizio l'accesso a tali risorse, è necessario essere il proprietario sia del cluster AKS che di un'applicazione di gruppo.

## <a name="configure-your-github-action"></a>Configurare l'azione GitHubConfigure your GitHub action

> [!IMPORTANT]
> È necessario che gitHub Actions sia abilitato per il repository. Per abilitare le azioni GitHub per il repository, passare al repository su GitHub, fare clic sulla scheda Azioni e scegliere di abilitare le azioni per il repository.

Passare al repository biforke e fare clic su *Impostazioni*. Fai clic su *Segreti* nella barra laterale sinistra. Fai clic su *Aggiungi un nuovo segreto* per aggiungere ogni nuovo segreto di seguito:

1. *AZURE_CREDENTIALS:* l'intero output della creazione dell'entità servizio.
1. *RESOURCE_GROUP:* il gruppo di risorse per il cluster AKS, che in questo esempio è *MyResourceGroup*.
1. *CLUSTER_NAME*: il nome del cluster AKS, che in questo esempio è *MyAKS*.
1. *CONTAINER_REGISTRY*: il *loginServer* per il file ACR.
1. *HOST*: l'host per lo spazio di sviluppo, che assume la forma *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, che in questo esempio è *dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET*: il nome del segreto che si desidera utilizzare, ad esempio *demo-secret*.
1. *MASTER_SPACE*: il nome dello spazio Dev padre, che in questo esempio è *dev*.
1. *REGISTRY_USERNAME:* *clientId* dall'output JSON dalla creazione dell'entità servizio.
1. *REGISTRY_PASSWORD:* *clientSecret* dall'output JSON dalla creazione dell'entità servizio.

> [!NOTE]
> Tutti questi segreti vengono utilizzati dall'azione GitHub e sono configurati in [.github/workflows/bikes.yml][github-action-yaml].

Facoltativamente, se si desidera aggiornare lo spazio master dopo l'unione della richiesta di disponibilità, aggiungere il *segreto GATEWAY_HOST,* che assume la forma *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, che in questo esempio è *dev.gateway.fedcab0987.eus.azds.io.* Dopo aver unito le modifiche nel ramo master nel fork, verrà eseguita un'altra azione per ricompilare ed eseguire l'intera applicazione nello spazio di sviluppo master. In questo esempio, lo spazio master è *dev*. Questa azione è configurata in [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

Inoltre, se si desidera che le modifiche nella PR vengano eseguite in uno spazio nipote, aggiornare i segreti *MASTER_SPACE* e *HOST.* Ad esempio, se l'applicazione è in esecuzione in *dev* con uno spazio figlio *dev/azureuser1*, per fare in modo che la richiesta di proprietà venga eseguita in uno spazio figlio di *dev/azureuser1*:

* Aggiornare *MASTER_SPACE* allo spazio figlio desiderato come spazio padre, in questo esempio *azureuser1*.
* Aggiornare *HOST* per *<GRANDPARENT_SPACE>>.HOST_SUFFIX <APP_NAME <> <GRANDPARENT_SPACE.<*, in questo esempio *dev.bikesharingweb.fedcab0987.eus.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Creare un nuovo ramo per le modifiche al codiceCreate a new branch for code changes

Individuare `BikeSharingApp/` e creare un nuovo ramo denominato *bike-images*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Modificare [Bikes/server.js][bikes-server-js] per rimuovere le righe 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

La sezione dovrebbe ora essere simile alla seguente:The section should now look like:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salvare il file `git add` `git commit` e quindi utilizzare e per mettere in scena le modifiche.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Spingere le modifiche

Utilizzare `git push` per eseguire il push del nuovo ramo nel repository forked:

```cmd
git push origin bike-images
```

Al termine del push, passare al repository forked su GitHub per creare una richiesta pull con il ramo *master* nel repository forked come ramo di base rispetto al ramo *bike-images.*

Dopo l'apertura della richiesta pull, passare alla scheda *Azioni.* Verificare che sia stata avviata una nuova azione e che venga creato il servizio *Bikes.*

## <a name="view-the-child-space-with-your-changes"></a>Visualizzare lo spazio figlio con le modifiche

Al termine dell'azione, verrà visualizzato un commento con un URL al nuovo spazio figlio in base alle modifiche nella richiesta pull.

> [!div class="mx-imgBorder"]
> ![URL azione GitHub](../media/github-actions/github-action-url.png)

Passare al servizio *bikesharingweb* aprendo l'URL dal commento. Seleziona *Aurelia Briggs (cliente)* come utente, quindi seleziona una bicicletta da noleggiare. Verificare che l'immagine segnaposto per la bicicletta non sia più visualizzata.

Se si uniscono le modifiche nel ramo *master* nel fork, verrà eseguita un'altra azione per ricompilare ed eseguire l'intera applicazione nello spazio di sviluppo padre. In questo esempio, lo spazio padre è *dev*. Questa azione è configurata in [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Pulire le risorse di Azure

```azurecli
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
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
