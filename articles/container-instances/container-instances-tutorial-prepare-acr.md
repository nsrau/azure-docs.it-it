---
title: Esercitazione di Istanze di contenitore di Azure - Preparare Registro contenitori di Azure | Microsoft Docs
description: Esercitazione di Istanze di contenitore di Azure - Preparare Registro contenitori di Azure
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, Micro-Service, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 7ac85bffb9593923808c77f2240e6f0e841e74cd
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Distribuire e usare il Registro contenitori di Azure

Questa è la parte due di un'esercitazione in tre parti. Nel [passaggio precedente](./container-instances-tutorial-prepare-app.md) è stata creta un'immagine del contenitore per una semplice applicazione Web scritta in [Node.js](http://nodejs.org). In questa esercitazione viene eseguito il push di questa immagine in un'istanza di Registro contenitori di Azure. Se l'immagine del contenitore non è stata creata, tornare all'[Esercitazione 1 - Creare l'immagine del contenitore](./container-instances-tutorial-prepare-app.md). 

Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questa esercitazione illustra la distribuzione di un'istanza di Registro contenitori di Azure e il push di un'immagine del contenitore in essa. I passaggi completati comprendono:

> [!div class="checklist"]
> * Distribuzione di un'istanza del Registro contenitori di Azure
> * Assegnazione di un tag all'immagine del contenitore per Registro contenitori di Azure
> * Caricamento dell'immagine in Registro contenitori di Azure

Nelle esercitazioni successive si distribuirà il contenitore dal registro privato a Istanze di contenitore di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione richiede che sia in esecuzione l'interfaccia della riga di comando di Azure 2.0.12 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="deploy-azure-container-registry"></a>Distribuire il Registro contenitori di Azure

Prima di distribuire un Registro contenitori di Azure, è necessario che esista un gruppo di risorse. Un gruppo di risorse di Azure è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare un Registro contenitori di Azure con il comando [az acr create](/cli/azure/acr#create). Il nome di un registro contenitori **deve essere univoco**. Nell'esempio seguente si usa il nome *mycontainerregistry082*.

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Nella parte restante di questa esercitazione si usa `<acrname>` come segnaposto per il nome del registro contenitori scelto.

## <a name="container-registry-login"></a>Accesso al registro contenitori

È necessario accedere all'istanza del Registro contenitori di Azure prima di eseguire il push di immagini in essa. Usare il comando [az acr login](https://docs.microsoft.com/en-us/cli/azure/acr#az_acr_login) per completare l'operazione. È necessario specificare il nome univoco assegnato al registro contenitori al momento della creazione.

```azurecli
az acr login --name <acrName>
```

Al termine, il comando restituisce un messaggio di accesso riuscito.

## <a name="tag-container-image"></a>Assegnare tag all'immagine del contenitore

Per distribuire un'immagine del contenitore da un registro privato, all'immagine deve essere assegnato un tag con il nome `loginServer` del registro.

Per visualizzare un elenco di immagini correnti, usare il comando `docker images`.

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Per ottenere il nome loginServer, eseguire questo comando.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Applicare all'immagine *aci-tutorial-app* il tag del server di accesso del registro contenitori. Aggiungere anche `:v1` alla fine del nome dell'immagine. Questo tag indica il numero di versione dell'immagine.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Una volta applicato il tag, eseguire `docker images` per verificare l'operazione.

```bash
docker images
```

Output:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Eseguire il push dell'immagine in Registro contenitori di Azure

Eseguire il push dell'immagine *aci-tutorial-app* nel registro.

Usando l'esempio seguente, sostituire il nome del server di accesso del registro contenitori con il nome del server di accesso dell'ambiente in uso.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

## <a name="list-images-in-azure-container-registry"></a>Elencare le immagini in Registro contenitori di Azure

Per restituire un elenco di immagini di cui è stato eseguito il push nel Registro contenitori di Azure, usare il comando [az acr repository list](/cli/azure/acr/repository#list). Aggiornare il comando con il nome del registro contenitori.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
aci-tutorial-app
```

Per visualizzare i tag per un'immagine specifica, usare il comando [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Output:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata preparata un'istanza di Registro contenitori di Azure da usare con Istanze di contenitore di Azure. È stato inoltre eseguito il push dell'immagine del contenitore. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione di un'istanza del Registro contenitori di Azure
> * Assegnazione di un tag all'immagine del contenitore per Registro contenitori di Azure
> * Caricamento dell'immagine in Registro contenitori di Azure

Passare alla prossima esercitazione per informazioni sulla distribuzione del contenitore in Azure con Istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Distribuire contenitori in Istanze di contenitore di Azure](./container-instances-tutorial-deploy-app.md)

