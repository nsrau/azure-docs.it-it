---
title: Abilitare il componente aggiuntivo del controller di ingresso per un nuovo cluster AKS con una nuova istanza del gateway applicazione Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per abilitare il componente aggiuntivo del controller di ingresso per il nuovo cluster AKS con una nuova istanza del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 4634421829cf71c0c5b9476f8ff3d08b9caa7dbd
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117344"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Esercitazione: abilitare il componente aggiuntivo del controller di ingresso (anteprima) per un nuovo cluster AKS con una nuova istanza del gateway applicazione

È possibile usare l'interfaccia della riga di comando di Azure per abilitare il componente aggiuntivo [controller di ingresso del gateway applicazione (AGIC)](ingress-controller-overview.md) per un cluster [Azure KUBERNETES Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . Il componente aggiuntivo è attualmente in versione di anteprima.

In questa esercitazione verrà creato un cluster AKS con il componente aggiuntivo AGIC abilitato. La creazione del cluster creerà automaticamente un'istanza di applicazione Azure gateway da usare. Verrà quindi distribuita un'applicazione di esempio che userà il componente aggiuntivo per esporre l'applicazione tramite il gateway applicazione. 

Il componente aggiuntivo fornisce un modo più rapido per distribuire AGIC per il cluster AKS rispetto a quello [precedente tramite Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Offre inoltre un'esperienza completamente gestita.    

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un gruppo di risorse. 
> * Creare un nuovo cluster AKS con il componente aggiuntivo AGIC abilitato. 
> * Distribuire un'applicazione di esempio usando AGIC per il traffico in ingresso nel cluster AKS.
> * Verificare che l'applicazione sia raggiungibile tramite il gateway applicazione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Registrare il flag della funzionalità *AKS-IngressApplicationGatewayAddon* usando il comando [AZ feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , come illustrato nell'esempio seguente. È necessario eseguire questa operazione una sola volta per ogni sottoscrizione mentre il componente aggiuntivo è ancora in anteprima.
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato `Registered` . È possibile controllare lo stato di registrazione con il comando [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register):
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse Microsoft. servizio contenitore usando il comando [AZ provider Register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) :
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Installare o aggiornare l'estensione AKS-Preview per questa esercitazione. Usare i comandi CLI di Azure seguenti:
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. Creare un gruppo di risorse usando [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *canadacentral* (Region): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Distribuire un cluster AKS con il componente aggiuntivo abilitato

È ora possibile distribuire un nuovo cluster AKS con il componente aggiuntivo AGIC abilitato. Se non si fornisce un'istanza del gateway applicazione esistente da usare in questo processo, verrà creata e configurata automaticamente una nuova istanza del gateway applicazione per gestire il traffico al cluster AKS.  

> [!NOTE]
> Il componente aggiuntivo del controller di ingresso del gateway applicazione supporta *solo* SKU del gateway applicazione V2 (standard e WAF) e *non* gli SKU V1 del gateway applicazione. Quando si distribuisce una nuova istanza del gateway applicazione tramite il componente aggiuntivo AGIC, è possibile distribuire solo un gateway applicazione Standard_v2 SKU. Se si vuole abilitare il componente aggiuntivo per un gateway applicazione WAF_v2 SKU, usare uno dei metodi seguenti:
>
> - Abilitare WAF sul gateway applicazione tramite il portale. 
> - Creare prima di tutto l'istanza del gateway applicazione WAF_v2, quindi seguire le istruzioni su come [abilitare il componente aggiuntivo AGIC con un cluster AKS esistente e un'istanza del gateway applicazione esistente](tutorial-ingress-controller-add-on-existing.md). 

Nell'esempio seguente verrà distribuito un nuovo cluster AKS denominato *cluster* usando [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) e le [identità gestite](https://docs.microsoft.com/azure/aks/use-managed-identity). Il componente aggiuntivo AGIC verrà abilitato nel gruppo di risorse creato, *myResourceGroup*. 

La distribuzione di un nuovo cluster AKS con il componente aggiuntivo AGIC abilitato senza specificare un'istanza del gateway applicazione esistente comporterà la creazione automatica di un'istanza del gateway applicazione SKU Standard_v2. Sarà quindi necessario specificare anche il nome e lo spazio degli indirizzi della subnet dell'istanza del gateway applicazione. Il nome dell'istanza del gateway applicazione sarà *myApplicationGateway*e lo spazio di indirizzi della subnet usato è 10.2.0.0/16. Assicurarsi di aver aggiunto o aggiornato l'estensione AKS-Preview all'inizio di questa esercitazione. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Per configurare parametri aggiuntivi per il `az aks create` comando, vedere [questi riferimenti](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> Il cluster AKS creato verrà visualizzato nel gruppo di risorse creato, *myResourceGroup*. Tuttavia, l'istanza del gateway applicazione creata automaticamente si troverà nel gruppo di risorse nodo, in cui si trovano i pool di agenti. Per impostazione predefinita, il gruppo di risorse node by è denominato *MC_resource-Group-name_cluster-name_location* , ma può essere modificato. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Distribuire un'applicazione di esempio tramite AGIC

Verrà ora distribuita un'applicazione di esempio nel cluster AKS creato dall'utente. L'applicazione userà il componente aggiuntivo AGIC per il traffico in ingresso e collegherà l'istanza del gateway applicazione al cluster AKS. 

Per prima cosa, ottenere le credenziali per il cluster AKS eseguendo il `az aks get-credentials` comando: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Ora che sono disponibili le credenziali, eseguire il comando seguente per configurare un'applicazione di esempio che usa AGIC per il traffico in ingresso nel cluster. AGIC aggiornerà l'istanza del gateway applicazione configurata in precedenza con le regole di routing corrispondenti alla nuova applicazione di esempio distribuita.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verificare che l'applicazione sia raggiungibile

Ora che l'istanza del gateway applicazione è configurata in modo da gestire il traffico verso il cluster AKS, verificare che l'applicazione sia raggiungibile. Per prima cosa, ottenere l'indirizzo IP del traffico in ingresso: 

```azurecli-interactive
kubectl get ingress
```

Verificare che l'applicazione di esempio creata sia in esecuzione in uno dei seguenti casi:

- Visitando l'indirizzo IP dell'istanza del gateway applicazione ottenuta dall'esecuzione del comando precedente.
- Utilizzando `curl` . 

Il gateway applicazione può richiedere un minuto per ottenere l'aggiornamento. Se il gateway applicazione è ancora in stato di **aggiornamento** nel portale, lasciarlo completato prima di tentare di raggiungere l'indirizzo IP. 

## <a name="clean-up-resources"></a>Eseguire la pulizia delle risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, l'istanza del gateway applicazione e tutte le risorse correlate:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sulla disabilitazione del componente aggiuntivo AGIC](./ingress-controller-disable-addon.md)
* [Informazioni sulle annotazioni supportate con AGIC](./ingress-controller-annotations.md)
* [Risolvere i problemi relativi a AGIC](./ingress-controller-troubleshoot.md)

