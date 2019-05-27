---
title: Creare un gateway applicazione che ospita più siti Web - Azure PowerShell
description: Informazioni su come creare un gateway applicazione che ospita più siti Web usando Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/13/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 961284f7a1afc8d4e420b3d9a43d987866fcc384
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "66133405"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-azure-powershell"></a>Creare un gateway applicazione che ospita più siti Web tramite Azure PowerShell

È possibile usare Azure PowerShell per [configurare l'hosting di più siti Web](multiple-site-overview.md) quando si crea un [gateway applicazione](overview.md). In questa esercitazione si definiranno pool di indirizzi back-end usando set di scalabilità di macchine virtuali. e quindi si configurano i listener e le regole in base ai domini di cui si è proprietari per assicurarsi che il traffico Web raggiunga i server appropriati nei pool. Questa esercitazione presuppone che l'utente sia proprietario di più domini e che usi gli esempi di *www.contoso.com* e *www.fabrikam.com*.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un gateway applicazione
> * Creare i listener back-end
> * Creare le regole di routing
> * Creare set di scalabilità di macchine virtuali con i pool back-end
> * Creare un record CNAME nel dominio

![Esempio di routing multisito](./media/tutorial-multiple-sites-powershell/scenario.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 1.0.0 o successiva. Per trovare la versione, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse di Azure usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete

Creare le configurazioni di subnet usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Creare la rete virtuale usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) con le configurazioni di subnet. Creare infine l'indirizzo IP pubblico usando [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Queste risorse vengono usate per fornire la connettività di rete al gateway applicazione e alle risorse associate.

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

### <a name="create-the-ip-configurations-and-frontend-port"></a>Creare le configurazioni IP e la porta front-end

Associare la subnet creata in precedenza al gateway applicazione usando [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Assegnare l'indirizzo IP pubblico al gateway applicazione usando [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

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

### <a name="create-the-backend-pools-and-settings"></a>Creare i pool e le impostazioni back-end

Creare il primo pool di indirizzi back-end per il gateway applicazione usando [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configurare le impostazioni del pool usando [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$contosoPool = New-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool

$fabrikamPool = New-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listeners-and-rules"></a>Creare i listener e le regole

I listener sono necessari per permettere al gateway applicazione di instradare il traffico ai pool di indirizzi back-end nel modo appropriato. In questa esercitazione vengono creati due listener per i due domini. In questo esempio vengono creati i listener per i domini *www.contoso.com* e *www.fabrikam.com*.

Creare il primo listener usando [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) con la configurazione front-end e la porta front-end create in precedenza. È necessaria una regola per comunicare al listener quale pool back-end usare per il traffico in ingresso. Creare una regola di base denominata *contosoRule* usando [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$contosolistener = New-AzApplicationGatewayHttpListener `
  -Name contosoListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.contoso.com"

$fabrikamlistener = New-AzApplicationGatewayHttpListener `
  -Name fabrikamListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.fabrikam.com"

$contosoRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name contosoRule `
  -RuleType Basic `
  -HttpListener $contosoListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings

$fabrikamRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name fabrikamRule `
  -RuleType Basic `
  -HttpListener $fabrikamListener `
  -BackendAddressPool $fabrikamPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Ora che sono state create le risorse di supporto necessarie, specificare i parametri per il gateway applicazione usando [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) e quindi crearlo usando [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool, $fabrikamPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $contosoListener, $fabrikamListener `
  -RequestRoutingRules $contosoRule, $fabrikamRule `
  -Sku $sku
```

## <a name="create-virtual-machine-scale-sets"></a>Creare set di scalabilità di macchine virtuali

In questo esempio si creano due set di scalabilità di macchine virtuali che supportano i due pool back-end creati. I set di scalabilità creati sono denominati *myvmss1* e *myvmss2*. Ogni set di scalabilità contiene due istanze di macchina virtuale in cui si installa IIS. Il set di scalabilità viene assegnato al pool back-end quando si configurano le impostazioni IP.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$contosoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw

$fabrikamPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 2; $i++)
{
  if ($i -eq 1) 
  {
    $poolId = $contosoPool.Id
  }
  if ($i -eq 2)
  {
    $poolId = $fabrikamPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Installare IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 2; $i++)
{
  $vmss = Get-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -VMScaleSetName myvmss$i

  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="create-cname-record-in-your-domain"></a>Creare un record CNAME nel dominio

Dopo aver creato il gateway applicazione con l'indirizzo IP pubblico, è possibile ottenere l'indirizzo DNS e usarlo per creare un record CNAME nel dominio. È possibile usare [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) per ottenere l’indirizzo DNS del gateway applicazione. Copiare il valore *fqdn* di DNSSettings e usarlo come valore del record CNAME creato. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare quando il gateway applicazione viene riavviato.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Immettere il nome di dominio nella barra degli indirizzi del browser. Ad esempio, http://www.contoso.com.

![Testare il sito contoso nel gateway applicazione](./media/tutorial-multiple-sites-powershell/application-gateway-iistest.png)

Sostituire l'indirizzo con l'altro dominio come nell'esempio seguente:

![Testare il sito fabrikam nel gateway applicazione](./media/tutorial-multiple-sites-powershell/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un gateway applicazione
> * Creare i listener back-end
> * Creare le regole di routing
> * Creare set di scalabilità di macchine virtuali con i pool back-end
> * Creare un record CNAME nel dominio

> [!div class="nextstepaction"]
> [Creare un gateway applicazione con regole di routing basate su percorsi URL](./tutorial-url-route-powershell.md)
