---
title: Creare un servizio di collegamento privato di Azure usando Azure PowerShellCreate an Azure private link service using Azure PowerShell Documenti Microsoft
description: Informazioni su come creare un servizio di collegamento privato di Azure usando Azure PowerShellLearn how to create an Azure private link service using Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932075"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Creare un servizio di collegamento privato usando Azure PowerShellCreate a Private Link service using Azure PowerShell
Questo articolo illustra come creare un servizio di collegamento privato in Azure usando Azure PowerShell.This article shows you how to create a private link service in Azure using Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, questo articolo richiede la versione più recente del modulo di Azure PowerShell.If you choose to install and use PowerShell locally, this article requires the latest Azure PowerShell module version. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di creare il collegamento privato, è necessario creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato myResourceGroup nel percorso *WestCentralUS:The following example* creates a resource group named *myResourceGroup* in the WestCentralUS location:

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale per il collegamento privato con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Nell'esempio seguente viene creata una rete virtuale denominata *myvnet* con subnet per frontend (*frontendSubnet*), back-end (*backendSubnet*), collegamento privato (*otherSubnet*):

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>Creare un servizio di bilanciamento del carico internoCreate Internal Load Balancer
Creare un servizio di bilanciamento del carico Standard interno con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Nell'esempio seguente viene creato un servizio di bilanciamento del carico Standard interno utilizzando la configurazione IP front-end, il probe, la regola e il pool back-end creati nei passaggi precedenti:The following example creates an internal Standard Load Balancer using the frontend IP configuration, probe, rule and backend pool that you created in the preceding steps:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato
Creare un servizio di collegamento privato con [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  In questo esempio viene creato un servizio di collegamento privato denominato *myPLS* utilizzando Load Balancer standard nel gruppo di risorse denominato *myResourceGroup*. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Ottenere il servizio di collegamento privatoGet private link service
Ottenere informazioni dettagliate sul servizio di collegamento privato con [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) come segue:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

In questa fase, il servizio Private Link è stato creato correttamente ed è pronto a ricevere il traffico. Si noti che l'esempio precedente è solo per illustrare la creazione di private Link Service tramite PowerShell.Note that above example is only to demonstrate creating Private Link Service using PowerShell.  Non sono stati configurati i pool back-end del servizio di bilanciamento del carico o qualsiasi applicazione nei pool back-end per l'ascolto del traffico. Se si desidera visualizzare i flussi di traffico end-to-end, è consigliabile configurare l'applicazione dietro il servizio di bilanciamento del carico standard. 

Successivamente verrà illustrato come eseguire il mapping di questo servizio a un endpoint privato in una rete virtuale diversa usando PowerShell.Next we will demonstrate how to map this service to a private endpoint in different VNet using PowerShell. Anche in questo caso, l'esempio è limitato alla creazione dell'endpoint privato e alla connessione al servizio di collegamento privato creato in precedenza. È possibile creare macchine virtuali nella rete virtuale per inviare/ricevere traffico all'endpoint privato per la creazione dello scenario. 

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato
### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale per l'endpoint privato con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In questo esempio viene creata una rete virtuale denominata vnetPE nel gruppo di risorse denominato *myResourceGroup:This*example creates a virtual network named *vnetPE* in resource group named myResourceGroup :
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Creare un endpoint privato
Creare un endpoint privato per l'utilizzo del servizio di collegamento privato creato in precedenza nella rete virtuale:Create a private endpoint for consuming private link service created above in your virtual network:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Ottenere l'endpoint privatoGet private endpoint
Ottenere l'indirizzo IP dell'endpoint privato con `Get-AzPrivateEndpoint` i seguenti:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Approvare la connessione all'endpoint privato
Approvare la connessione dell'endpoint privato al servizio di collegamento privato con 'Approve-AzPrivateEndpointConnection'.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul collegamento privato di [AzureLearn](private-link-overview.md) more about Azure private link
 
