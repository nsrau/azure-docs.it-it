---
title: Guida introduttiva - Creare un registro Docker privato in Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come apprendere rapidamente a creare un registro Docker privato con l'interfaccia della riga di comando di Azure.
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b3fb9a3ea090f0083e8f113ddf13312fe42b59a
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Creare un registro di contenitori usando l'interfaccia della riga di comando di Azure

Registro contenitori di Azure è un servizio gestito di registri contenitori Docker usato per l'archiviazione di immagini di un contenitore Docker privato. Questa guida descrive la creazione di un'istanza di Registro contenitori di Azure con l'interfaccia della riga di comando di Azure.

Questa guida introduttiva richiede l'interfaccia della riga di comando di Azure 2.0.20 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

È anche necessario avere Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

In questa guida introduttiva viene creato un registro contenitori di *base*. Registro contenitori di Azure è disponibile in diversi SKU, descritti brevemente nella tabella riportata di seguito. Per altri dettagli su ogni SKU, vedere [SKU di Registro contenitori](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Creare un'istanza di Registro contenitori di Azure usando il comando [azure acr create](/cli/azure/acr#create).

Il nome del registro **deve essere univoco**. Nell'esempio seguente viene usato il nome *myContainerRegistry007*. Aggiornarlo a un valore univoco.

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
```

Quando viene creato il registro, l'output è simile al seguente:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Nella parte restante di questa guida introduttiva viene usato `<acrname>` come segnaposto per il nome del registro contenitori.

## <a name="log-in-to-acr"></a>Accedere al record di controllo di accesso

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro contenitori di Azure. A tale scopo usare il comando [az acr login](/cli/azure/acr#login).

```azurecli
az acr login --name <acrname>
```

Al termine, il comando restituisce un messaggio di accesso riuscito.

## <a name="push-image-to-acr"></a>Eseguire il push di un'immagine nel record di controllo di accesso

Per eseguire il push di un'immagine nel registro contenitori di Azure è necessario innanzitutto disporre di un'immagine. Se necessario, eseguire il comando seguente per eseguire il pull di un'immagine creata in precedenza dall'Hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

L'immagine deve essere contrassegnata con il nome del server di accesso del record di controllo di accesso. Eseguire il comando seguente per restituire il nome del server di accesso dell'istanza del record di controllo di accesso.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Contrassegnare l'immagine usando il comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Sostituire *<acrLoginServer>* con il nome del server di accesso dell'istanza del record di controllo di accesso.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Infine, usare [docker push](https://docs.docker.com/engine/reference/commandline/push/) per eseguire il push dell'immagine nell'istanza del record di controllo di accesso. Sostituire *<acrLoginServer>* con il nome del server di accesso dell'istanza del record di controllo di accesso.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Elencare le immagini del contenitore

L'esempio seguente elenca i repository in un registro:

```azurecli
az acr repository list -n <acrname> -o table
```

Output:

```bash
Result
----------------
aci-helloworld
```

L'esempio seguente elenca i tag nel repository **aci-helloworld**.

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

Output:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse, l'istanza del record di controllo di accesso e tutte le immagini del contenitore non sono più necessari è possibile usare il comando [az group delete](/cli/azure/group#delete) per rimuoverli.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un Registro contenitori di Azure con l'interfaccia della riga di comando di Azure. Se si desidera usare il Registro contenitori di Azure con le istanze di contenitore di Azure, continuare con l'esercitazione relativa alle istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di contenitore di Azure](../container-instances/container-instances-tutorial-prepare-app.md)