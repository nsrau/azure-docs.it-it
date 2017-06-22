---
title: Cluster Azure Kubernetes per Windows | Microsoft Docs
description: Distribuire e iniziare a usare un cluster Kubernetes per contenitori Windows nel servizio contenitore di Azure
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Introduzione ai contenitori Kubernetes e Windows nel servizio contenitore


Questo articolo illustra come creare nel servizio contenitore di Azure un cluster Kubernetes che include nodi Windows per l'esecuzione di contenitori Windows. Per iniziare, usare i comandi `az acs` dell'interfaccia della riga di comando di Azure 2.0 per creare il cluster Kubernetes nel servizio contenitore di Azure. Usare quindi lo strumento da riga di comando `kubectl` di Kubernetes per iniziare a usare contenitori Windows creati da immagini Docker. 

> [!NOTE]
> Il supporto per i contenitori Windows con Kubernetes è disponibile in anteprima nel servizio contenitore di Azure. 
>



Nell'immagine seguente è illustrata l'architettura di un cluster Kubernetes nel servizio contenitore di Azure con un nodo master Linux e due nodi agente Windows. 

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* Il nodo master Linux è usato dall'API REST Kubernetes ed è accessibile da SSH alla porta 22 o da `kubectl` alla porta 443. 
* I nodi agente Windows vengono raggruppati in un set di disponibilità di Azure ed eseguono i contenitori. I nodi Windows sono accessibili tramite un tunnel SSH RDP tramite il nodo master. Le regole del servizio di bilanciamento del carico di Azure vengono aggiunte in modo dinamico al cluster in base ai servizi esposti.



Tutte le macchine virtuali si trovano nella stessa rete privata virtuale e sono completamente accessibili le une alle altre. Tutte le macchine virtuali eseguono kubelet, Docker e un proxy.

Per altre informazioni, vedere l'[introduzione al servizio contenitore di Azure](container-service-intro.md) e la [documentazione su Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Prerequisiti
Per creare un cluster del servizio contenitore di Azure usando l'interfaccia della riga di comando di Azure 2.0, è necessario:
* Avere un account Azure ([versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/))
* Aver installato l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso

Per il cluster Kubernetes sono necessari anche gli elementi riportati di seguito. Possono essere preparati in anticipo oppure essere generati automaticamente durante la distribuzione del cluster usando le opzioni del comando `az acs create`. 

