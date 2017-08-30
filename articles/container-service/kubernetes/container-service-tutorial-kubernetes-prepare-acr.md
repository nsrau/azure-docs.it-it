---
title: Esercitazione sul servizio contenitore di Azure - Preparare il Registro contenitori di Azure | Microsoft Docs
description: Esercitazione sul servizio contenitore di Azure - Preparare il Registro contenitori di Azure
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, Micro-Service, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 3e1f7617bf2fc52ee4c15598f51a46276f4dc57d
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Distribuire e usare il Registro contenitori di Azure

Il Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questa esercitazione, parte due di sette, illustra la distribuzione di un'istanza di Registro contenitori di Azure e il push di un'immagine del contenitore al suo interno. I passaggi completati comprendono:

> [!div class="checklist"]
> * Distribuzione di un'istanza di Registro contenitori di Azure
> * Assegnazione di tag a un'immagine del contenitore per Registro contenitori di Azure
> * Caricamento dell'immagine in Registro contenitori di Azure

Nelle esercitazioni successive, questa istanza del registro contenitori di Azure viene integrata con un cluster Kubernetes del servizio contenitore di Azure, per eseguire in modo sicuro le immagini del contenitore. 

## <a name="before-you-begin"></a>Prima di iniziare

Nell'[esercitazione precedente](./container-service-tutorial-kubernetes-prepare-app.md) è stata creta un'immagine del contenitore per una semplice applicazione Azure Voting. In questa esercitazione viene eseguito il push di questa immagine in un'istanza di Registro contenitori di Azure. Se l'immagine dell'app Azure Vote non è stata creata, tornare all'[Esercitazione 1 - Creare immagini del contenitore](./container-service-tutorial-kubernetes-prepare-app.md). In alternativa, la procedura illustrata di seguito funziona con qualsiasi immagine del contenitore.

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Distribuire il Registro contenitori di Azure

Prima di distribuire un Registro contenitori di Azure, è necessario che esista un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Creare un Registro contenitori di Azure con il comando [az acr create](/cli/azure/acr#create). Il nome di un registro contenitori **deve essere univoco**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Nella parte restante di questa esercitazione si usa "acrname" come segnaposto per il nome del registro contenitori scelto.

## <a name="container-registry-login"></a>Accesso al registro contenitori

È necessario accedere all'istanza del Registro contenitori di Azure prima di eseguire il push di immagini in essa. Usare il comando [az acr login](https://docs.microsoft.com/en-us/cli/azure/acr#login) per completare l'operazione. È necessario specificare il nome univoco assegnato al registro contenitori al momento della creazione.

```azurecli
az acr login --name <acrName>
```

Al termine, il comando restituisce un messaggio di accesso riuscito.

## <a name="tag-container-images"></a>Assegnare tag alle immagini del contenitore

Ogni immagine del contenitore deve essere contrassegnata con il nome del server di accesso del registro. Questo tag viene usato per il routing quando si esegue il push delle immagini del contenitore nel registro delle immagini.

Per visualizzare un elenco di immagini correnti, usare il comando [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Per ottenere il nome loginServer, eseguire questo comando.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Applicare ora all'immagine *azure-vote-front* il tag loginServer del registro contenitori. Aggiungere anche `:redis-v1` alla fine del nome dell'immagine. Questo tag indica la versione dell'immagine.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Dopo aver assegnato il tag, eseguire [docker images] (https://docs.docker.com/engine/reference/commandline/images/) per verificare l'operazione.

```bash
docker images
```

Output:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Eseguire il push delle immagini nel registro

Eseguire il push dell'immagine *azure-vote-front* nel registro. 

Nell'esempio seguente sostituire il nome del loginServer del Registro contenitori di Azure con il loginServer dell'ambiente in uso.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

Il completamento dell'operazione richiede alcuni minuti.

## <a name="list-images-in-registry"></a>Elencare le immagini nel registro

Per restituire un elenco di immagini di cui è stato eseguito il push nel Registro contenitori di Azure, usare il comando [az acr repository list](/cli/azure/acr/repository#list). Aggiornare il comando con il nome dell'istanza del Registro contenitori di Azure.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
azure-vote-front
```

Per visualizzare i tag per un'immagine specifica, usare il comando [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Output:

```azurecli
Result
--------
redis-v1
```

Al termine dell'esercitazione, l'immagine del contenitore sarà stata archiviata in un'istanza privata di Registro contenitori di Azure. Questa immagine verrà distribuita da Registro contenitori di Azure a un cluster Kubernetes nelle esercitazioni successive.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione un Registro contenitori di Azure è stato preparato per l'uso in un cluster Kubernetes del servizio contenitore di Azure. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione di un'istanza di Registro contenitori di Azure
> * Assegnazione di tag a un'immagine del contenitore per Registro contenitori di Azure
> * Caricamento dell'immagine in Registro contenitori di Azure

Passare all'esercitazione successiva per informazioni sulla distribuzione di un cluster Kubernetes in Azure.

> [!div class="nextstepaction"]
> [Distribuire un cluster Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md).
