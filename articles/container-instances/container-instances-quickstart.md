---
title: Creare il primo contenitore di Istanze di contenitore di Azure | Azure Docs
description: Distribuire e iniziare a usare Istanze di contenitore di Azure
services: container-service
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 933299ce5a5d6f5b2262d40ae768019ccaf8796a
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Creare il primo contenitore in Istanze di contenitore di Azure

Istanze di contenitore di Azure semplifica la creazione e la gestione dei contenitori in Azure. In questa guida introduttiva si creerà un contenitore in Azure e lo si esporrà a Internet con un indirizzo IP pubblico. Per completare questa operazione, è sufficiente un solo comando. In pochi secondi, nel browser verrà visualizzato quanto segue:

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][aci-app-browser]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

I comandi dell'interfaccia della riga di comando di Istanze di contenitore di Azure attualmente sono disponibili solo in Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Le istanze di contenitore di Azure sono risorse di Azure e devono essere inserite in un gruppo di risorse di Azure, una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Creare un contenitore

Per creare un contenitore, specificare un nome, un'immagine Docker e un gruppo di risorse di Azure. È facoltativamente possibile esporre il contenitore a Internet con un indirizzo IP pubblico. In questo caso, verrà usato un contenitore che ospita un'app Web molto semplice scritta in [Node.js](http://nodejs.org).

I comandi dell'interfaccia della riga di comando di Istanze di contenitore di Azure attualmente sono disponibili solo in Azure Cloud Shell.

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public 
```

In pochi secondi, verrà visualizzata una risposta alla richiesta. Il contenitore inizialmente presenterà lo stato **Creating**, ma verrà avviato entro alcuni secondi. È possibile controllare lo stato usando il comando `show`:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

Nella parte inferiore dell'output verranno visualizzati lo stato del provisioning e l'indirizzo IP del contenitore:

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Dopo che lo stato del contenitore è diventato **Succeeded**, è possibile raggiungerlo nel browser usando l'indirizzo IP fornito. 

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][aci-app-browser]

## <a name="pull-the-container-logs"></a>Effettuare il pull dei log del contenitore

È possibile effettuare il pull dei log per il contenitore creato usando il comando `logs`:

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

Output:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>Eliminare il contenitore

Quando il contenitore non è più necessario, è possibile rimuoverlo usando il comando `delete`:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Tutto il codice per il contenitore usato in questa guida introduttiva è disponibile [su GitHub][app-github-repo], con il documento Dockerfile. Per provare a compilarlo personalmente e a distribuirlo in Istanze di contenitore di Azure usando il Registro contenitori di Azure, passare all'esercitazione su Istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazioni su Istanze di contenitore di Azure](./container-instances-tutorial-prepare-app.md)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
