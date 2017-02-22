---
title: Guida introduttiva per i cluster Kubernetes in Azure | Documentazione Microsoft
description: Distribuire e iniziare a usare un cluster Kubernetes nel servizio contenitore di Azure
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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Motore del servizio contenitore di Microsoft Azure - Procedura dettagliata per Kubernetes

## <a name="prerequisites"></a>Prerequisiti
Questa procedura dettagliata presuppone che sia installato lo [strumento da riga di comando "azure-cli"](https://github.com/azure/azure-cli#installation) e che sia stata creata la [chiave pubblica SSH](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) in `~/.ssh/id_rsa.pub`.

## <a name="overview"></a>Panoramica

Applicando le istruzioni seguenti, viene creato un cluster Kubernetes con un nodo master e due nodi di lavoro.
Il master gestisce l'API REST Kubernetes.  I nodi di lavoro vengono raggruppati in un set di disponibilità di Azure ed eseguono i contenitori. Tutte le macchine virtuali si trovano nella stessa rete privata virtuale e sono completamente accessibili le une alle altre.

> [!NOTE]
> Il supporto per Kubernetes nel servizio contenitore di Azure è attualmente disponibile in anteprima.
>

L'immagine seguente illustra l'architettura di un cluster del servizio contenitore con un master e due agenti:

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Creazione del cluster Kubernetes

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Per creare il cluster, prima di tutto è necessario creare un gruppo di risorse in una posizione specifica, con il codice seguente:
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Creare un cluster
Dopo avere creato il gruppo di risorse, è possibile creare un cluster in tale gruppo con il codice seguente:
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> azure-cli caricherà `~/.ssh/id_rsa.pub` nelle VM Linux.
>

Al termine dell'esecuzione del comando, sarà disponibile un cluster Kubernetes funzionante.

### <a name="configure-kubectl"></a>Configurare kubectl
`kubectl` è il client della riga di comando Kubernetes.  Se non è già stato installato, è possibile installarlo con il codice seguente:

```console
az acs kubernetes install-cli
```

Al termine dell'installazione di `kubectl`, eseguire questo comando per scaricare la configurazione cluster Kubernetes del master nel file ~/.kube/config:
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

A questo punto, sarà possibile accedere al cluster dal computer. Provare a eseguire:
```console
kubectl get nodes
```

Verificare che i computer siano visualizzati nel cluster.

## <a name="create-your-first-kubernetes-service"></a>Creare il primo servizio Kubernetes

La procedura dettagliata illustra come eseguire queste operazioni:
 * Distribuire un'applicazione Docker ed esporla a tutti gli utenti
 * Usare `kubectl exec` per eseguire comandi in un contenitore 
 * Accedere al dashboard Kubernetes

### <a name="start-a-simple-container"></a>Avviare un contenitore semplice
È possibile eseguire un contenitore semplice (in questo caso, il server Web `nginx`) usando:

```console
kubectl run nginx --image nginx
```

Questo comando avvia il contenitore Docker nginx in un pod in uno dei nodi.

È possibile eseguire
```console
kubectl get pods
```

per visualizzare il contenitore in esecuzione.

### <a name="expose-the-service-to-the-world"></a>Esporre il servizio
Per esporre il servizio,  si crea un elemento `Service` Kubernetes di tipo `LoadBalancer`:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Kubernetes creerà quindi un servizio Azure Load Balancer con un IP pubblico. La propagazione della modifica al servizio di bilanciamento del carico richiede circa 2-3 minuti.

Per verificare il passaggio del servizio da "in sospeso" a un IP esterno, digitare:
```console
watch 'kubectl get svc'
```

  ![Immagine della verifica della transizione da in sospeso a IP esterno](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Dopo la visualizzazione dell'IP esterno, è possibile passare all'IP nel browser:

  ![Immagine del passaggio a nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Esplorare l'interfaccia utente Kubernetes
Per visualizzare l'interfaccia Web Kubernetes, è possibile usare:

```console
kubectl proxy
```
Questo comando esegue in localhost un proxy autenticato semplice, che è possibile usare per visualizzare l'[interfaccia utente Kubernetes](http://localhost:8001/ui)

![Immagine del dashboard Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sessioni remote nei contenitori
Kubernetes consente di eseguire comandi in un contenitore Docker remoto in esecuzione nel cluster.

```console
# Get the name of your nginx pod
kubectl get pods
```

Usando il nome del pod, è possibile eseguire un comando remoto sul pod.  Ad esempio:
```console
kubectl exec nginx-701339712-retbj date
```

È anche possibile ottenere una sessione completamente interattiva usando i flag `-it`:

```console
kubectl exec nginx-701339712-retbj -it bash
```

![Immagine di curl sull'IP del pod](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>Dettagli
### <a name="installing-the-kubectl-configuration-file"></a>Installazione del file di configurazione kubectl
Quando si è eseguito `az acs kubernetes get-credentials`, il file di configurazione kube per l'accesso remoto è stato archiviato nella home directory ~/.kube/config.

Se è necessario scaricarlo direttamente, è possibile usare `ssh` in Linux o OS X oppure `Putty` in Windows:

#### <a name="windows"></a>Windows
Per usare pscp da [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Assicurarsi che il certificato sia esposto tramite [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X o Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>Altre informazioni

### <a name="azure-container-service"></a>Servizio contenitore di Azure

1. [Documentazione del servizio contenitore di Azure](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Azure Container Service Open Source Engine](https://github.com/azure/acs-engine) (Motore open source del servizio contenitore di Azure)

### <a name="kubernetes-community-documentation"></a>Documentazione della community di Kubernetes

1. [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/): illustra come distribuire, ridimensionare ed eseguire il debug di applicazione inserite in contenitori.
2. [Guida dell'utente di Kubernetes](http://kubernetes.io/docs/user-guide/): offre informazioni sull'esecuzione di programmi in un cluster Kubernetes esistente.
3. [Esempi di Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples): fornisce alcuni esempi relativi all'esecuzione di applicazioni effettive con Kubernetes.



<!--HONumber=Jan17_HO4-->


