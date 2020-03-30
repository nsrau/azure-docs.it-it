---
title: Creare un'immagine con Cloud Native Buildpack
description: Usare il comando az acr pack build per compilare un'immagine del contenitore da un'app ed eseguire il push al Registro di sistema del contenitore di Azure, senza usare un dockerfile.Use the az acr pack build command to build a container image from an app and push to Azure Container Registry, without using a Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087084"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Creare ed eseguire il push di un'immagine da un'app usando un Buildpack nativo cloudBuild and push an image from an app using a Cloud Native Buildpack

Il comando `az acr pack build` dell'interfaccia della riga di comando di Azure usa lo [`pack`](https://github.com/buildpack/pack) strumento DELL'interfaccia della riga di comando, da Buildpacks , per compilare un'app ed eseguire il push dell'immagine in un Registro di sistema del contenitore di Azure.The Azure CLI command uses the CLI tool, from [Buildpacks](https://buildpacks.io/), to build an app and push its image into an Azure container registry. Questa funzionalità fornisce un'opzione per compilare rapidamente un'immagine contenitore dal codice sorgente dell'applicazione in Node.js, Java e altri linguaggi senza dover definire un Dockerfile.This feature provides an option to quickly build a container image from your application source code in Node.js, Java, and other languages without having to define a Dockerfile.

È possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per eseguire gli esempi in questo articolo. Se si desidera utilizzarlo in locale, è necessaria la versione 2.0.70 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli-install]you need to install or upgrade, see Install Azure CLI.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="use-the-build-command"></a>Usare il comando build

Per compilare ed eseguire il push di un'immagine contenitore usando Cloud Native Buildpacks, eseguire il comando [az acr pack build.][az-acr-pack-build] Mentre il comando [az acr build][az-acr-build] compila e invia un'immagine da un'origine Dockerfile e dal codice correlato, con `az acr pack build` l'utente specifica direttamente un albero di origine dell'applicazione.

Quando si esegue: `az acr pack build`

* Un registro contenitore di Azure in cui si esegue il comandoAn Azure container registry where you run the command
* Un nome di immagine e un tag per l'immagine risultante
* Uno dei percorsi di [contesto supportati](container-registry-tasks-overview.md#context-locations) per le attività ACR, ad esempio una directory locale, un repository GitHub o un tarball remoto
* Nome di un'immagine del generatore Buildpack adatta per l'applicazione. Il Registro di sistema `cloudfoundry/cnb:0.0.34-cflinuxfs3` contenitore di Azure memorizza nella cache le immagini del generatore, ad esempio per compilazioni più veloci.  

`az acr pack build`supporta altre funzionalità dei comandi Attività ACR, tra cui [le variabili](container-registry-tasks-reference-yaml.md#run-variables) di esecuzione e i registri di esecuzione [delle attività](container-registry-tasks-logs.md) che vengono trasmessi in streaming e salvati anche per un successivo recupero.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Esempio: Crea un'immagine Node.js con il generatore di cloud

L'esempio seguente crea un'immagine del contenitore da un'app Node.js nel repository [Azure-Samples/nodejs-docs-hello-world,](https://github.com/Azure-Samples/nodejs-docs-hello-world) usando il `cloudfoundry/cnb:0.0.34-cflinuxfs3` generatore. Questo generatore viene memorizzato nella `--pull` cache dal Registro di sistema del contenitore di Azure, pertanto non è necessario un parametro:This builder is cached by Azure Container Registry, so a parameter isn't required:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

In questo esempio `node-app` l'immagine viene compilata con il `1.0` tag e viene estratta nel Registro di sistema del contenitore *myregistry.* In questo esempio, il nome del Registro di sistema di destinazione viene anteposto in modo esplicito al nome dell'immagine. Se non specificato, il nome del server di accesso al Registro di sistema viene automaticamente anteposto al nome dell'immagine.

L'output del comando mostra lo stato di avanzamento della compilazione e del push dell'immagine. 

Dopo aver creato correttamente l'immagine, è possibile eseguirla con Docker, se è installata. Per prima cosa accedi al tuo Registro:

```azurecli
az acr login --name myregistry
```

Eseguire l'immagine:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Passare `localhost:1337` a nel browser preferito per visualizzare l'app Web di esempio. Premere `[Ctrl]+[C]` per arrestare il contenitore.

## <a name="example-build-java-image-with-heroku-builder"></a>Esempio: Crea un'immagine Java con il generatore Heroku

L'esempio seguente compila un'immagine contenitore dall'app Java nel repository [buildpack/sample-java-app,](https://github.com/buildpack/sample-java-app) usando il `heroku/buildpacks:18` generatore. Il `--pull` parametro specifica che il comando deve eseguire il pull dell'immagine del generatore più recente. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

In questo esempio `java-app` viene compilata l'immagine contrassegnata con l'ID di esecuzione del comando e viene esportata nel Registro di sistema del contenitore *myregistry.*

L'output del comando mostra lo stato di avanzamento della compilazione e del push dell'immagine. 

Dopo aver creato correttamente l'immagine, è possibile eseguirla con Docker, se è installata. Per prima cosa accedi al tuo Registro:

```azurecli
az acr login --name myregistry
```

Eseguire l'immagine, sostituendo il tag dell'immagine con *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Passare `localhost:8080` a nel browser preferito per visualizzare l'app Web di esempio. Premere `[Ctrl]+[C]` per arrestare il contenitore.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato e e `az acr pack build`eseguito il push di un'immagine contenitore con , è possibile distribuirla come qualsiasi immagine in una destinazione di propria scelta. Le opzioni di distribuzione di Azure includono l'esecuzione nel [servizio app](../app-service/containers/tutorial-custom-docker-image.md) o nel servizio [Azure Kubernetes,](../aks/tutorial-kubernetes-deploy-cluster.md)tra gli altri.

Per ulteriori informazioni sulle funzionalità attività ACR, vedere Automatizzare le compilazioni e la manutenzione delle [immagini del contenitore con le attività ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
