---
title: Creazione di un servizio di collegamento privato di Azure tramite l'interfaccia della riga di comando
description: Informazioni su come creare un servizio di collegamento privato Azure con l'interfaccia della riga di comando di Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: c67d2cd4e90b2fa61a4d95e89a68c888a6e1fe3f
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273644"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Creare un servizio di collegamento privato usando l'interfaccia della riga di comando
Questo articolo illustra come creare un servizio di collegamento privato in Azure usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se invece si decide di installare e usare l'interfaccia della riga di comando di Azure localmente, questa Guida introduttiva richiede l'uso della versione più recente dell'interfaccia della riga di comando Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato
### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per poter creare una rete virtuale, è prima necessario creare un gruppo di risorse per l'hosting della rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). Questo esempio crea un gruppo di risorse denominato *myResourceGroup* nella località *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Questo esempio crea una rete virtuale predefinita denominata *myVirtualNetwork* con una subnet denominata *Subnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Creare una subnet
Creare una subnet per la rete virtuale con [AZ Network VNET subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). In questo esempio viene creata una subnet *denominata subnet nella* rete virtuale *myVirtualNetwork* :

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Creare un Load Balancer interno 
Creare un servizio di bilanciamento del carico interno con [AZ Network lb create](/cli/azure/network/lb#az-network-lb-create). Questo esempio crea un servizio di bilanciamento del carico interno denominato *myILB* nel gruppo di risorse denominato *myResourceGroup*. 

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

In questo esempio non viene trattata la creazione di macchine virtuali. È possibile seguire la procedura descritta in [creare un servizio di bilanciamento del carico interno per bilanciare il carico delle VM usando l'interfaccia](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) della riga di comando di Azure per creare due macchine virtuali da usare come server back-end per il bilanciamento del carico. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Disabilitare i criteri di rete del servizio di collegamento privato nella subnet 
Il servizio di collegamento privato richiede un indirizzo IP da qualsiasi subnet scelta all'interno di una rete virtuale. Attualmente non sono supportati i criteri di rete in questi IP.  Di conseguenza, è necessario disabilitare i criteri di rete nella subnet. Aggiornare la subnet per disabilitare i criteri di rete del servizio di collegamento privato con [AZ Network VNET subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato  
 
Creare un servizio di collegamento privato usando Load Balancer Standard configurazione IP front [-end con AZ network private-Link-Service create](/cli/azure/network/private-link-service#az-network-private-link-service-create). Questo esempio crea un servizio di collegamento privato denominato *myPLS* usando Load Balancer standard denominato *myLoadBalancer* nel gruppo di risorse denominato *myResourceGroup*. 
 
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
Una volta creati, prendere nota dell'ID del servizio di collegamento privato. Questa operazione sarà necessaria in seguito per la richiesta di connessione al servizio.  
 
In questa fase, il servizio di collegamento privato viene creato correttamente ed è pronto per ricevere il traffico. Si noti che nell'esempio precedente viene illustrata la creazione di un servizio di collegamento privato tramite l'interfaccia della riga di comando  I pool back-end del servizio di bilanciamento del carico o qualsiasi applicazione nei pool back-end non sono stati configurati per l'ascolto del traffico. Se si desidera visualizzare i flussi di traffico end-to-end, è consigliabile configurare l'applicazione dietro la Load Balancer Standard.  
 
Verrà ora illustrato come eseguire il mapping di questo servizio a un endpoint privato in una rete virtuale diversa usando l'interfaccia della riga di comando di Azure. Anche in questo caso, è possibile creare l'endpoint privato e connettersi al servizio di collegamento privato creato in precedenza tramite l'interfaccia della riga di comando di Azure. Inoltre, è possibile creare macchine virtuali nella rete virtuale per inviare/ricevere traffico all'endpoint privato.        
 
## <a name="private-endpoints"></a>Endpoint privati

### <a name="create-the-virtual-network"></a>Creare la rete virtuale 
Creare una rete virtuale con [AZ Network VNET create](/cli/azure/network/vnet#az-network-vnet-create). Questo esempio crea una rete virtuale denominata *myPEVNet* nel gruppo di risorse denominato *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Creare la subnet 
Creare una subnet nella rete virtuale con [AZ Network VNET subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Questo esempio crea una subnet denominata *subnet nella rete virtuale denominata* *myPEVnet* nel gruppo di risorse denominato *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Disabilitare i criteri di rete dell'endpoint privato nella subnet 
L'endpoint privato può essere creato in qualsiasi subnet di propria scelta all'interno di una rete virtuale. Attualmente non sono supportati i criteri di rete sugli endpoint privati.  Di conseguenza, è necessario disabilitare i criteri di rete nella subnet. Aggiornare la subnet per disabilitare i criteri di rete di endpoint privati con [AZ Network VNET subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Creare un endpoint privato e connettersi al servizio di collegamento privato 
Creare un endpoint privato per l'utilizzo del servizio di collegamento privato creato in precedenza nella rete virtuale:
  
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
È possibile ottenere la *connessione privata-Resource-ID* con `az network private-link-service show` il servizio di collegamento privato. L'ID sarà simile al seguente:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/Providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Mostra connessioni al servizio di collegamento privato 
 
Vedere la pagina relativa alle richieste di connessione nel servizio di collegamento privato tramite [AZ network private-Link-Service Show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [servizio di collegamento privato di Azure](private-link-service-overview.md)
 
