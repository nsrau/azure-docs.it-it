---
title: Esercitazione di Istanze di contenitore di Azure - Distribuire un'app
description: Esercitazione di Istanze di contenitore di Azure - Distribuire un'app
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 3b651526f5ee3197e7d04accb6a87e2f10bf0791
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Distribuire un contenitore in Istanze di contenitore di Azure

Questa è l'ultima di un'esercitazione in tre parti. Nelle sezioni precedenti, [un'immagine del contenitore è stata creata](container-instances-tutorial-prepare-app.md) e [inserita in un'istanza di Registro contenitori di Azure](container-instances-tutorial-prepare-acr.md). Questa sezione completa l'esercitazione distribuendo il contenitore in Istanze di contenitore di Azure. I passaggi completati comprendono:

> [!div class="checklist"]
> * Distribuzione del contenitore da Registro contenitori di Azure tramite l'interfaccia della riga di comando di Azure
> * Visualizzazione dell'applicazione nel browser
> * Visualizzare i log dei contenitori

## <a name="before-you-begin"></a>Prima di iniziare

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.20 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

Per completare questa esercitazione è necessario un ambiente di sviluppo Docker. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. È quindi consigliabile un'installazione locale dell'ambiente di sviluppo dell'interfaccia della riga di comando di Azure e di Docker.

## <a name="deploy-the-container-using-the-azure-cli"></a>Distribuire il contenitore tramite l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure consente di distribuire un contenitore in Istanze di contenitore di Azure con un unico comando. L'immagine del contenitore è ospitata nell'istanza privata di Registro contenitori di Azure, quindi è necessario includere le credenziali necessarie per l'accesso. Se necessario, è possibile trovarle con una query come illustrato di seguito.

Server di accesso del registro contenitori (sostituire con il nome del registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

Password del registro contenitori:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Per distribuire l'immagine del contenitore dal registro contenitori con una richiesta di risorse di 1 core CPU e 1 GB di memoria, eseguire il comando seguente:

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure Resource Manager. Per visualizzare lo stato della distribuzione, usare:

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query state
```

È possibile continuare a eseguire questo comando finché lo stato non passa da *pending* a *running*. Sarà quindi possibile procedere.

## <a name="view-the-application-and-container-logs"></a>Visualizzare l'applicazione e i log dei contenitori

Dopo avere completato la distribuzione, aprire il browser all'indirizzo IP indicato nell'output del comando seguente:

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

```json
"13.88.176.27"
```

![App Hello World nel browser][aci-app-browser]

È anche possibile visualizzare l'output del log del contenitore:

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

Output:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati distribuiti contenitori in Istanze di contenitore di Azure. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione del contenitore da Registro contenitori di Azure tramite l'interfaccia della riga di comando di Azure
> * Visualizzazione dell'applicazione nel browser
> * Visualizzare i log dei contenitori

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
