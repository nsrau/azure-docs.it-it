---
title: 'Esercitazione: Abilitare il componente aggiuntivo Controller in ingresso per un nuovo cluster del servizio Azure Kubernetes con una nuova istanza del gateway applicazione di Azure'
description: Questa esercitazione illustra come usare l'interfaccia della riga di comando di Azure per abilitare il componente aggiuntivo Controller in ingresso per il nuovo cluster del servizio Azure Kubernetes con una nuova istanza del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: 43aadee627c7dc12a37a8f3895ba4dfed472808c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182904"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Esercitazione: Abilitare il componente aggiuntivo Controller in ingresso (anteprima) per un nuovo cluster del servizio Azure Kubernetes con una nuova istanza del gateway applicazione

È possibile usare l'interfaccia della riga di comando di Azure per abilitare il componente aggiuntivo [Controller in ingresso del gateway applicazione](ingress-controller-overview.md) per un cluster del [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/). Il componente aggiuntivo è attualmente disponibile in anteprima.

In questa esercitazione verrà creato un cluster del servizio Azure Kubernetes con il componente aggiuntivo Controller in ingresso del gateway applicazione abilitato. Quando si crea il cluster, viene creata automaticamente un'istanza del gateway applicazione di Azure da usare. Verrà quindi distribuita un'applicazione di esempio che userà il componente aggiuntivo per esporre l'applicazione tramite il gateway applicazione. 

