---
title: Creare immagini del contenitore per Azure Service Fabric | Microsoft Docs
description: "Informazioni su come creare immagini del contenitore per un'applicazione di più contenitori di Service Fabric."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, contenitori, microservizi, Service Fabric, Azure
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: eb7a357369b605464b9986123f3c026fd1595a6f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-container-images-for-service-fabric"></a>Creare immagini del contenitore per Service Fabric

Questa esercitazione è la prima parte di una serie di esercitazioni in cui viene illustrato come usare i contenitori in un cluster Linux di Service Fabric. In questa esercitazione si prepara un'applicazione di più contenitori per l'uso in Service Fabric. Nelle esercitazioni successive, queste immagini vengono usate come parte di un'applicazione di Service Fabric. In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Clonare l'origine applicazione da GitHub  
> * Creare un'immagine del contenitore dall'origine applicazione
> * Distribuire un'istanza di Registro contenitori di Azure (ACR)
> * Assegnare tag a un'immagine del contenitore per Registro contenitori di Azure
> * Caricare l'immagine in Registro contenitori di Azure

In questa serie di esercitazioni si apprenderà come: 

> [!div class="checklist"]
> * Creare immagini del contenitore per Service Fabric
> * [Creare ed eseguire un'applicazione di Service Fabric con i contenitori](service-fabric-tutorial-package-containers.md)
> * [Come vengono gestiti i failover e il ridimensionamento in Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>prerequisiti

- Configurare l'ambiente di sviluppo Linux di Service Fabric. Seguire le istruzioni [qui](service-fabric-get-started-linux.md) per configurare l'ambiente Linux. 
- Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 
- Inoltre, richiede la presenza di una sottoscrizione di Azure disponibile. Per ulteriori informazioni su una versione di valutazione gratuita, passare [qui](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'applicazione di esempio usata in questa esercitazione è un'app di voto. L'applicazione è costituita da un componente Web front-end e un'istanza Redis back-end. I componenti sono inseriti in pacchetti nelle immagini del contenitore. 

Usare git per scaricare una copia dell'applicazione per l'ambiente di sviluppo.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

La soluzione contiene due cartelle e un file docker-compose.yml. La cartella 'azure-vote' contiene il servizio front-end Python unitamente al Dockerfile usato per creare l'immagine. La directory 'Voting' contiene il pacchetto dell'applicazione Service Fabric che viene distribuito nel cluster. Queste directory contengono le risorse necessarie per questa esercitazione.  

## <a name="create-container-images"></a>Creare immagini del contenitore

All'interno della directory **azure-vote** eseguire il comando seguente per creare l'immagine per il componente Web front-end. Questo comando usa il Dockerfile in questa directory per creare l'immagine. 

```bash
docker build -t azure-vote-front .
```

L'esecuzione di questo comando può richiedere tempo perché è necessario eseguire il pull di tutte le dipendenze necessarie da Hub Docker. Al termine usare il comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) per vedere le immagini create.

```bash
docker images
```

Si noti che sono state scaricate o create due immagini. L'immagine *azure-vote-front* contiene l'applicazione. È stata ottenuta da un'immagine *python* dall'Hub Docker.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Distribuire il Registro contenitori di Azure

Eseguire per prima cosa il comando **az login** per accedere al proprio account Azure. 

```bash
az login
```

Successivamente, usare il comando **az account** per scegliere la sottoscrizione per creare il Registro contenitori di Azure. Sostituire <subscription_id> con l'ID della sottoscrizione di Azure. 

```bash
az account set --subscription <subscription_id>
```

Prima di distribuire un Registro contenitori di Azure, è necessario che esista un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando **az group create**. In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *westus*.

```bash
az group create --name <myResourceGroup> --location westus
```

Creare un Registro contenitori di Azure con il comando **az acr create**. Sostituire \<acrName> con il nome del registro contenitori da creare nella sottoscrizione. Questo nome deve essere alfanumerico e univoco. 

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

Nella parte restante di questa esercitazione si usa "acrName" come segnaposto per il nome del registro contenitori scelto. Prendere nota di questo valore. 

## <a name="log-in-to-your-container-registry"></a>Accedere al registro contenitori

Accedere all'istanza Registro contenitori di Azure prima di eseguire il push di immagini in essa. Usare il comando **az acr login** per completare l'operazione. Specificare il nome univoco assegnato al registro contenitori al momento della creazione.

```bash
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
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Per ottenere il nome loginServer, eseguire questo comando:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Viene visualizzata una tabella come i risultati seguenti. Questo risultato verrà usato per contrassegnare l'immagine **azure-vote-front** prima di eseguirne il push nel registro contenitori nel passaggio successivo.

```bash
Result
------------------
<acrName>.azurecr.io
```

Applicare ora all'immagine *azure-vote-front* il tag loginServer del registro contenitori. Aggiungere anche `:v1` alla fine del nome dell'immagine. Questo tag indica la versione dell'immagine.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Una volta applicato il tag, eseguire 'docker images' per verificare l'operazione.


Output:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Eseguire il push delle immagini nel registro

Eseguire il push dell'immagine *azure-vote-front* nel registro. 

Nell'esempio seguente sostituire il nome del loginServer del Registro contenitori di Azure con il loginServer dell'ambiente in uso.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

I comandi di esecuzione del push di docker richiedono un paio di minuti per il completamento.

## <a name="list-images-in-registry"></a>Elencare le immagini nel registro

Per restituire un elenco di immagini di cui è stato eseguito il push nel Registro contenitori di Azure, usare il comando [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list). Aggiornare il comando con il nome dell'istanza del Registro contenitori di Azure.

```bash
az acr repository list --name <acrName> --output table
```

Output:

```bash
Result
----------------
azure-vote-front
```

Al termine dell'esercitazione, l'immagine del contenitore sarà stata archiviata in un'istanza privata di Registro contenitori di Azure. Questa immagine verrà distribuita da Registro contenitori di Azure a un cluster Service Fabric nelle esercitazioni successive.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è stato effettuato il pull di un'applicazione da Github e sono state create immagini del contenitore ed è stato eseguito il push a un registro di sistema. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Clonare l'origine applicazione da GitHub  
> * Creare un'immagine del contenitore dall'origine applicazione
> * Distribuire un'istanza di Registro contenitori di Azure (ACR)
> * Assegnare tag a un'immagine del contenitore per Registro contenitori di Azure
> * Caricare l'immagine in Registro contenitori di Azure

Passare all'esercitazione successiva per ulteriori informazioni sulla creazione di pacchetti di contenitori in un'applicazione di Service Fabric usando Yeoman. 

> [!div class="nextstepaction"]
> [Creare un pacchetto e distribuire contenitori come un'applicazione di Service Fabric](service-fabric-tutorial-package-containers.md)
