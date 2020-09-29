---
title: 'Esercitazione: Abilitare il componente aggiuntivo Controller in ingresso per un cluster esistente del servizio Azure Kubernetes con il gateway applicazione di Azure esistente'
description: Questa esercitazione consente di abilitare il componente aggiuntivo Controller in ingresso per il cluster del servizio Azure Kubernetes esistente con un gateway applicazione esistente
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: d0ce58c5bb6de4712117959f10b48ae3449f0b97
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285656"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>Esercitazione: Abilitare il componente aggiuntivo Controller in ingresso del gateway applicazione per un cluster del servizio Azure Kubernetes esistente con un gateway applicazione esistente tramite l'interfaccia della riga di comando di Azure (anteprima)

È possibile usare l'interfaccia della riga di comando di Azure per abilitare il componente aggiuntivo [Controller in ingresso del gateway applicazione](ingress-controller-overview.md), attualmente disponibile in anteprima, per il cluster del [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/). Questa esercitazione illustra come usare il componente aggiuntivo Controller in ingresso del gateway applicazione per esporre l'applicazione Kubernetes in un cluster del servizio Azure Kubernetes esistente tramite un gateway applicazione esistente distribuito in reti virtuali separate. Per iniziare, si creerà un cluster del servizio Azure Kubernetes in una rete virtuale e un gateway applicazione in una rete virtuale separata per simulare le risorse esistenti. Sarà quindi possibile abilitare il componente aggiuntivo Controller in ingresso del gateway applicazione, eseguire il peering combinato delle due reti virtuali e distribuire un'applicazione di esempio che verrà esposta tramite il gateway applicazione usando il componente aggiuntivo Controller in ingresso del gateway applicazione. Se si abilita il componente aggiuntivo Controller in ingresso del gateway applicazione per un gateway applicazione esistente e un cluster del servizio Azure Kubernetes esistente nella stessa rete virtuale, è possibile ignorare il passaggio relativo al peering illustrato di seguito. Il componente aggiuntivo costituisce una soluzione di gran lunga più rapida per distribuire Controller in ingresso del gateway applicazione per il cluster del servizio Azure Kubernetes rispetto a [quella precedente basata su Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) e offre anche un'esperienza completamente gestita.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un gruppo di risorse 
> * Creare un nuovo cluster del servizio Azure Kubernetes 
> * Creare un nuovo gateway applicazione 
> * Abilitare il componente aggiuntivo Controller in ingresso del gateway applicazione nel cluster del servizio Azure Kubernetes esistente con il gateway applicazione esistente 
> * Eseguire il peering della rete virtuale del gateway applicazione con la rete virtuale del cluster del servizio Azure Kubernetes
> * Distribuire un'applicazione di esempio usando Controller in ingresso del gateway applicazione per il traffico in ingresso nel cluster del servizio Azure Kubernetes
> * Verificare che l'applicazione sia raggiungibile tramite il gateway applicazione

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successive. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per registrare il flag di funzionalità *AKS-IngressApplicationGatewayAddon*, usare il comando [az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) come illustrato nell'esempio seguente. È necessario eseguire questa operazione una sola volta per ogni sottoscrizione finché il componente aggiuntivo è disponibile in anteprima.
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Potrebbe essere necessario attendere alcuni minuti prima che venga visualizzato lo stato Registered. È possibile controllare lo stato di registrazione usando il comando [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register):
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.containerservice/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse Microsoft.ContainerService usando il comando [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register):
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Assicurarsi di installare/aggiornare l'estensione aks-preview per questa esercitazione. A tale scopo, usare i comandi seguenti dell'interfaccia della riga di comando di Azure
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. Creare un gruppo di risorse usando [az group create](/cli/azure/group#az-group-create). L'esempio seguente consente di creare un gruppo di risorse denominato *myResourceGroup* nella località (area) *canadacentral*. 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Distribuire un nuovo cluster del servizio Azure Kubernetes

Verrà ora distribuito un nuovo cluster del servizio Azure Kubernetes per simulare la presenza di un cluster del servizio Azure Kubernetes esistente per il quale si vuole abilitare il componente aggiuntivo Controller in ingresso del gateway applicazione.  

Nell'esempio seguente verrà distribuito un nuovo cluster del servizio Azure Kubernetes denominato *myCluster* usando [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) e [Identità gestite](https://docs.microsoft.com/azure/aks/use-managed-identity) nel gruppo di risorse creato, ovvero *myResourceGroup*.    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Per configurare parametri aggiuntivi per il comando `az aks create`, vedere [questi argomenti di riferimento](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Distribuire un nuovo gateway applicazione 

Verrà ora distribuito un nuovo gateway applicazione per simulare la presenza di un gateway applicazione esistente da usare per bilanciare il carico del traffico indirizzato al cluster del servizio Azure Kubernetes, ovvero *myCluster*. Il nome del gateway applicazione sarà *myApplicationGateway*, ma è prima necessario creare una risorsa IP pubblico denominata *myPublicIp* e una nuova rete virtuale denominata *myVnet* con spazio indirizzi 11.0.0.0/8, nonché una subnet con spazio indirizzi 11.1.0.0/16 denominata *mySubnet* e quindi distribuire il gateway applicazione in *mySubnet* usando *myPublicIp*. 

Quando si usa un cluster del servizio Azure Kubernetes e un gateway applicazione in reti virtuali separate, gli spazi indirizzi delle due reti virtuali non devono sovrapporsi. Lo spazio indirizzi predefinito distribuito da un cluster del servizio Azure Kubernetes è 10.0.0.0/8, di conseguenza il prefisso dell'indirizzo della rete virtuale del gateway applicazione è impostato su 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Il componente aggiuntivo Controller in ingresso del gateway applicazione supporta **solo** SKU del gateway applicazione V2 (Standard e WAF) e **non** SKU del gateway applicazione V1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>Abilitare il componente aggiuntivo Controller in ingresso del gateway applicazione nel cluster del servizio Azure Kubernetes esistente con il gateway applicazione esistente 

A questo punto, verrà abilitato il componente aggiuntivo Controller in ingresso del gateway applicazione nel cluster del servizio Azure Kubernetes creato, ovvero *myCluster*, e verrà specificato il componente aggiuntivo Controller in ingresso del gateway applicazione per usare il gateway applicazione esistente creato, ovvero *myApplicationGateway*. Assicurarsi di aver aggiunto o aggiornato l'estensione aks-preview all'inizio di questa esercitazione. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>Eseguire il peering combinato delle due reti virtuali

Dal momento che il cluster del servizio Azure Kubernetes è stato distribuito nella propria rete virtuale e il gateway applicazione è stato distribuito in un'altra rete virtuale, sarà necessario eseguire il peering delle due reti virtuali per consentire il flusso del traffico dal gateway applicazione ai pod nel cluster. Per eseguire il peering delle due reti virtuali, è necessario eseguire per due volte il comando dell'interfaccia della riga di comando di Azure per garantire che la connessione sia bidirezionale. Il primo comando creerà una connessione di peering dalla rete virtuale del gateway applicazione alla rete virtuale del servizio Azure Kubernetes; il secondo comando creerà una connessione di peering nell'altra direzione. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>Distribuire un'applicazione di esempio con Controller in ingresso del gateway applicazione 

Verrà ora distribuita un'applicazione di esempio nel cluster del servizio Azure Kubernetes creato che userà il componente aggiuntivo Controller in ingresso del gateway applicazione per il traffico in ingresso e connetterà il gateway applicazione al cluster del servizio Azure Kubernetes. Per prima cosa, ottenere le credenziali per il cluster del servizio Azure Kubernetes distribuito eseguendo il comando `az aks get-credentials`. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Ora che le credenziali per il cluster creato sono disponibili, eseguire il comando seguente per configurare un'applicazione di esempio che usa Controller in ingresso del gateway applicazione per il traffico in ingresso nel cluster. Controller in ingresso del gateway applicazione aggiornerà il gateway applicazione, configurato in precedenza con le regole di gestione corrispondenti, alla nuova applicazione di esempio distribuita.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verificare che l'applicazione sia raggiungibile

Ora che il gateway applicazione è configurato in modo da gestire il traffico indirizzato al cluster del servizio Azure Kubernetes, verificare che l'applicazione sia raggiungibile. Ottenere innanzitutto l'indirizzo IP dell'ingresso. 

```azurecli-interactive
kubectl get ingress
```

Verificare che l'applicazione di esempio creata sia in esecuzione, visitando l'indirizzo IP del gateway applicazione ottenuto eseguendo il comando precedente o verificandolo con `curl`. Il recupero dell'aggiornamento del gateway applicazione può richiedere un minuto. Se il gateway applicazione si trova ancora nello stato "Aggiornamento" nel portale, attendere il completamento dell'operazione prima di provare a raggiungere l'indirizzo IP. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sulla disabilitazione del componente aggiuntivo Controller in ingresso del gateway applicazione](./ingress-controller-disable-addon.md)