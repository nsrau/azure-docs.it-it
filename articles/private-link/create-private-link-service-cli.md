---
title: Creare un servizio Azure Private Link usando l'interfaccia della riga di comando di AzureCreate an Azure Private Link service using Azure CLI
description: Informazioni su come creare un servizio Azure Private Link usando l'interfaccia della riga di comando di AzureLearn how to create an Azure Private Link service using Azure CLI
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350247"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Creare un servizio di collegamento privato usando l'interfaccia della riga di comando di AzureCreate a Private Link service using Azure CLI
Questo articolo illustra come creare un servizio di collegamento privato in Azure usando l'interfaccia della riga di comando di Azure.This article shows you how to create a Private Link service in Azure using Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se invece si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, questa guida introduttiva richiede l'uso della versione più recente dell'interfaccia della riga di comando di Azure.If you decide to install and use Azure CLI locally instead, this quickstart requires you to use the latest Azure CLI version. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato
### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per poter creare una rete virtuale, è prima necessario creare un gruppo di risorse per l'hosting della rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). Questo esempio crea un gruppo di risorse denominato *myResourceGroup* nella località *westcentralus*:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). In questo esempio viene creata una rete virtuale predefinita denominata *myVirtualNetwork* con una subnet denominata *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Creare una subnet
Creare una subnet per la rete virtuale con [az subnet vnet di rete create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). In questo esempio viene creata una subnet denominata mySubnet nella rete virtuale *myVirtualNetwork:This example* creates a subnet named *mySubnet* in the myVirtualNetwork virtual network:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Creare un servizio di bilanciamento del carico internoCreate a Internal Load Balancer 
Creare un servizio di bilanciamento del carico interno con [az network lb create](/cli/azure/network/lb#az-network-lb-create). In questo esempio viene creato un servizio di bilanciamento del carico interno denominato *myILB* nel gruppo di risorse denominato *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Creare un probe di integrità per il servizio di bilanciamento del carico

Un probe di integrità controlla tutte le istanze di una macchina virtuale per assicurarsi che possano ricevere il traffico di rete. L'istanza della macchina virtuale con controlli di probe falliti non viene rimossa dal servizio di bilanciamento del carico fino a quando non è nuovamente online e il controllo dei probe ne certifica l'integrità. Creare un probe di integrità con [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) per monitorare l'integrità delle macchine virtuali. 

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

In questo esempio non viene coperta la creazione di macchine virtuali. È possibile seguire i passaggi descritti in Creare un servizio di [bilanciamento del carico interno per bilanciare il carico](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) delle macchine virtuali usando l'interfaccia della riga di comando di Azure per creare due macchine virtuali da usare come server back-end per il servizio di bilanciamento del carico. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Disabilitare i criteri di rete del servizio Private Link nella subnetDisable Private Link service network policies on subnet 
Il servizio Private Link richiede un indirizzo IP da qualsiasi subnet di propria scelta all'interno di una rete virtuale. Attualmente, non sono supportati i criteri di rete su questi IP.  Di conseguenza, è necessario disabilitare i criteri di rete nella subnet. Aggiornare la subnet per disabilitare i criteri di rete del servizio Private Link con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato  
 
Creare un servizio Private Link utilizzando la configurazione IP front-end di Load Balancer standard con [az network private-link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create). In questo esempio viene creato un servizio Private Link denominato *myPLS* utilizzando il servizio di bilanciamento del carico standard denominato *myLoadBalancer* nel gruppo di risorse denominato *myResourceGroup*. 
 
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
Una volta creato, prendere nota dell'ID servizio di collegamento privato. Sarà necessario in un secondo momento per richiedere la connessione a questo servizio.  
 
In questa fase, il servizio Private Link viene creato correttamente ed è pronto a ricevere il traffico. Si noti che l'esempio precedente è solo per illustrare la creazione di servizio di collegamento privato usando l'interfaccia della riga di comando di Azure.Note that above example is only to demonstrate creating Private Link service using Azure CLI.  Non sono stati configurati i pool back-end del servizio di bilanciamento del carico o qualsiasi applicazione nei pool back-end per l'ascolto del traffico. Se si desidera visualizzare i flussi di traffico end-to-end, si consiglia di configurare l'applicazione dietro il servizio di bilanciamento del carico Standard.  
 
Successivamente, verrà illustrato come eseguire il mapping di questo servizio a un endpoint privato in una rete virtuale diversa usando l'interfaccia della riga di comando di Azure.Next, we will demonstrate how to map this service to a private endpoint in different virtual network using Azure CLI. Anche in questo caso, l'esempio è limitato alla creazione dell'endpoint privato e alla connessione al servizio Private Link creato in precedenza tramite l'interfaccia della riga di comando di Azure.Again, the example is limited to creating the private endpoint and connecting to Private Link service created above using Azure CLI. Inoltre, è possibile creare macchine virtuali nella rete virtuale per inviare/ricevere traffico all'endpoint privato.        
 
## <a name="private-endpoints"></a>Endpoint privati

### <a name="create-the-virtual-network"></a>Creare la rete virtuale 
Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). In questo esempio viene creata una rete virtuale denominata *myPEVNet* nel gruppo di risorse denominato *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Creare la subnetCreate the subnet 
Creare una subnet nella rete virtuale con [az subnet vnet di rete create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). In questo esempio viene creata una subnet denominata *mySubnet* nella rete virtuale denominata *myPEVnet* nel gruppo di risorse denominato *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Disabilitare i criteri di rete degli endpoint privati nella subnetDisable private endpoint network policies on subnet 
L'endpoint privato può essere creato in qualsiasi subnet di propria scelta all'interno di una rete virtuale. Attualmente, non sono supportati i criteri di rete negli endpoint privati.  Di conseguenza, è necessario disabilitare i criteri di rete nella subnet. Aggiornare la subnet per disabilitare i criteri di rete degli endpoint privati con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Creare un endpoint privato e connettersi al servizio di collegamento privatoCreate private endpoint and connect to private link service 
Creare un endpoint privato per l'utilizzo del servizio di collegamento privato creato in precedenza nella rete virtuale:Create a private endpoint for consuming Private Link service created above in your virtual network:
  
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
È possibile ottenere l'ID-risorsa `az network private-link-service show` di connessione privata con il servizio Private Link.You can get the *private-connection-resource-id* with on Private Link service. L'ID sarà simile al seguente:   
/subscriptions/subID/resourceGroups/resourcegroup/resourcegroupname /providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** *resourcegroupname* 
 
## <a name="show-private-link-service-connections"></a>Mostra connessioni al servizio di collegamento privato 
 
Visualizzare le richieste di connessione nel servizio Private Link utilizzando [az network private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [servizio Azure Private LinkLearn](private-link-service-overview.md) more about Azure Private Link service
 
