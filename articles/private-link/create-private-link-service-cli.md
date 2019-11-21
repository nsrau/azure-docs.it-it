---
title: Create an Azure Private Link service using Azure CLI
description: Learn how to create an Azure Private Link service using Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 3cc171ddabbe8241622d4e599b4b3cd281558976
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229369"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Create a Private Link service using Azure CLI
This article shows you how to create a Private Link service in Azure using Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you decide to install and use Azure CLI locally instead, this quickstart requires you to use the latest Azure CLI version. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato
### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per poter creare una rete virtuale, è prima necessario creare un gruppo di risorse per l'hosting della rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). This example creates a resource group named *myResourceGroup* in the *westcentralus* location:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Crea una rete virtuale
Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). This example creates a default virtual network named *myVirtualNetwork* with one subnet named *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Creare una subnet
Create a subnet for the virtual network with [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). This example creates a subnet named *mySubnet* in the *myVirtualNetwork* virtual network:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Create a Internal Load Balancer 
Create a internal load balancer with [az network lb create](/cli/azure/network/lb#az-network-lb-create). This example creates a internal load balancer named *myILB* in resource group named *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Creare un probe di integrità per il servizio di bilanciamento del carico

Un probe di integrità controlla tutte le istanze di una macchina virtuale per assicurarsi che possano ricevere il traffico di rete. L'istanza della macchina virtuale con controlli di probe falliti non viene rimossa dal servizio di bilanciamento del carico fino a quando non è nuovamente online e il controllo dei probe ne certifica l'integrità. Creare un probe di integrità con il comando [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) per monitorare l'integrità delle macchine virtuali. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alla porta di origine e di destinazione necessaria. Creare una regola di bilanciamento del carico *myHTTPRule* con il comando [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) per l'ascolto sulla porta 80 nel pool front-end *myFrontEnd* e l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end *myBackEndPool* sempre tramite la porta 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Creare i server back-end

In this example, we don't cover virtual machine creation. You can follow the steps in [Create an internal load balancer to load balance VMs using Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) to create two virtual machines to be used as backend servers for the load balancer. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Disable Private Link service network policies on subnet 
Private Link service requires an IP from any subnet of your choice  within a virtual network. Currently, we don’t support Network Policies on these IPs.  Hence, we have to disable the network policies on the subnet. Update the subnet to disable Private Link service network policies with [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato  
 
Create a Private Link service using Standard Load Balancer frontend IP configuration with [az network private-link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create). This example creates a Private Link service named *myPLS* using Standard Load Balancer named *myLoadBalancer* in resource group named *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Once created, take note of the Private Link Service ID. You will need that later for requesting connection to this service.  
 
At this stage, your Private Link service is successfully created and is ready to receive the traffic. Note that above example is only to demonstrate creating Private Link service using Azure CLI.  We haven't configured the load balancer backend pools or any application on the backend pools to listen to the traffic. If you want to see end-to-end traffic flows, you are strongly advised to configure your application behind your Standard Load Balancer.  
 
Next, we will demonstrate how to map this service to a private endpoint in different virtual network using Azure CLI. Again, the example is limited to creating the private endpoint and connecting to Private Link service created above using Azure CLI. Additionally, you can create virtual machines in the virtual network to send/receive traffic to the private endpoint.        
 
## <a name="private-endpoints"></a>Endpoint privati

### <a name="create-the-virtual-network"></a>Creare la rete virtuale 
Create a virtual network with [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). This example creates a virtual network named *myPEVNet* in resource group named *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Create the subnet 
Create a subnet in virtual network with [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). This example creates a subnet named *mySubnet* in virtual network named *myPEVnet* in resource group named *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Disable private endpoint network policies on subnet 
Private endpoint can be created in any subnet of your choice within a virtual network. Currently, we don’t support network policies on private endpoints.  Hence, we have to disable the network policies on the subnet. Update the subnet to disable private endpoint network policies with [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Create private endpoint and connect to private link service 
Create a private endpoint for consuming Private Link service created above in your virtual network:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
You can get the *private-connection-resource-id* with `az network private-link-service show` on Private Link service. The ID will look like:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Show Private Link service connections 
 
See connection requests on your Private Link service  using [az network private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Passaggi successivi
- Learn more about [Azure Private Link service](private-link-service-overview.md)
 
