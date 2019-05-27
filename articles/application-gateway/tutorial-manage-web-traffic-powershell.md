---
title: Gestire il traffico Web - Azure PowerShell
description: Informazioni su come creare un gateway applicazione con un set di scalabilità di macchine virtuali per gestire il traffico Web tramite Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 5/1/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 15c6ba7c5659350143d75dee8bd5d5afcbf426e9
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "66133367"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-azure-powershell"></a>Gestire il traffico Web con un gateway applicazione tramite Azure PowerShell

Il gateway applicazione viene usato per gestire e proteggere il traffico Web verso i server gestiti. È possibile usare Azure PowerShell per creare un [gateway applicazione](overview.md) che usa un [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per permettere ai server back-end di gestire il traffico Web. In questo esempio il set di scalabilità contiene due istanze di macchine virtuali che vengono aggiunte al pool back-end predefinito del gateway applicazione.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un gateway applicazione
> * Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito

Se si preferisce, è possibile completare questa procedura usando l'[interfaccia della riga di comando di Azure](tutorial-manage-web-traffic-cli.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Per trovare la versione, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse di Azure usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete 

Configurare le subnet denominate *myBackendSubnet* e *myAGSubnet* usando [New-AZVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Creare la rete virtuale *myVNet* usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) con le configurazioni di subnet. Creare infine l'indirizzo IP pubblico denominato *myAGPublicIPAddress* con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Queste risorse vengono usate per fornire la connettività di rete al gateway applicazione e alle risorse associate.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

In questa sezione verranno create risorse che supportano il gateway applicazione e infine verrà creato quest'ultimo. Le risorse create includono:

- *Configurazioni IP e porta front-end*:per associare la subnet creata in precedenza al gateway applicazione e assegnare una porta da usare per accedervi.
- *Pool predefinito*: tutti i gateway applicazione devono avere almeno un pool back-end di server.
- *Listener e regola predefiniti*: il listener predefinito è in ascolto del traffico sulla porta assegnata e la regola predefinita invia il traffico al pool predefinito.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Creare le configurazioni IP e la porta front-end

Associare la subnet *myAGSubnet* creata in precedenza al gateway applicazione usando [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Assegnare *myAGPublicIPAddress* al gateway applicazione usando [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[1]

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Creare il pool e le impostazioni back-end

Creare il pool back-end denominato *appGatewayBackendPool* per il gateway applicazione usando [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configurare le impostazioni per i pool di indirizzi back-end usando [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Creare il listener predefinito e la regola

È necessario un listener per consentire al gateway applicazione di instradare il traffico in modo appropriato al pool back-end. In questo esempio viene creato un listener di base in ascolto del traffico nell'URL radice. 

Creare un listener denominato *mydefaultListener* usando [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) con la configurazione front-end e la porta front-end create in precedenza. È necessaria una regola per comunicare al listener quale pool back-end usare per il traffico in ingresso. Creare una regola di base denominata *rule1* usando [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Dopo aver creato le risorse di supporto necessarie, specificare i parametri per il gateway applicazione usando [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) e quindi crearlo usando [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="create-a-virtual-machine-scale-set"></a>Creare un set di scalabilità di macchine virtuali

In questo esempio viene creato un set di scalabilità di macchine virtuali per fornire i server per il pool back-end nel gateway applicazione. Il set di scalabilità viene assegnato al pool back-end quando si configurano le impostazioni IP.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[0].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2_v2 `
  -UpgradePolicyMode Automatic

Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage

Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss

Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig

New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Installare IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss

Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Usare [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) per ottenere l'indirizzo IP pubblico del gateway applicazione. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testare l'URL di base nel gateway applicazione](./media/tutorial-manage-web-traffic-powershell/tutorial-iistest.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passaggi successivi

[Limitare il traffico Web con un web application firewall](./tutorial-restrict-web-traffic-powershell.md)
