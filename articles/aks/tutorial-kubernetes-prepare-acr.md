---
title: Esercitazione su Kubernetes in Azure - Creare un registro contenitori
description: In questa esercitazione sul servizio Azure Kubernetes si crea un'istanza di Registro Azure Container e si carica un'immagine del contenitore dell'applicazione di esempio.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5089326af1d7f6e057667cd916f35de92bf517ef
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614252"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Esercitazione: Distribuire e usare Registro Azure Container

Registro Azure Container è un registro privato per le immagini dei contenitori. Un registro contenitori privato consente di compilare e distribuire le applicazioni e il codice personalizzato in modo sicuro. In questa esercitazione, parte due di sette, si distribuisce un'istanza di Registro Azure Container, in cui si esegue il push di un'immagine del contenitore. Si apprenderà come:

> [!div class="checklist"]
> * Creare un'istanza di Registro Azure Container
> * Assegnare tag a un'immagine del contenitore per Registro Azure Container
> * Caricare l'immagine in Registro Azure Container
> * Visualizzare le immagini nel registro

In altre esercitazioni questa istanza di Registro Azure Container verrà integrata con un cluster Kubernetes nel servizio Azure Kubernetes e verrà distribuita un'applicazione dall'immagine.

## <a name="before-you-begin"></a>Prima di iniziare

Nell'[esercitazione precedente][aks-tutorial-prepare-app] è stata creta un'immagine del contenitore per una semplice applicazione Azure Voting. Se l'immagine dell'app Azure Vote non è stata creata, tornare all'[Esercitazione 1 - Creare immagini del contenitore][aks-tutorial-prepare-app].

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.53 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

Per creare un'istanza di Registro Azure Container, è necessario che esista un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create][az-group-create]. Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare un'istanza di Registro Azure Container con il comando [az acr create][az-acr-create] e specificare il nome del registro. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Nella parte restante di questa esercitazione viene usato `<acrName>` come segnaposto per il nome del registro contenitori. Specificare un nome di registro univoco. Lo SKU *Basic* è un punto di ingresso con costi ottimali a fini di sviluppo, che assicura l'equilibrio tra spazio di archiviazione e velocità effettiva.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Accedere al registro contenitori

Per usare l'istanza di Registro Azure Container, prima è necessario accedere. Usare il comando [az acr login][az-acr-login] e specificare il nome univoco assegnato al registro contenitori nel passaggio precedente.

```azurecli
az acr login --name <acrName>
```

Al termine, il comando restituisce un messaggio di *Accesso riuscito*.

## <a name="tag-a-container-image"></a>Contrassegnare un'immagine del contenitore

Per visualizzare un elenco delle immagini locali correnti, usare il comando [docker images][docker-images]:

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Per usare l'immagine del contenitore *azure-vote-front* con Registro Azure Container, l'immagine deve essere contrassegnata con l'indirizzo del server di accesso del registro. Questo tag viene usato per il routing quando si esegue il push delle immagini del contenitore nel registro delle immagini.

Per ottenere l'indirizzo del server di accesso, usare il comando [az acr list][az-acr-list] e cercare *loginServer*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Contrassegnare ora l'immagine *azure-vote-front* locale con l'indirizzo *acrloginServer* del registro contenitori. Per indicare la versione dell'immagine, aggiungere *:v1* alla fine del nome dell'immagine:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Per verificare che i tag siano applicati, eseguire di nuovo [docker images][docker-images]. Un'immagine è contrassegnata con l'indirizzo dell'istanza di Registro Azure Container e un numero di versione.

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Eseguire il push delle immagini nel registro

Dopo aver creato e contrassegnato l'immagine, eseguire il push dell'immagine *azure-vote-front* nell'istanza di Registro Azure Container. Usare [docker push][docker-push] e specificare l'indirizzo *acrLoginServer* per l'immagine come di seguito:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Il completamento del push dell'immagine in Registro Azure Container potrebbe richiedere alcuni minuti.

## <a name="list-images-in-registry"></a>Elencare le immagini nel registro

Per restituire un elenco di immagini di cui è stato eseguito il push nell'istanza di Registro Azure Container, usare il comando [az acr repository list][az-acr-repository-list]. Immettere il valore `<acrName>` personalizzato come di seguito:

```azurecli
az acr repository list --name <acrName> --output table
```

L'output di esempio seguente elenca l'immagine *azure-vote-front* disponibile nel registro:

```
Result
----------------
azure-vote-front
```

Per visualizzare i tag per un'immagine specifica, usare il comando [az acr repository show-tags][az-acr-repository-show-tags] come di seguito:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

L'esempio di output seguente mostra l'immagine *v1* contrassegnata in un passaggio precedente:

```
Result
--------
v1
```

È ora disponibile un'immagine del contenitore archiviata in un'istanza privata di Registro Azure Container. Questa immagine verrà distribuita da Registro Azure Container a un cluster Kubernetes nell'esercitazione successiva.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'istanza di Registro Azure Container ed è stato eseguito il push di un'immagine da usare in un cluster del servizio Azure Kubernetes. Si è appreso come:

> [!div class="checklist"]
> * Creare un'istanza di Registro Azure Container
> * Assegnare tag a un'immagine del contenitore per Registro Azure Container
> * Caricare l'immagine in Registro Azure Container
> * Visualizzare le immagini nel registro

Passare all'esercitazione successiva per informazioni su come distribuire un cluster Kubernetes in Azure.

> [!div class="nextstepaction"]
> [Distribuire un cluster Kubernetes][aks-tutorial-deploy-cluster].

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
