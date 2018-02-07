---
title: Esercitazione di Istanze di contenitore di Azure - Preparare Registro contenitori di Azure
description: Esercitazione di Istanze di contenitore di Azure - Parte 2 di 3 - Preparare Registro contenitori di Azure
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 94ecba44b8281460da4518c146aab814d2eaa850
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Distribuire e usare il Registro contenitori di Azure

Questa è la parte due di un'esercitazione in tre parti. Nel [passaggio precedente](container-instances-tutorial-prepare-app.md) è stata creta un'immagine del contenitore per una semplice applicazione Web scritta in [Node.js][nodejs]. In questa esercitazione si esegue il push dell'immagine in un'istanza del Registro contenitori di Azure. Se l'immagine del contenitore non è stata creata, tornare all'[Esercitazione 1 - Creare l'immagine del contenitore](container-instances-tutorial-prepare-app.md).

Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questa esercitazione illustra la distribuzione di un'istanza di Registro contenitori di Azure e il push di un'immagine del contenitore in essa.

In questo articolo, che corrisponde alla seconda parte della serie, è possibile eseguire queste operazioni:

> [!div class="checklist"]
> * Distribuire un'istanza di Registro contenitori di Azure
> * Assegnare un tag all'immagine del contenitore per il Registro contenitori di Azure
> * Caricare l'immagine nel registro

Nell'articolo successivo, che corrisponde all'ultima esercitazione della serie, il contenitore viene distribuito dal registro privato a Istanze di contenitore di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.23 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-install].

Per completare questa esercitazione è necessario un ambiente di sviluppo Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. È necessario installare l'interfaccia della riga di comando di Azure e l'ambiente di sviluppo Docker nel computer locale per completare questa esercitazione.

## <a name="deploy-azure-container-registry"></a>Distribuire il Registro contenitori di Azure

Prima di distribuire un Registro contenitori di Azure, è necessario che esista un gruppo di risorse. Un gruppo di risorse di Azure è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create][az-group-create]. In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare un registro contenitori di Azure con il comando [az acr create][az-acr-create]. Il nome del registro contenitori deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Sostituire `<acrName>` con un nome univoco per il registro:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Ad esempio, per creare un registro contenitori di Azure denominato *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Nella parte restante di questa esercitazione si usa `<acrName>` come segnaposto per il nome del registro contenitori scelto.

## <a name="container-registry-login"></a>Accesso al registro contenitori

È necessario accedere all'istanza del Registro contenitori di Azure prima di eseguire il push di immagini. Usare il comando [az acr login][az-acr-login] per completare l'operazione. È necessario specificare il nome univoco assegnato al registro contenitori al momento della creazione.

```azurecli
az acr login --name <acrName>
```

Il comando restituisce un messaggio `Login Succeeded` al termine dell'esecuzione.

## <a name="tag-container-image"></a>Assegnare tag all'immagine del contenitore

Per distribuire un'immagine del contenitore da un registro privato, all'immagine deve essere assegnato un tag con il nome `loginServer` del registro.

Per visualizzare un elenco di immagini correnti, usare il comando [docker images][docker-images].

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Per ottenere il nome loginServer, eseguire il comando [az acr show][az-acr-show]. Sostituire `<acrName>` con il nome del registro contenitori.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Output di esempio:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Applicare all'immagine *aci-tutorial-app* il tag loginServer del registro contenitori. Aggiungere anche `:v1` alla fine del nome dell'immagine. Questo tag indica il numero di versione dell'immagine. Sostituire `<acrLoginServer>` con il risultato del comando [az acr show][az-acr-show] appena eseguito.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Una volta applicato il tag, eseguire `docker images` per verificare l'operazione.

```bash
docker images
```

Output:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Eseguire il push dell'immagine in Registro contenitori di Azure

Eseguire il push dell'immagine *aci-tutorial-app* nel registro con il comando [docker push][docker-push]. Sostituire `<acrLoginServer>` con il nome del server di accesso completo ottenuto nel passaggio precedente.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

L'operazione `push` dovrebbe richiedere da alcuni secondi a qualche minuto a seconda della connessione Internet e l'output è simile al seguente:

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Elencare le immagini in Registro contenitori di Azure

Per restituire un elenco di immagini di cui è stato eseguito il push nel Registro contenitori di Azure, usare il comando [az acr repository list][az-acr-repository-list]. Aggiornare il comando con il nome del registro contenitori.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
aci-tutorial-app
```

Per visualizzare i tag per un'immagine specifica, usare il comando [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Output:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata preparata un'istanza del Registro contenitori di Azure per l'uso con Istanze di contenitore di Azure ed è stato eseguito il push di un'immagine del contenitore nel registro. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione di un'istanza di Registro contenitori di Azure
> * Assegnazione di un tag all'immagine del contenitore per il Registro contenitori di Azure
> * Caricamento di un'immagine nel Registro contenitori di Azure

Passare alla prossima esercitazione per informazioni sulla distribuzione del contenitore in Azure con Istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Distribuire contenitori in Istanze di contenitore di Azure](./container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
