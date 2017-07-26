---
title: Esercitazione sul servizio contenitore di Azure - Distribuire un cluster | Microsoft Docs
description: Esercitazione sul servizio contenitore di Azure - Distribuire un cluster
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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 9735d204413d4fff182adc8799074510757f83a7
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Distribuire un cluster Kubernetes nel servizio contenitore di Azure

Kubernetes fornisce una piattaforma distribuita per l'esecuzione di applicazioni moderne e nei contenitori. Con il servizio contenitore di Azure, il provisioning di un cluster Kubernetes pronto per la produzione è semplice e rapido. I passaggi base necessari per distribuire un cluster Kubernetes sono descritti in dettaglio in questo avvio rapido. I passaggi completati comprendono:

> [!div class="checklist"]
> * Distribuzione di un cluster del servizio contenitore di Azure Kubernetes
> * Installazione dell'interfaccia della riga di comando Kubernetes (kubectl)
> * Configurazione di kubectl

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti le immagini del contenitore sono state create e caricate in un'istanza del Registro contenitori di Azure. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-kubernetes-cluster"></a>Creare un cluster Kubernetes

Nell'[esercitazione precedente](./container-service-tutorial-kubernetes-prepare-acr.md) è stato creato un gruppo di risorse denominato *myResourceGroup*. Se questa operazione non è stata ancora eseguita, creare ora il gruppo di risorse.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Creare un cluster Kubernetes nel servizio contenitore di Azure con il comando [az acs create](/cli/azure/acs#create). 

L'esempio seguente crea un cluster denominato *myK8sCluster* con un nodo master Linux e tre nodi agente Linux. Se non esistono già, vengono create le chiavi SSH. Per usare un set specifico di chiavi in un percorso non predefinito, usare l'opzione `--ssh-key-value`.

```azurecli-interactive 
az acs create --orchestrator-type=kubernetes --resource-group myResourceGroup --name=myK8SCluster --generate-ssh-keys 
```

Dopo alcuni minuti, il comando viene completato e restituisce le informazioni sulla distribuzione del servizio contenitore di Azure.

## <a name="install-the-kubectl-cli"></a>Installare l'interfaccia della riga di comando di kubectl

Per connettersi al cluster Kubernetes dal computer client, usare [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), il client da riga di comando di Kubernetes. 

Se si usa Azure CloudShell, `kubectl` è già installato. Se lo si vuole installare in locale, usare il comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Se è in esecuzione in Linux o MacOS, potrebbe essere necessario procedere all'esecuzione con sudo. In Windows accertarsi che la shell sia stata eseguita come amministratore.

```azurecli-interactive 
az acs kubernetes install-cli 
```

In Windows l'installazione predefinita è *c:\program files (x86)\kubectl.exe*. Potrebbe essere necessario aggiungere questo file al percorso di Windows. 

## <a name="connect-with-kubectl"></a>Connettersi con kubectl

Per configurare `kubectl` per connettersi al cluster Kubernetes, eseguire il comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8SCluster
```

Per verificare la connessione al cluster, eseguire il comando [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get nodes
```

Output:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Nella competizione dell'esercitazione è disponibile un cluster Kubernetes del servizio contenitore di Azure pronto per il carico di lavoro. Nelle esercitazioni successive, in questo cluster viene distribuita un'applicazione multi-contenitore, quindi viene scalata orizzontalmente, aggiornata e monitorata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un cluster Kubernetes del servizio contenitore di Azure. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione di un cluster del servizio contenitore di Azure Kubernetes
> * Installazione dell'interfaccia della riga di comando Kubernetes (kubectl)
> * Configurazione di kubectl

Passare all'esercitazione successiva per apprendere come eseguire l'applicazione nel cluster.

> [!div class="nextstepaction"]
> [Distribuire un'applicazione in Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
