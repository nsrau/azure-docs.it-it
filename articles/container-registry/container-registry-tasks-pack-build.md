---
title: Creare un'immagine di Container Registry di Azure da un'app
description: Usare il comando AZ ACR Pack Build per compilare un'immagine del contenitore da un'app e eseguire il push in Azure Container Registry, senza usare un Dockerfile.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/06/2019
ms.author: danlep
ms.openlocfilehash: 4e41bcaff8faef2c4eaec9ae852955d4b7ce354b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839901"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Compilare ed eseguire il push di un'immagine da un'app usando una Buildpack nativa cloud

Il comando dell'interfaccia `az acr pack build` della riga [`pack`](https://github.com/buildpack/pack) di comando di Azure usa lo strumento CLI, da [Buildpacks](https://buildpacks.io/), per compilare un'app ed effettuare il push dell'immagine in un registro contenitori di Azure. Questa funzionalità offre un'opzione per creare rapidamente un'immagine del contenitore dal codice sorgente dell'applicazione in node. js, Java e altri linguaggi senza dover definire un Dockerfile.

Per eseguire gli esempi in questo articolo, è possibile usare l'Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si vuole usarlo localmente, è richiesta la versione 2.0.70 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="use-the-build-command"></a>Usare il comando Compila

Per compilare ed eseguire il push di un'immagine del contenitore usando Buildpacks nativo del cloud, eseguire il comando [AZ ACR Pack Build][az-acr-pack-build] . Mentre il comando [AZ ACR Build][az-acr-build] compila ed effettua il push di un'immagine da un'origine Dockerfile e dal codice `az acr pack build` correlato, con si specifica direttamente un albero di origine dell'applicazione.

Specificare come minimo quanto segue quando si esegue `az acr pack build`:

* Registro contenitori di Azure in cui si esegue il comando
* Nome dell'immagine e tag per l'immagine risultante
* Uno dei [percorsi di contesto supportati](container-registry-tasks-overview.md#quick-task) per le attività ACR, ad esempio una directory locale, un repository GitHub o un archivio remoto
* Nome di un'immagine del compilatore Buildpack, ad esempio `cloudfoundry/cnb:bionic`.  

`az acr pack build`supporta altre funzionalità dei comandi delle attività ACR, incluse le [variabili Run](container-registry-tasks-reference-yaml.md#run-variables) e i [log di esecuzione delle attività](container-registry-tasks-overview.md#view-task-logs) , che vengono trasmessi e salvati per il recupero successivo.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Esempio: Creare un'immagine node. js con Cloud Foundry Builder

Nell'esempio seguente viene compilata un'immagine del contenitore dall'app node. js nel repository [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) usando il `cloudfoundry/cnb:bionic` generatore:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --pull --builder cloudfoundry/cnb:bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

In questo esempio l' `node-app` immagine viene compilata con il `1.0` tag e ne viene creato il push nel registro contenitori registro di *sistema* . In questo caso, il nome del registro di sistema di destinazione viene anteposto in modo esplicito al nome dell'immagine. Se non specificato, l'URL del registro di sistema viene anteposto automaticamente al nome dell'immagine.

Il `--pull` parametro specifica che il comando esegue il pull dell'immagine del compilatore più recente.

L'output del comando Mostra lo stato di avanzamento della compilazione e del push dell'immagine. 

Dopo che l'immagine è stata compilata correttamente, è possibile eseguirla con Docker, se è installata. Accedere prima al registro di sistema:

```azurecli
az acr login --name myregistry
```

Eseguire l'immagine:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Passare a `localhost:1337` nel browser preferito per visualizzare l'app Web di esempio. Premere `[Ctrl]+[C]` per arrestare il contenitore.

## <a name="example-build-java-image-with-heroku-builder"></a>Esempio: Creare un'immagine Java con il generatore Heroku

Nell'esempio seguente viene compilata un'immagine del contenitore dall'app Java nel repository [buildpack/Sample-Java-app](https://github.com/buildpack/sample-java-app) , `heroku/buildpacks:18` usando il generatore:

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

In questo esempio viene `java-app` compilata l'immagine contrassegnata con l'ID esecuzione del comando e ne viene eseguito il push nel registro contenitori *Registro di sistema* .

Il `--pull` parametro specifica che il comando esegue il pull dell'immagine del compilatore più recente.

L'output del comando Mostra lo stato di avanzamento della compilazione e del push dell'immagine. 

Dopo che l'immagine è stata compilata correttamente, è possibile eseguirla con Docker, se è installata. Accedere prima al registro di sistema:

```azurecli
az acr login --name myregistry
```

Eseguire l'immagine, sostituendo il tag immagine per *RunId*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Passare a `localhost:8080` nel browser preferito per visualizzare l'app Web di esempio. Premere `[Ctrl]+[C]` per arrestare il contenitore.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato ed eseguito il push di un' `az acr pack build`immagine del contenitore con, è possibile distribuirla come qualsiasi immagine a una destinazione di propria scelta. Le opzioni di distribuzione di Azure includono l'esecuzione nel servizio [app](../app-service/containers/tutorial-custom-docker-image.md) o nel [servizio Azure Kubernetes](../aks/tutorial-kubernetes-deploy-cluster.md), tra gli altri.

Per altre informazioni sulle funzionalità delle attività di ACR, vedere automatizzare le compilazioni di [Immagini del contenitore e la manutenzione con le attività di ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
