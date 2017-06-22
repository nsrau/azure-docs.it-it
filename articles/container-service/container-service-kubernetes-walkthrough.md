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
ms.date: 05/08/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 0604a85192ed632b621113b98cc44172c584ea01
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Introduzione a un cluster Kubernetes nel servizio contenitore


Questa procedura dettagliata illustra come usare i comandi dell'interfaccia della riga di comando di Azure 2.0 per creare un cluster Kubernetes nel servizio contenitore di Azure. È quindi possibile usare lo strumento `kubectl` da riga di comando per iniziare a lavorare con i contenitori nel cluster.

L'immagine seguente illustra l'architettura di un cluster del servizio contenitore con un master Linux e due agenti Linux. Il master gestisce l'API REST Kubernetes. I nodi agente vengono raggruppati in un set di disponibilità di Azure ed eseguono i contenitori. Tutte le macchine virtuali si trovano nella stessa rete privata virtuale e sono completamente accessibili le une alle altre.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

Per altre informazioni, vedere l'[introduzione al servizio contenitore di Azure](container-service-intro.md) e la [documentazione su Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Prerequisiti
Per creare un cluster del servizio contenitore di Azure usando l'interfaccia della riga di comando di Azure 2.0, è necessario:
* Avere un account Azure ([versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/))
* Avere installato e configurato l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2)

Sono anche necessari gli elementi seguenti, che in alternativa possono essere generati automaticamente durante la distribuzione del cluster tramite l'interfaccia della riga di comando di Azure:

