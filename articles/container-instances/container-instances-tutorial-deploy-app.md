---
title: Esercitazione di Istanze di contenitore di Azure - Distribuire un'app
description: Esercitazione di Istanze di contenitore di Azure - Parte 3 di 3 - Distribuire un'applicazione
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 471caa1b24dc7017c70782c072b2068f9635244b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Distribuire un contenitore in Istanze di contenitore di Azure

Questa è l'ultima esercitazione di una serie in tre parti. Nelle sezioni precedenti, [un'immagine del contenitore è stata creata](container-instances-tutorial-prepare-app.md) e [inserita in un'istanza di Registro contenitori di Azure](container-instances-tutorial-prepare-acr.md). Questo articolo completa la serie di esercitazioni distribuendo il contenitore in Istanze di contenitore di Azure.

In questa esercitazione:

> [!div class="checklist"]
> * Distribuzione del contenitore da Registro contenitori di Azure tramite l'interfaccia della riga di comando di Azure
> * Visualizzazione dell'applicazione nel browser
> * Visualizzazione dei log del contenitore

## <a name="before-you-begin"></a>Prima di iniziare

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.23 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-install].

Per completare questa esercitazione è necessario un ambiente di sviluppo Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. È necessario installare l'interfaccia della riga di comando di Azure e l'ambiente di sviluppo Docker nel computer locale per completare questa esercitazione.

## <a name="deploy-the-container-using-the-azure-cli"></a>Distribuire il contenitore tramite l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure consente di distribuire un contenitore in Istanze di contenitore di Azure con un unico comando. L'immagine del contenitore è ospitata nell'istanza privata di Registro contenitori di Azure, quindi è necessario includere le credenziali necessarie per l'accesso. Ottenere le credenziali con i comandi seguenti dell'interfaccia della riga di comando di Azure.

Server di accesso del registro contenitori (sostituire con il nome del registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

Password del registro contenitori:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Per distribuire l'immagine del contenitore dal registro contenitori con una richiesta di risorse di 1 core CPU e 1 GB di memoria, eseguire il comando seguente: Sostituire `<acrLoginServer>` e `<acrPassword>` con i valori ottenuti dai due comandi precedenti.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public --ports 80
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure Resource Manager. Per visualizzare lo stato della distribuzione, usare il comando [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Ripetere il comando [az container show][az-container-show] fino a quando lo stato cambia da *In sospeso* a *In esecuzione*, operazione che dovrebbe richiedere meno di un minuto. Quando il contenitore è *In esecuzione*, procedere al passaggio successivo.

## <a name="view-the-application-and-container-logs"></a>Visualizzare l'applicazione e i log dei contenitori

Quando la distribuzione ha esito positivo, visualizzare l'indirizzo IP pubblico del contenitore con il comando [az container show][az-container-show]:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.ip
```

Output di esempio: `"13.88.176.27"`

Per visualizzare l'applicazione in esecuzione, esplorare l'indirizzo IP pubblico nel browser preferito.

![App Hello World nel browser][aci-app-browser]

È anche possibile visualizzare l'output del log del contenitore:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Output:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se non è più necessaria alcuna delle risorse create in questa serie di esercitazioni, è possibile eseguire il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Questo comando elimina il registro del contenitore creato, nonché il contenitore in esecuzione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati distribuiti contenitori in Istanze di contenitore di Azure. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Il contenitore da Registro contenitori di Azure è stato distribuito tramite l'interfaccia della riga di comando di Azure
> * L'applicazione è stata visualizzata nel browser
> * I log del contenitore sono stati visualizzati

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md