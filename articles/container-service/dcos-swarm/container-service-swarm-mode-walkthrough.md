---
title: Guida introduttiva - Cluster Docker CE di Azure per Linux
description: Informazioni per creare rapidamente un cluster Docker CE per contenitori Linux nel servizio contenitore di Azure con l'interfaccia della riga di comando di Azure.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 3c06aaa09366df89ad73cb60780511d2087d5994
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-docker-ce-cluster"></a>Distribuire un cluster Docker CE

In questa guida introduttiva viene distribuito un cluster Docker CE usando l'interfaccia della riga di comando di Azure. Un'applicazione multicontenitore costituita dal front-end Web e da un'istanza di Redis viene quindi distribuita ed eseguita nel cluster. Al termine, l'applicazione è accessibile tramite Internet.

Docker CE nel servizio contenitore di Azure è in anteprima e **non deve essere usato per carichi di lavoro di produzione**.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *ukwest*.

```azurecli-interactive
az group create --name myResourceGroup --location ukwest
```

Output:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "ukwest",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Creare un cluster Docker Swarm

Creare un cluster Docker CE nel servizio contenitore di Azure con il comando [az acs create](/cli/azure/acs#az_acs_create). 

L'esempio seguente crea un cluster denominato *mySwarmCluster* con un nodo master Linux e tre nodi agente Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

In alcuni casi la sottoscrizione di Azure ha accesso limitato alle risorse di Azure, ad esempio con una versione di valutazione gratuita limitata. Se la distribuzione non riesce a causa di core disponibili limitati, ridurre il numero di agenti predefinito aggiungendo `--agent-count 1` al comando [az acs create](/cli/azure/acs#az_acs_create). 

Dopo alcuni minuti, il comando viene completato e restituisce le informazioni in formato JSON sul cluster.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Nel corso di questa guida introduttiva è necessario il nome di dominio completo sia del master Docker Swarm che del pool di agenti Docker. Eseguire questo comando per ottenere entrambi i nomi di dominio completi.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Output:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Creare un tunnel SSH per il master Swarm. Sostituire `MasterFQDN` con l'indirizzo FQDN del master Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Impostare la variabile di ambiente `DOCKER_HOST`. In questo modo è possibile eseguire comandi di Docker su Docker Swarm senza dover specificare il nome dell'host.

```bash
export DOCKER_HOST=localhost:2374
```

È ora possibile eseguire servizi Docker in Docker Swarm.


## <a name="run-the-application"></a>Eseguire l'applicazione

Creare un file denominato `azure-vote.yaml` e copiarvi il contenuto seguente.


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Eseguire il comando [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) per creare il servizio Azure Vote.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Output:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Usare il comando [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) per ottenere lo stato della distribuzione dell'applicazione.

```bash
docker stack ps azure-vote
```

Quando il valore `CURRENT STATE` di ogni servizio è `Running`, l'applicazione è pronta.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Test dell'applicazione

Passare al nome di dominio completo del pool di agenti Swarm per testare l'applicazione Azure Vote.

![Immagine del passaggio ad Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Eliminare il cluster
Quando il cluster non è più necessario, è possibile usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Ottenere il codice

In questa guida introduttiva sono state usate immagini del contenitore già create per creare un servizio Docker. Il codice dell'applicazione correlato, Dockerfile, e il file Compose sono disponibili in GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un cluster Docker Swarm in cui è stata quindi distribuita un'applicazione multicontenitore.

Per informazioni sull'integrazione di Docker Swarm con Visual Studio Team Services, vedere l'articolo relativo a integrazione e distribuzione continua con Docker Swarm e VSTS.

> [!div class="nextstepaction"]
> [Integrazione continua e distribuzione continua con Docker Swarm e VSTS](./container-service-docker-swarm-setup-ci-cd.md)
