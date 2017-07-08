---
title: Guida introduttiva - Cluster Kubernetes Azure per Linux | Microsoft Docs
description: Informazioni per creare in modo rapido un cluster Kubernetes per contenitori Linux nel servizio contenitore di Azure con l'interfaccia della riga di comando di Azure.
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 25043f6bf5e5ab3def8563bd2c096b79706bfec1
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Distribuire cluster Kubernetes per contenitori Linux

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida illustra in modo dettagliato come usare l'interfaccia della riga di comando di Azure per distribuire un cluster [Kubernetes](https://kubernetes.io/docs/home/) nel [servizio contenitore di Azure](container-service-intro.md). Dopo aver distribuito il cluster, è possibile connettersi a esso con lo strumento da riga di comando `kubectl` di Kubernetes e distribuire il primo contenitore Linux.

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Creare un cluster Kubernetes
Creare un cluster Kubernetes nel servizio contenitore di Azure con il comando [az acs create](/cli/azure/acs#create). 

L'esempio seguente crea un cluster denominato *myK8sCluster* con un nodo master Linux e due nodi agente Linux. Questo esempio crea le chiavi SSH, se non sono già presenti nelle posizioni predefinite. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. Aggiornare il nome del cluster scegliendo un nome appropriato per l'ambiente. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

Dopo alcuni minuti, il comando viene completato e mostra le informazioni sulla distribuzione.

## <a name="install-kubectl"></a>Installare kubectl

Per connettersi al cluster Kubernetes dal computer client, usare [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), il client da riga di comando di Kubernetes. 

Se si usa Azure CloudShell, `kubectl` è già installato. Se lo si vuole installare in locale, è possibile usare il comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

L'interfaccia della riga di comando di Azure seguente installa `kubectl` nel sistema. Se si esegue l'interfaccia della riga di comando di Azure in macOS o Linux, potrebbe essere necessario eseguire il comando con `sudo`.

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>Connettersi con kubectl

Per configurare `kubectl` per connettersi al cluster Kubernetes, eseguire il comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). L'esempio seguente scarica la configurazione del cluster per il cluster Kubernetes.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Per verificare la connessione al cluster dal computer, provare a eseguire:

```azurecli-interactive
kubectl get nodes
```

`kubectl` elenca i nodi master e agente.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>Distribuire un contenitore NGINX

È possibile eseguire un contenitore Docker all'interno di un *pod* Kubernetes, che contiene uno o più contenitori. 

Il comando seguente avvia il contenitore Docker NGINX in un pod Kubernetes in uno dei nodi. In questo caso, il contenitore esegue il server Web NGINX derivato da un'immagine nell'[hub Docker](https://hub.docker.com/_/nginx/).

```azurecli-interactive
kubectl run nginx --image nginx
```
Per verificare che il contenitore sia in esecuzione, eseguire:

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>Visualizzare la pagina iniziale di NGINX
Per esporre il server NGINX con un indirizzo IP pubblico, digitare il comando seguente:

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Con questo comando, Kubernetes crea un servizio e una [regola di Azure Load Balancer](container-service-kubernetes-load-balancing.md) con un indirizzo IP pubblico per il servizio. 

Eseguire questo comando per visualizzare lo stato del servizio.

```azurecli-interactive
kubectl get svc
```

L'indirizzo IP viene inizialmente visualizzato come `pending`. Dopo alcuni minuti, viene impostato l'indirizzo IP esterno del servizio:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

È possibile usare il Web browser che si preferisce per vedere la pagina iniziale di NGINX predefinita all'indirizzo IP esterno:

![Immagine del passaggio a Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>Eliminare il cluster
Quando il cluster non è più necessario, è possibile usare il comando [az group delete](/cli/azure/group#delete) per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un cluster Kubernetes, è stata eseguita la connessione con `kubectl` ed è stato distribuito un pod con un contenitore NGINX. Per altre informazioni sul servizio contenitore di Azure, continuare con l'esercitazione sui cluster Kubernetes.

> [!div class="nextstepaction"]
> [Gestire un cluster Kubernetes ACS](./container-service-tutorial-kubernetes-prepare-app.md)