* **Chiave pubblica SSH RSA**: se si vogliono creare chiavi SSH (Secure Shell) RSA, vedere le indicazioni per [macOS e Linux](../virtual-machines/linux/mac-create-ssh-keys.md) o per [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **ID client e segreto dell'entità servizio**: per altre informazioni e la procedura necessaria per creare un'entità servizio di Azure Active Directory, vedere le [informazioni sull'entità servizio per un cluster Kubernetes](container-service-kubernetes-service-principal.md).

Il comando di esempio contenuto in questo articolo genera automaticamente le chiavi SSH e l'entità servizio.
  
## <a name="create-your-kubernetes-cluster"></a>Creare il cluster Kubernetes

Di seguito sono riportati i comandi dell'interfaccia della riga di comando di Azure 2.0 per creare il cluster. 

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse in una località in cui è [disponibile](https://azure.microsoft.com/regions/services/) il servizio contenitore di Azure. Il comando seguente crea un gruppo di risorse denominato *myKubernetesResourceGroup* nella località *westus*:

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Creare un cluster Kubernetes con nodi agente Windows

Creare un cluster Kubernetes nel gruppo di risorse usando il comando `az acs create` con `--orchestrator-type=kubernetes` e l'opzione `--windows` per gli agenti. Per la sintassi del comando, vedere le [informazioni della guida](/cli/azure/acs#create) per `az acs create`.

Il comando seguente crea un cluster del servizio contenitore denominato *myKubernetesClusterName*, con prefisso DNS *myPrefix* per il nodo di gestione e le credenziali specificate per raggiungere i nodi Windows. Questa versione del comando genera automaticamente le chiavi SSH RSA e l'entità servizio per il cluster Kubernetes.


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

Dopo alcuni minuti, il comando viene completato e l'utente deve disporre di un cluster Kubernetes funzionante.

> [!IMPORTANT]
> Se l'account dell'utente non ha le autorizzazioni necessarie per creare l'entità servizio di Azure AD, il comando genera un errore simile a `Insufficient privileges to complete the operation.` Per altre informazioni, vedere le [informazioni sull'entità servizio per un cluster Kubernetes](container-service-kubernetes-service-principal.md). 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>Connettersi al cluster con kubectl

Per connettersi al cluster Kubernetes dal computer client, usare [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), il client da riga di comando di Kubernetes. 

Se `kubectl` non è installato in locale, può essere installato con `az acs kubernetes install-cli`. Può anche essere scaricato dal [sito Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/).

**Linux o macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> Per impostazione predefinita, questo comando installa il file binario di `kubectl` in `/usr/local/bin/kubectl` su un sistema Linux o macOS oppure in `C:\Program Files (x86)\kubectl.exe` su Windows. Per specificare un percorso di installazione diverso, usare il parametro `--install-location`.
>
> Dopo aver installato `kubectl`, verificare che la relativa directory si trovi nel percorso di sistema oppure aggiungerla al percorso. 


Eseguire quindi questo comando per scaricare la configurazione del cluster Kubernetes del master nel file `~/.kube/config` locale:

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

A questo punto è possibile accedere al cluster dal computer. Provare a eseguire:

```bash
kubectl get nodes
```

Verificare che i computer siano visualizzati nel cluster.

![Nodi in esecuzione in un cluster Kubernetes](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>Creare il primo servizio Kubernetes

Dopo aver creato il cluster e aver eseguito la connessione con `kubectl`, provare ad avviare un'app Windows da un contenitore Docker ed esporla a Internet. Questo esempio di base usa un file JSON per specificare un contenitore Microsoft Internet Information Server (IIS) e quindi lo crea con `kubctl apply`. 

1. Creare un file locale denominato `iis.json` e copiare quanto segue. Questo file indica a Kubernetes di eseguire IIS su Windows Server 2016 Server Core usando un'immagine pubblica dall'[hub Docker](https://hub.docker.com/r/microsoft/iis/). Il contenitore usa la porta 80, ma inizialmente è accessibile solo all'interno della rete di cluster.

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
          "image": "microsoft/iis",
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
2. Per avviare l'applicazione, digitare:  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. Per tenere traccia della distribuzione del contenitore, digitare:  
  ```bash
  kubectl get pods
  ```
  Durante la distribuzione del contenitore, lo stato è `ContainerCreating`. 

  ![Contenitore IIS con stato ContainerCreating](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  A causa delle dimensioni dell'immagine IIS, prima che il contenitore passi allo stato `Running` possono trascorrere alcuni minuti.

  ![Contenitore IIS con stato Running](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. Per esporre pubblicamente il contenitore, digitare il comando seguente:

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  Con questo comando, Kubernetes crea una regola di bilanciamento del carico di Azure con un indirizzo IP pubblico. La propagazione della modifica al servizio di bilanciamento del carico richiede alcuni minuti. Per informazioni dettagliate, vedere [Bilanciare il carico dei contenitori in un cluster Kubernetes nel servizio contenitore di Azure](container-service-kubernetes-load-balancing.md).

5. Eseguire questo comando per visualizzare lo stato del servizio.

  ```bash
  kubectl get svc
  ```

  L'indirizzo IP viene inizialmente visualizzato come `pending`:

  ![Indirizzo IP esterno in sospeso](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  Dopo alcuni minuti, l'indirizzo IP risulta impostato:
  
  ![Indirizzo IP esterno per IIS](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. Quando l'indirizzo IP esterno è disponibile, è possibile passare all'indirizzo IP nel browser:

  ![Immagine del passaggio a IIS](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. Per eliminare il pod IIS, digitare:

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>Passaggi successivi

* Per usare l'interfaccia utente di Kubernetes, eseguire il comando `kubectl proxy`. Passare quindi a http://localhost:8001/ui.

* Per la procedura necessaria per creare un sito Web IIS personalizzato ed eseguirlo in un contenitore Windows, vedere le indicazioni disponibili nell'[hub Docker](https://hub.docker.com/r/microsoft/iis/).

* Per accedere ai nodi Windows tramite un tunnel SSH RDP al master con PuTTy, vedere la [documentazione sul motore del servizio contenitore di Azure](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master). 

