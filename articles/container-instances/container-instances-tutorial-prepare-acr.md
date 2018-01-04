---
title: Esercitazione di Istanze di contenitore di Azure - Preparare Registro contenitori di Azure
description: 'Azure istanze di contenitori esercitazione parte 2 di 3: preparare Registro di sistema contenitore di Azure'
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c0aad1f9bbaac9a456b34f75633faba92f57f498
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Distribuire e usare il Registro contenitori di Azure

Questa è la parte due di un'esercitazione in tre parti. Nel [passaggio precedente](container-instances-tutorial-prepare-app.md), un'immagine contenitore è stata creata per un'applicazione web semplice scritta in [Node.js][nodejs]. In questa esercitazione si esegue il push dell'immagine in un'istanza del Registro contenitori di Azure. Se l'immagine del contenitore non è stata creata, tornare all'[Esercitazione 1 - Creare l'immagine del contenitore](container-instances-tutorial-prepare-app.md).

Il Registro di sistema di contenitore di Azure è un registro di sistema basato su Azure, privata per le immagini contenitore Docker. In questa esercitazione è tramite la distribuzione di un'istanza del Registro di sistema di Azure contenitore e l'inserimento di un'immagine contenitore.

In questo articolo, parte 2 di serie, è:

> [!div class="checklist"]
> * Distribuire un'istanza del Registro di sistema di Azure contenitore
> * Tag di un'immagine del contenitore per il Registro di sistema del contenitore di Azure
> * Caricare l'immagine al Registro di sistema

Vedere l'articolo successivo dell'esercitazione finale della serie, distribuire il contenitore dal Registro di sistema privato per le istanze di contenitore di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione, è necessario che sia in esecuzione l'interfaccia CLI di Azure versione 2.0.23 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare Azure CLI 2.0][azure-cli-install].

Per completare questa esercitazione, è necessario un ambiente di sviluppo Docker installato localmente. Docker offre pacchetti che consente di configurare facilmente Docker in qualsiasi [Mac][docker-mac], [Windows][docker-windows], o [Linux] [ docker-linux] sistema.

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. Nel computer locale per completare questa esercitazione, è necessario installare l'ambiente di sviluppo CLI di Azure e Docker.

## <a name="deploy-azure-container-registry"></a>Distribuire il Registro contenitori di Azure

Prima di distribuire un Registro contenitori di Azure, è necessario che esista un gruppo di risorse. Un gruppo di risorse di Azure è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create][az-group-create]. In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare un registro di sistema di contenitore di Azure con il [az acr creare] [ az-acr-create] comando. Il nome del registro contenitori **deve essere univoco** in Azure e contenere da 5 a 50 caratteri alfanumerici. Sostituire `<acrName>` con un nome univoco per il registro:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Ad esempio, per creare un registro contenitori di Azure denominato *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Nella parte restante di questa esercitazione si usa `<acrName>` come segnaposto per il nome del registro contenitori scelto.

## <a name="container-registry-login"></a>Accesso al registro contenitori

È necessario accedere all'istanza del Registro di sistema di Azure contenitore prima dell'inserimento di immagini. Utilizzare il [accesso acr az] [ az-acr-login] comando per completare l'operazione. È necessario fornire il nome specificato per il Registro di sistema del contenitore, quando è stato creato.

```azurecli
az acr login --name <acrName>
```

Il comando restituisce un messaggio `Login Succeeded` al termine dell'esecuzione.

## <a name="tag-container-image"></a>Assegnare tag all'immagine del contenitore

Per distribuire un'immagine contenitore da un registro di sistema privato, è necessario contrassegnare l'immagine con il `loginServer` nome del Registro di sistema.

Per visualizzare un elenco di immagini corrente, utilizzare il [immagini docker] [ docker-images] comando.

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Per ottenere il nome loginServer, eseguire il [Mostra acr az] [ az-acr-show] comando. Sostituire `<acrName>` con il nome del registro contenitori.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Output di esempio:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Applicare all'immagine *aci-tutorial-app* il tag loginServer del registro contenitori. Aggiungere anche `:v1` alla fine del nome dell'immagine. Questo tag indica il numero di versione dell'immagine. Sostituire `<acrLoginServer>` con il risultato di [Mostra acr az] [ az-acr-show] comando appena eseguita.

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

Push di *aci-esercitazione-app* immagine al Registro di sistema con il [push di docker] [ docker-push] comando. Sostituire `<acrLoginServer>` con il nome del server di accesso completo ottenuto nel passaggio precedente.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

Il `push` operazione dovrebbe richiedere alcuni secondi per qualche minuto a seconda della connessione internet e l'output è simile al seguente:

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

Per restituire un elenco di immagini che sono stati inseriti nel Registro di sistema contenitore di Azure, utilizzare il [elenco repository di az acr] [ az-acr-repository-list] comando. Aggiornare il comando con il nome del registro contenitori.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
aci-tutorial-app
```

Per visualizzare i tag per un'immagine specifica, quindi utilizzare il [az acr repository Mostra-tag] [ az-acr-repository-show-tags] comando.

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

In questa esercitazione, preparato del Registro di sistema un contenitore di Azure per l'uso con istanze di contenitori di Azure e inserito un'immagine contenitore nel Registro di sistema. Sono stati completati i passaggi seguenti:

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
