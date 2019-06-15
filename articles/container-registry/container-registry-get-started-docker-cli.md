---
title: Effettuare il push di un'immagine Docker in un registro contenitori di Azure privato
description: Effettuare il push e il pull di immagini Docker in un registro contenitori privato in Azure tramite l'interfaccia della riga di comando di Docker
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 2cb401dfd68075ff0867ae3f89eee3474000b5de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60828759"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Effettuare il push della prima immagine in un registro contenitori Docker privato tramite l'interfaccia della riga di comando di Docker

Un registro di contenitori di Azure archivia e gestisce le immagini dei contenitori [Docker](https://hub.docker.com) private, in modo analogo a come [Docker Hub](https://hub.docker.com/) archivia le immagini Docker pubbliche. È possibile usare l'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/) per eseguire l'[accesso](https://docs.docker.com/engine/reference/commandline/login/), il [push](https://docs.docker.com/engine/reference/commandline/push/), il [pull](https://docs.docker.com/engine/reference/commandline/pull/) e altre operazioni sul registro contenitori.

La procedura seguente esegue il download di un'[immagine di Nginx](https://store.docker.com/images/nginx) ufficiale dal registro pubblico di Docker Hub, la contrassegna per il registro contenitori di Azure privato, ne effettua il push nel registro e quindi ne effettua il pull dal registro.

## <a name="prerequisites"></a>Prerequisiti

* **Registro Azure Container**: creare un registro di contenitori nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).
* **Interfaccia della riga di comando di Docker**: è anche necessario avere Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [macOS][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Accedere a un registro

Esistono [diversi modi per eseguire l'autenticazione](container-registry-authentication.md) nel registro contenitori privato. È il metodo consigliato quando si usa una riga di comando è rappresentato dal comando dell'interfaccia della riga di comando di Azure [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Ad esempio, per accedere a un registro denominato *myregistry*:

```azurecli
az acr login --name myregistry
```

È anche possibile eseguire l'accesso con il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Ad esempio, è possibile che sia stata [assegnata un'entità servizio](container-registry-authentication.md#service-principal) al registro per uno scenario di automazione. Quando si esegue il comando seguente, specificare in modo interattivo l'appID dell'entità servizio (nome utente) e la password quando richiesto. Per le procedure consigliate relative alla gestione delle credenziali di accesso, vedere le informazioni di riferimento sul comando [docker login](https://docs.docker.com/engine/reference/commandline/login/):

```
docker login myregistry.azurecr.io
```

Entrambi i comandi restituiscono `Login Succeeded` una volta completati.

> [!TIP]
> Specificare sempre il nome completo (tutto in maiuscolo) del registro quando si usa `docker login` e quando le immagini vengono contrassegnate per l'esecuzione del push nel registro. Negli esempi riportati in questo articolo il nome completo è *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Eseguire il pull dell'immagine di Nginx ufficiale

Eseguire prima il pull dell'immagine pubblica di Nginx nel computer locale.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Eseguire il contenitore in locale

Eseguire quindi il comando [docker run](https://docs.docker.com/engine/reference/run/) per avviare l'istanza locale del contenitore Nginx (`-it`) in modalità interattiva sulla porta 8080. L'argomento `--rm` specifica che il contenitore deve essere rimosso quando si arresta.

```
docker run -it --rm -p 8080:80 nginx
```

Passare a `http://localhost:8080` per visualizzare la pagina web predefinita servita da Nginx nel contenitore in esecuzione. Verrà visualizzata una pagina simile alla seguente:

![Nginx sul computer locale](./media/container-registry-get-started-docker-cli/nginx.png)

Poiché il contenitore è stato avviato in modalità interattiva con `-it`, è possibile visualizzare l'output del server Nginx nella riga di comando nel browser in uso.

Per arrestare e rimuovere il contenitore, premere `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Creare un alias dell'immagine

Usare [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) per creare un alias dell'immagine, con un percorso completo del registro. Questo esempio specifica lo spazio dei nomi `samples` per evitare confusione nella radice del registro.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Per altre informazioni sull'assegnazione di tag con spazi dei nomi, vedere la sezione [Spazi dei nomi dell'archivio](container-registry-best-practices.md#repository-namespaces) nell'argomento [Procedure consigliate per Registro Azure Container](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Eseguire il push dell'immagine nel registro

Dopo aver contrassegnato l'immagine con il percorso completo del registro privato, è possibile eseguirne il push nel registro con [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Eseguire il pull dell'immagine dal registro

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per eseguire il pull dell'immagine dal registro:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Avviare il contenitore Nginx

Usare il comando [docker run](https://docs.docker.com/engine/reference/run/) per eseguire l'immagine di cui è stato eseguito il pull dal registro:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Passare a `http://localhost:8080` per visualizzare il contenitore in esecuzione.

Per arrestare e rimuovere il contenitore, premere `Control`+`C`.

## <a name="remove-the-image-optional"></a>Rimuovere l'immagine (facoltativo)

Se l'immagine di Nginx non è più necessaria, è possibile eliminarla in locale con il comando [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Per rimuovere le immagini dal registro contenitori di Azure, è possibile usare il comando dell'interfaccia della riga di comando di Azure [az acr repository delete](/cli/azure/acr/repository#az-acr-repository-delete). Il comando seguente, ad esempio, elimina il manifesto a cui fa riferimento il tag `samples/nginx:latest`, tutti i dati di livello univoci e tutti gli altri tag che fanno riferimento al manifesto.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Passaggi successivi

Una volta apprese le nozioni di base, si è pronti per iniziare a usare il registro. È possibile, ad esempio, distribuire le immagini del contenitore dal registro nella posizione seguente:

* [Servizio Azure Kubernetes](../aks/tutorial-kubernetes-prepare-app.md)
* [Istanze di Azure Container](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Installare eventualmente l'[estensione Docker per Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e l'estensione [Account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) per l'uso dei registri contenitori di Azure. Eseguire il pull e il push delle immagini in un registro contenitori di Azure o eseguire Attività del Registro Azure Container, il tutto all'interno di Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
