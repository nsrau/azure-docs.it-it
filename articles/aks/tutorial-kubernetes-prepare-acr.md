---
title: 'Esercitazione su Kubernetes in Azure: preparare Registro contenitori di Azure'
description: 'Esercitazione sul servizio contenitore di Azure: preparare Registro contenitori di Azure'
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0bef14a03c27feb07683f4805aa4264eb91eda6e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Distribuire e usare il Registro contenitori di Azure

Il Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questa esercitazione, la seconda di otto, illustra la distribuzione di un'istanza di Registro contenitori di Azure e il push di un'immagine del contenitore in tale istanza. I passaggi completati comprendono:

> [!div class="checklist"]
> * Distribuzione di un'istanza di Registro contenitori di Azure
> * Assegnazione di tag a un'immagine del contenitore per Registro contenitori di Azure
> * Caricamento dell'immagine in Registro contenitori di Azure

Nelle esercitazioni successive, questa istanza di Registro contenitori di Azure verrà integrata con un cluster Kubernetes nel servizio contenitore di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Nell'[esercitazione precedente][aks-tutorial-prepare-app] è stata creata un'immagine del contenitore per una semplice applicazione Azure Voting. Se l'immagine dell'app Azure Voting non è stata creata, tornare all'[Esercitazione 1 - Creare immagini del contenitore][aks-tutorial-prepare-app].

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.27 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="deploy-azure-container-registry"></a>Distribuire il Registro contenitori di Azure

Prima di distribuire un Registro contenitori di Azure, è necessario che esista un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create][az-group-create]. In questo esempio viene creato un gruppo di risorse denominato `myResourceGroup` nell'area `eastus`.

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare un registro contenitori di Azure con il comando [az acr create][az-acr-create]. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Nella parte restante di questa esercitazione si usa `<acrName>` come segnaposto per il nome del registro contenitori.

## <a name="container-registry-login"></a>Accesso al registro contenitori

Usare il comando [az acr login][az-acr-login] per accedere all'istanza del Registro contenitori di Azure. È necessario specificare il nome univoco assegnato al registro contenitori al momento della creazione.

```azurecli
az acr login --name <acrName>
```

Al termine, il comando restituisce un messaggio di accesso riuscito.

## <a name="tag-container-images"></a>Assegnare tag alle immagini del contenitore

Per visualizzare un elenco di immagini correnti, usare il comando [docker images][docker-images].

```console
docker images
```

Output:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Ogni immagine del contenitore deve essere contrassegnata con il nome del server di accesso del registro. Questo tag viene usato per il routing quando si esegue il push delle immagini del contenitore nel registro delle immagini.

Per ottenere il nome loginServer, eseguire questo comando.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Applicare ora il tag loginServer del registro contenitori all'immagine `azure-vote-front`. Aggiungere anche `:v1` alla fine del nome dell'immagine. Questo tag indica la versione dell'immagine.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Dopo l'assegnazione del tag, eseguire [docker images][docker-images] per verificare l'operazione.

```console
docker images
```

Output:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Eseguire il push delle immagini nel registro

Eseguire il push dell'immagine `azure-vote-front` nel registro.

Nell'esempio seguente sostituire il nome del loginServer del Registro contenitori di Azure con il loginServer dell'ambiente in uso.

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Il completamento dell'operazione richiede alcuni minuti.

## <a name="list-images-in-registry"></a>Elencare le immagini nel registro

Per restituire un elenco di immagini di cui è stato eseguito il push nel Registro contenitori di Azure, usare il comando [az acr repository list][az-acr-repository-list]. Aggiornare il comando con il nome dell'istanza del Registro contenitori di Azure.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
azure-vote-front
```

Per visualizzare i tag per un'immagine specifica, usare il comando [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Output:

```azurecli
Result
--------
v1
```

Al termine dell'esercitazione, l'immagine del contenitore sarà stata archiviata in un'istanza privata di Registro contenitori di Azure. Questa immagine verrà distribuita da Registro contenitori di Azure a un cluster Kubernetes nelle esercitazioni successive.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato preparata un'istanza di Registro contenitori di Azure da usare in un cluster del servizio contenitore di Azure. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione di un'istanza di Registro contenitori di Azure
> * Assegnazione di tag a un'immagine del contenitore per Registro contenitori di Azure
> * Caricamento dell'immagine in Registro contenitori di Azure

Passare all'esercitazione successiva per informazioni sulla distribuzione di un cluster Kubernetes in Azure.

> [!div class="nextstepaction"]
> [Distribuire un cluster Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md