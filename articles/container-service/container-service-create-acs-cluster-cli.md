---
title: Distribuire un cluster per contenitori Docker - Interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Distribuire una soluzione Kubernetes, DC/OS o Docker Swarm nel servizio contenitore di Azure usando l&quot;interfaccia della riga di comando di Azure 2.0
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2464901d22bb91cbf396ef60f4bda6d979b578b7
ms.openlocfilehash: a0cbf24c2e2f1e6f3a1d2097e6146c09b4eee4fe
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>Distribuire una soluzione di hosting di contenitori Docker usando l'interfaccia della riga di comando di Azure 2.0

Usare i comandi `az acs` nell'interfaccia della riga di comando di Azure 2.0 per creare e gestire i cluster nel servizio contenitore di Azure. È anche possibile distribuire un cluster del servizio contenitore di Azure usando il [portale di Azure](container-service-deployment.md) o le API del servizio contenitore di Azure.

Per informazioni sui comandi `az acs`, passare il parametro `-h` a qualsiasi comando. Ad esempio: `az acs create -h`.



## <a name="prerequisites"></a>Prerequisiti
Per creare un cluster del servizio contenitore di Azure usando l'interfaccia della riga di comando di Azure 2.0, è necessario:
* Avere un account Azure ([versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/))
* Avere installato e configurato l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2)

## <a name="get-started"></a>Introduzione 
### <a name="log-in-to-your-account"></a>Accedere all'account
```azurecli
az login 
```

Seguire i prompt per accedere in modo interattivo. Per altri metodi di accesso, vedere [Get started with Azure CLI 2.0](/cli/azure/get-started-with-az-cli2) (Introduzione all'interfaccia della riga di comando di Azure 2.0).

### <a name="set-your-azure-subscription"></a>Configurare la sottoscrizione di Azure

Se si hanno più sottoscrizioni Azure, configurare la sottoscrizione predefinita. Ad esempio:

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
È consigliabile creare un gruppo di risorse per ogni cluster. Specificare un'area di Azure in cui sia [disponibile](https://azure.microsoft.com/en-us/regions/services/) il servizio contenitore di Azure. Ad esempio:

```azurecli
az group create -n acsrg1 -l "westus"
```
L'output è simile al seguente:

![Creare un gruppo di risorse](media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Creare un cluster del servizio contenitore di Azure

Per creare un cluster, usare `az acs create`.
Un nome per il cluster e il nome del gruppo di risorse creato nel passaggio precedente sono parametri obbligatori. 

Altri input vengono impostati su valori predefiniti, come illustrato nella schermata seguente, a meno che non vengano sovrascritti tramite le rispettive opzioni. Ad esempio, l'agente di orchestrazione viene impostato per impostazione predefinita su DC/OS. Se non si specifica alcun valore, viene creato un prefisso del nome DNS in base al nome del cluster.

![az acs create usage](media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>Operazione rapida con i valori predefiniti per `acs create`
Se è disponibile un file di chiave pubblica SSH RSA `id_rsa.pub` nel percorso predefinito o se ne è stato creato uno per [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) o [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md), usare un comando simile al seguente:

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
Se non è disponibile alcuna chiave pubblica SSH, usare il comando seguente. Questo comando con l'opzione `--generate-ssh-keys` crea automaticamente il file.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

Dopo avere immesso il comando, attendere circa 10 minuti per la creazione del cluster. L'output del comando include nomi di dominio completi dei nodi master e agente e un comando SSH per la connessione al primo master. Ecco un output abbreviato:

![Immagine del comando create del servizio contenitore di Azure](media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> La [Procedura dettagliata per Kubernetes](container-service-kubernetes-walkthrough.md) illustra come usare `az acs create` con i valori predefiniti per creare un cluster Kubernetes.
>

## <a name="manage-acs-clusters"></a>Gestire i cluster del servizio contenitore di Azure

Usare comandi `az acs` aggiuntivi per gestire il cluster. Di seguito sono riportati alcuni esempi.

### <a name="list-clusters-under-a-subscription"></a>Elencare i cluster in una sottoscrizione

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>Elencare i cluster in un gruppo di risorse

```azurecli
az acs list -g acsrg1 --output table
```

![acs list](media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>Visualizzare i dettagli di un cluster del servizio contenitore

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![acs show](media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>Ridimensionare il cluster
È consentito sia ridurre che aumentare il numero di istanze dei nodi agente. Il parametro `new-agent-count` è il nuovo numero di agenti nel cluster del servizio contenitore di Azure.

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![acs scale](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Eliminare un cluster del servizio contenitore
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
Questo comando non elimina tutte le risorse (di rete e di archiviazione) create durante la creazione del servizio contenitore. Per eliminare facilmente tutte le risorse, è consigliabile distribuire ogni cluster in un gruppo di risorse distinto. Eliminare quindi il gruppo di risorse quando il cluster non è più necessario.

## <a name="next-steps"></a>Passaggi successivi
Ora che si ha a disposizione un cluster funzionante, vedere i documenti seguenti per informazioni dettagliate sulla connessione e la gestione:

* [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md)
* [Gestione di contenitori tramite l'API REST](container-service-mesos-marathon-rest.md)
* [Gestione dei contenitori con Docker Swarm](container-service-docker-swarm.md)
* [Uso del servizio contenitore di Azure e Kubernetes](container-service-kubernetes-walkthrough.md)
