---
title: Creare un'immagine di Container Registry di Azure da un'app
description: Usare il comando AZ ACR Pack Build per compilare un'immagine del contenitore da un'app e eseguire il push in Azure Container Registry, senza usare un Dockerfile.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/24/2019
ms.author: danlep
ms.openlocfilehash: 34ef0fe4be00cfa7ce3e73c23eec636784071e56
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965906"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Compilare ed eseguire il push di un'immagine da un'app usando una Buildpack nativa cloud

Il comando dell'interfaccia della riga di comando di Azure `az acr pack build` usa lo strumento dell'interfaccia della riga di comando di [`pack`](https://github.com/buildpack/pack) , da [Buildpacks](https://buildpacks.io/), per creare un'app e inserirla in un registro contenitori di Azure Questa funzionalità offre un'opzione per creare rapidamente un'immagine del contenitore dal codice sorgente dell'applicazione in node. js, Java e altri linguaggi senza dover definire un Dockerfile.

Per eseguire gli esempi in questo articolo, è possibile usare l'Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si vuole usarlo localmente, è richiesta la versione 2.0.70 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="use-the-build-command"></a>Usare il comando Compila

Per compilare ed eseguire il push di un'immagine del contenitore usando Buildpacks nativo del cloud, eseguire il comando [AZ ACR Pack Build][az-acr-pack-build] . Mentre il comando [AZ ACR Build][az-acr-build] compila ed esegue il push di un'immagine da un'origine Dockerfile e dal codice correlato, con `az acr pack build` si specifica direttamente un albero di origine dell'applicazione.

Specificare come minimo quanto segue quando si esegue `az acr pack build`:

* Registro contenitori di Azure in cui si esegue il comando
* Nome dell'immagine e tag per l'immagine risultante
* Uno dei [percorsi di contesto supportati](container-registry-tasks-overview.md#context-locations) per le attività ACR, ad esempio una directory locale, un repository GitHub o un archivio remoto
* Nome di un'immagine del compilatore Buildpack adatta per l'applicazione. Azure Container Registry memorizza nella cache le immagini di generatore, ad esempio `cloudfoundry/cnb:0.0.34-cflinuxfs3` per compilazioni più veloci.  

`az acr pack build` supporta altre funzionalità dei comandi delle attività ACR, incluse le [variabili Run](container-registry-tasks-reference-yaml.md#run-variables) e i [log di esecuzione delle attività](container-registry-tasks-overview.md#view-task-logs) , che vengono trasmessi e salvati per il recupero successivo.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Esempio: compilare un'immagine node. js con Cloud Foundry Builder

L'esempio seguente crea un'immagine del contenitore da un'app node. js nel repository [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) , usando il generatore di `cloudfoundry/cnb:0.0.34-cflinuxfs3`. Questo generatore viene memorizzato nella cache da Azure Container Registry, quindi non è necessario un parametro di `--pull`:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

In questo esempio viene compilata l'immagine di `node-app` con il tag `1.0` e ne viene creato il push nel registro contenitori registro di *sistema* . In questo esempio, il nome del registro di sistema di destinazione viene anteposto in modo esplicito al nome dell'immagine. Se non specificato, il nome del server di accesso del registro di sistema viene anteposto automaticamente al nome dell'immagine.

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

## <a name="example-build-java-image-with-heroku-builder"></a>Esempio: creare un'immagine Java con il generatore Heroku

L'esempio seguente crea un'immagine del contenitore dall'app Java nel repository [buildpack/Sample-Java-app](https://github.com/buildpack/sample-java-app) , usando il generatore di `heroku/buildpacks:18`. Il parametro `--pull` specifica che il comando deve effettuare il pull dell'immagine del compilatore più recente. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

In questo esempio viene compilata l'immagine del `java-app` contrassegnata con l'ID di esecuzione del comando e ne viene eseguito il push nel registro contenitori *Registro di sistema* .

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

Dopo aver compilato ed eseguito il push di un'immagine del contenitore con `az acr pack build`, è possibile distribuirla come qualsiasi immagine a una destinazione di propria scelta. Le opzioni di distribuzione di Azure includono l'esecuzione nel servizio [app](../app-service/containers/tutorial-custom-docker-image.md) o nel [servizio Azure Kubernetes](../aks/tutorial-kubernetes-deploy-cluster.md), tra gli altri.

Per altre informazioni sulle funzionalità delle attività di ACR, vedere [automatizzare le compilazioni di immagini del contenitore e la manutenzione con le attività di ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
