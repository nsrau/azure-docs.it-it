---
title: Effettuare il push di un'immagine Docker in un registro di Azure privato
description: Effettuare il push e il pull di immagini Docker in un registro di contenitori privati in Azure tramite l'interfaccia della riga di comando di Docker
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21d1abfbb49eaeae654a600d35ab350b96a12fd3
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Effettuare il push della prima immagine in un registro per contenitori Docker privati tramite l'interfaccia della riga di comando di Docker

Un registro di contenitori di Azure archivia e gestisce le immagini dei contenitori [Docker](http://hub.docker.com) private, in modo analogo a come [Docker Hub](https://hub.docker.com/) archivia le immagini Docker pubbliche. È possibile usare l'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/) per eseguire l'[accesso](https://docs.docker.com/engine/reference/commandline/login/), il [push](https://docs.docker.com/engine/reference/commandline/push/), il [pull](https://docs.docker.com/engine/reference/commandline/pull/) e altre operazioni sul registro di contenitori.

La procedura seguente esegue il download di un'[immagine di Nginx](https://store.docker.com/images/nginx) ufficiale dal registro pubblico di Docker Hub, la contrassegna per il registro di contenitori di Azure privato, ne effettua il push nel registro e quindi ne effettua il pull dal registro.

## <a name="prerequisites"></a>Prerequisiti

* **Registro di contenitori di Azure**: creare un registro di contenitori nella sottoscrizione di Azure. Ad esempio, usare il [Portale di Azure](container-registry-get-started-portal.md) o l'[interfaccia della riga di comando di Azure 2.0](container-registry-get-started-azure-cli.md).
* **Interfaccia della riga di comando di Docker**: per configurare il computer locale come host Docker e accedere ai comandi della riga di comando di Docker, installare [Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Accedere a un registro

Esistono [diversi modi per eseguire l'autenticazione](container-registry-authentication.md) nel registro contenitori privato. È il metodo consigliato quando si usa una riga di comando è rappresentato dal comando dell'interfaccia della riga di comando di Azure [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login). Ad esempio, per accedere a un registro denominato *myregistry*:

```azurecli
az acr login --name myregistry
```

È anche possibile eseguire l'accesso con il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). L'esempio seguente passa l'ID e la password di un'[entità servizio](../active-directory/active-directory-application-objects.md) di Azure Active Directory. Ad esempio, è possibile che sia stata [assegnata un'entità servizio](container-registry-authentication.md#service-principal) al registro per uno scenario di automazione.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Entrambi i comandi restituiscono `Login Succeeded` una volta completati. Se viene usato il comando `docker login`, potrebbe venire visualizzato anche un avviso di sicurezza in cui si consiglia l'uso del parametro `--password-stdin`. Sebbene il suo utilizzo non rientri nell'ambito di questo articolo, si raccomanda di seguire questa procedura consigliata. Per altre informazioni, vedere la guida comandi di [accesso di Docker](https://docs.docker.com/engine/reference/commandline/login/).

> [!TIP]
> Specificare sempre il nome completo (tutto in maiuscolo) del registro quando si usa `docker login` e quando le immagini vengono contrassegnate per l'esecuzione del push nel registro. Negli esempi riportati in questo articolo il nome completo è *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Eseguire il pull dell'immagine di Nginx ufficiale

Eseguire prima il pull dell'immagine pubblica di Nginx nel computer locale.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Eseguire il contenitore in locale

Eseguire quindi il comando [docker run](https://docs.docker.com/engine/reference/run/) per avviare l'istanza locale del contenitore Nginx (`-it`) in modalità interattiva sulla porta 8080. L'argomento `--rm` specifica che il contenitore deve essere rimosso quando si arresta.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Passare a [http://localhost:8080](http://localhost:8080) per visualizzare la pagina Web predefinita servita da Nginx nel contenitore in esecuzione. Verrà visualizzata una pagina simile alla seguente:

![Nginx sul computer locale](./media/container-registry-get-started-docker-cli/nginx.png)

Poiché il contenitore è stato avviato in modalità interattiva con `-it`, è possibile visualizzare l'output del server Nginx nella riga di comando nel browser in uso.

Per arrestare e rimuovere il contenitore, premere `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Creare un alias dell'immagine

Usare [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) per creare un alias dell'immagine, con un percorso completo del registro. Questo esempio specifica lo spazio dei nomi `samples` per evitare confusione nella radice del registro.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Per altre informazioni sull'assegnazione di tag con spazi dei nomi, vedere la sezione [Spazi dei nomi dell'archivio](container-registry-best-practices.md#repository-namespaces) nell'argomento [Procedure consigliate per il Registro contenitori di Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Eseguire il push dell'immagine nel registro

Dopo aver contrassegnato l'immagine con il percorso completo del registro privato, è possibile eseguirne il push nel registro con [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Eseguire il pull dell'immagine dal registro

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per eseguire il pull dell'immagine dal registro:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Avviare il contenitore Nginx

Usare il comando [docker run](https://docs.docker.com/engine/reference/run/) per eseguire l'immagine di cui è stato eseguito il pull dal registro:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Passare a [http://localhost:8080/](http://localhost:8080) per visualizzare il contenitore in esecuzione.

Per arrestare e rimuovere il contenitore, premere `Control`+`C`.

## <a name="remove-the-image-optional"></a>Rimuovere l'immagine (facoltativo)

Se l'immagine di Nginx non è più necessaria, è possibile eliminarla in locale con il comando [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Per rimuovere le immagini dal Registro contenitori di Azure, è possibile usare il comando dell'interfaccia della riga di comando di Azure [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete). Ad esempio, il comando seguente elimina il manifesto a cui un tag fa riferimento, i dati livello associati e tutti gli altri tag che fanno riferimento al manifesto.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Passaggi successivi

Una volta apprese le nozioni di base, si è pronti per iniziare a usare il registro. Ad esempio, distribuire le immagini del contenitore dal registro in un cluster del [servizio contenitore di Azure](../aks/tutorial-kubernetes-prepare-app.md).
