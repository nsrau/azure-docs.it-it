---
title: Guida introduttiva - Creare un registro Docker privato in Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come apprendere rapidamente a creare un registro Docker privato con l'interfaccia della riga di comando di Azure.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f31f4e5e2b3fe5db85873894a7f2fa9c415392c1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Creare un registro di contenitori usando l'interfaccia della riga di comando di Azure

Registro contenitori di Azure è un servizio gestito di registri contenitori Docker usato per l'archiviazione di immagini di un contenitore Docker privato. Questa guida descrive la creazione di un'istanza di Registro contenitori di Azure con l'interfaccia della riga di comando di Azure.

Questa Guida rapida richiede che sia in esecuzione l'interfaccia CLI di Azure versione 2.0.21 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare Azure CLI 2.0][azure-cli].

È anche necessario avere Docker installato localmente. Docker offre pacchetti che consente di configurare facilmente Docker in qualsiasi [Mac][docker-mac], [Windows][docker-windows], o [Linux] [ docker-linux] sistema.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create][az-group-create]. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

In questa guida introduttiva viene creato un registro contenitori di *base*. Registro contenitori di Azure è disponibile in diversi SKU, descritti brevemente nella tabella riportata di seguito. Per maggiori dettagli su ognuna, vedere [Registro di sistema contenitore SKU][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Creare un'istanza di record con il [az acr creare] [ az-acr-create] comando.

Il nome del registro **deve essere univoco**. Nell'esempio seguente viene usato il nome *myContainerRegistry007*. Aggiornarlo a un valore univoco.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
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
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Nella parte restante di questa Guida introduttiva viene usato `<acrName>` come segnaposto per il nome del registro contenitori.

## <a name="log-in-to-acr"></a>Accedere al record di controllo di accesso

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro contenitori di Azure. A tale scopo, utilizzare il [accesso acr az] [ az-acr-login] comando.

```azurecli
az acr login --name <acrName>
```

Il comando restituisce un messaggio `Login Succeeded` al termine dell'esecuzione.

## <a name="push-image-to-acr"></a>Eseguire il push di un'immagine nel record di controllo di accesso

Per eseguire il push di un'immagine nel registro contenitori di Azure è necessario innanzitutto disporre di un'immagine. Se si dispone ancora di tutte le immagini contenitore locale, eseguire il comando seguente per inserire un'immagine esistente dall'Hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

Prima al Registro di sistema, è possibile distribuire un'immagine, è necessario contrassegnarlo con il nome completo del server di accesso di record. Eseguire il comando seguente per ottenere il nome del server di accesso completo dell'istanza del record.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Tag di immagine usando il [tag docker] [ docker-tag] comando. Sostituire `<acrLoginServer>` con il nome del server di accesso dell'istanza del record.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Infine, utilizzare [push di docker] [ docker-push] per effettuare il push dell'immagine per l'istanza del record. Sostituire `<acrLoginServer>` con il nome del server di accesso dell'istanza del record.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Elencare le immagini del contenitore

L'esempio seguente elenca i repository in un registro:

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```bash
Result
----------------
aci-helloworld
```

L'esempio seguente elenca i tag nel repository **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Output:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile utilizzare il [eliminazione gruppo az] [ az-group-delete] comando per rimuovere il gruppo di risorse, l'istanza di record e tutte le immagini contenitore.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida rapida è stato creato un Registro contenitori di Azure con l'interfaccia della riga di comando di Azure. Se si desidera usare il Registro contenitori di Azure con le istanze di contenitore di Azure, continuare con l'esercitazione relativa alle istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazione per istanze di contenitori di Azure][container-instances-tutorial-prepare-app]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md