* **Chiave pubblica SSH RSA**: se si vogliono creare chiavi SSH (Secure Shell) RSA in anticipo, vedere le indicazioni per [macOS e Linux](../virtual-machines/linux/mac-create-ssh-keys.md) o per [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **ID client e segreto dell'entità servizio**: per altre informazioni e la procedura necessaria per creare un'entità servizio di Azure Active Directory, vedere le [informazioni sull'entità servizio per un cluster Kubernetes](container-service-kubernetes-service-principal.md).

 Il comando di esempio contenuto in questo articolo genera automaticamente le chiavi SSH e l'entità servizio.

## <a name="create-your-kubernetes-cluster"></a>Creare il cluster Kubernetes

Di seguito vengono elencati brevi comandi di shell Bash che usano l'interfaccia della riga di comando di Azure 2.0 per creare il cluster. 

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Per creare un cluster è prima necessario creare un gruppo di risorse in una località in cui è [disponibile](https://azure.microsoft.com/regions/services/) il servizio contenitore di Azure. Eseguire comandi simili ai seguenti:

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Creare un cluster
Creare un cluster Kubernetes nel gruppo di risorse usando il comando `az acs create` con `--orchestrator-type=kubernetes`. Per la sintassi del comando, vedere le [informazioni della guida](/cli/azure/acs#create) per `az acs create`.

Questa versione del comando genera automaticamente le chiavi SSH RSA e l'entità servizio per il cluster Kubernetes.



```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```

Dopo alcuni minuti, il comando viene completato e l'utente deve disporre di un cluster Kubernetes funzionante.

> [!IMPORTANT]
> Se l'account non ha le autorizzazioni per creare l'entità servizio di Azure AD, il comando genera un errore simile a **I privilegi non sono sufficienti per completare l'operazione**. Per altre informazioni, vedere [About the service principal for a Kubernetes cluster](container-service-kubernetes-service-principal.md) (Informazioni sull'entità servizio per un cluster Kubernetes).
> 



### <a name="connect-to-the-cluster"></a>Connettersi al cluster

Per connettersi al cluster Kubernetes dal computer client si usa [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), il client della riga di comando di Kubernetes. 

Se non è già stato installato `kubectl`, è possibile installarlo con `az acs kubernetes install-cli`. Può anche essere scaricato dal [sito Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/).

```azurecli
sudo az acs kubernetes install-cli
```

> [!TIP]
> Per impostazione predefinita, questo comando installa il file binario `kubectl` in `/usr/local/bin/kubectl` su un sistema Linux o macOS o in `C:\Program Files (x86)\kubectl.exe` su Windows. Per specificare un percorso di installazione diverso, usare il parametro `--install-location`.
>
> Dopo aver installato `kubectl`, assicurarsi che la relativa directory sia nel percorso di sistema oppure aggiungerla al percorso. 
>


Quindi eseguire il comando seguente per scaricare la configurazione del cluster Kubernetes del master nel file `~/.kube/config`:

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

Per altre opzioni di installazione e per la configurazione di `kubectl`, vedere [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md).

A questo punto, sarà possibile accedere al cluster dal computer. Provare a eseguire:

```bash
kubectl get nodes
```

Verificare che i computer siano visualizzati nel cluster.

## <a name="create-your-first-kubernetes-service"></a>Creare il primo servizio Kubernetes

La procedura dettagliata illustra come eseguire queste operazioni:
* Distribuire un'applicazione Docker ed esporla a tutti gli utenti
* Usare `kubectl exec` per eseguire comandi in un contenitore 
* Accedere al dashboard Kubernetes

### <a name="start-a-container"></a>Avviare un contenitore
È possibile eseguire un contenitore, in questo caso il server Web Nginx, usando:

```bash
kubectl run nginx --image nginx
```

Questo comando avvia il contenitore Docker Nginx in un pod in uno dei nodi.

Per visualizzare il contenitore in esecuzione, eseguire:

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Esporre il servizio
Per esporre il servizio a tutti gli utenti, creare un `Service` Kubernetes di tipo `LoadBalancer`:

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Con questo comando Kubernetes creerà una regola di bilanciamento del carico di Azure con un indirizzo IP pubblico. La propagazione della modifica al servizio di bilanciamento del carico richiede alcuni minuti. Per altre informazioni, vedere [Bilanciare il carico dei contenitori in un cluster Kubernetes nel servizio contenitore di Azure](container-service-kubernetes-load-balancing.md).

Eseguire il comando seguente per modificare il servizio da `pending` alla visualizzazione di un indirizzo IP esterno:

```bash
watch 'kubectl get svc'
```

  ![Immagine della verifica della transizione da in sospeso a indirizzo IP esterno](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Dopo la visualizzazione dell'indirizzo IP esterno, è possibile passare all'indirizzo IP nel browser:

  ![Immagine del passaggio a Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Esplorare l'interfaccia utente Kubernetes
Per visualizzare l'interfaccia Web Kubernetes, è possibile usare:

```bash
kubectl proxy
```
Questo comando esegue in localhost un proxy autenticato, che è possibile usare per visualizzare l'interfaccia utente Web di Kubernetes in esecuzione in [http://localhost:8001/ui](http://localhost:8001/ui). Per altre informazioni, vedere [Uso dell'interfaccia utente Web Kubernetes con il servizio contenitore di Azure](container-service-kubernetes-ui.md).

![Immagine del dashboard Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sessioni remote nei contenitori
Kubernetes consente di eseguire comandi in un contenitore Docker remoto in esecuzione nel cluster.

```bash
# Get the name of your nginx pods
kubectl get pods
```

Usando il nome del pod, è possibile eseguire un comando remoto sul pod. Ad esempio:

```bash
kubectl exec <pod name> date
```

È anche possibile ottenere una sessione completamente interattiva usando i flag `-it`:

```bash
kubectl exec <pod name> -it bash
```

![Sessione remota in un contenitore](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Passaggi successivi

Per altre operazioni con il cluster Kubernetes, vedere le risorse seguenti:

* [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/): illustra come distribuire, ridimensionare ed eseguire il debug di applicazione inserite in contenitori.
* [Guida dell'utente di Kubernetes](http://kubernetes.io/docs/user-guide/): offre informazioni sull'esecuzione di programmi in un cluster Kubernetes esistente.
* [Esempi di Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples): fornisce esempi relativi all'esecuzione di applicazioni effettive con Kubernetes.

