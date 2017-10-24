---
title: Avvio rapido - Creare il primo contenitore di 	Istanze di contenitore di Azure
description: Distribuire e iniziare a usare Istanze di contenitore di Azure
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0aaf199d309be289b215a75bb4c222e76e1fb1b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Creare il primo contenitore in Istanze di contenitore di Azure

Istanze di contenitore di Azure semplifica la creazione e gestione di contenitori Docker in Azure, senza dover eseguire il provisioning di macchine virtuali o di adottare un servizio di livello superiore. In questo avvio rapido si crea un contenitore in Azure e lo si espone a Internet con un indirizzo IP pubblico. Per completare questa operazione, è sufficiente un solo comando. In pochi secondi, nel browser verrà visualizzato quanto segue:

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][aci-app-browser]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.12 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Le istanze di contenitore di Azure sono risorse di Azure e devono essere inserite in un gruppo di risorse di Azure, una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create][az-group-create].

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Creare un contenitore

Può essere creato un contenitore specificando un nome, un'immagine Docker e un gruppo di risorse di Azure al comando [az container create][az-container-create]. È facoltativamente possibile esporre il contenitore a Internet con un indirizzo IP pubblico. In questo caso, verrà usato un contenitore che ospita un'app Web molto semplice scritta in [Node.js](http://nodejs.org).

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public
```

In pochi secondi, verrà visualizzata una risposta alla richiesta. Il contenitore inizialmente presenterà lo stato **Creating**, ma verrà avviato entro alcuni secondi. È possibile controllare lo stato usando il comando [az container show][az-container-show]:

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

Dopo che lo stato del contenitore è diventato **Completato**, è possibile raggiungerlo nel browser usando l'indirizzo IP fornito.

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][aci-app-browser]

## <a name="pull-the-container-logs"></a>Effettuare il pull dei log del contenitore

È possibile effettuare il pull dei log per il contenitore creato usando il comando [az container logs][az-container-logs]:

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

Quando il contenitore non è più necessario, è possibile rimuoverlo usando il comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Tutto il codice per il contenitore usato in questo avvio rapido è disponibile [in GitHub][app-github-repo], con il relativo documento Dockerfile. Per provare a compilarlo personalmente e a distribuirlo in Istanze di contenitore di Azure usando il Registro contenitori di Azure, passare all'esercitazione su Istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazioni su Istanze di contenitore di Azure](./container-instances-tutorial-prepare-app.md)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show


<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png