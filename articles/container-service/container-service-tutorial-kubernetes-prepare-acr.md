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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: adc00cb52d5805e152ca742f4cd6b9fab3ed3788
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Distribuire e usare il Registro contenitori di Azure

Il Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questa esercitazione illustra la distribuzione di un'istanza del Registro contenitori di Azure e il push di immagini del contenitore in essa. I passaggi completati comprendono:

> [!div class="checklist"]
> * Distribuzione di un'istanza del Registro contenitori di Azure
> * Applicazione di tag alle immagini del contenitore per il Registro contenitori di Azure
> * Caricamento di immagini nel Registro contenitori di Azure

Nelle esercitazioni successive, questa istanza del registro contenitori di Azure viene integrata con un cluster Kubernetes del servizio contenitore di Azure, per eseguire in modo sicuro le immagini del contenitore. 

## <a name="before-you-begin"></a>Prima di iniziare

Nell'[esercitazione precedente](./container-service-tutorial-kubernetes-prepare-app.md), le immagini del contenitore sono state create per una semplice applicazione Azure Voting. In questa esercitazione viene eseguito il push di queste immagini in un Registro contenitori di Azure. Se non sono state create le immagini dell'app Azure Voting, tornare al [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore). In alternativa, la procedura illustrata di seguito funziona con qualsiasi immagine del contenitore.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Distribuire il Registro contenitori di Azure

Prima di distribuire un Registro contenitori di Azure, è necessario che esista un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Creare un Registro contenitori di Azure con il comando [az acr create](/cli/azure/acr#create). Il nome di un registro contenitori **deve essere univoco**. Usando l'esempio seguente aggiornare il nome con alcuni caratteri casuali.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

## <a name="get-acr-information"></a>Ottenere informazioni sul Registro contenitori di Azure 

Dopo avere creato l'istanza del Registro contenitori di Azure, sono necessari il nome, il nome del server di accesso e la password di autenticazione. Il codice seguente restituisce ognuno di questi valori. Prendere nota di ogni valore; in questa esercitazione viene fatto riferimento ad essi.  

Nome del Registro contenitori di Azure e server di accesso:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrName:name,acrLoginServer:loginServer}" --output table
```

Password del Registro contenitori di Azure: eseguire l'aggiornamento con il nome del Registro contenitori di Azure.

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value -o tsv
```

## <a name="container-registry-login"></a>Accesso al registro contenitori

È necessario accedere all'istanza del Registro contenitori di Azure prima di eseguire il push di immagini in essa. Usare il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) per completare l'operazione. Quando si esegue l'accesso a docker, è necessario fornire il nome del server di accesso e le credenziali del Registro contenitori di Azure.

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

Al termine, il comando restituisce un messaggio di accesso riuscito.

## <a name="tag-container-images"></a>Assegnare tag alle immagini del contenitore

Ogni immagine del contenitore deve essere contrassegnata con il nome `loginServer` del registro. Questo tag viene usato per il routing quando si esegue il push delle immagini del contenitore nel registro delle immagini.

Per visualizzare un elenco di immagini correnti, usare il comando `docker images`.

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

Applicare all'immagine *azure voto-anteriore* il tag loginServer del registro contenitori. Aggiungere anche `:v1` alla fine del nome dell'immagine. Questo tag indica il numero di versione dell'immagine.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Ripetere il comando con l'immagine *azure-vote-back*.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

Una volta applicato il tag, eseguire `docker images` per verificare l'operazione.

```bash
docker images
```

Output:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                                      latest              a9dace4e1a17        7 minutes ago       407 MB
mycontainerregistry082.azurecr.io/azure-vote-back    v1                  a9dace4e1a17        7 minutes ago       407 MB
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1                  eaf2b9c57e5e        8 minutes ago       716 MB
mysql                                                latest              e799c7f9ae9c        6 weeks ago         407 MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-acr"></a>Eseguire il push delle immagini nel Registro contenitori di Azure

Eseguire il push dell'immagine *azure-vote-front* nel registro. 

Nell'esempio seguente sostituire il nome del loginServer del Registro contenitori di Azure con il loginServer dell'ambiente in uso. Il completamento dell'operazione richiede alcuni minuti.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Eseguire la stessa operazione nell'immmagine *azure-vote-back*.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

## <a name="list-images-in-acr"></a>Elencare le immagini nel Registro contenitori di Azure 

Per restituire un elenco di immagini di cui è stato eseguito il push nel Registro contenitori di Azure, usare il comando [az acr repository list](/cli/azure/acr/repository#list). Aggiornare il comando con il nome dell'istanza del Registro contenitori di Azure.

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

Output:

```azurecli
Result
----------------
azure-vote-back
azure-vote-front
```

Per visualizzare i tag per un'immagine specifica, usare il comando [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli-interactive
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository azure-vote-front --output table
```

Output:

```azurecli
Result
--------
v1
```

Al termine dell'esercitazione, le due immagini del contenitore sono state archiviate in un'istanza privata del Registro contenitori di Azure. Queste immagini verranno distribuite dal Registro contenitori di Azure in un cluster Kubernetes nelle esercitazioni successive.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione un Registro contenitori di Azure è stato preparato per l'uso in un cluster Kubernetes del servizio contenitore di Azure. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione di un'istanza del Registro contenitori di Azure
> * Applicazione di tag alle immagini del contenitore per il Registro contenitori di Azure
> * Caricamento di immagini nel Registro contenitori di Azure

Passare all'esercitazione successiva per informazioni sulla distribuzione di un cluster Kubernetes in Azure.

> [!div class="nextstepaction"]
> [Distribuire un cluster Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md).