Il componente aggiuntivo costituisce una soluzione di gran lunga più rapida per distribuire Controller in ingresso del gateway applicazione per il cluster del servizio Azure Kubernetes rispetto a [quella precedente basata su Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Offre inoltre un'esperienza completamente gestita.    

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un gruppo di risorse. 
> * Creare un nuovo cluster del servizio Azure Kubernetes con il componente aggiuntivo Controller in ingresso del gateway applicazione abilitato. 
> * Distribuire un'applicazione di esempio usando Controller in ingresso del gateway applicazione per il traffico in ingresso nel cluster del servizio Azure Kubernetes.
> * Verificare che l'applicazione sia raggiungibile tramite il gateway applicazione.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Per questa esercitazione è necessaria la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

 - Per registrare il flag di funzionalità *AKS-IngressApplicationGatewayAddon*, usare il comando [az feature register](/cli/azure/feature#az-feature-register), come illustrato nell'esempio seguente. È necessario eseguire questa operazione una sola volta per ogni sottoscrizione finché il componente aggiuntivo è disponibile in anteprima.
    ```azurecli-interactive
    az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
    ```

   Dopo alcuni minuti dovrebbe essere visualizzato lo stato `Registered`. È possibile controllare lo stato di registrazione con il comando [az feature list](/cli/azure/feature#az-feature-register):
    ```azurecli-interactive
    az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
    ```

 - Quando si è pronti, aggiornare la registrazione del provider di risorse Microsoft.ContainerService usando il comando [az provider register](/cli/azure/provider#az-provider-register):
    ```azurecli-interactive
    az provider register --namespace Microsoft.ContainerService
    ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. Creare un gruppo di risorse usando [az group create](/cli/azure/group#az-group-create). L'esempio seguente consente di creare un gruppo di risorse denominato *myResourceGroup* nella località (area) *canadacentral*: 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Distribuire un cluster del servizio Azure Kubernetes con il componente aggiuntivo abilitato

Verrà ora distribuito un nuovo cluster del servizio Azure Kubernetes con il componente aggiuntivo Controller in ingresso del gateway applicazione abilitato. Se non si specifica un'istanza del gateway applicazione esistente da usare in questo processo, verrà creata e configurata automaticamente una nuova istanza del gateway applicazione per gestire il traffico indirizzato al cluster del servizio Azure Kubernetes.  

> [!NOTE]
> Il componente aggiuntivo Controller in ingresso del gateway applicazione supporta *solo* SKU del gateway applicazione V2 (Standard e WAF) e *non* SKU del gateway applicazione V1. Quando si distribuisce una nuova istanza del gateway applicazione tramite il componente aggiuntivo Controller in ingresso del gateway applicazione, è possibile distribuire solo uno SKU del gateway applicazione Standard_v2. Se si vuole abilitare il componente aggiuntivo per uno SKU WAF_v2 del gateway applicazione, usare uno di questi metodi:
>
> - Abilitare WAF nel gateway applicazione tramite il portale. 
> - Creare prima di tutto l'istanza del gateway applicazione WAF_v2 e quindi seguire le istruzioni su come [abilitare il componente aggiuntivo Controller in ingresso del gateway applicazione con un cluster del servizio Azure Kubernetes esistente e l'istanza del gateway applicazione esistente](tutorial-ingress-controller-add-on-existing.md). 

Nell'esempio seguente verrà distribuito un nuovo cluster del servizio Azure Kubernetes denominato *myCluster* usando [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) e le [identità gestite](../aks/use-managed-identity.md). Il componente aggiuntivo Controller in ingresso del gateway applicazione verrà abilitato nel gruppo di risorse creato, ovvero *myResourceGroup*. 

Se si distribuisce un nuovo cluster del servizio Azure Kubernetes con il componente aggiuntivo Controller in ingresso del gateway applicazione abilitato senza specificare un'istanza del gateway applicazione esistente, verrà creata automaticamente un'istanza del gateway applicazione con SKU Standard_v2. Sarà quindi necessario specificare anche il nome e lo spazio indirizzi della subnet dell'istanza del gateway applicazione. Il nome dell'istanza del gateway applicazione sarà *myApplicationGateway* e lo spazio indirizzi della subnet che verrà usato è 10.2.0.0/16. Assicurarsi di aver aggiunto o aggiornato l'estensione aks-preview all'inizio di questa esercitazione. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" --generate-ssh-keys
```

Per configurare parametri aggiuntivi per il comando `az aks create`, vedere [questi argomenti di riferimento](/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> Il cluster del servizio Azure Kubernetes creato verrà visualizzato nel gruppo di risorse creato, ovvero *myResourceGroup*. Tuttavia, l'istanza del gateway applicazione creata automaticamente si troverà nel gruppo di risorse del nodo, in cui si trovano i pool di agenti. Per impostazione predefinita, il nome del gruppo di risorse del nodo è *MC_resource-group-name_cluster-name_location*, ma può essere modificato. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Distribuire un'applicazione di esempio con Controller in ingresso del gateway applicazione

Verrà ora distribuita un'applicazione di esempio nel cluster del servizio Azure Kubernetes creato. L'applicazione userà il componente aggiuntivo Controller in ingresso del gateway applicazione per il traffico in ingresso e connetterà l'istanza del gateway applicazione al cluster del servizio Azure Kubernetes. 

Per prima cosa, ottenere le credenziali per il cluster del servizio Azure Kubernetes eseguendo il comando `az aks get-credentials`: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Ora che le credenziali sono disponibili, eseguire il comando seguente per configurare un'applicazione di esempio che usa Controller in ingresso del gateway applicazione per il traffico in ingresso nel cluster. Controller in ingresso del gateway applicazione aggiornerà l'istanza del gateway applicazione, configurata in precedenza con le regole di gestione corrispondenti, alla nuova applicazione di esempio distribuita.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verificare che l'applicazione sia raggiungibile

Ora che l'istanza del gateway applicazione è configurata in modo da gestire il traffico indirizzato al cluster del servizio Azure Kubernetes, verificare che l'applicazione sia raggiungibile. Ottenere innanzitutto l'indirizzo IP dell'ingresso: 

```azurecli-interactive
kubectl get ingress
```

Verificare che l'applicazione di esempio creata sia in esecuzione in uno dei modi seguenti:

- Visitare l'indirizzo IP dell'istanza del gateway applicazione ottenuto eseguendo il comando precedente.
- Usare `curl`. 

Il recupero dell'aggiornamento del gateway applicazione può richiedere un minuto. Se il gateway applicazione si trova ancora nello stato **Aggiornamento** nel portale, attendere il completamento dell'operazione prima di provare a raggiungere l'indirizzo IP. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, l'istanza del gateway applicazione e tutte le risorse correlate:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulla disabilitazione del componente aggiuntivo Controller in ingresso del gateway applicazione](./ingress-controller-disable-addon.md)