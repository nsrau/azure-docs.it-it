---
title: Guida introduttiva - Cluster Kubernetes Azure per Windows | Microsoft Docs
description: Informazioni per creare in modo rapido un cluster Kubernetes per contenitori Windows nel servizio contenitore di Azure con l'interfaccia della riga di comando di Azure.
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 6a03f668c6b9e18bf80cb04ea9feddeef4b6bd9e
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---

# <a name="deploy-kubernetes-cluster-for-windows-containers"></a>Distribuire cluster Kubernetes per contenitori Windows

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida illustra in modo dettagliato come usare l'interfaccia della riga di comando di Azure per distribuire un cluster [Kubernetes](https://kubernetes.io/docs/home/) nel [servizio contenitore di Azure](../container-service-intro.md). Dopo aver distribuito il cluster, è possibile connettersi a esso con lo strumento da riga di comando `kubectl` di Kubernetes e distribuire il primo contenitore Windows.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Il supporto per i contenitori Windows in Kubernetes nel servizio contenitore di Azure è disponibile in versione di anteprima. 
>

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Creare un cluster Kubernetes
Creare un cluster Kubernetes nel servizio contenitore di Azure con il comando [az acs create](/cli/azure/acs#create). 

L'esempio seguente crea un cluster denominato *myK8sCluster* con un nodo master Linux e due nodi agente Windows. Questo esempio crea le chiavi SSH necessarie per la connessione al nodo master Linux. L'esempio usa *azureuser* come nome utente amministrativo e *myPassword12* come password nei nodi Windows. Aggiornare i valori in modo che siano appropriati all'ambiente. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Dopo alcuni minuti, il comando viene completato e mostra le informazioni sulla distribuzione.

## <a name="install-kubectl"></a>Installare kubectl

Per connettersi al cluster Kubernetes dal computer client, usare [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), il client da riga di comando di Kubernetes. 

Se si usa Azure CloudShell, `kubectl` è già installato. Se lo si vuole installare in locale, è possibile usare il comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

L'interfaccia della riga di comando di Azure seguente installa `kubectl` nel sistema. In Windows eseguire questo comando come amministratore.

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

## <a name="deploy-a-windows-iis-container"></a>Distribuire un contenitore IIS Windows

È possibile eseguire un contenitore Docker all'interno di un *pod* Kubernetes, che contiene uno o più contenitori. 

Questo esempio di base usa un file JSON per specificare un contenitore Microsoft Internet Information Server (IIS) e quindi crea il pod usando il comando `kubctl apply`. 

Creare un file locale denominato `iis.json` e copiare il testo seguente. Questo file indica a Kubernetes di eseguire IIS su Windows Server 2016 Nano Server usando un'immagine di contenitore pubblica dall'[hub Docker](https://hub.docker.com/r/microsoft/iis/). Il contenitore usa la porta 80, ma inizialmente è accessibile solo all'interno della rete di cluster.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Per avviare il pod, digitare:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Per tenere traccia della distribuzione, digitare:
  
```azurecli-interactive
kubectl get pods
```

Durante la distribuzione del pod, lo stato è `ContainerCreating`. Per il passaggio del contenitore allo stato `Running`, possono essere necessari alcuni minuti.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>Visualizzare la pagina iniziale di IIS

Per esporre il pod con un indirizzo IP pubblico, digitare il comando seguente:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Con questo comando, Kubernetes crea un servizio e una [regola di Azure Load Balancer](container-service-kubernetes-load-balancing.md) con un indirizzo IP pubblico per il servizio. 

Eseguire questo comando per visualizzare lo stato del servizio.

```azurecli-interactive
kubectl get svc
```

L'indirizzo IP viene inizialmente visualizzato come `pending`. Dopo alcuni minuti, viene impostato l'indirizzo IP esterno del pod `iis`:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

È possibile usare il Web browser che si preferisce per vedere la pagina iniziale di IIS predefinita all'indirizzo IP esterno:

![Immagine del passaggio a IIS](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>Eliminare il cluster
Quando il cluster non è più necessario, è possibile usare il comando [az group delete](/cli/azure/group#delete) per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un cluster Kubernetes, è stata eseguita la connessione con `kubectl` ed è stato distribuito un pod con un contenitore IIS. Per altre informazioni sul servizio contenitore di Azure, continuare con l'esercitazione su Kubernetes.

> [!div class="nextstepaction"]
> [Gestire un cluster Kubernetes ACS](container-service-tutorial-kubernetes-prepare-app.md)

