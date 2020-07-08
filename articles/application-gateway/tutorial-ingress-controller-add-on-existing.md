---
title: Abilita il componente aggiuntivo del controller di ingresso per il cluster AKS esistente con il gateway di applicazione Azure esistente
description: Usare questa esercitazione per abilitare il componente aggiuntivo del controller di ingresso per il cluster AKS esistente con un gateway applicazione esistente
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 6cbfac4794a685e5858e689c20d6603807edcedf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987977"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>Esercitazione: abilitare il componente aggiuntivo del controller di ingresso del gateway applicazione per un cluster AKS esistente con un gateway applicazione esistente tramite l'interfaccia della riga di comando di Azure (anteprima)

È possibile usare l'interfaccia della riga di comando di Azure per abilitare il componente aggiuntivo [controller di ingresso del gateway applicazione (AGIC)](ingress-controller-overview.md) , attualmente in anteprima, per il cluster di [servizi Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . Questa esercitazione illustra come usare il componente aggiuntivo AGIC per esporre l'applicazione Kubernetes in un cluster AKS esistente tramite un gateway applicazione esistente distribuito in reti virtuali separate. Si inizierà creando un cluster AKS in una rete virtuale e un gateway applicazione in una rete virtuale separata per simulare le risorse esistenti. Sarà quindi possibile abilitare il componente aggiuntivo AGIC, eseguire il peering delle due reti virtuali insieme e distribuire un'applicazione di esempio che verrà esposta tramite il gateway applicazione usando il componente aggiuntivo AGIC. Se si Abilita il componente aggiuntivo AGIC per un gateway applicazione esistente e un cluster AKS esistente nella stessa rete virtuale, è possibile ignorare il passaggio del peering riportato di seguito. Il componente aggiuntivo fornisce un metodo molto più rapido per la distribuzione di AGIC per il cluster AKS rispetto a quello [precedente tramite Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) e offre anche un'esperienza completamente gestita.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un gruppo di risorse 
> * Creare un nuovo cluster AKS 
> * Creare un nuovo gateway applicazione 
> * Abilitare il componente aggiuntivo AGIC nel cluster AKS esistente usando il gateway applicazione esistente 
> * Peering della rete virtuale del gateway applicazione con la rete virtuale del cluster AKS
> * Distribuire un'applicazione di esempio usando AGIC per il traffico in ingresso nel cluster AKS
> * Verificare che l'applicazione sia raggiungibile tramite il gateway applicazione

Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successive. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Registrare il flag della funzionalità *AKS-IngressApplicationGatewayAddon* usando il comando [AZ feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , come illustrato nell'esempio seguente: Questa operazione dovrà essere eseguita una sola volta per ogni sottoscrizione mentre il componente aggiuntivo è ancora in anteprima:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato registrato. È possibile controllare lo stato di registrazione usando il comando [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register):
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse Microsoft.ContainerService usando il comando [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register):
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Assicurarsi di installare/aggiornare l'estensione AKS-Preview per questa esercitazione. usare i seguenti comandi dell'interfaccia della riga di comando di Azure
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. Creare un gruppo di risorse usando [az group create](/cli/azure/group#az-group-create). Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nella località *canadacentral* (Region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Distribuire un nuovo cluster AKS

Verrà ora distribuito un nuovo cluster AKS per simulare la presenza di un cluster AKS esistente per il quale si vuole abilitare il componente aggiuntivo AGIC.  

Nell'esempio seguente verrà distribuito un nuovo cluster AKS denominato *cluster* con [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) e le [identità gestite](https://docs.microsoft.com/azure/aks/use-managed-identity) nel gruppo di risorse creato, *myResourceGroup*.    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Per configurare parametri aggiuntivi per il `az aks create` comando, vedere i riferimenti [qui](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Distribuire un nuovo gateway applicazione 

Verrà ora distribuito un nuovo gateway applicazione, per simulare la presenza di un gateway applicazione esistente che si vuole usare per bilanciare il carico del traffico verso il cluster AKS, ovvero il *cluster*. Il nome del gateway applicazione sarà *myApplicationGateway*, ma sarà necessario creare prima di tutto una risorsa IP pubblica, denominata *myPublicIp*, e una nuova rete virtuale denominata *myVnet* con lo spazio degli indirizzi 11.0.0.0/8 e una subnet con spazio di indirizzi 11.1.0.0/16 denominata *subnet*e distribuire il gateway applicazione in *subnet* usando *myPublicIp*. 

Quando si usa un cluster AKS e un gateway applicazione in reti virtuali separate, gli spazi di indirizzi delle due reti virtuali non devono sovrapporsi. Lo spazio di indirizzi predefinito distribuito da un cluster AKS è 10.0.0.0/8, quindi il prefisso dell'indirizzo della rete virtuale del gateway applicazione viene impostato su 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Il componente aggiuntivo del controller di ingresso del gateway applicazione (AGIC) supporta **solo** SKU del gateway applicazione V2 (standard e WAF) e **non** gli SKU V1 del gateway applicazione. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>Abilitare il componente aggiuntivo AGIC nel cluster AKS esistente con il gateway applicazione esistente 

A questo punto, si Abilita il componente aggiuntivo AGIC nel cluster AKS creato, in *cluster*e si specifica il componente aggiuntivo AGIC per usare il gateway applicazione esistente creato, *myApplicationGateway*. Assicurarsi di aver aggiunto o aggiornato l'estensione AKS-Preview all'inizio di questa esercitazione. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>Peering tra due reti virtuali

Poiché il cluster AKS è stato distribuito nella propria rete virtuale e nel gateway applicazione in un'altra rete virtuale, sarà necessario eseguire il peering delle due reti virtuali per consentire il flusso del traffico dal gateway applicazione ai pod del cluster. Il peering delle due reti virtuali richiede l'esecuzione del comando dell'interfaccia della riga di comando di Azure due volte separate, per garantire che la connessione sia bidirezionale. Il primo comando creerà una connessione peering dalla rete virtuale del gateway applicazione alla rete virtuale AKS; il secondo comando creerà una connessione di peering nell'altra direzione. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>Distribuire un'applicazione di esempio con AGIC 

Verrà ora distribuita un'applicazione di esempio nel cluster AKS creato che userà il componente aggiuntivo AGIC per il traffico in ingresso e collegherà il gateway applicazione al cluster AKS. In primo luogo, si otterranno le credenziali per il cluster AKS distribuito eseguendo il `az aks get-credentials` comando. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Dopo aver ottenuto le credenziali per il cluster creato, eseguire il comando seguente per configurare un'applicazione di esempio che usa AGIC per il traffico in ingresso nel cluster. AGIC aggiornerà il gateway applicazione configurato in precedenza con le regole di routing corrispondenti alla nuova applicazione di esempio distribuita.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verificare che l'applicazione sia raggiungibile

Ora che il gateway applicazione è configurato per gestire il traffico verso il cluster AKS, è possibile verificare che l'applicazione sia raggiungibile. Si otterrà prima l'indirizzo IP del traffico in ingresso. 

```azurecli-interactive
kubectl get ingress
```

Verificare che l'applicazione di esempio creata sia in esecuzione, visitando l'indirizzo IP del gateway applicazione ottenuto eseguendo il comando precedente o verificando `curl` . Il gateway applicazione può richiedere un minuto per ottenere l'aggiornamento, quindi se lo stato del gateway applicazione è ancora in corso di aggiornamento nel portale, è necessario completare l'operazione prima di tentare di raggiungere l'indirizzo IP. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sulla disabilitazione del componente aggiuntivo AGIC](./ingress-controller-disable-addon.md)
* [Altre informazioni sulle annotazioni supportate con AGIC](./ingress-controller-annotations.md)
* [Risolvere i problemi relativi a AGIC](./ingress-controller-troubleshoot.md)

