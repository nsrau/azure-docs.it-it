---
title: Esercitazione di Istanze di contenitore di Azure - Distribuire un'app
description: 'Esercitazione per istanze di contenitori di Azure parte 3 di 3: distribuire l''applicazione'
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Distribuire un contenitore in Istanze di contenitore di Azure

Si tratta dell'esercitazione finale in una serie di tre parti. In precedenza in serie, [è stata creata un'immagine contenitore](container-instances-tutorial-prepare-app.md) e [inserito un registro di sistema di contenitore di Azure](container-instances-tutorial-prepare-acr.md). In questo articolo viene completata la serie di esercitazioni distribuendo il contenitore di istanze di contenitori di Azure.

In questa esercitazione:

> [!div class="checklist"]
> * Distribuire il contenitore dal Registro di contenitore di Azure usando l'interfaccia CLI di Azure
> * Visualizzare l'applicazione nel browser
> * Visualizzare i log del contenitore

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione, è necessario che sia in esecuzione l'interfaccia CLI di Azure versione 2.0.23 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare Azure CLI 2.0][azure-cli-install].

Per completare questa esercitazione, è necessario un ambiente di sviluppo Docker installato localmente. Docker offre pacchetti che consente di configurare facilmente Docker in qualsiasi [Mac][docker-mac], [Windows][docker-windows], o [Linux] [ docker-linux] sistema.

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. Nel computer locale per completare questa esercitazione, è necessario installare l'ambiente di sviluppo CLI di Azure e Docker.

## <a name="deploy-the-container-using-the-azure-cli"></a>Distribuire il contenitore tramite l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure consente di distribuire un contenitore in Istanze di contenitore di Azure con un unico comando. L'immagine del contenitore è ospitata nell'istanza privata di Registro contenitori di Azure, quindi è necessario includere le credenziali necessarie per l'accesso. Ottenere le credenziali con i seguenti comandi CLI di Azure.

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

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure Resource Manager. Per visualizzare lo stato della distribuzione, utilizzare [Mostra contenitore az][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Ripetere il [Mostra contenitore az] [ az-container-show] comando fino a quando lo stato cambia da *in sospeso* per *esecuzione*, l'operazione dovrebbe richiedere meno di un minuto. Quando il contenitore è *In esecuzione*, procedere al passaggio successivo.

## <a name="view-the-application-and-container-logs"></a>Visualizzare l'applicazione e i log dei contenitori

Dopo la distribuzione ha esito positivo, visualizzare l'indirizzo IP pubblico del contenitore con il [Mostra contenitore az] [ az-container-show] comando:

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

Se non è più necessario tutte le risorse a cui è stato creato in questa serie di esercitazioni, è possibile eseguire il [eliminazione gruppo az] [ az-group-delete] comando per rimuovere il gruppo di risorse e tutte le risorse che contiene. Questo comando elimina il registro del contenitore creato, nonché il contenitore in esecuzione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati distribuiti contenitori in Istanze di contenitore di Azure. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuire il contenitore dal Registro di contenitore di Azure usando l'interfaccia CLI di Azure
> * Visualizzare l'applicazione nel browser
> * Visualizzare i log del contenitore

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