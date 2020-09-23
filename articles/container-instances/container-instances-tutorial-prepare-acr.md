---
title: "Esercitazione: Preparare il registro contenitori per la distribuzione dell'immagine"
description: Esercitazione di Istanze di Azure Container - Parte 2 di 3 - Preparare un Registro Azure Container ed eseguire il push di un'immagine
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 44a7f21c067897b046413851ef5a2c73bfccc24f
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708037"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>Esercitazione: Creare un Registro Azure Container ed eseguire il push di un'immagine del contenitore

Questa è la parte due di un'esercitazione in tre parti. Nella [prima parte](container-instances-tutorial-prepare-app.md) dell'esercitazione è stata creata un'immagine del contenitore Docker per un'applicazione Web Node.js. In questa esercitazione si esegue il push dell'immagine in Registro Azure Container. Se l'immagine del contenitore non è ancora stata creata, tornare all'esercitazione [1: Creare un'immagine del contenitore](container-instances-tutorial-prepare-app.md).

Registro Azure Container è il registro Docker privato in Azure. In questa esercitazione, la seconda della serie, vengono illustrate le attività seguenti:

> [!div class="checklist"]
> * Creare un'istanza di Registro Azure Container usando l'interfaccia della riga di comando di Azure
> * Assegnare un tag all'immagine del contenitore per il Registro Azure Container
> * Caricare l'immagine nel registro

Nell'articolo successivo, che corrisponde all'ultimo della serie, il contenitore viene distribuito dal registro privato a Istanze di Azure Container.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>Assegnare tag all'immagine del contenitore

Per eseguire il push di un'immagine del contenitore in un registro privato come Registro Azure Container, è prima necessario assegnare all'immagine un tag con il nome completo del server di accesso del registro.

Per prima cosa, ottenere il nome completo del server di accesso dell'istanza di Registro Azure Container. Eseguire questo comando [az acr show][az-acr-show], sostituendo `<acrName>` con il nome del registro appena creato:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Se il nome del registro è *mycontainerregistry082*, ad esempio:

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
Result
------------------------
mycontainerregistry082.azurecr.io
```

Visualizzare quindi l'elenco delle immagini locali con il comando [docker images][docker-images]:

```bash
docker images
```

Insieme a tutte le altre immagini presenti nel computer, verrà visualizzata l'immagine *aci-tutorial-app* creata nell'[esercitazione precedente](container-instances-tutorial-prepare-app.md):

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Applicare all'immagine *aci-tutorial-app* il tag del server di accesso del registro contenitori. Aggiungere anche il tag `:v1` alla fine del nome dell'immagine per indicarne il numero di versione. Sostituire `<acrLoginServer>` con il risultato del comando [az acr show][az-acr-show] eseguito in precedenza.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Eseguire di nuovo `docker images` per verificare l'operazione di assegnazione di tag:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Eseguire il push dell'immagine in Registro Azure Container

Dopo aver assegnato all'immagine *aci-tutorial-app* il tag con il nome completo del server di accesso del registro privato, è possibile eseguire il push dell'immagine nel registro con il comando [docker push][docker-push]. Sostituire `<acrLoginServer>` con il nome completo del server di accesso ottenuto nel passaggio precedente.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

L'operazione `push` dovrebbe richiedere da alcuni secondi a qualche minuto a seconda della connessione Internet e l'output è simile al seguente:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Elencare le immagini in Registro Azure Container

Per verificare che l'immagine di cui è appena stato eseguito il push si trovi effettivamente nell'istanza di Registro Azure Container, visualizzare l'elenco delle immagini nel registro con il comando [az acr repository list][az-acr-repository-list]. Sostituire `<acrName>` con il nome del registro contenitori.

```azurecli
az acr repository list --name <acrName> --output table
```

Ad esempio:

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
Result
----------------
aci-tutorial-app
```

Per visualizzare i *tag* per un'immagine specifica, usare il comando [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

L'output dovrebbe essere simile al seguente:

```console
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata preparata un'istanza di Registro Azure Container da usare con Istanze di Azure Container ed è stato eseguito il push di un'immagine del contenitore nel registro. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Creazione di un'istanza di Registro Azure Container usando l'interfaccia della riga di comando di Azure
> * Assegnazione di un tag all'immagine del contenitore per Registro Azure Container
> * Caricamento di un'immagine in Registro Azure Container

Passare all'esercitazione successiva per apprendere come distribuire il contenitore in Azure usando Istanze di Azure Container:

> [!div class="nextstepaction"]
> [Distribuire un contenitore in Istanze di Azure Container](container-instances-tutorial-deploy-app.md)

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
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
