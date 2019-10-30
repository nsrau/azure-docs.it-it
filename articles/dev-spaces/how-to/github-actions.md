---
title: Azioni di GitHub & servizio Azure Kubernetes
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Esaminare e testare le modifiche da una richiesta pull direttamente nel servizio Azure Kubernetes usando le azioni di GitHub e Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori, azioni di GitHub, Helm, mesh dei servizi, routing mesh del servizio, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 2638fe2cd12407e43d3b3b698cdfca5231362db4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065938"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Azioni di GitHub & servizio Azure Kubernetes (anteprima)

Azure Dev Spaces fornisce un flusso di lavoro con azioni di GitHub che consente di testare le modifiche da una richiesta pull direttamente in AKS prima che la richiesta pull venga unita al ramo principale del repository. Il fatto che un'applicazione in esecuzione riveda le modifiche di una richiesta pull può aumentare la confidenza sia per lo sviluppatore che per i membri del team. Questa applicazione in esecuzione può anche aiutare i membri del team, ad esempio i responsabili del prodotto e i progettisti, a diventare parte del processo di revisione durante le fasi iniziali dello sviluppo.

In questa guida si apprenderà come:

- Configurare Azure Dev Spaces in un cluster Kubernetes gestito in Azure.
- Distribuire un'applicazione di grandi dimensioni con più microservizi in uno spazio di sviluppo.
- Configurare CI/CD con le azioni di GitHub.
- Testare un singolo microservizio in uno spazio di sviluppo isolato all'interno del contesto dell'applicazione completa.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
- [L'interfaccia della riga di comando di Azure installata][azure-cli-installed].
- [Helm 2.13 o versioni successive installato][helm-installed].
- Un account GitHub con [azioni di GitHub abilitata][github-actions-beta-signup].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

È necessario creare un cluster del servizio Azure Kubernetes in un'[area supportata][supported-regions]. I comandi seguenti creano un gruppo di risorse denominato *MyResourceGroup* e un cluster del servizio Azure Kubernetes denominato *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Abilitare Azure Dev Spaces nel cluster del servizio Azure Kubernetes

Usare il comando `use-dev-spaces` per abilitare Dev Spaces nel cluster del servizio Azure Kubernetes e seguire i prompt. Il comando seguente abilita Dev Spaces nel cluster *MyAKS* all'interno del gruppo *MyResourceGroup* e crea uno spazio di sviluppo denominato *dev*.

> [!NOTE]
> Il comando `use-dev-spaces` installerà anche l'interfaccia della riga di comando di Azure Dev Spaces se non è già installata. Non è possibile installare l'interfaccia della riga di comando Azure Dev Spaces in Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

Creare un Container Registry di Azure (ACR):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Il nome del record di registro di sistema deve essere univoco all'interno di Azure e contenere 5-50 caratteri alfanumerici. Tutte le lettere utilizzate devono essere minuscole.

Salvare il valore *loginServer* dall'output perché verrà usato in un passaggio successivo.

## <a name="create-a-service-principal-for-authentication"></a>Creare un'entità servizio per l'autenticazione

Usare [AZ ad SP create-for-RBAC][az-ad-sp-create-for-rbac] per creare un'entità servizio. ad esempio:

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

## <a name="get-sample-application-code"></a>Ottenere il codice dell'applicazione di esempio

In questo articolo si usa l' [applicazione di esempio Azure Dev Spaces bike sharing][bike-sharing-gh] per illustrare l'uso di Azure Dev Spaces con le azioni di GitHub.

Creare un fork del repository di esempio Azure Dev Spaces quindi passare al repository con fork. Fare clic sulla scheda *azioni* e scegliere di abilitare le azioni per questo repository.

Clonare il repository con fork e passare alla relativa directory:

```cmd
git clone https://github.com/USERNAME/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Recuperare il valore HostSuffix per *dev*

Usare il comando `azds show-context` per visualizzare il valore HostSuffix per *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aggiornare il grafico Helm con il valore HostSuffix

Aprire [charts/values.yaml][bike-sharing-values-yaml] e sostituire tutte le istanze di `<REPLACE_ME_WITH_HOST_SUFFIX>` con il valore HostSuffix recuperato in precedenza. Salvare le modifiche e chiudere il file.

## <a name="run-the-sample-application-in-kubernetes"></a>Eseguire l'applicazione di esempio in Kubernetes

I comandi per l'esecuzione dell'applicazione di esempio in Kubernetes fanno parte di un processo esistente e non presentano alcuna dipendenza dagli strumenti di Azure Dev Spaces. In questo caso, Helm è lo strumento usato per eseguire questa applicazione di esempio, ma potrebbero essere usati altri strumenti per eseguire l'intera applicazione in uno spazio dei nomi all'interno di un cluster. La destinazione dei comandi di Helm è lo spazio di sviluppo denominato *dev* creato in precedenza, ma questo spazio è anche uno spazio dei nomi Kubernetes. Di conseguenza, gli spazi di sviluppo possono essere la destinazione di altri strumenti come altri spazi dei nomi.

È possibile usare Azure Dev Spaces per lo sviluppo dopo che un'applicazione è in esecuzione in un cluster, indipendentemente dagli strumenti usati per distribuirla.

Usare i comandi `helm init` e `helm install` per configurare e installare l'applicazione di esempio in un cluster.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

> [!Note]
> **Se si usa un cluster abilitato per il controllo degli accessi in base al ruolo**, è necessario configurare un [account del servizio per Tiller][tiller-rbac]. In caso contrario i comandi `helm` non funzionano.

Il completamento del comando `helm install` può richiedere alcuni minuti. L'output del comando mostra lo stato di tutti i servizi che ha distribuito nel cluster una volta completato:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

Dopo l'installazione dell'applicazione di esempio nel cluster e l'abilitazione di Dev Spaces nel cluster, usare il comando `azds list-uris` per visualizzare gli URL per l'applicazione di esempio nello spazio di sviluppo *dev* attualmente selezionato.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Passare al servizio *bikesharingweb* aprendo l'URL pubblico dal comando `azds list-uris`. Nell'esempio precedente l'URL pubblico per il servizio *bikesharingweb* è `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selezionare *Aurelia Briggs (Customer)* come utente, quindi selezionare una bicicletta da affittare. Verificare che venga visualizzata un'immagine segnaposto per la bicicletta.

## <a name="configure-your-github-action"></a>Configurare l'azione GitHub

Passare al repository con fork e fare clic su *Settings (impostazioni*). Fare clic su *Secrets (segreti* ) nella barra laterale sinistra. Fare clic su *Aggiungi un nuovo segreto* per aggiungere ogni nuovo segreto:

1. *AZURE_CREDENTIALS*: l'intero output della creazione dell'entità servizio.
1. *RESOURCE_GROUP*: il gruppo di risorse per il cluster AKS, che in questo esempio è *MyResourceGroup*.
1. *CLUSTER_NAME*: il nome del cluster AKS, che in questo esempio è *MyAKS*.
1. *CONTAINER_REGISTRY*: *LOGINSERVER* per l'ACR.
1. *Host*: host per lo spazio di sviluppo, che assume il formato *< MASTER_SPACE >. < APP_NAME >. < HOST_SUFFIX >* , che in questo esempio è *dev.bikesharingweb.fedcab0987.EUS.azds.io*.
1. *IMAGE_PULL_SECRET*: il nome del segreto che si vuole usare, ad esempio *demo-Secret*.
1. *MASTER_SPACE*: il nome dello spazio di sviluppo padre, che in questo esempio è *dev*.
1. *REGISTRY_USERNAME*: *ClientID* dall'output JSON della creazione dell'entità servizio.
1. *REGISTRY_PASSWORD*: *CLIENTSECRET* dall'output JSON della creazione dell'entità servizio.

> [!NOTE]
> Tutti questi segreti vengono usati dall'azione GitHub e sono configurati in [. github/workflows/Bikes. yml][github-action-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Creare un nuovo ramo per le modifiche al codice

Tornare a `BikeSharingApp/` e creare un nuovo ramo denominato *bike-images*.

```cmd
cd ..
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

Al termine del push, passare al repository con fork in GitHub creare una richiesta pull con il *Master* nel repository con fork come Branch di base rispetto al ramo *bike-images* .

Dopo l'apertura della richiesta pull, passare alla scheda *azioni* . Verificare che sia stata avviata una nuova azione che sta creando il servizio *Bikes* .

## <a name="view-the-child-space-with-your-changes"></a>Visualizzare lo spazio figlio con le modifiche

Al termine dell'azione, viene visualizzato un commento con un URL per il nuovo spazio figlio in base alle modifiche apportate alla richiesta pull.

> [!div class="mx-imgBorder"]
> ![URL azione GitHub](../media/github-actions/github-action-url.png)

Passare al servizio *bikesharingweb* aprendo l'URL dal commento. Selezionare *Aurelia Briggs (Customer)* come utente, quindi selezionare una bicicletta da affittare. Verificare che l'immagine segnaposto per la bicicletta non sia più visualizzata.

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