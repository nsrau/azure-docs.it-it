---
title: Uso di Registro contenitori di Azure (ACR) con un cluster DC/OS | Microsoft Docs
description: Usare Registro contenitori di Azure con un cluster DC/OS nel servizio contenitore di Azure
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: Docker, contenitori, micro-servizi, Mesos, Azure, FileShare, CIFS
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: fa07135d550bf9ea0f6d1e03089b988cf0d5dddc
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Usare Registro contenitori di Azure (ACR) con un cluster DC/OS per distribuire l'applicazione

In questo articolo viene illustrato l'uso del Registro contenitori di Azure con un cluster del controller di dominio/sistema operativo. L'uso del record di controllo di accesso consente di archiviare privatamente e di gestire le immagini del contenitore. Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Distribuire il Registro contenitori di Azure (se necessario)
> * Configurare l'autenticazione del record di controllo di accesso in un cluster del controller di dominio/sistema operativo
> * Caricare un'immagine nel Registro contenitori di Azure
> * Eseguire un'immagine del contenitore dal Registro contenitori di Azure

È necessario un cluster del controller di dominio/sistema operativo del servizio contenitore di Azure per completare i passaggi in questa esercitazione. Se necessario, questo [script di esempio](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) può crearne uno appositamente.

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Distribuire il Registro contenitori di Azure

Se necessario, creare un Registro contenitori di Azure con il comando [az acr create](/cli/azure/acr#create). 

Nell'esempio seguente viene creato un registro con un nome generato in modo casuale. Il registro viene anche configurato con un account amministratore tramite l'argomento `--admin-enabled`.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic --admin-enabled true
```

Dopo la creazione del registro, l'interfaccia della riga di comando di Azure restituisce dati simili ai seguenti. Annotare `name` e `loginServer`, che verranno usati nei passaggi successivi.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Ottenere le credenziali del registro contenitori tramite il comando [az acr credential show](/cli/azure/acr/credential). Sostituire `--name` con il valore annotato nel passaggio precedente. Prendere nota di una password, che sarà necessaria in un passaggio successivo.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Per altre informazioni sul Registro contenitori di Azure, vedere [Introduzione ai registri per contenitori Docker privati](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Gestire l'autenticazione del record di controllo di accesso

Il modo convenzionale per effettuare il push e il pull di un'immagine da un registro privato consiste nell'eseguire prima l'autenticazione con il registro. A tale scopo, usare il `docker login` comando in qualsiasi client che richieda l'accesso al registro privato. Poiché un cluster di controller di dominio/sistema operativo può contenere molti nodi, ognuno dei quali deve essere autenticato con il record di controllo di accesso, è utile automatizzare questo processo in ogni nodo. 

### <a name="create-shared-storage"></a>Creare uno spazio di archiviazione condiviso

Questo processo usa una condivisione di file di Azure che è stata montata in ogni nodo del cluster. Se non è già stato impostato lo spazio di archiviazione condiviso, vedere [Creare e montare una condivisione di file per un cluster DC/OS](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Configurare l'autenticazione del record di controllo di accesso

Innanzitutto, ottenere il nome di dominio completo del master del controller di dominio/sistema operativo e archiviarlo in una variabile.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Creare un collegamento SSH con il master (o il primo master) del cluster basato su controller di dominio/sistema operativo. Aggiornare il nome utente se è stato usato un valore non predefinito al momento della creazione del cluster.

```azurecli-interactive
ssh azureuser@$FQDN
```

Eseguire il comando seguente per l'accesso al Registro contenitori di Azure. Sostituire `--username` con il nome del registro contenitori e `--password` con una delle password fornite. Sostituire l'ultimo argomento *mycontainerregistry.azurecr.io* nell'esempio con il nome loginServer del registro contenitori. 

Questo comando archivia i valori di autenticazione in locale nel percorso `~/.docker`.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Creare un file compresso che contenga i valori di autenticazione del registro contenitori.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Copiare questo file nello spazio di archiviazione condiviso del cluster. Questo passaggio rende il file disponibile in tutti i nodi del cluster del controller di dominio/sistema operativo.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Caricare un'immagine nel record di controllo di accesso

A questo punto, da un computer di sviluppo o da qualsiasi altro sistema con Docker installato, creare un'immagine e caricarla nel Registro contenitori di Azure.

Creare un contenitore dall'immagine Ubuntu.

```azurecli-interactive
docker run ubunut --name base-image
```

Ora è possibile acquisire il contenitore in una nuova immagine. È necessario che l'immagine includa il nome `loginServer` del registro contenitori nel formato `loginServer/imageName`.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

Accedere al Registro contenitori di Azure. Sostituire il nome con il nome loginServer, --username con il nome del registro contenitori, e -- password con una delle password fornite.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Infine, caricare l'immagine nel registro dei record di controllo di accesso. In questo esempio si carica un'immagine denominata dcos-demo.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Eseguire un'immagine dal record di controllo di accesso

Per usare un'immagine dal registro dei record di controllo di accesso, creare il nome del file *acrDemo.json* e copiarvi il seguente testo. Sostituire il nome dell'immagine con il nome del registro contenitori loginServer e il nome dell'immagine, ad esempio `loginServer/imageName`. Annotare la proprietà `uris`. Questa proprietà contiene la posizione del file di autenticazione del registro contenitori, che in questo caso corrisponde alla condivisione del file di Azure che è montato in ogni nodo del cluster del controller di dominio/sistema operativo.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Distribuire l'applicazione con l'interfaccia della riga di comando del controller di dominio/sistema operativo.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è necessario configurare il controller di dominio/sistema operativo per usare il Registro contenitori di Azure che comprende le seguenti attività:

> [!div class="checklist"]
> * Distribuire il Registro contenitori di Azure (se necessario)
> * Configurare l'autenticazione del record di controllo di accesso in un cluster del controller di dominio/sistema operativo
> * Caricare un'immagine nel Registro contenitori di Azure
> * Eseguire un'immagine del contenitore dal Registro contenitori di Azure

