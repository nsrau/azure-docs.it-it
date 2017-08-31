---
title: Guida introduttiva - Cluster Kubernetes Azure per Linux | Microsoft Docs
description: Informazioni per creare in modo rapido un cluster Kubernetes per contenitori Linux nel servizio contenitore di Azure con l'interfaccia della riga di comando di Azure.
services: container-service
documentationcenter: 
author: neilpeterson
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
ms.date: 08/21/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 5a2131659903e79b28f4d1b795d25a31d8d4ce8d
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Distribuire cluster Kubernetes per contenitori Linux

In questa guida introduttiva viene distribuito un cluster Kubernetes usando l'interfaccia della riga di comando di Azure. Un'applicazione multicontenitore costituita dal front-end Web e da un'istanza di Redis viene quindi distribuita ed eseguita nel cluster. Al termine, l'applicazione è accessibile tramite Internet. 

L'applicazione di esempio usata in questo documento è scritta in Python. I concetti e i passaggi descritti possono essere usati per distribuire un'immagine del contenitore in un cluster Kubernetes. Il codice, Dockerfile, e il file manifesto di Kubernetes creato in precedenza per questo progetto sono disponibili in [GitHub](https://github.com/Azure-Samples/azure-voting-app-redis.git).

![Immagine del passaggio ad Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Questa guida introduttiva presuppone una conoscenza di base dei concetti relativi a Kubernetes. Per informazioni dettagliate su Kubernetes, vedere la [documentazione di Kubernetes]( https://kubernetes.io/docs/home/).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *westeurope*.

```azurecli-interactive 
az group create --name myResourceGroup --location westeurope
```

Output:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>Creare un cluster Kubernetes

Creare un cluster Kubernetes nel servizio contenitore di Azure con il comando [az acs create](/cli/azure/acs#create). L'esempio seguente crea un cluster denominato *myK8sCluster* con un nodo master Linux e tre nodi agente Linux.

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys 
```

Dopo alcuni minuti, il comando viene completato e restituisce le informazioni in formato json sul cluster. 

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Per gestire un cluster Kubernetes, usare [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), il client da riga di comando di Kubernetes. 

Se si usa Azure CloudShell, kubectl è già installato. Se lo si vuole installare in locale, è possibile usare il comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Per configurare kubectl per connettersi al cluster Kubernetes, eseguire il comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Con questo passaggio si scaricano le credenziali e si configura l'interfaccia della riga di comando di Kubernetes per il loro uso.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) per restituire un elenco dei nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

Output:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un file manifesto di Kubernetes definisce uno stato desiderato per il cluster, incluse le immagini del contenitore da eseguire. Per questo esempio, viene usato un manifesto per creare tutti gli oggetti necessari per eseguire l'applicazione Azure Vote. 

Creare un file denominato `azure-vote.yml` e copiarvi il codice YAML seguente. Se si usa Azure Cloud Shell, questo file può essere creato usando vi o Nano come se si usasse un sistema virtuale o fisico.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:redis-v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Usare il comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) per eseguire l'applicazione.

```azurecli-interactive
kubectl create -f azure-vote.yml
```

Output:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Test dell'applicazione

Mentre l'applicazione viene eseguita, viene creato un [servizio di Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) che espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti. 

Per monitorare lo stato, usare il comando [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) con l'argomento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

**EXTERNAL-IP** per il servizio *azure-vote-front* inizialmente viene visualizzato come *pending*. Dopo che l'indirizzo EXTERNAL-IP passa da *pending* a un *indirizzo IP*, usare `CTRL-C` per arrestare il processo kubectl watch. 
  
```bash
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

È ora possibile passare all'indirizzo IP esterno per visualizzare l'app Azure Vote.

![Immagine del passaggio ad Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>Eliminare il cluster
Quando il cluster non è più necessario, è possibile usare il comando [az group delete](/cli/azure/group#delete) per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Ottenere il codice

In questa guida introduttiva sono state usate immagini del contenitore già creato per creare una distribuzione di Kubernetes. Il codice dell'applicazione correlato, Dockerfile, e il file manifesto di Kubernetes sono disponibili su GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un cluster Kubernetes in cui è stata quindi distribuita un'applicazione multicontenitore. 

Per altre informazioni sul servizio contenitore di Azure e l'analisi di un codice completo per la distribuzione dell'esempio, passare all'esercitazione sul cluster Kubernetes.

> [!div class="nextstepaction"]
> [Gestire un cluster Kubernetes ACS](./container-service-tutorial-kubernetes-prepare-app.md)

