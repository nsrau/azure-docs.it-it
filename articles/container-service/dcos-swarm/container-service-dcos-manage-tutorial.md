---
title: Esercitazione sul servizio contenitore di Azure - Gestire DC/OS | Microsoft Docs
description: Esercitazione sul servizio contenitore di Azure - Gestire DC/OS
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
ms.date: 07/17/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 2b4ebe3d026b38916dd127312ad684b7c973ac0d
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---

# <a name="azure-container-service-tutorial---manage-dcos"></a>Esercitazione sul servizio contenitore di Azure - Gestire DC/OS

DC/OS fornisce una piattaforma distribuita per l'esecuzione di applicazioni in contenitori e moderne. Con il servizio contenitore di Azure, il provisioning di un cluster DC/OS pronto per la produzione è semplice e rapido. Questa guida rapida illustra in dettaglio i passaggi di base necessari per distribuire un cluster DC/OS ed eseguire un carico di lavoro di base.

> [!div class="checklist"]
> * Creare un cluster DC/OS del servizio contenitore di Azure
> * Connettersi al cluster
> * Installare l'interfaccia della riga di comando di DC/OS
> * Distribuire un'applicazione nel cluster
> * Ridimensionare un'applicazione nel cluster
> * Ridimensionare i nodi del cluster DC/OS
> * Gestione di base di DC/OS
> * Eliminare il cluster DC/OS

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Creare un cluster DC/OS

Creare prima di tutto un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare prima di tutto un cluster DC/OS con il comando [az acs create](/cli/azure/acs#create).

L'esempio seguente crea un cluster DC/OS denominato *myDCOSCluster* e crea le chiavi SSH se non esistono già. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Dopo alcuni minuti, il comando viene completato e restituisce le informazioni sulla distribuzione.

## <a name="connect-to-dcos-cluster"></a>Connettersi al cluster DC/OS

Dopo aver creato un cluster DC/OS, è possibile accedervi tramite un tunnel SSH. Eseguire il comando seguente per restituire l'indirizzo IP pubblico del master DC/OS. L'indirizzo IP viene archiviato in una variabile e usato nel passaggio successivo.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Per creare il tunnel SSH, eseguire il comando seguente e seguire le istruzioni visualizzate. Se la porta 80 è già in uso, il comando non riesce. Aggiornare la porta di tunneling selezionandone una non in uso, come `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>Installare l'interfaccia della riga di comando di DC/OS

Installare l'interfaccia della riga di comando di DC/OS con il comando [az acs dcos install-cli](/azure/acs/dcos#install-cli). Se si usa Azure CloudShell, l'interfaccia della riga di comando di DC/OS è già installata. Se si esegue l'interfaccia della riga di comando di Azure in macOS o Linux, potrebbe essere necessario eseguire il comando con sudo.

```azurecli
az acs dcos install-cli
```

Prima di poter usare l'interfaccia della riga di comando con il cluster, deve essere configurato per usare il tunnel SSH. A tale scopo, eseguire il comando seguente, modificando la porta se necessario.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Eseguire un'applicazione

Il meccanismo di pianificazione predefinito per un cluster DC/OS del servizio contenitore di Azure è Marathon. Marathon viene usato per avviare un'applicazione e gestire lo stato dell'applicazione nel cluster DC/OS. Per pianificare un'applicazione tramite Marathon, creare un file denominato **marathon-app.json** e copiarvi il contenuto seguente. 

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Eseguire il comando seguente per pianificare l'esecuzione dell'applicazione nel cluster DC/OS.

```azurecli
dcos marathon app add marathon-app.json
```

Per visualizzare lo stato di distribuzione per l'app, eseguire il comando seguente.

```azurecli
dcos marathon app list
```

Quando il valore della colonna **TASKS** (Attività) passa da *0/1* a *1/1*, la distribuzione dell'applicazione è stata completata.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Ridimensionare l'applicazione Marathon

Nell'esempio precedente è stata creata un'applicazione a istanza singola. Per aggiornare la distribuzione in modo che siano disponibili tre istanze dell'applicazione, aprire il file **marathon-app.json** e impostare la proprietà per le istanze su 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Aggiornare l'applicazione con il comando `dcos marathon app update`.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Per visualizzare lo stato di distribuzione per l'app, eseguire il comando seguente.

```azurecli
dcos marathon app list
```

Quando il valore della colonna **TASKS** (Attività) passa da *1/3* a *3/1*, la distribuzione dell'applicazione è stata completata.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Eseguire un'app accessibile su Internet

Il cluster DC/OS del servizio contenitore di Azure è costituito da due set di nodi, uno pubblico accessibile su Internet e uno privato non accessibile su Internet. Il set predefinito è quello dei nodi privati, usato nell'ultimo esempio.

Per rendere un'applicazione accessibile su Internet, distribuirla nel set di nodi pubblico. A tale scopo, assegnare all'oggetto `acceptedResourceRoles` il valore `slave_public`.

Creare un file denominato **nginx-public.json** e copiare il contenuto seguente nel file.

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Eseguire il comando seguente per pianificare l'esecuzione dell'applicazione nel cluster DC/OS.

```azurecli 
dcos marathon app add nginx-public.json
```

Ottenere l'indirizzo IP pubblico degli agenti del cluster pubblico DC/OS.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Passando a questo indirizzo viene restituito il sito NGINX predefinito.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Ridimensionare il cluster DC/OS

Negli esempi precedenti, un'applicazione è stata ridimensionata per più istanze. Anche l'infrastruttura di DC/OS può essere ridimensionata per offrire maggiore o minore capacità di calcolo. Questa operazione viene eseguita con il comando [az acs scale](). 

Per visualizzare il numero corrente di agenti di DC/OS, usare il comando [az acs show](/cli/azure/acs#show).

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Per aumentare il conteggio a 5, usare il comando [az acs scale](/cli/azure/acs#scale). 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Eliminare il cluster DC/OS

Quando non serve più, è possibile rimuovere il gruppo di risorse, il cluster DC/OS e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#delete).

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state descritte le attività di gestione di base di DC/OS, incluse le seguenti. 

> [!div class="checklist"]
> * Creare un cluster DC/OS del servizio contenitore di Azure
> * Connettersi al cluster
> * Installare l'interfaccia della riga di comando di DC/OS
> * Distribuire un'applicazione nel cluster
> * Ridimensionare un'applicazione nel cluster
> * Ridimensionare i nodi del cluster DC/OS
> * Eliminare il cluster DC/OS

Passare all'esercitazione successiva per apprendere come bilanciare il carico delle applicazioni in DC/OS in Azure. 

> [!div class="nextstepaction"]
> [Bilanciare il carico delle applicazioni](container-service-load-balancing.md)
