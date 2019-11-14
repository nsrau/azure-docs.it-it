---
title: Terminazione SSL con PowerShell
titleSuffix: Azure Application Gateway
description: Informazioni su come creare un gateway applicazione e aggiungere un certificato per la terminazione SSL con Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2ce5b8472fe10b51cff34677c9ce5a89888bdc01
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075069"
---
# <a name="create-an-application-gateway-with-ssl-termination-using-azure-powershell"></a>Creare un gateway applicazione con la terminazione SSL tramite Azure PowerShell

È possibile usare Azure PowerShell per creare un [gateway applicazione](overview.md) con un certificato per la [terminazione SSL](ssl-overview.md) che usa un [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per i server back-end. In questo esempio il set di scalabilità contiene due istanze di macchine virtuali che vengono aggiunte al pool back-end predefinito del gateway applicazione. 

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un certificato autofirmato
> * Configurare una rete
> * Creare un gateway applicazione con il certificato
> * Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Questo articolo richiede il modulo Azure PowerShell versione 1.0.0 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installare il modulo di Azure PowerShell). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per la produzione è necessario importare un certificato valido firmato da un provider attendibile. Per questo articolo, si crea un certificato autofirmato usando [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). È possibile usare [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) con l'identificazione personale restituita per esportare un file pfx dal certificato.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

L'output sarà simile al risultato seguente:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Usare l'identificazione personale per creare il file pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse di Azure denominato *myResourceGroupAG* con [New-AZResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete

Configurare le subnet denominate *myBackendSubnet* e *myAGSubnet* usando [New-AZVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Creare la rete virtuale denominata *myVNet* usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) con le configurazioni di subnet. Creare infine l'indirizzo IP pubblico denominato *myAGPublicIPAddress* con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Queste risorse vengono usate per fornire la connettività di rete al gateway applicazione e alle risorse associate.

```powershell
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
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

### <a name="create-the-ip-configurations-and-frontend-port"></a>Creare le configurazioni IP e la porta front-end

Associare la subnet *myAGSubnet* creata in precedenza al gateway applicazione usando [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Assegnare *myAGPublicIPAddress* al gateway applicazione usando [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```powershell
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
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Creare il pool e le impostazioni back-end

Creare il pool back-end denominato *appGatewayBackendPool* per il gateway applicazione usando [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configurare le impostazioni per il pool back-end usando [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```powershell
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

È necessario un listener per consentire al gateway applicazione di instradare il traffico in modo appropriato al pool back-end. In questo esempio viene creato un listener di base in ascolto del traffico HTTPS a livello dell'URL radice. 

Creare un oggetto certificato con il comando [New-AZApplicationGatewaySslCertificate](/powershell/module/az.network/new-azapplicationgatewaysslcertificate) e quindi creare un listener denominato *mydefaultListener* con il comando [New-AZApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) con la configurazione front-end, la porta front-end e il certificato creato in precedenza. È necessaria una regola per comunicare al listener quale pool back-end usare per il traffico in ingresso. Creare una regola di base denominata *rule1* usando [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText

$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd

$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -SslCertificate $cert

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-certificate"></a>Creare il gateway applicazione con il certificato

Ora che sono state create le risorse di supporto necessarie, specificare i parametri per il gateway applicazione denominato *myAppGateway* usando [New-AZApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) e quindi crearlo usando [New-AZApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) con il certificato.

### <a name="create-the-application-gateway"></a>Creare il gateway applicazione

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
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
  -Sku $sku `
  -SslCertificates $cert
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
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
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

Si può usare [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) per ottenere l'indirizzo IP pubblico del gateway applicazione. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Avviso di sicurezza](./media/tutorial-ssl-powershell/application-gateway-secure.png)

Per accettare l'avviso di sicurezza se si è usato un certificato autofirmato, selezionare **Dettagli** e quindi **Continua per la pagina Web**. Il sito Web IIS protetto viene quindi visualizzato come illustrato nell'esempio seguente:

![Testare l'URL di base nel gateway applicazione](./media/tutorial-ssl-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passaggi successivi

[Creare un gateway applicazione che ospita più siti Web](./tutorial-multiple-sites-powershell.md)
