---
title: Creare un servizio di collegamento privato di Azure usando Azure PowerShell | Microsoft Docs
description: Informazioni su come creare un servizio di collegamento privato di Azure usando Azure PowerShell
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 09158a935aac023382049d3aa9ce23a711972023
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104753"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Creare un servizio di collegamento privato utilizzando Azure PowerShell
Questo articolo illustra come creare un servizio di collegamento privato in Azure usando Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, questo articolo richiede la versione più recente del modulo Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di poter creare il collegamento privato, è necessario creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *WestCentralUS* :

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale per il collegamento privato con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Nell'esempio seguente viene creata una rete virtuale denominata *myvnet* con subnet per frontend (*frontendSubnet*), backend (*backendSubnet*), collegamento privato (*otherSubnet*):

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
## <a name="create-internal-load-balancer"></a>Crea Load Balancer interno
Creare un Load Balancer Standard interno con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). L'esempio seguente crea un Load Balancer Standard interno usando la configurazione IP front-end, il probe, la regola e il pool back-end creati nei passaggi precedenti:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Creazione di un servizio di collegamento privato
Creare un servizio di collegamento privato con [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  Questo esempio crea un servizio di collegamento privato denominato *myPLS* usando Load Balancer standard in un gruppo di risorse denominato *myResourceGroup*. 
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
-LoadBalancerFrontendIpConfiguration $frontendIP`
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Ottenere il servizio di collegamento privato
Per informazioni dettagliate sul servizio di collegamento privato, usare [New-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) come indicato di seguito:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

In questa fase, il servizio di collegamento privato viene creato correttamente ed è pronto per ricevere il traffico. Si noti che l'esempio precedente è solo per dimostrare la creazione di un servizio di collegamento privato tramite PowerShell.  I pool back-end del servizio di bilanciamento del carico o qualsiasi applicazione nei pool back-end non sono stati configurati per l'ascolto del traffico. Se si desidera visualizzare i flussi di traffico end-to-end, è consigliabile configurare l'applicazione in base al servizio di bilanciamento del carico standard. 

Verrà ora illustrato come eseguire il mapping di questo servizio a un endpoint privato in VNet diversi usando PowerShell. Anche in questo caso, l'esempio è limitato alla creazione dell'endpoint privato e alla connessione al servizio di collegamento privato creato in precedenza. È possibile creare macchine virtuali nella rete virtuale per inviare/ricevere traffico all'endpoint privato per la compilazione dello scenario. 

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato
### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale per l'endpoint privato con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Questo esempio crea una rete virtuale denominata *vnetPE* nel gruppo di risorse denominato *myResourceGroup*:
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName myResourceGroup `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```
### <a name="create-a-private-endpoint"></a>Creare un endpoint privato
Creare un endpoint privato per l'utilizzo del servizio di collegamento privato creato in precedenza nella rete virtuale:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
 ```
### Get private endpoint
Get the IP address of the private endpoint with `Get-AzPrivateEndpoint` as follows:

```azurepowershell

# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 
 ```
  
### <a name="approve-the-private-endpoint-connection"></a>Approva la connessione all'endpoint privato
Approva la connessione di endpoint privato al servizio di collegamento privato con ' Approve-AzPrivateEndpointConnection '.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 
 ``` 
## <a name="next-steps"></a>Passaggi successivi
- Scopri di più sul [collegamento privato di Azure](private-link-overview.md)
 
