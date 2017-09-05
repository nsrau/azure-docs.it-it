---
title: Creare un registro per contenitori Docker privati in Azure - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Introduzione alla creazione e gestione dei registri per contenitori Docker privati con l'interfaccia della riga di comando di Azure 2.0
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>Creare un registro contenitori gestito con l'interfaccia della riga di comando di Azure

Registro contenitori di Azure è un servizio gestito di registri contenitori Docker usato per l'archiviazione di immagini di un contenitore Docker privato. Questa guida descrive la creazione di un'istanza gestita di Registro contenitori di Azure con l'interfaccia della riga di comando di Azure.

I registri contenitori di Azure gestiti sono in anteprima e non disponibili in tutte le aree di Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *westcentralus*.

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

Creare un'istanza di Registro contenitori di Azure usando il comando [azure acr create](/cli/azure/acr#create).

> [!NOTE]
> Quando si crea un registro, specificare un nome di dominio univoco globale di primo livello, contenente solo lettere e numeri.

 Il nome del registro nell'esempio è *myContainerRegistry1*, ma è necessario sostituirlo con un nome univoco personalizzato.

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

Quando viene creato il registro, l'output è simile al seguente:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>Accedere all'istanza di Registro contenitori di Azure

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro contenitori di Azure. A tale scopo usare il comando [az acr login](/cli/azure/acr#login).

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

Al termine, il comando restituisce un messaggio di accesso riuscito.

## <a name="use-azure-container-registry"></a>Usare Registro contenitori di Azure

### <a name="list-container-images"></a>Elencare le immagini del contenitore

Usare i comandi dell'interfaccia della riga di comando `az acr` per eseguire query su immagini e tag in un repository.

> [!NOTE]
> Attualmente, il servizio Container Registry non supporta il comando `docker search` per eseguire query relative a immagini e tag.

### <a name="list-repositories"></a>Elencare repository

L'esempio seguente elenca i repository in un registro, in formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Elencare tag

L'esempio seguente elenca i tag sul repository **samples/nginx**, in formato JSON:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'istanza gestita di Registro contenitori di Azure con l'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md)

